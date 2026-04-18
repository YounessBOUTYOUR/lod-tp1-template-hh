# Benchmark des référentiels externes

- **Groupe** : hh | **Date** : 18 avril 2026
- **Jeu de données** : Établissements de l'enseignement supérieur universitaire public — 2024-2025
- **Producteur** : MESRSI — data.gov.ma | **Licence** : ODbL
- **Colonnes réelles** : Université, Établissement, Abréviation, Effectifs 2023-2024, Nom arabe établissement, Nom arabe université, Ville, Adresse, Téléphone, Fax
- **Volume** : 165 établissements, 12 universités, 38 villes

---

## 1. Types d'entités retenus

| Type d'entité | Exemple tiré du fichier réel | Raison du choix | Champs candidats |
|---|---|---|---|
| **Établissement** | Ecole Mohammadia d'Ingénieurs Rabat | Entité centrale ; 165 enregistrements avec nom, abréviation, adresse, effectifs et nom arabe | `Etablissement`, `Abreviation`, `Adresse`, `Etablissement_AR` |
| **Université** | Université Mohammed V - Rabat | Entité hiérarchique ; 12 universités nommées en français et en arabe | `Universite`, `Universite_AR` |
| **Ville** | Fès, Béni Mellal, Dakhla, Es-Semara | Entité géographique ; 38 villes distinctes ; présente dans chaque ligne | `Ville` |

---

## 2. Benchmark — Établissements : Wikidata vs DBpedia

| Critère | Wikidata | DBpedia |
|---|---|---|
| **Couverture** | Bonne pour les grandes écoles nationales (EMI, ENSIAS, ENSAM, ENCG). Faible pour les facultés génériques (FSJES, FLSH) démultipliées en plusieurs villes. | Très partielle : seules les universités-mères ont une page Wikipédia complète. Les 165 composantes ne sont quasi pas référencées. |
| **Précision** | Élevée pour les établissements à nom propre distinctif. Les facultés polydisciplinaires récentes (post-2007) sont souvent absentes. | Faible : extraction automatique des infoboxes Wikipédia, souvent incomplètes pour les composantes marocaines. |
| **Qualité des identifiants** | Excellente : QID stable et déréférençable (ex. Q2963869 pour ENSIAS Rabat). | Moyenne : URI basée sur le titre Wikipédia, sensible aux renommages. |
| **Richesse sémantique** | Très riche : P571, P17, P131, P856, P749, P527. Les labels arabes du dataset permettent une vérification directe des labels ar: de Wikidata. | Limitée : dbo:country, dbo:city, dbo:established disponibles uniquement pour les grandes universités. |
| **Facilité d'utilisation** | Bonne : API REST + SPARQL. Les noms arabes du dataset (colonne `Etablissement_AR`) facilitent la recherche multilingue via `wbsearchentities`. | Correcte en théorie, mais endpoint souvent désynchronisé avec Wikipédia. |
| **Limites pour ce dataset** | Les abréviations contiennent la ville en suffixe (« ENSIAS Rabat ») — nettoyage obligatoire avant appariement. | Non adapté aux composantes universitaires qui forment la majorité des 165 enregistrements. |
| **Recommandation** | ✅ Référentiel principal | ⚠️ Usage secondaire uniquement |

---

## 3. Benchmark — Universités : Wikidata vs data.gov.ma

| Critère | Wikidata | data.gov.ma (MESRSI) |
|---|---|---|
| **Couverture** | Excellente : les 12 universités publiques marocaines sont toutes présentes avec fiches documentées. | Complète : source officielle du dataset, autorité de référence pour les noms. |
| **Qualité des identifiants** | Excellente : QIDs stables pour les 12 universités. | Nulle : aucun URI stable exposé ; les noms textuels incluent des tirets et espaces parasites (ex. « Université Mohammed V - Rabat - »). |
| **Richesse sémantique** | Très riche : P527 permet de lier chaque université à ses composantes. Labels en arabe vérifiables via `Universite_AR`. | Très limitée : données tabulaires sans liens vers d'autres ressources. |
| **Facilité d'utilisation** | Bonne : recherche directe en français ou en arabe. | Utile pour valider les noms officiels ; inutilisable comme référentiel LOD. |
| **Recommandation** | ✅ Référentiel principal | 📋 Source de validation des noms uniquement |

---

## 4. Benchmark — Villes : GeoNames vs Wikidata

