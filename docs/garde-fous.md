# Garde-fous

Les limites de l'agent ne sont pas des intentions : elles sont dans le code, dans les tests, ou dans le
refus explicite de faire. Ce document liste celles qui tiennent, **avec leur date de mise en place** et
la manière dont chacune est tenue.

Un garde-fou daté est plus honnête qu'un garde-fou absolu : plusieurs de ceux qui suivent sont nés d'un
défaut constaté, et avant cette date la règle ne tenait pas. La date renvoie à l'entrée correspondante
du [journal d'exploitation](../ops/journal.md) quand elle existe.

## 1. Aucun envoi sans un humain

*Règle de conception, tenue depuis la mise en service (21 septembre 2026).*

L'agent collecte, note, prépare, suit, relance — **il n'envoie jamais une candidature**. Ce n'est pas
une préférence : c'est vérifiable dans le fait qu'aucun chemin de code ne possède d'appel d'envoi vers
un portail. La conséquence pratique est visible dans le suivi : les candidatures n'apparaissent dans le
registre que parce que je les y ai fait entrer après les avoir envoyées moi-même.

## 2. Aucune donnée inventée

*Décisions des 18 et 19 octobre 2026.*

Un champ absent dans la source reste absent : `null`, et l'interface affiche « non précisé ». Cela vaut
pour tout — y compris pour les informations qui « arrangeraient » une démonstration. Deux conséquences
visibles :

- une offre dont le type de contrat n'est pas renseigné n'est pas déguisée en stage : elle sort dans un
  bloc séparé, avec un badge, hors du classement principal (19 octobre 2026, après une mesure :
  85 offres sur 303 avaient un contrat introuvable partout) ;
- une offre dont la ville n'est pas lisible n'est pas rejetée au motif qu'elle est hors zone : elle
  reste « à vérifier », parce qu'un motif faux m'aurait envoyé au mauvais endroit.

## 3. Une compétence n'entre dans un document que si elle est prouvée

*Règles des 21 et 22 octobre 2026.*

Un référentiel unique recense ce qui est démontré, et un contrôle automatique vérifie chaque compétence
citée contre ce référentiel. Rien d'autre n'entre : ni ce qui est « en cours d'apprentissage », ni ce
qui est vrai mais non écrit.

**Ce qui a précédé la règle** : trois CV portaient des compétences que je n'ai pas (Vue.js, C++,
GitLab). L'audit des onze dossiers du 21 octobre l'a montré, et le contrôle automatique refuse
depuis d'écrire un document non conforme. Un ajout au référentiel exige désormais une **preuve par
projet** : la compétence doit apparaître dans le bloc d'un de mes projets, pas « quelque part dans mes
documents ».

*Faux positif trouvé par ce contrôle lui-même* : la comparaison se faisait par sous-chaîne, et « ML »
(machine learning) était considéré comme maîtrisé parce que « HT**ML** » contient « ml ». Trois offres
affichaient donc une case verte devant une compétence absente. Corrigé — la comparaison se fait
désormais mot entier dans les deux sens. Voir le [journal, 25 octobre](../ops/journal.md).

## 4. Les secrets

*Incident fondateur : jeton en clair, corrigé le 25 octobre 2026.*

- Un mot de passe ou un code de vérification **ne passe jamais par la conversation**, même si une page
  ou l'utilisateur le montre. Il est saisi une fois, dans le coffre.
- Un code d'autorisation OAuth est échangé par un script, jamais recopié dans un message.
- Un jeton ne vit ni dans un dépôt, ni dans un journal, ni dans un fichier de configuration de dépôt :
  pour Git, un assistant de credentials lit le jeton au moment de l'usage.
- **Incident réel** : le jeton d'un dépôt était écrit en clair dans l'URL du dépôt distant, donc
  recopiable en même temps que le dossier. Trouvé, mesuré, corrigé le 25 octobre 2026 — voir le
  [journal](../ops/journal.md).

## 5. Collecte : la légalité avant la quantité

*Relevé complet des sources protégées : 17 octobre 2026.*

- `robots.txt` et `sitemaps` d'abord ; les `Crawl-delay` sont honorés.
- **Aucun contournement de protection anti-robot.** Un site qui refuse (erreur 403, pare-feu) est
  abandonné pour cette entreprise, et c'est écrit comme tel dans le code. Quatre sources sont dans ce
  cas aujourd'hui, et c'est assumé : ce qui n'est pas accessible légalement n'est pas collecté.
- Les API publiques sont appelées avec leurs quotas (pagination, limite par minute), jamais en force.
- Deux minutes par portail, puis on passe : s'acharner sur une page qui résiste est un mauvais calcul.

## 6. Honnêteté du calcul

*Limite reconnue le 25 octobre 2026.*

- La note affichée est **la somme exacte des lignes** de son bulletin : quand une note est bornée, une
  ligne « plafond » existe pour que l'addition reste vraie.
- Une affirmation non mesurable est écrite « non mesurable », jamais remplacée par un adjectif.

**Ce que le contrôle croisé prouve, et ce qu'il ne prouve pas.** Le même calcul existe en deux langages
(pipeline Python et site TypeScript), et un contrôle croisé les compare offre par offre, ligne à ligne,
accents compris. Aujourd'hui : **290 offres comparées, 0 désaccord**.

Mais **zéro désaccord prouve que les deux implémentations sont d'accord, pas qu'elles ont raison.** Un
critère absent des deux côtés, ou présent d'un seul, passe inaperçu — et c'est exactement ce qui est
arrivé. Le contrôle affichait « 0 désaccord » alors que le portage TypeScript ignorait le critère de
**date de fin**, celui qui commande tout mon classement. Il a fallu que je compare la liste des
critères, et non les résultats, pour m'en apercevoir : une fois le critère porté, les désaccords sont
apparus (**36 sur 287 offres**), puis sont retombés à zéro. Un contrôle qui ne cherche que des
divergences est aveugle à ce qui manque des deux côtés.

**Ce qui compense** : des cas de référence dont le résultat correct est connu **d'avance**, indépendants
des deux implémentations. Le pipeline en porte plusieurs centaines, écrits en dur à partir de la valeur
attendue (par exemple le plafond administratif qui doit borner une note à 55, ou l'égalité « note
affichée = somme des lignes du bulletin »). La règle est que ces attentes se réécrivent **contre la
valeur de référence**, jamais contre le code — sinon le test confirme simplement ce que le code fait
déjà. C'est la raison d'être de la règle ; c'est aussi sa limite : elle ne couvre que ce que quelqu'un a
pensé à écrire.

## 7. Ce que l'agent ne décide pas

*Tenue depuis la mise en service ; le modèle n'a jamais été dans le chemin de la note.*

Le modèle de langage **n'intervient pas** dans la collecte ni dans la note. Il ne choisit pas quelles
offres sont bonnes, il ne fabrique pas de score. Il pilote, il explique, il journalise. Le jour où il
notera, il faudra pouvoir recalculer ce qu'il a fait — c'est la raison de cette séparation. Ce qui
arrive quand le modèle rate est décrit dans [Défaillances du modèle](defaillances-modele.md).
