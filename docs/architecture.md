# Architecture

L'agent repose sur une séparation stricte : **le raisonnement, l'exécution, la planification et la
mémoire sont quatre choses distinctes**, chacune observable séparément. C'est ce qui permet de dire
*ce qui* a échoué quand quelque chose échoue.

## Les pièces

### 1. La passerelle (`hermes-gateway.service`)

Un service `systemd` qui maintient la conversation ouverte sur Telegram. C'est le seul point d'entrée
humain : je parle à l'agent dans un fil, il me répond là. Le service est relancé automatiquement ; il
ne contient aucun secret en clair (ils vivent dans `~/.hermes/.env`, en `600`).

Pourquoi un service plutôt qu'un terminal : les tâches planifiées tournent la nuit, et une alerte de
stage qui arrive à 19 h n'a de valeur que si personne n'est devant la machine.

### 2. Le planificateur (`cron`)

Onze tâches planifiées, dont dix actives. Deux familles :

- **les tâches à script** (`job-search-alert`, `job-search-daily`, `job-search-purge`,
  `jobbot-site-refresh`, `rappel-evenements`…) : elles n'appellent pas de modèle du tout, elles
  exécutent du code déterministe et **restent muettes quand tout va bien** — une tâche qui parle pour
  ne rien dire est une tâche qu'on cesse de lire ;
- **les tâches à consigne** (relances de candidature) : elles réveillent le modèle avec un texte
  précis et un contexte borné.

### 3. Les compétences (73)

Une compétence est une procédure écrite, chargée **seulement quand la tâche la concerne** : API d'un
site, conventions d'un dépôt, méthode de vérification. Ce dépôt en montre le principe, pas le
contenu personnel.

### 4. L'exécution

Terminal, fichiers, recherche web, navigateur piloté, et **délégation** : l'agent peut confier un lot
de travail à des sous-agents parallèles, chacun avec son contexte isolé — le résultat remonte sous
forme de synthèse, jamais sous forme de flux brut. C'est ce qui permet de traiter « les 287 offres »
sans saturer la conversation.

### 5. La mémoire

Deux étages, volontairement petits :

- **la mémoire durable** (notes factuelles, quelques milliers de caractères) injectée à chaque tour :
  qui je suis, ce qui est stable dans l'environnement, les conventions ;
- **l'historique de session**, interrogeable, où vit le détail.

La règle : *une procédure va dans une compétence, un fait durable va dans la mémoire, le reste n'est
pas mémorisé*. C'est ce qui évite qu'un agent devienne un dépotoir qui se répète.

### 6. Les secrets

Trois niveaux : le coffre de mots de passe (saisi une fois par site, jamais affiché), le fichier
d'environnement du serveur (`600`), et — pour Git — un assistant de credentials qui lit le jeton au
moment du `push` sans jamais l'écrire dans un dépôt. Voir [garde-fous](garde-fous.md).

## Le cycle d'une automatisation

```
1. une tâche planifiée se réveille (script, sans modèle)
2. elle lit la source de vérité (base, API, agenda) et produit un fait
3. le fait est journalisé, mesuré, ou comparé à une référence connue
4. si et seulement si quelque chose mérite l'attention humaine :
      → message Telegram, borné, avec le lien et le chiffre
   sinon :
      → silence (et une ligne dans un journal local)
```

## Ce que l'architecture refuse

- **Aucun modèle dans le chemin critique des données.** La collecte et la note d'une offre sont du
  code déterministe : la même entrée donne toujours la même sortie, et on peut la recalculer à la
  main. Un modèle qui note des offres produirait un classement qu'on ne saurait pas défendre.
- **Aucun envoi automatique.** L'agent prépare, l'humain envoie.
- **Aucun secret dans un dépôt, un journal ou la conversation.**