| Critère | GeoNames | Wikidata |
|---|---|---|
| **Couverture** | Exhaustive : toutes les 38 villes du dataset (y compris Berkene, Dakhla, Es-Semara, Sidi Bennour) sont référencées. | Très bonne pour les grandes villes ; correcte pour les villes secondaires. |
| **Qualité des identifiants** | Excellente : geonames_id numérique stable ; URI déréférençable `http://sws.geonames.org/{id}/`. | Excellente : QID stable + lien P1566 → GeoNames. |
| **Richesse sémantique** | Bonne : noms en français, arabe, amazigh ; coordonnées GPS ; région administrative ; population. | Supérieure sur le plan encyclopédique et administratif. |
| **Facilité d'utilisation** | Très bonne : API simple, données librement téléchargeables. La forme française avec accents du dataset correspond aux formes GeoNames (Fès ✓, Béni Mellal ✓, Tétouan ✓). | Bonne ; peut servir de pont vers GeoNames via P1566. |
| **Erreur détectée** | La valeur « FEG Guelmim » apparaît dans le champ Ville (ligne 145) — confusion avec l'abréviation de l'établissement. À corriger en « Guelmim » avant appariement. | — |
| **Recommandation** | ✅ Référentiel principal pour l'ancrage géographique | 📎 Référentiel complémentaire |

---

## 5. Cas d'appariement manuel (6 cas)

| # | Entité locale (dataset réel) | Référentiel / URI candidate | Indices utilisés | Confiance | Ambiguïté | Décision |
|---|---|---|---|---|---|---|
| 1 | Ecole Nationale Supérieure d'Informatique et d'Analyse des Systèmes Rabat | Wikidata Q2963869 | Nom complet, abréviation « ENSIAS Rabat », ville Rabat, nom arabe المدرسة الوطنية العليا للإعلاميات | ★★★★★ | Aucune | Confirmé |
| 2 | Université Mohammed V - Rabat | Wikidata Q2064250 | Nom officiel, ville Rabat, nom arabe جامعة محمد الخامس | ★★★★★ | Aucune | Confirmé |
| 3 | Université Hassan II - Casablanca | Wikidata Q3552327 | Nom, ville Casablanca, nom arabe جامعة الحسن الثاني | ★★★★☆ | À distinguer de l'ancienne Université Hassan II de Mohammedia (fusionnée en 2014) | Probable ; vérifier l'année de création dans Wikidata |
| 4 | Ecole Nationale de Commerce et de Gestion Agadir | Wikidata Q22679872 | Nom complet, abréviation ENCG, ville Agadir, université Ibn Zohr | ★★★★☆ | Plusieurs ENCG existent (Agadir, Casablanca, Fès, Marrakech, Oujda, Tanger...) — le seul sigle ne suffit pas | Probable avec contrainte ville=Agadir |
| 5 | Rabat (ville) | GeoNames 2538475 | Nom, code pays MA, capitale du Maroc | ★★★★★ | Aucune avec filtre countryCode=MA | Confirmé |
| 6 | Fès (ville) | GeoNames 2295420 | Nom avec accent, code pays MA | ★★★★★ | Forme anglaise « Fez » dans certains contextes — levée par la forme française du dataset | Confirmé ; utiliser « Fès » comme label @fr |

---

## 6. Analyse critique

- **Problème structurel principal** : Les abréviations du dataset incluent systématiquement la ville en suffixe (« ENSIAS Rabat », « ENSA Fès », « FSJES Agdal Rabat »). Ce format composite empêche toute utilisation directe comme identifiant ou comme clé d'appariement sans extraction préalable du sigle pur.
- **Atout inattendu** : La présence des noms en arabe pour 100% des enregistrements (`Etablissement_AR`, `Universite_AR`) constitue un avantage réel pour la vérification des appariements Wikidata, dont les labels arabes sont souvent disponibles.
- **Erreur de saisie confirmée** : Ligne 145 — valeur « FEG Guelmim » dans le champ Ville au lieu de « Guelmim ». À corriger avant tout alignement géographique.
- **Risque de collision de sigles** : ENS, ENSA, ENCG, EST, FLSH, FSJES, FMP existent dans de nombreuses villes. Un alignement automatique sur le sigle seul produirait des faux positifs massifs.
- **Effectifs manquants** : 5 enregistrements sans effectifs (IS Rabat, IERA Rabat, IUEAEMIA Rabat, EST Tétouan, EST Ouarzazate) — probablement instituts de recherche ou établissements en démarrage.
