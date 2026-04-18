[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/c542ZXXS)
# TP1 - Introduction aux données ouvertes liées

Ce dépôt contient le support et les gabarits du **TP1** du module **Linked Open Data**.

Le TP1 adopte une approche **conceptuelle, analytique et progressive**. Il introduit les principes des données ouvertes liées sans entrer encore dans la modélisation RDF formelle, qui sera abordée dans les prochaines séances.

## Objectifs pédagogiques

À l'issue de cette séance, vous devez être capables de :

- distinguer `Open Data`, `Linked Data` et `Linked Open Data`
- expliquer l'intérêt des identifiants, des ressources et des liens entre données
- évaluer si un jeu de données ouvert se prête à une future publication en données liées
- identifier les entités principales d'un jeu de données
- proposer des liens pertinents vers des ressources externes
- construire un diagnostic de qualité et de maturité du jeu de données
- rédiger un rapport technique argumenté, appuyé sur des observations vérifiables

## Résultats attendus

Le TP1 se déroule en deux temps :

- **pendant la séance** : vous produisez un diagnostic initial du jeu de données
- **avant la prochaine séance** : vous réalisez un travail hors séance distinct, centré sur les référentiels externes, la normalisation et la préparation du TP2

Chaque groupe doit donc organiser son rendu en deux sous-dossiers :

- `deliverables/Groupe-XX/seance/`
- `deliverables/Groupe-XX/hors_seance/`

Contenu attendu :

- `seance/01_fiche_analyse_dataset.md`
- `seance/02_carte_des_liens.md`
- `hors_seance/01_benchmark_referentiels.md`
- `hors_seance/02_plan_normalisation.md`
- `hors_seance/03_rapport_hors_seance.pdf`
- éventuellement un sous-dossier `annexes/`

## Structure du dépôt

- `TP1.md` : énoncé complet du TP
- `TRAVAIL_A_RENDRE.md` : énoncé séparé du travail hors séance
- `data/` : jeu de données simplifié pour démarrer rapidement
- `templates/` : gabarits à compléter
- `resources/` : liens utiles et ressources de référence
- `deliverables/` : emplacement des rendus par groupe

## Démarrage rapide

1. Lisez [`TP1.md`](./TP1.md).
2. Choisissez soit le jeu de données fourni dans `data/`, soit un jeu de données ouvert pertinent trouvé sur un portail Open Data.
3. Copiez les gabarits de `templates/` vers `deliverables/Groupe-XX/seance/` et `deliverables/Groupe-XX/hors_seance/`.
4. Complétez pendant la séance la fiche d'analyse et la carte des liens.
5. Lisez [`TRAVAIL_A_RENDRE.md`](./TRAVAIL_A_RENDRE.md) puis réalisez le benchmark des référentiels, le plan de normalisation et le rapport associé.

## Niveau d'exigence

Une réponse purement descriptive n'est pas suffisante. En particulier :

- chaque affirmation importante doit être justifiée par un exemple, une colonne, une valeur ou une observation précise
- vous devez distinguer clairement `entité`, `attribut`, `identifiant`, `valeur contrôlée` et `ressource externe`
- les liens externes proposés doivent être motivés par une véritable stratégie d'appariement, et non par simple intuition

## Consigne importante

Dans ce TP, vous ne devez **pas encore modéliser en RDF** ni écrire de triplets. Le travail demandé reste au niveau :

- de l'analyse de jeu de données
- de l'identification d'entités
- de la proposition de liens
- de la justification technique

## Proposition de workflow GitHub

Si ce dépôt est distribué via GitHub :

1. faites un `fork` du dépôt
2. créez un dossier `deliverables/Groupe-XX/`
3. committez vos livrables au fur et à mesure
4. ouvrez une `Pull Request` 

## Travail à rendre avant la prochaine séance

Le travail hors séance est **différent** du travail mené pendant le TP. Il ne s'agit pas simplement de finaliser les documents de séance, mais de produire une **étude complémentaire** servant de préparation directe au TP2.

L'énoncé séparé du travail à rendre se trouve dans [`TRAVAIL_A_RENDRE.md`](./TRAVAIL_A_RENDRE.md).

Les gabarits à utiliser sont fournis dans :

- [`templates/benchmark_referentiels.md`](./templates/benchmark_referentiels.md)
- [`templates/plan_normalisation.md`](./templates/plan_normalisation.md)
- [`templates/rapport_hors_seance_template.md`](./templates/rapport_hors_seance_template.md)

## Évaluation

Le barème détaillé figure dans [`TP1.md`](./TP1.md), mais la note porte principalement sur :

- la qualité de l'analyse du jeu de données
- la pertinence des entités et des liens proposés
- la capacité de justification
- la qualité du rapport
