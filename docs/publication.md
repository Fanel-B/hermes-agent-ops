# Ce qui ne sort pas d'ici

Un dépôt qui montre un agent doit dire **ce qu'il protège**. Les règles ci-dessous sont appliquées
avant chaque publication dans ce dépôt, pas seulement quand une fuite est suspectée.

## Jamais publié

| Catégorie | Pourquoi |
| --- | --- |
| Jetons, clés d'API, mots de passe, codes | un secret publié est un secret perdu, même supprimé ensuite (l'historique Git garde tout) |
| Dossiers de candidature (CV, lettres) | documents nominaux, liés à des offres précises |
| Registre de candidatures, historique d'échanges | révèle où l'utilisateur postule, et quand |
| Boîtes mail, identifiants de comptes | données personnelles et accès |
| Profil détaillé (notes, coordonnées, avis internes) | peut être préjudiciable hors contexte |

## Publié après anonymisation

- **Des extraits de code** : gardés parce qu'ils montrent une méthode (une comparaison, une
  vérification, un garde-fou), sans les données qu'ils traitent.
- **Des extraits de journaux** : la structure et le chiffre sont conservés, l'identifiant et le
  contenu personnel retirés.
- **Des schémas** : le principe, jamais l'adresse du serveur ni le détail de l'installation qui
  ouvrirait une porte.

## La vérification, avant chaque publication

1. Recherche des motifs de secrets dans les fichiers suivis (`token`, `key`, `secret`, `password`,
   empreintes de jetons connues) ;
2. recherche de données personnelles (adresses e-mail, numéros, identifiants de comptes) dans les
   fichiers exportés ;
3. vérification que la configuration citée **décrit le rôle** d'un accès sans jamais en donner la
   valeur.

Ces trois étapes sont exécutées à la main, et la troisième est la plus utile : une configuration
lisible qui ne contient que des noms de clés est une preuve en soi.
