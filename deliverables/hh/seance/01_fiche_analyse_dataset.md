# Fiche d'analyse du jeu de données

## Identification

- **Groupe :** hh
- **Date :** 18 avril 2026
- **Nom du jeu de données :** higher_education_sample.csv
- **Source / producteur :** Fichier pédagogique fourni dans le cadre du TP1 (données synthétiques inspirées du paysage universitaire marocain)
- **URL de la source :** Fichier local — `data/higher_education_sample.csv` (dépôt du module)
- **Domaine métier :** Enseignement supérieur public / Gouvernance institutionnelle

---

## Description générale

- **Objectif du jeu de données :** Servir de support d'analyse en séance. Il représente un panel simplifié d'établissements marocains : universités publiques, écoles d'ingénieurs, facultés et instituts spécialisés, implantés dans plusieurs villes.
- **Format(s) disponible(s) :** CSV (texte brut, séparateur virgule)
- **Langue(s) :** Anglais (noms de colonnes et valeurs contrôlées) ; noms d'établissements en français 
- **Licence :** Non précisée (fichier pédagogique interne au module)
- **Fréquence de mise à jour :** Ponctuelle (fichier statique fourni pour le TP)
- **Unité d'observation :** Un établissement d'enseignement supérieur
- **Granularité des enregistrements :** Établissement (niveau institutionnel — ni programme, ni étudiant, ni personnel)

---

## Structure du jeu de données

| Champ | Signification | Exemple de valeur | Observation |
|---|---|---|---|
| `record_id` | Identifiant entier séquentiel | 1, 2, 3... | Technique, sans signification sémantique, instable si le fichier est réordonné |
| `institution_name` | Nom complet de l'établissement | Ecole Nationale Superieure d'Informatique et d'Analyse des Systemes | Sans accents, casse mixte, non normalisé |
| `short_name` | Sigle ou acronyme | ENSIAS, EMI, UM5, UCA | Relativement stable ; potentiellement ambigu à l'échelle internationale |
| `city` | Ville d'implantation | Rabat, Casablanca, Fes, Kenitra, Tetouan, Marrakesh, Agadir | Texte libre en anglais, sans code géographique |
| `country` | Pays | Morocco | Valeur unique visible — toujours "Morocco" ; en anglais sans code ISO |
| `website` | URL du site institutionnel | https://www.ensias.um5.ac.ma | Présent pour tous les enregistrements visibles ; meilleure ancre d'appariement disponible |
| `institution_type` | Catégorie fonctionnelle | university, faculty, public school, engineering school | 4 valeurs distinctes observées ; vocabulaire en anglais, non formellement contrôlé |
| `main_field` | Domaine principal de formation | computer science, engineering, science, multidisciplinary, civil engineering, applied sciences | Granularité hétérogène ; texte libre en anglais |
| `year_created` | Année de création | 1957, 1959, 1971, 1992, 2008 | Entier, exploitable pour la contextualisation historique |
| `portal_hint` | Portail de données suggéré | data.gov.ma | Même valeur pour tous les enregistrements visibles ; utilité à confirmer |

---

## Évaluation "Open Data"

| Critère | Observation | Score / 5 |
|---|---|---|
| Accessibilité | Fichier fourni directement dans le dépôt, aucune barrière d'accès | 5/5 |
| Réutilisabilité | Aucune licence explicite — usage pédagogique présumé | 2/5 |
| Documentation | Pas de dictionnaire de données ; description minimale dans l'énoncé du TP | 2/5 |
| Format exploitable | CSV est un format ouvert, non propriétaire, lisible par tous les outils | 5/5 |
| Présence d'une licence | Absente | 0/5 |

---

## Évaluation "Linked Data readiness"

| Point observé | Oui / Non / Partiel | Commentaire |
|---|---|---|
| Identifiants stables pour les enregistrements | Non | `record_id` est séquentiel et sans signification sémantique ; instable si le fichier est modifié |
| Entités clairement distinguables | Partiel | Université, école, faculté coexistent dans le même tableau sans hiérarchie ni relation explicite |
| Valeurs normalisées | Partiel | `institution_type` est en anglais avec 4 valeurs distinctes mais non définies formellement ; `institution_name` est sans accents |
| Informations géographiques exploitables | Non | `city` est en texte libre en anglais, sans code GeoNames ni coordonnées |
| Possibilité de lien vers des référentiels externes | Oui | `website` est une ancre d'appariement forte ; `city` et `institution_name` permettent un alignement sur Wikidata et GeoNames |
| Présence de clés candidates plausibles | Partiel | `short_name` est candidat mais ambigu ; `website` est le champ le plus discriminant |
| Présence de codes ou nomenclatures réutilisables | Non | Aucun code officiel (ni ISO 3166, ni GeoNames ID, ni ISCED) présent dans le dataset |

---

## Maturité "5 étoiles" (Tim Berners-Lee)

| Niveau | Atteint ? | Justification |
|---|---|---|
| ★ : données disponibles sur le Web | Non | Fichier local dans un dépôt pédagogique, pas publié sur un portail Open Data avec URL pérenne |
| ★★ : données structurées | Oui | Format tabulaire CSV avec colonnes nommées et cohérentes |
| ★★★ : format non propriétaire | Oui | CSV est un format ouvert |
| ★★★★ : usage d'URI pour identifier les choses | Non | Aucune URI attribuée aux établissements ni aux villes |
| ★★★★★ : liens vers d'autres données | Non | Aucun lien vers des référentiels externes |



