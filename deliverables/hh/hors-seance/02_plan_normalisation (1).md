# Plan de normalisation et d'identification

- **Groupe** : hh | **Date** : 18 avril 2026
- **Jeu de données** : Établissements de l'enseignement supérieur universitaire public — 2024-2025
- **Producteur** : MESRSI — data.gov.ma | **Colonnes** : 10 | **Enregistrements** : 165

---

## 1. Entités prioritaires

| Type d'entité | Pourquoi prioritaire ? | Identifiant actuel | Stratégie proposée |
|---|---|---|---|
| **Établissement** | Entité centrale ; 165 enregistrements ; aucun identifiant sémantique | Aucun (pas de colonne ID stable) | URI locale construite à partir du sigle nettoyé + ville : `http://data.education.ma/etablissement/{sigle-pur}-{ville}` |
| **Université** | Entité hiérarchique ; 12 universités ; nom textuel avec caractères parasites | Nom textuel avec tirets et espaces parasites (ex. « Université Mohammed V - Rabat - ») | Aligner sur QID Wikidata ; URI locale `http://data.education.ma/universite/{sigle}` + `owl:sameAs` → QID |
| **Ville** | Entité géographique ; 38 villes ; texte brut sans code officiel | Texte libre (ex. « Fès », « Béni Mellal ») | Aligner sur GeoNames ID ; utiliser `http://sws.geonames.org/{id}/` comme identifiant déréférençable |

---

## 2. Champs à normaliser en priorité

| Champ | Problème observé (données réelles) | Règle de normalisation proposée | Impact LOD |
|---|---|---|---|
| `Abreviation` | Contient systématiquement la ville en suffixe (« ENSIAS Rabat », « FSJES Agdal Rabat », « ENSA  Béni Mellal » avec double espace) | Extraire le sigle pur par suppression de la ville en suffixe ; supprimer les espaces multiples ; stocker dans un nouveau champ `sigle_pur` | Rend le sigle utilisable comme composant d'URI et comme clé d'appariement |
| `Universite` | Nom textuel avec tirets parasites et espaces en fin de chaîne (« Université Mohammed V - Rabat - ») | Supprimer les tirets finaux et espaces parasites ; extraire la ville-siège dans un champ séparé `ville_siege_universite` | Nom propre exploitable comme label RDF |
| `Ville` | Erreur de saisie ligne 145 : « FEG Guelmim » au lieu de « Guelmim » | Corriger manuellement les valeurs aberrantes ; créer une table de correspondance ville → GeoNames ID | Alignement géographique fiable |
| `Adresse` | 9 adresses manquantes ; format non uniforme (présence ou absence du code postal, variations de ponctuation) | Signaler les manquants ; normaliser selon le format : Rue, Quartier, Ville, CP si disponible | Enrichissement possible via geocodage |
| `Telephone` / `Fax` | 10 téléphones et 13 fax manquants ; format non standardisé (présence ou absence de l'indicatif +212) | Standardiser au format E.164 : `+212-5XX-XXXXXX` | Non critique pour le LOD mais important pour la qualité des données |
| `Effectifs_2023_2024` | 5 valeurs manquantes (instituts de recherche et établissements en démarrage) | Signaler les manquants comme `xsd:integer` ; laisser vide plutôt que de mettre 0 | Évite les faux zéros dans les requêtes SPARQL |
| `Etablissement_AR` / `Universite_AR` | Présents pour 100% des enregistrements — atout majeur | Nettoyer les éventuels espaces parasites ; utiliser comme `rdfs:label @ar` | Enrichissement sémantique multilingue direct |

---

## 3. Stratégie d'identifiants

| Entité | URI locale proposée | Exemple concret | Risque | Recommandation |
|---|---|---|---|---|
| **Établissement** | `http://data.education.ma/etablissement/{sigle-pur}-{ville-normalisee}` | `/etablissement/ENSIAS-Rabat` | Collision si deux établissements ont le même sigle dans la même ville (non observé dans le dataset) | Vérifier l'unicité sur les 165 enregistrements après nettoyage des sigles |
| **Université** | `http://data.education.ma/universite/{sigle}` | `/universite/UM5` | Instabilité si renommage futur ; sigle non présent dans le fichier, à dériver du nom | Associer obligatoirement un `owl:sameAs` vers le QID Wikidata |
| **Ville** | URI GeoNames externe : `http://sws.geonames.org/{id}/` | `http://sws.geonames.org/2538475/` pour Rabat | Dépendance à un service tiers | Créer une table de correspondance locale `ville → geonames_id` pour les 38 villes |
| **Pays Maroc** | Wikidata Q1028 ou code ISO 3166-1 `MA` | `https://www.wikidata.org/wiki/Q1028` | Aucun | Utiliser comme valeur de la propriété `P17` (pays) |

### Table de correspondance villes → GeoNames (extrait, 10 villes prioritaires)

| Ville (dataset) | GeoNames ID | URI |
|---|---|---|
| Rabat | 2538475 | http://sws.geonames.org/2538475/ |
| Casablanca | 2553604 | http://sws.geonames.org/2553604/ |
| Fès | 2295420 | http://sws.geonames.org/2295420/ |
| Marrakech | 2542997 | http://sws.geonames.org/2542997/ |
| Agadir | 2510852 | http://sws.geonames.org/2510852/ |
| Tanger | 2530335 | http://sws.geonames.org/2530335/ |
| Meknès | 2542083 | http://sws.geonames.org/2542083/ |
| Oujda | 2534906 | http://sws.geonames.org/2534906/ |
| Kénitra | 2542496 | http://sws.geonames.org/2542496/ |
| Tétouan | 2527089 | http://sws.geonames.org/2527089/ |

---

## 4. Risques et limites

### Champs trop faibles pour servir d'identifiant
- **`Abreviation` brut** : Contient la ville en suffixe et des espaces multiples — inutilisable sans nettoyage.
- **`Universite`** : Tirets et espaces parasites en fin de chaîne (« - Rabat - ») — inutilisable tel quel.
- **`Ville`** seul : Texte libre avec erreur de saisie confirmée (« FEG Guelmim »).

### Collisions et ambiguïtés possibles
- **Sigles génériques** : ENS, ENSA, ENCG, EST, FSJES, FLSH, FMP, FP existent dans de nombreuses villes. Après extraction du sigle pur, la combinaison `sigle-pur + ville` doit être vérifiée pour l'unicité.
- **ENSA Béni Mellal et ENSA Khouribga** : deux ENSA affiliées à la même université (Sultan Moulay Slimane) dans des villes différentes — la combinaison `ENSA-Béni Mellal` et `ENSA-Khouribga` les distingue correctement.
- **Facultés polydisciplinaires** : Plusieurs FP dans des villes variées (Taza, Khouribga, Nador, Safi, Sidi Bennour, Ouarzazate, Taroudant, Es-Semara, Larache, Kssar El Kébir, Errachidia) — toutes distinguables par la ville.

### Informations manquantes critiques
- **Aucun site web** dans le dataset réel (contrairement au CSV pédagogique). Le champ `website` qui était la meilleure ancre d'appariement dans l'exercice est absent du dataset MESRSI — l'appariement repose donc uniquement sur le nom et la ville.
- **Aucun code officiel MESRSI** : l'absence d'un identifiant administratif national est la lacune principale.
- **Aucune coordonnée GPS** : l'adresse postale est présente mais non géocodée.
- **Aucun domaine de formation** : le type d'établissement n'est pas renseigné, contrairement au CSV pédagogique (champ `main_field`).
- **Aucune date de création** : le champ `year_created` du CSV pédagogique n'existe pas dans le dataset réel.
