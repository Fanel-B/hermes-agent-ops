# Cas d'usage : JobBot

Le projet qui a servi de banc d'essai à l'agent, et qui tourne encore tous les jours.

**Le problème** : trouver, parmi des centaines d'annonces de stage, celles qui correspondent
réellement à un profil — puis pouvoir expliquer pourquoi, offre par offre.

## Ce que l'agent fait pour ce projet

| Étape | Qui agit | Fréquence |
| --- | --- | --- |
| Collecte depuis 4 familles de sources publiques | code déterministe | toutes les heures |
| Dédoublonnage, éligibilité, note | code déterministe | à chaque collecte |
| Alerte si une offre dépasse le seuil | tâche planifiée → Telegram | toutes les heures |
| Rapport du jour, « dernier appel », hebdomadaire | tâche planifiée → Telegram | quotidien / hebdomadaire |
| Purge de la base (fenêtre de 7 jours, offres protégées) | tâche planifiée | dimanche 21 h |
| Suivi des candidatures dans une feuille de calcul | agent + API officielle | à l'envoi et à la relance |
| Rafraîchissement du site public | tâche planifiée (export → build → redémarrage) | quotidien |

## Ce que l'agent ne fait pas

Il ne postule pas. Il ne note pas « à la main » : la note est du code, recalculable. Il ne publie
rien qui ne soit pas vérifié.

## Les mesures qui tiennent ce projet

- **290 offres** publiées depuis un export réel, chacune avec sa source, sa date et son bulletin ;
- **0 désaccord** entre le calcul du site (TypeScript) et celui du pipeline (Python), comparés
  **ligne à ligne sur chaque offre** — nom du critère, points, maximum, détail, accents compris ;
- **403 contrôles** automatiques côté pipeline, **201 tests** côté site ;
- **4 familles de sources**, toutes publiques : interfaces des entreprises (Airbus, Thales, Sopra
  Steria, SII, Capgemini, Atos, CNES), service public de l'apprentissage, France Travail, et les
  alertes e-mail que je reçois moi-même (lues en lecture seule).

**Ce que l'écart entre ces deux chiffres veut dire** : **546 annonces collectées**, **290 publiées**.
  La différence n'est pas une perte, c'est un filtre — hors zone, hors fenêtre de dates, doublon d'une
  autre source, type de contrat introuvable, diplôme exigé hors de ma portée. La base garde tout ; le
  site ne publie que ce qui est présentable avec sa justification.

## Les trois défauts que ce projet a révélés

1. **Le site et le pipeline ne calculaient pas la même note** — un critère manquait de chaque côté.
   Trouvé par le contrôle croisé, pas par la lecture.
2. **Une compétence fausse était affichée comme maîtrisée** (« ML » lu dans « HTML »). Trouvé par un
   contrôle sur le référentiel.
3. **Une absence de donnée se présentait comme une compatibilité** (palier par défaut « compatible »).
   Trouvé en instrumentant une mesure qui donnait un résultat trop beau.

Détail de l'architecture du site et de la provenance des offres : le
[README du dépôt JobBot](https://github.com/Fanel-B/jobbot).
