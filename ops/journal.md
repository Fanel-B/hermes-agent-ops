# Journal d'exploitation

Ce que mon système a trouvé, corrigé et vérifié. Chaque entrée est datée et porte un chiffre : sans
mesure, il n'y a pas de ligne.

Format : **ce que j'ai observé → ce que j'ai corrigé → ce qui le prouve.**

Ce n'est pas une liste de succès, et c'est volontaire : chaque entrée commence par un défaut, souvent
un défaut que personne n'avait vu. J'y écris aussi ce que ma vérification **ne** prouve pas.

---

## 25 octobre 2026

**Mon contrôle croisé ne couvrait pas le critère qui commande tout.** Mon site recalcule la note en
TypeScript à partir du même référentiel que mon pipeline Python, mais il ignorait la règle de **date de
fin** — celle qui décide de tout pour moi (fin impérative au 31/07/2027, quatre paliers, malus de
classement au-delà de cinq mois).
*Corrigé* : j'ai porté le critère à l'identique, avec ses drapeaux et son malus.
*Preuve* : contrôle croisé → **36 désaccords sur 287 offres, puis 0** ; **178 tests verts**.

**« ML » comptait comme « HTML ».** Ma comparaison des compétences se faisait par sous-chaîne : trois
offres qui demandent du machine learning affichaient une case verte devant « ML », et leur critère de
cohérence était gonflé de quelques points.
*Corrigé* : comparaison **mot entier dans les deux sens**, sauf les locutions à plusieurs mots.
*Effet mesuré* : la ligne « cohérence » ne bouge que sur **les 3 offres visées** — et au passage
« Java » n'est plus confondu avec JavaScript (**28 exigences**) ni « GitLab » avec Git, sans perdre
aucune couverture réelle. Ce que ça m'a appris : ma première correction avait été trop stricte et
faisait disparaître la couverture de « API » sur 13 offres ; je ne l'ai vu qu'en comparant **les
décisions de couverture**, pas les notes.

**Une fin non calculable se présentait comme « compatible ».** Le type qui décrit la fin d'un stage
avait pour valeur par défaut le palier « compatible » : un lecteur — humain ou code — qui oubliait de
vérifier la date lisait « rien à négocier » là où l'annonce ne disait rien.
*Corrigé* : plus de palier par défaut.
*Preuve* : **3 contrôles ajoutés** (début inconnu, durée inconnue, les deux) et **403 contrôles Python
verts, 0 échec**. Ce piège avait faussé une de mes propres mesures le même jour : elle annonçait « fin
calculable » sur 77 offres qui n'annonçaient aucune durée.

**Un test reposait sur une offre qui avait quitté mon export.** Mon cas de référence « ni contrat, ni
lieu, ni description » n'existe plus : sans lieu, aucune zone n'est calculable, donc l'offre n'est pas
publiée.
*Corrigé* : le test porte sur une offre réelle sans type de contrat, et la propriété d'origine (le lieu
et la description ne sont pas notés) est vérifiée en les effaçant en mémoire.

**Mon jeton Git était dans l'URL du dépôt distant.** Un `.git/config` copié l'emportait avec lui.
*Corrigé* : l'URL est propre ; un assistant de credentials lit le jeton depuis le fichier
d'environnement de mon serveur (permissions `600`) au moment du `push`.
*Preuve* : **aucune occurrence du jeton** dans la configuration du dépôt, ni dans aucun autre dépôt de
la machine, ni dans l'historique de mon shell ; l'accès au distant fonctionne toujours.

**La copie de mon moteur dans mon dépôt public était périmée.** Les quatre fichiers Python déposés en
lecture seule pour servir de référence dataient d'**avant le 22 octobre** : ils décrivaient une
version du calcul que je n'utilise plus (413 lignes contre 623 pour le fichier de notation). Un
recruteur qui les lisait apprenait un système qui n'existe pas.
*Corrigé* : resynchronisés depuis mon moteur ; l'écart est vérifié fichier par fichier.
*Preuve* : les quatre fichiers sont désormais identiques à ceux du pipeline, et l'oracle repasse à
**0 désaccord sur 290 offres**.

**Ma branche principale avait divergé de deux générations.** Mon travail de portage vivait sur une
branche latérale, tandis que la ligne principale portait une version antérieure du calcul et une
fonctionnalité de profil. Fusion mesurée : **201 tests verts** (les deux lignées réunies), **0
désaccord sur 290 offres**, construction de production verte (285 fiches + page de profil).

**J'avais des règles de publication… que je n'exécutais pas.** J'ai écrit dans ce dépôt ce qui ne doit
jamais sortir (jetons, boîte mail, dossiers de candidature, profil détaillé), puis je les ai
**appliquées** au dépôt lui-même avant de le considérer prêt.
*Preuve* : recherche de motifs sur l'ensemble des fichiers — **0 motif de secret, 0 donnée
personnelle** (adresses, numéros, identifiants de mes documents, adresse de mon serveur).

---

## 24 octobre 2026

**J'ai reçu un dossier de candidature de référence et je l'ai mesuré** (un CV et une lettre réels) :
une page chacun, en-tête de durée sans date de fin, plan de lettre à cinq paragraphes dont un
paragraphe de manque assumé. Quatre divergences avec ma méthode corrigée : je les ai portées à
l'arbitrage plutôt que de trancher à la place de l'auteur.

**Docker figurait encore au référentiel de mon site** alors que je l'avais retiré côté pipeline : le
site ne pénalisait donc pas les offres qui l'exigent.
*Corrigé* : les deux listes sont alignées. *Preuve* : contrôle croisé, puis **0 désaccord sur 287
offres**.

**Deux de mes contrôles étaient périmés par le temps.** Des dates figées dépassaient le seuil de
fraîcheur : un test échouait, l'autre passait pour la mauvaise raison.
*Corrigé* : les fixtures se calculent à partir de l'instant du test.

**Un événement sans horaire annoncé a été posé en journée entière** — jamais une heure inventée, parce
qu'une heure fausse dans un agenda devient une contrainte fausse.

**Mon rappel d'agenda n'existait plus.** Constat : aucun des rappels prévus ne tournait.
*Corrigé* : une seule tâche quotidienne couvre tous les événements (J-7, J-2, J-1), y compris ceux
ajoutés plus tard, et **reste silencieuse** s'il n'y a rien à dire.

---

## 22 octobre 2026

**Mon critère de durée ne voyait pas le problème.** Un stage de 4 mois démarrant en juin a une durée
parfaitement conforme… et finit en octobre, après la rentrée. La contrainte réelle était la **fin**,
pas la durée.
*Corrigé* : quatre paliers de fin, drapeau de négociation, malus de classement au-delà.
*Preuve* : contrôles dédiés par palier, dont la garantie qu'une offre « à raccourcir » ne dépasse
jamais une offre équivalente compatible.

---

## 21 octobre 2026

**Une offre déjà postulée m'était reproposée.** Mon registre de candidatures portait un titre avec des
barres obliques, la comparaison lisait un titre normalisé sans ponctuation : l'offre n'était donc
jamais reconnue comme traitée.
*Corrigé* : normalisation du comparateur — et non de la donnée — plus une lecture **unique** du
registre, partagée par le rapport, le classement et les alertes, pour que la règle « ne jamais
reproposer une offre traitée » n'existe qu'à un endroit.
*Preuve* : offre fraîche déjà postulée → **0 alerte avec registre, 1 sans**.