## Clés candidates et identifiants

| Objet ou entité cible | Champ(s) candidat(s) | Fiabilité | Limites | Recommandation |
|---|---|---|---|---|
| Établissement | `website` | Élevée | Une URL peut changer ; des établissements partagent un domaine parent (ex. `ensias.um5.ac.ma` et `fsr.um5.ac.ma` partagent `um5.ac.ma`) | Meilleure clé actuelle ; à compléter par un identifiant sémantique stable |
| Établissement | `short_name` | Moyenne | Les sigles ne sont pas uniques internationalement ; `UAE` désigne aussi les Émirats Arabes Unis | Utilisable comme clé locale avec préfixe pays : `MA-ENSIAS`, `MA-UM5` |
| Établissement | `record_id` | Faible | Entier séquentiel sans signification, instable si le fichier est réordonné ou enrichi | À ne pas utiliser comme identifiant pérenne |
| Ville | `city` | Faible | Texte libre en anglais ("Fes" au lieu de "Fès"), sans code officiel | Aligner sur GeoNames ID |

---

## Normalisation préalable nécessaire

| Champ | Problème observé | Impact pour le LOD | Action recommandée |
|---|---|---|---|
| `institution_name` | Absence d'accents ("Ecole", "Superieure", "Abdelmalek") ; casse non uniforme | Appariement textuel difficile avec des référentiels qui utilisent les accents | Normaliser en UTF-8 avec accents ; appliquer une casse titre cohérente |
| `city` | Anglicisation des noms ("Fes" au lieu de "Fès", "Marrakesh" au lieu de "Marrakech") ; aucun code géographique | Impossible de lier automatiquement à GeoNames sans normalisation | Corriger vers la forme officielle française ; ajouter un champ `geonames_id` |
| `country` | "Morocco" en anglais, sans code ISO 3166 | Valeur non directement alignable sur les référentiels utilisant ISO 3166-1 | Ajouter un champ `country_code` avec la valeur `MA` |
| `institution_type` | 4 valeurs observées non définies formellement ; "public school" et "faculty" peuvent se recouper sémantiquement | Ambiguïté pour l'alignement sur schema.org ou ISCED | Documenter les 4 valeurs avec leur définition ; envisager un alignement sur `schema:CollegeOrUniversity`, `schema:EducationalOrganization` |
| `main_field` | Granularité hétérogène ("multidisciplinary" très large vs "civil engineering" très spécifique) | Comparaison et regroupement difficiles | Normaliser sur la classification ISCED-F 2013 (domaines à 2 chiffres) |
| `short_name` | Ambiguïté internationale possible (ex. `UAE`) | Risque de faux appariement si utilisé seul comme identifiant | Préfixer systématiquement par le code pays : `MA-{SIGLE}` |
| `portal_hint` | Valeur identique pour tous les enregistrements visibles | Champ redondant s'il ne varie jamais | Vérifier la variabilité sur l'ensemble du dataset ; supprimer ou enrichir |

---

## Qualité des données

- **Forces observées :** Format CSV ouvert et directement exploitable ; présence du champ `website` qui constitue la meilleure ancre d'appariement disponible ; `short_name` facilite la reconnaissance des entités ; `year_created` permet une contextualisation historique ; diversité des types d'établissements et des villes.
- **Faiblesses observées :** Noms d'établissements sans accents ; noms de villes anglicisés sans code géographique officiel ; aucun identifiant sémantique stable ; vocabulaire `institution_type` et `main_field` non formellement contrôlé ; aucune licence déclarée.
- **Ambiguïtés ou doublons potentiels :** Le sigle `UAE` (Abdelmalek Essaadi University, Tetouan) est ambigu car il correspond aussi aux Émirats Arabes Unis dans d'autres contextes. La valeur `multidisciplinary` dans `main_field` est trop générique pour permettre un alignement thématique.
- **Champs manquants pour une meilleure interconnexion :** Identifiant stable local, code GeoNames pour les villes, code ISO 3166 pour le pays, code ISCED-F pour les domaines, coordonnées GPS, identifiant Wikidata.
- **Risque principal pour une future mise en relation :** L'absence d'accents dans les noms et les noms de villes anglicisés compliquent l'appariement automatique avec des référentiels qui utilisent les formes françaises officielles.

---

## Conclusion

Ce jeu de données pédagogique constitue un support d'analyse pertinent pour le TP1, précisément parce qu'il est volontairement imparfait. Il atteint le niveau 2-3 étoiles du modèle de Tim Berners-Lee : structuré dans un format ouvert (CSV), mais sans URI, sans codes officiels, et sans liens vers des référentiels externes.

Ses principales forces pour l'exercice sont la présence du champ `website` (ancre d'appariement exploitable et discriminante), la diversité des types d'établissements couverts, et la présence de `year_created` et `main_field` qui enrichissent le modèle conceptuel. Ses principales faiblesses sont l'absence d'accents dans les noms, les villes anglicisées sans code géographique, et un vocabulaire de `institution_type` et `main_field` non contrôlé formellement.

Avant toute transformation en graphe RDF, trois actions sont prioritaires : corriger l'encodage des noms d'établissements et des villes, construire un identifiant local stable à partir de `short_name` préfixé du code pays (`MA-ENSIAS`, `MA-UM5`, etc.), et aligner les villes sur leurs GeoNames ID. Ces corrections conditionneront directement la fiabilité des appariements lors du TP2.