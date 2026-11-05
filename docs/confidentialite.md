# Confidentialité

Cette page décrit ce que fait l'application que j'ai créée pour mon propre usage, ce à quoi elle
accède, et ce qu'elle n'en fait pas. Elle existe parce que Google la demande avant de publier une
application qui demande l'accès à une boîte de réception.

## À quoi elle sert

Elle sert à **une seule personne : moi**. C'est l'authentification de mon agent personnel, qui
m'aide dans ma recherche de stage : il lit les alertes carrière qui arrivent dans ma boîte, il tient
à jour mon propre tableau de suivi de candidatures, et il me rappelle mes rendez-vous. Je n'ai aucun
utilisateur, aucun client, et rien à vendre.

## Ce à quoi elle accède, et pourquoi

| Accès | Pourquoi | Ce qui est réellement lu ou écrit |
|---|---|---|
| Boîte de réception (lecture) | Repérer les alertes d'offres envoyées par des sites carrière et des plateformes d'emploi | L'expéditeur, l'objet et le contenu de ces alertes uniquement |
| Feuilles de calcul (lecture et écriture) | Tenir mon tableau de suivi de candidatures, qui m'appartient | Seulement les feuilles que je lui ai désignées |
| Agenda (lecture) | Me rappeler les échéances et les rendez-vous | Seulement mes propres événements |

Aucun autre service n'est demandé, et l'accès est limité à mon propre compte.

## Où vont les données

Nulle part ailleurs. Les jetons d'accès sont stockés **sur mon propre serveur**, en lecture
restreinte, et ne quittent pas ma machine. Les informations lues sont conservées localement, dans
une base qui m'appartient, sur le même serveur.

## Ce qu'elle ne fait pas

- Elle ne transmet rien à un tiers : pas de partage, pas de revente, pas de publicité.
- Elle n'analyse rien pour le compte de quelqu'un d'autre.
- Elle ne collecte aucune donnée sur qui que ce soit d'autre que moi.
- Elle n'envoie aucun message en mon nom sans que je l'aie validé.

## Reprendre le contrôle

Je peux retirer cet accès à tout moment, de mon côté :
<https://myaccount.google.com/permissions>. Le retrait est immédiat et l'application cesse
aussitôt de fonctionner.

## Contact

Écris-moi à l'adresse indiquée comme adresse d'assistance sur la page de consentement de cette
application. Je réponds moi-même : il n'y a pas de service client, parce qu'il n'y a pas de client.

---

*Dernière mise à jour : 5 novembre 2026.*
