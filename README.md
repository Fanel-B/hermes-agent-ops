# Hermes — l'agent qui fait tourner mes projets

Ce dépôt documente **l'agent que j'exploite au quotidien** : ce qu'il est, comment il est installé,
comment il est configuré, quels garde-fous l'encadrent, et **ce qu'il a réellement produit**.

Il ne contient pas de code d'agent : l'agent lui-même est [Hermes Agent](https://hermes-agent.nousresearch.com/)
(Nous Research). Ce qui est documenté ici, c'est **l'exploitation** — l'installation, les choix de
configuration, les automatisations, les incidents et leur correction.

> **Le principe qui gouverne tout ce dépôt** : je conçois, je spécifie et je vérifie ; l'agent
> exécute, mesure et journalise. Aucune ligne de ce dépôt ne raconte une victoire sans le chiffre qui
> la prouve.

## Ce que l'agent fait, concrètement

Il tourne en service sur un serveur personnel (VPS, Ubuntu 26.04, 4 vCPU) et travaille sans
intervention :

- **il collecte et classe des offres de stage** pour mon projet [JobBot](cases/jobbot.md) — quatre
  familles de sources publiques, une note déterministe, des alertes ;
- **il parle sur Telegram** : alerte dès qu'une offre dépasse un seuil, rapport du jour, relances de
  candidature, rappels d'agenda ;
- **il tient mon suivi de candidatures** dans une feuille de calcul, colonnes comprises ;
- **il surveille l'agenda** et prévient une semaine avant, deux jours avant, la veille ;
- **il se maintient lui-même** : détecter une collecte en échec, diagnostiquer, corriger, vérifier.

## Le squelette du système

```
        Telegram (un canal, un fil)          serveur personnel (Ubuntu, systemd)
                 │                                     │
                 │                            ┌────────▼────────┐
                 └───────────────────────────►│  hermes-gateway │  service systemd
                                              └────────┬────────┘
                                                       │
                            ┌──────────────────────────┼──────────────────────────┐
                            │                          │                          │
                    ┌───────▼───────┐         ┌────────▼────────┐        ┌────────▼────────┐
                    │  planificateur│         │   compétences   │        │  exécution      │
                    │  cron (11)    │         │  (73 skills)    │        │  terminal, web, │
                    │               │         │                 │        │  navigateur,    │
                    └───────┬───────┘         └────────┬────────┘        │  délégation     │
                            │                          │                 └────────┬────────┘
                            └──────────────┬───────────┘                          │
                                           ▼                                      │
                              ┌────────────────────────┐                          │
                              │ mémoire + secrets      │                          │
                              │ (notes durables, coffre│                          │
                              │  de mots de passe)     │                          │
                              └────────────────────────┘                          │
                                           │                                      │
                                           ▼                                      ▼
                              ┌────────────────────────────────────────────────────────┐
                              │ projets réels : JobBot (site + pipeline), Google Agenda,│
                              │ feuille de suivi, dépôts Git, documents de candidature │
                              └────────────────────────────────────────────────────────┘
```

## Les documents

- [Architecture](docs/architecture.md) — les pièces du système, et pourquoi elles sont séparées.
- [Installation](docs/installation.md) — comment il a été mis en place, étape par étape.
- [Configuration](docs/configuration.md) — l'historique des réglages et leur raison d'être.
- [Garde-fous](docs/garde-fous.md) — ce que l'agent a interdiction de faire, et comment c'est tenu.
- [Journal d'exploitation](ops/journal.md) — ce qui a été trouvé, corrigé et vérifié, daté.
- [Cas d'usage : JobBot](cases/jobbot.md) — l'automatisation de bout en bout, avec ses mesures.
- [Ce qui ne sort pas d'ici](docs/publication.md) — les règles de publication.

## Ce que ce dépôt ne contient pas

Ni jeton, ni mot de passe, ni clé d'API. Ni mes dossiers de candidature, ni ma boîte mail, ni mon
profil détaillé. Les extraits de code et de journaux sont anonymisés. Les règles sont écrites dans
[docs/publication.md](docs/publication.md) — un garde-fou vaut aussi par ce qu'il protège.

## Auteur

**Fanel Balemo** — L3 MIAGE, Université de Toulouse (2026-2027)

- GitHub : <https://github.com/Fanel-B>
- Portfolio : <https://portfolio-v2-fanel-65.vercel.app>
