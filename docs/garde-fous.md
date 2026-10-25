# Garde-fous

Les limites de l'agent ne sont pas des intentions : elles sont dans le code, dans les tests, ou dans
le refus explicite de faire. Ce document liste celles qui tiennent, avec la manière dont chacune est
tenue.

## 1. Aucun envoi sans un humain

L'agent collecte, note, prépare, suit, relance — **il n'envoie jamais une candidature**. Ce n'est pas
une préférence : c'est une règle de conception, vérifiable dans le fait qu'aucun chemin de code ne
possède d'appel d'envoi vers un portail.

## 2. Aucune donnée inventée

Un champ absent dans la source reste absent : `null`, et l'interface affiche « non précisé ». Cela
vaut pour tout — y compris pour les informations qui « arrangeraient » une démonstration. Deux
conséquences visibles :

- une offre dont le type de contrat n'est pas renseigné n'est pas déguisée en stage : elle sort dans
  un bloc séparé, avec un badge ;
- un document de candidature qui mentionne une compétence non prouvée est **refusé au moment de sa
  génération**, pas corrigé plus tard.

## 3. Une compétence n'entre dans un document que si elle est prouvée

Un référentiel unique recense ce qui est démontré, et un contrôle automatique vérifie chaque
compétence citée contre ce référentiel. Rien d'autre n'entre : ni ce qui est « en cours
d'apprentissage », ni ce qui est vrai mais non écrit.

*Faux positif trouvé par ce contrôle lui-même* : la comparaison se faisait par sous-chaîne, et « ML »
(machine learning) était considéré comme maîtrisé parce que « HT**ML** » contient « ml ». Trois
offres affichaient donc une case verte devant une compétence absente. Corrigé — la comparaison se
fait désormais mot entier dans les deux sens. Voir le [journal](ops/journal.md).

## 4. Les secrets

- Un mot de passe ou un code de vérification **ne passe jamais par la conversation**, même si une
  page ou l'utilisateur le montre. Il est saisi une fois, dans le coffre.
- Un code d'autorisation OAuth est échangé par un script, jamais recopié dans un message.
- Un jeton ne vit ni dans un dépôt, ni dans un journal, ni dans un fichier de configuration de
  dépôt : pour Git, un assistant de credentials lit le jeton au moment de l'usage.
- **Incident réel** : le jeton d'un dépôt était écrit en clair dans l'URL du dépôt distant, donc
  recopiable en même temps que le dossier. Trouvé, mesuré, corrigé.

## 5. Collecte : la légalité avant la quantité

- `robots.txt` et `sitemaps` d'abord ; les `Crawl-delay` sont honorés.
- **Aucun contournement de protection anti-robot.** Un site qui refuse (erreur 403, pare-feu) est
  abandonné pour cette entreprise, et c'est écrit comme tel. Quatre sources sont dans ce cas
  aujourd'hui, et c'est assumé : ce qui n'est pas accessible légalement n'est pas collecté.
- Les API publiques sont appelées avec leurs quotas (pagination, limite par minute), jamais en force.
- Deux minutes par portail, puis on passe : s'acharner sur une page qui résiste est un mauvais calcul.

## 6. Honnêteté du calcul

- La note affichée est **la somme exacte des lignes** de son bulletin : quand une note est bornée, une
  ligne « plafond » existe pour que l'addition reste vraie.
- Le même calcul existe en deux langages (pipeline et site) et **un contrôle croisé les compare
  offre par offre, ligne à ligne, accents compris**. Aujourd'hui : 290 offres, 0 désaccord.
- Une affirmation non mesurable est écrite « non mesurable » — jamais remplacée par un adjectif.

## 7. Ce que l'agent ne décide pas

Le modèle de langage **n'intervient pas** dans la collecte ni dans la note. Il ne choisit pas quelles
offres sont bonnes, il ne fabrique pas de score. Il pilote, il explique, il journalise. Le jour où il
notera, il faudra pouvoir recalculer ce qu'il a fait — c'est la raison de cette séparation.
