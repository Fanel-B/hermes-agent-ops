# Configuration — ce qui a été réglé, et pourquoi

Ce document est un **historique**, pas une liste de réglages. Chaque entrée dit ce qui a été choisi,
quelle alternative existait, et ce que ça a coûté.

## Modèle et routage

- **Défaut** : un modèle rapide et bon marché (`deepseek/deepseek-v4.1-flash` via OpenRouter), parce
  que l'immense majorité des tours sont courts : lire un fichier, lancer un script, formater un
  message.
- **Routage par tâche** : les analyses longues partent sur un modèle à grand contexte, le scoring
  complexe sur un modèle de raisonnement, et les documents importants (CV, lettre, texte public)
  sont confiés à un modèle plus fort, appelé explicitement.
- **Pourquoi** : payer le prix d'un grand modèle pour répondre « oui, le test passe » est du
  gaspillage ; l'utiliser pour rédiger une lettre de motivation ne l'est pas.

## Garde-fous du raisonnement

- **Compression de contexte** activée, avec protection des derniers messages et du début de
  conversation : une session longue ne doit pas perdre ses consignes.
- **Garde-fous de boucle** : avertissement après deux échecs identiques, arrêt dur après cinq — un
  agent qui répète la même commande qui échoue coûte cher et ne progresse pas.
- **Arrêt dur en mode non interactif** : dans une tâche planifiée, personne ne peut répondre à une
  question, donc mieux vaut s'arrêter proprement que tourner en rond.

## Fuseau horaire

`Europe/Paris`, **explicitement**, alors que le serveur est en UTC. Leçon appliquée : toute heure
affichée est convertie ; une heure locale écrite par erreur dans un agenda devient une contrainte
fausse. Un événement sans horaire annoncé est posé en **journée entière**.

## Canaux et sortie

- Un seul canal (Telegram), un seul fil.
- **Tâches silencieuses par défaut** : une tâche à script n'écrit que si quelque chose mérite
  l'attention. La sortie vide est un succès, pas un oubli.
- Notifications de processus longs regroupées, pour ne pas noyer le fil.

## Mémoire

- Mémoire durable et profil utilisateur **bornés en caractères** (quelques milliers). Quand la limite
  approche, on **remplace ou on fusionne** — jamais on ne cesse d'écrire.
- À quoi ça sert : qui je suis, les faits d'environnement stables, les conventions. Tout le reste va
  dans une compétence ou dans l'historique.

## Délégation

- Plafond de sous-agents parallèles et d'itérations par mission, volontairement fini : un lot de
  travail parallèle doit apporter une réponse, pas un flux de texte à relire.
- Règle de confiance : **la synthèse d'un sous-agent est une déclaration, pas une preuve.** Un
  sous-agent qui écrit « c'est fait » n'est cru qu'après lecture de l'objet produit.

## Ce qui a été refusé, volontairement

| Réglage | Choix | Raison |
| --- | --- | --- |
| Télémétrie | désactivée | rien ne sort du serveur sans décision explicite |
| Utilisateurs autorisés | liste nominative | un agent qui répond à tout le monde est une porte ouverte |
| Approbations destructives | confirmées | une suppression ne se demande pas en une ligne |
| Contournement anti-robot | refusé | voir [garde-fous](garde-fous.md) |
| Envoi automatique de candidature | refusé | l'agent prépare, l'humain envoie |

## Ce que la configuration ne contient pas

Aucun secret. Les clés vivent dans un fichier à permissions restreintes ; la configuration ne décrit
que leur **rôle**. C'est vérifiable en parcourant ce fichier : on y lit des noms, jamais des valeurs.
