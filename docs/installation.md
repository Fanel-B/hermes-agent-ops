# Installation

Comment l'agent a été mis en place, et pourquoi dans cet ordre. Les valeurs sensibles (jetons, clés)
sont décrites **par leur rôle**, jamais recopiées.

## 0. Le socle

- Serveur personnel (VPS), **Ubuntu 26.04 LTS**, 4 vCPU, 7,6 Go de mémoire — dimensionné pour faire
  tourner en continu une passerelle, des tâches planifiées et une construction de site.
- **Python 3.14** (système), **Node 26** (installé à part, dans `~/.hermes/node/`, pour que le site
  et ses outils ne dépendent pas des paquets du système).
- Accès `sudo` sans mot de passe pour l'utilisateur de service : l'agent peut redémarrer un service
  qu'il a lui-même reconstruit, sans qu'une intervention humaine soit nécessaire à 3 h du matin.

## 1. L'agent

```bash
# installation de l'agent (écrit dans ~/.hermes/, binaire dans ~/.local/bin/hermes)
hermes --version      # → Hermes Agent v0.21.3
```

L'état utilisateur vit sous `~/.hermes/` :

```
~/.hermes/
├── config.yaml        réglages (non secrets) : modèle, fuseau, canaux, garde-fous
├── .env               secrets uniquement, permissions 600
├── skills/            procédures (73)
├── scripts/           scripts appelés par les tâches planifiées
├── cron/jobs.json     les tâches planifiées
├── logs/              agent.log, errors.log, gateway.log
├── data/              données de travail (base d'offres, suivi, documents)
└── profiles/          profils isolés (un seul utilisé ici)
```

## 2. La passerelle en service

L'agent tourne en `systemd`, ce qui lui survit aux déconnexions et aux redémarrages :

```
hermes-gateway.service   — Messaging Platform Integration   (actif)
jobbot-site.service      — JobBot V2, site public, port 80  (actif)
```

Le second est le seul service que l'agent a construit lui-même : c'est le site du
[cas d'usage](cases/jobbot.md). Il écoute sur le port 80 via la capacité
`CAP_NET_BIND_SERVICE`, sans tourner en root, et ne contient aucun secret.

## 3. Le canal humain

Un seul canal est ouvert : **Telegram**, un fil privé, avec une liste d'utilisateurs autorisés
explicite (`allow_all_users: false`). Un canal, pas dix : l'agent doit s'adresser à *une* personne,
dans *un* fil, sinon ses messages deviennent du bruit.

## 4. Les accès

Chaque accès est posé **une fois**, avec le minimum de portée :

| Accès | Moyen | Portée |
| --- | --- | --- |
| Google (Agenda, Sheets, Gmail en lecture) | OAuth, jeton renouvelable | lecture des alertes d'offres, écriture limitée au suivi |
| Dépôts Git | jeton d'accès, lu à la volée par un assistant de credentials | écriture sur mes dépôts uniquement |
| API publiques d'offres | clés propres à chaque service | quotas respectés, aucune donnée personnelle |

La règle, née d'un incident réel : **un jeton ne vit ni dans un dépôt, ni dans un journal, ni dans la
conversation.** Le cas du jeton écrit en clair dans une URL de dépôt distant est raconté dans le
[journal](ops/journal.md) — il a été trouvé et corrigé.

## 5. Les automatisations

Elles sont posées après coup, une par une, chacune avec sa raison :

1. collecte et alertes d'offres → voir [JobBot](cases/jobbot.md) ;
2. rapports quotidiens, hebdomadaires, « dernier appel » ;
3. purge hebdomadaire de la base (fenêtre de 7 jours, protection des offres récentes et de celles qui
   portent une action) ;
4. suivi des candidatures et relances ;
5. rappels d'agenda (J-7, J-2, J-1) ;
6. rafraîchissement quotidien du site public.

## 6. Vérifier, avant de faire confiance

Aucune automatisation n'a été activée sans une preuve :

- un **contrôle croisé** entre le calcul du site et le calcul du pipeline (deux langages, mêmes
  résultats, ligne à ligne) ;
- des tests unitaires sur les règles, dont certains **relatifs au temps** — une règle de fraîcheur
  testée avec des dates figées finit par passer pour la mauvaise raison ;
- un journal d'exploitation : ce qui a été trouvé, corrigé, et comment c'est vérifié.
