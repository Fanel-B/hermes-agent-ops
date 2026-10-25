# Journal d'exploitation

Ce que le système a trouvé, corrigé et vérifié. Chaque entrée est datée et porte un chiffre :
sans mesure, il n'y a pas de ligne.

Format : **ce qui a été observé → ce qui a été corrigé → ce qui le prouve.**

---

## 25 octobre 2026

**Le portage TypeScript ignorait le cinquième critère.** Le site recalculait la note en TypeScript à
partir du même référentiel que le pipeline Python, mais sans la règle de **date de fin** — celle qui
commande tout (fin impérative au 31/07/2027, quatre paliers, malus de classement).
*Corrigé* : le critère est porté à l'identique, avec ses drapeaux et son malus.
*Preuve* : contrôle croisé → **36 désaccords sur 287 offres → 0** ; 178 tests verts.

**« ML » comptait comme « HTML ».** La comparaison des compétences se faisait par sous-chaîne : trois
offres qui demandent du machine learning affichaient une case verte devant « ML », et leur critère de
cohérence était gonflé.
*Corrigé* : comparaison mot entier dans les deux sens, sauf les locutions à plusieurs mots.
Effet mesuré : **la ligne « cohérence » ne bouge que sur les 3 offres visées**, et au passage « Java »
n'est plus confondu avec JavaScript (28 exigences) ni « GitLab » avec Git — sans perdre aucune
couverture réelle.

**Une fin non calculable se présentait comme « compatible ».** Le type qui décrit la fin d'un stage
avait pour valeur par défaut le palier « compatible » : un lecteur — humain ou code — qui oubliait de
vérifier la date lisait « rien à négocier » là où l'annonce ne disait rien.
*Corrigé* : plus de palier par défaut.
*Preuve* : **3 contrôles ajoutés** (début inconnu, durée inconnue, les deux) et **403 contrôles
Python verts, 0 échec**. Ce piège avait faussé une de mes propres mesures le même jour : elle
annonçait « fin calculable » sur 77 offres qui n'annonçaient aucune durée.

**Un test reposait sur une offre qui avait quitté l'export.** Le cas de référence « ni contrat, ni
lieu, ni description » n'existe plus : sans lieu, aucune zone n'est calculable, donc l'offre n'est
pas publiée.
*Corrigé* : le test porte sur une offre réelle sans type de contrat, et la propriété d'origine (le
lieu et la description ne sont pas notés) est vérifiée en les effaçant en mémoire.

**Le jeton Git était dans l'URL du dépôt distant.** Un `.git/config` copié l'emportait avec lui.
*Corrigé* : l'URL est propre, un assistant de credentials lit le jeton depuis le fichier
d'environnement du serveur (permissions `600`) au moment du `push`.
*Preuve* : aucune occurrence du jeton dans la configuration du dépôt, ni dans aucun autre dépôt de la
machine ; l'accès au distant fonctionne toujours.

**La branche principale avait divergé de deux générations.** Le travail de portage vivait sur une
branche latérale, tandis que la ligne principale portait une version antérieure du calcul et une
fonctionnalité de profil. Fusion mesurée : **201 tests verts** (les deux lignées réunies), **0
désaccord sur 290 offres**, construction de production verte (285 fiches + page de profil).

---

## 24 octobre 2026

**Un dossier de référence a été reçu et mesuré** (un CV et une lettre réels) : une page chacun,
en-tête de durée sans date de fin, plan de lettre à cinq paragraphes dont un paragraphe de manque
assumé. Quatre divergences avec la méthode corrigée ont été posées à l'utilisateur plutôt que
tranchées à sa place.

**Docker figurait encore au référentiel du site** alors qu'il en avait été retiré côté pipeline : le
site ne pénalisait donc pas les offres qui l'exigent.
*Corrigé* : les deux listes sont alignées. *Preuve* : contrôle croisé, puis **0 désaccord sur 287
offres**.

**Deux contrôles étaient périmés par le temps.** Des dates figées dépassaient le seuil de fraîcheur :
un test échouait, l'autre passait pour la mauvaise raison.
*Corrigé* : les fixtures se calculent à partir de l'instant du test.

**Un événement sans horaire annoncé a été posé en journée entière** — jamais une heure inventée,
parce qu'une heure fausse dans un agenda devient une contrainte fausse.

**Le rappel d'agenda n'existait plus.** Constat : aucun des rappels prévus ne tournait.
*Corrigé* : une seule tâche quotidienne couvre tous les événements (J-7, J-2, J-1), y compris ceux
ajoutés plus tard, et **reste silencieuse** s'il n'y a rien à dire.

---

## 22 octobre 2026

**Le critère de durée ne voyait pas le problème.** Un stage de 4 mois démarrant en juin a une durée
parfaitement conforme… et finit en octobre, après la rentrée. La contrainte réelle était la **fin**,
pas la durée.
*Corrigé* : quatre paliers de fin, drapeau de négociation, malus de classement au-delà.
*Preuve* : contrôles dédiés par palier, dont la garantie qu'une offre « à raccourcir » ne dépasse
jamais une offre équivalente compatible.

---

## 21 octobre 2026

**Une offre déjà postulée était reproposée.** Le registre des candidatures portait un titre avec des
barres obliques, la comparaison lisait un titre normalisé sans ponctuation : l'offre n'était donc
jamais reconnue comme traitée.
*Corrigé* : normalisation du comparateur — et non de la donnée — plus une lecture **unique** du
registre, partagée par le rapport, le classement et les alertes, pour que la règle « ne jamais
reproposer une offre traitée » n'existe qu'à un endroit.
*Preuve* : offre fraîche déjà postulée → 0 alerte avec registre, 1 sans.

---

## Ce que ce journal n'est pas

Ce n'est pas une liste de succès : chaque entrée commence par un défaut, souvent un défaut que
personne n'avait vu. C'est le seul intérêt de l'exercice — et c'est vérifiable, puisque les
corrections sont testées et les mesures rejouables.
