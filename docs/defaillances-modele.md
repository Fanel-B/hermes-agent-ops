# Défaillances du modèle — ce qui arrive quand il rate

Ce dépôt décrit beaucoup ce que le modèle **ne fait pas** : il ne collecte pas, il ne note pas, il
n'envoie rien. Ce document décrit l'autre moitié, celle qu'on ne trouve nulle part ailleurs : **ce qui
se passe quand il échoue**.

Les manques sont écrits comme des manques. C'est l'esprit du [journal](../ops/journal.md) : un lecteur
technique préfère « pas de chaîne de repli à ce jour » à un silence.

## Où le modèle intervient

Trois endroits, et aucun n'est dans le chemin critique des données :

| Usage | Modèle | Chemin |
| --- | --- | --- |
| L'agent lui-même (conversation, tâches planifiées) | `deepseek/deepseek-v4.1-flash` (OpenRouter) | passerelle, un seul canal |
| Rédaction des documents de candidature (CV, lettre) | CLI `claude`, **modèle figé `opus`** (repli `sonnet`) depuis le 26/10/2026, lancée en processus séparé | `cv_generator.py`, `lettre_generator.py`, via `journal_modeles.py` |
| Pitch court dans les messages Telegram | CLI `claude`, **même modèle figé**, avec **repli sur un gabarit fixe** | `telegram_commands.py`, via `journal_modeles.py` |

Le modèle n'écrit **jamais** en base, ne calcule aucun score et ne publie rien : il propose du texte,
un script le vérifie, puis l'écrit ou le refuse.

## 1. Comment une sortie invalide est détectée

Chaque appel de modèle est suivi d'un contrôle, et le contrôle est dans le code, pas dans mon
appréciation.

**Documents (CV)** — `claude_edits()` attend une réponse JSON contenant une liste `edits`. Quatre
situations sont traitées explicitement :

