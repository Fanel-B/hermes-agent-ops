# Hermes — l'agent qui fait tourner mes projets

Ce dépôt documente **l'agent que j'exploite au quotidien** : ce qu'il est, comment je l'ai installé,
comment je l'ai configuré, quels garde-fous je lui impose, et **ce qu'il a réellement produit**.

Il ne contient pas de code d'agent : l'agent lui-même est [Hermes Agent](https://hermes-agent.nousresearch.com/)
(Nous Research). Ce que je documente ici, c'est **mon exploitation** — l'installation, mes choix de
configuration, mes automatisations, les incidents et leurs corrections.

> **Le principe qui gouverne tout ce dépôt** : je conçois, je spécifie et je vérifie ; l'agent exécute,
> mesure et journalise. Aucune ligne de ce dépôt ne raconte une victoire sans le chiffre qui la prouve.

## Ce que je lui fais faire, concrètement

Je le fais tourner en service sur mon serveur personnel (VPS, Ubuntu 26.04, 4 vCPU) : il travaille sans
intervention de ma part.

- **Il collecte et classe des offres de stage** pour mon projet [JobBot](cases/jobbot.md) — quatre
  familles de sources publiques, une note déterministe, des alertes.
- **Il me parle sur Telegram** : alerte dès qu'une offre dépasse mon seuil, rapport du jour, relances
  de candidature, rappels d'agenda.
- **Il tient mon suivi de candidatures** dans une feuille de calcul — entreprise, poste, date d'envoi,
  délai de réponse, quel CV et quelle lettre sont partis, relance.
- **Il surveille mon agenda** et me prévient une semaine avant, deux jours avant, la veille.
- **Il se maintient** : détecter une collecte en échec, diagnostiquer, corriger, vérifier — et me le
  dire, chiffre en main.

## Le squelette de mon installation

```
        Telegram (un canal, un fil)          mon serveur (Ubuntu, systemd)
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
                              │ mes projets : JobBot (site + pipeline), Google Agenda, │
                              │ suivi de candidatures, dépôts Git, mes documents       │
                              └────────────────────────────────────────────────────────┘
```

## Mes documents

- [Architecture](docs/architecture.md) — les pièces du système, et pourquoi je les sépare.
- [Installation](docs/installation.md) — comment je l'ai mis en place, étape par étape.
- [Configuration](docs/configuration.md) — l'historique de mes réglages et leur raison d'être.
- [Garde-fous](docs/garde-fous.md) — ce que j'interdis à l'agent, et comment je le tiens.
- [Journal d'exploitation](ops/journal.md) — ce que j'ai trouvé, corrigé et vérifié, daté, chiffré.
- [Cas d'usage : JobBot](cases/jobbot.md) — mon automatisation de bout en bout, avec ses mesures.
- [Ce qui ne sort pas d'ici](docs/publication.md) — mes règles de publication.

## Ce que je ne publie pas

Ni jeton, ni mot de passe, ni clé d'API. Ni mes dossiers de candidature, ni ma boîte mail, ni mon
profil détaillé. Les extraits de code et de journaux sont anonymisés. J'ai écrit mes règles dans
[docs/publication.md](docs/publication.md), et je les applique à ce dépôt avant chaque publication —
un garde-fou vaut aussi par ce qu'il protège.

## Qui je suis

**Fanel Balemo** — L3 MIAGE, Université de Toulouse (2026-2027)

- GitHub : <https://github.com/Fanel-B>
- Portfolio : <https://portfolio-v2-fanel-65.vercel.app>