| Sortie | Traitement |
| --- | --- |
| Le processus échoue (code retour ≠ 0) | exception, avec le début du message d'erreur |
| Réponse sans JSON | exception « réponse sans JSON » |
| JSON valide sans champ `edits` (ou d'un autre type que liste) | exception « champ `edits` absent » |
| Délai dépassé | exception, l'appel est borné par un délai maximal |

Il n'y a **pas** de cinquième cas toléré : une réponse vide tombe dans « réponse sans JSON ». Le
document n'est alors pas écrit du tout.

**Documents (lettre)** — le texte produit passe par un vérificateur qui contrôle le nombre de mots
(250 à 350), l'absence de tiret cadratin, les ouvertures bannies, le nombre de projets cités, la
présence d'une intention d'apprendre et le test de suppression (si retirer le nom de l'entreprise ne
change rien, la lettre est interchangeable). Une lettre générée sans mes propres mots part avec un
drapeau **BROUILLON** et l'alerte le dit.

**Pitch Telegram** — si le modèle échoue, un **gabarit fixe** prend le relais, et le message signale
que le pitch vient du repli. Ici, l'échec est absorbé : mieux vaut un pitch standard qu'une absence de
réponse.

### Ce que chaque appel laisse derrière lui (ajouté le 26 octobre 2026)

Avant cette date : rien. On ne pouvait pas dire quel modèle avait écrit un document, ni même qu'un
document avait été produit par un modèle. Chaque génération écrit désormais une ligne dans
`~/.hermes/data/job-search/logs/documents.log` :

```
2026-10-26T16:52:05  test  modele=opus  repli=sonnet  cli=2.1.289 (Claude Code)  code=1  duree=0.9s
```

Date, genre (cv / lettre / pitch), modèle demandé, repli déclaré, version de la CLI, code de retour,
durée. Cette ligne-là a été produite par un **échec réel** — c'est la panne d'authentification décrite
plus bas, et l'échec y est **tracé** au lieu d'être deviné.

**Ce que la ligne ne dit pas** : si le repli a réellement servi. La CLI ne l'expose pas, donc c'est
écrit *non mesurable* — pas supposé.

L'appel est défini **à un seul endroit** (`journal_modeles.py`) : les trois chemins l'utilisent, aucun
ne peut le contourner par inadvertance.

## 2. Ce qui est réessayé, et combien de fois

- **Documents : aucun réessai.** Un appel, une chance. Un échec est visible immédiatement (le fichier
  n'existe pas, la commande le dit), et je relance moi-même après avoir lu l'erreur. Réessayer en
  boucle une rédaction qui échoue consommerait du temps et masquerait la cause.
- **Collecte des offres : réessais bornés** sur les appels réseau (les adapters réessaient quelques fois
  avec attente croissante). Cela concerne la collecte, pas le modèle.
- **L'agent : garde-fous de boucle.** Avertissement après deux échecs identiques, arrêt dur après
  cinq — et arrêt dur systématique en mode non interactif, où personne ne peut répondre à une question.

## 3. Ce qui bascule sur un gabarit, ce qui échoue sèchement

- **Bascule sur gabarit** : le pitch des messages Telegram, et lui seul.
- **Échec sec** : la génération d'un CV ou d'une lettre. Le fichier n'est pas écrit, la commande
  n'annonce pas de document, et rien n'est présenté comme réussi. C'est volontaire : un document de
  candidature à moitié correct est pire qu'un document manquant.

## 4. La chaîne de repli du modèle de conversation : il n'y en a aucune

**Constat, écrit tel quel** : la configuration ne déclare **aucun modèle de secours** pour la
conversation. À ne pas confondre avec les documents, qui ont un repli **au niveau de la CLI** depuis le
26 octobre 2026 (`opus` → `sonnet`, voir section 1) : la conversation, elle, n'en a aucun. Si OpenRouter
devient indisponible, je n'ai plus d'interlocuteur : l'agent ne bascule pas
sur un autre fournisseur, il s'arrête. C'est le point le plus faible de l'installation, et c'est le
prochain chantier identifié.

**Illustration datée (26 octobre 2026)** : la CLI `claude` qui rédige les documents ne s'authentifie
plus (« session OAuth expirée »). Conséquence mesurée : le chemin des documents est indisponible, et la
défaillance est **bruyante** — le processus de génération lève une erreur, aucun fichier n'est écrit,
et rien ne laisse croire à un succès. Le pitch, lui, retombe sur son gabarit fixe. Le système reste donc
utilisable sans le modèle, sauf pour produire de nouveaux documents.

## 5. Coût et latence

**Dépense réelle mesurée** (relevé du 26 octobre 2026) : **13,99 $ consommés sur 15 $ achetés** chez
OpenRouter, le seul poste facturé à l'usage. Rapporté à la durée de vie de l'installation (mise en
service le 21 septembre 2026, soit **35 jours**), cela fait environ **0,40 $ par jour, soit de l'ordre
de 12 $ par mois**.

*Ce que cette estimation suppose* : que les crédits ont été achetés à la mise en service. Je ne peux pas
le vérifier depuis le serveur, donc c'est une hypothèse, pas une mesure — et elle est écrite comme
telle.

**Répartition par famille de tâches : non mesurable.** OpenRouter n'expose qu'un total consommé, et mes
journaux ne portent pas le coût par appel. Les rédactions de documents passent par la CLI `claude`,
authentifiée par un abonnement dont la consommation m'est invisible. Je ne peux donc pas dire quelle
part revient à la collecte, aux conversations ou aux documents — et je préfère l'écrire que
l'estimer.

**Latence** :

- **tours d'agent des tâches planifiées** : 7 exécutions journalisées, médiane **5,2 s**, maximum
  **199 s** (une analyse longue). Les tâches purement scriptées ne sont pas journalisées dans le même
  fichier, donc leur durée n'est pas couverte par ce chiffre ;
- **documents de candidature** : **11 dossiers en 3 à 4 minutes** (mesure du 21 octobre 2026), soit
  environ **20 secondes par document**, appel de modèle compris.

## 6. Ce que j'ai décidé de ne pas faire

| Décision | Raison |
| --- | --- |
| Pas de repli automatique vers un autre fournisseur pour la conversation | Un interlocuteur qui change en silence rendrait les incidents difficiles à diagnostiquer ; je préfère une panne visible à une substitution invisible |
| Pas de boucle de réessai sur les documents | Un échec de rédaction doit être lu, pas contourné |
| Pas de document généré sans mes propres mots | Une lettre sans vécu personnel est interchangeable, et le vérificateur le refuse |
| Aucun modèle dans la collecte ni dans la note | Une note doit être recalculable, à l'identique, sans modèle : voir [garde-fous](garde-fous.md), section 7 |
| Pas de journalisation du coût par appel | Elle n'existe pas côté fournisseur d'abonnement ; l'écrire depuis mes propres compteurs serait une approximation présentée comme un chiffre |
