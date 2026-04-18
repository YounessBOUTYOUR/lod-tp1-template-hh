# Benchmark des référentiels externes

## Informations générales

- **Module** : Linked Open Data
- **TP** : TP1 – Travail hors séance
- **Jeu de données** : Établissements de l'enseignement supérieur universitaire public au Maroc (2024-2025)
- **Producteur** : MESRSI – Portail data.gov.ma
- **Licence** : ODbL

---

## 1. Types d'entités retenus

Trois types d'entités ont été identifiés comme prioritaires pour l'alignement avec des référentiels externes :

| Type d'entité | Exemple local | Raison du choix | Champs candidats pour l'appariement |
|---|---|---|---|
| **Établissement** | ENSIAS, EMI, FSR | Entité centrale du dataset ; porteur de la majorité des attributs | `institution_name`, `short_name`, `website`, `year_created` |
| **Université** | Université Mohammed V, Université Hassan II | Entité hiérarchique ; regroupe plusieurs établissements | `institution_name`, `short_name`, `website`, `city` |
| **Ville** | Rabat, Casablanca, Fès | Entité géographique présente dans le champ `city` ; pivot de liaison vers GeoNames | `city`, `country` |

---

## 2. Benchmark des référentiels – Type : Établissement

### Référentiel A : Wikidata

| Critère | Observation |
|---|---|
| **Couverture** | Très bonne pour les grandes écoles et universités marocaines connues (ENSIAS, UM5, EMI). Partielle pour les établissements récents ou moins visibles. |
| **Précision apparente** | Élevée : les fiches Wikidata pour les établissements marocains incluent souvent la date de création, le campus, la discipline et la hiérarchie universitaire. |
| **Qualité des identifiants** | Excellente : chaque entité dispose d'un QID stable et déréférençable (ex. `Q2064250` pour UM5). |
| **Richesse sémantique** | Très riche : propriétés `P571` (date de fondation), `P17` (pays), `P131` (localisation administrative), `P856` (site web officiel), `P749` (organisation parente). |
| **Facilité d'utilisation** | Bonne : API REST et SPARQL disponibles. Recherche par label en arabe, français ou anglais possible via `wbsearchentities`. |
| **Limites** | Certains établissements récents (post-2007) sont absents ou incomplets. Les noms peuvent différer (translittération arabe/français). |

### Référentiel B : DBpedia

| Critère | Observation |
|---|---|
| **Couverture** | Inférieure à Wikidata pour le Maroc : seules les plus grandes universités (UM5, UCA, UAE) ont des pages complètes dérivées de Wikipédia. |
| **Précision apparente** | Moyenne : les données sont extraites automatiquement des infoboxes Wikipédia, avec risque d'erreur ou d'incomplétude. |
| **Qualité des identifiants** | Bonne : URIs basées sur le nom Wikipédia (ex. `dbr:Mohammed_V_University`), mais moins stables qu'un QID. |
| **Richesse sémantique** | Modérée : propriétés `dbo:country`, `dbo:city`, `dbo:established`, `dbo:type`. Moins complète que Wikidata pour les établissements spécialisés. |
| **Facilité d'utilisation** | Correcte : endpoint SPARQL `dbpedia.org/sparql`, mais résultats parfois instables ou désynchronisés avec Wikipédia. |
| **Limites** | Forte dépendance à la qualité des articles Wikipédia en français ou en anglais. Les écoles d'ingénieurs marocaines y sont peu représentées. |

### Conclusion comparative – Établissements

> **Wikidata est nettement préférable** pour l'alignement des établissements marocains, en raison de sa meilleure couverture, de la stabilité de ses QIDs et de la richesse de son modèle sémantique. DBpedia peut être utilisée en complément pour croiser les données, mais ne constitue pas un référentiel principal fiable pour ce jeu de données.

---

## 3. Benchmark des référentiels – Type : Université

### Référentiel A : Wikidata

| Critère | Observation |
|---|---|
| **Couverture** | Excellente pour les 12 universités publiques marocaines. Toutes sont présentes avec QID identifiable. |
| **Précision apparente** | Très bonne : données de fondation, affiliation, site web, localisation. |
| **Qualité des identifiants** | Excellente (QIDs stables). |
| **Richesse sémantique** | Permet de relier l'université à ses établissements membres via `P527` (a pour partie). |
| **Facilité d'utilisation** | Recherche directe par label. Résultats immédiatement exploitables. |

### Référentiel B : MESRSI / data.gov.ma

| Critère | Observation |
|---|---|
| **Couverture** | Complète pour le contexte marocain : 12 universités officielles référencées. |
| **Précision apparente** | Source officielle, mais sans identifiants URI stables exposés publiquement. |
| **Qualité des identifiants** | Faible : pas d'URI déréférençable pour chaque université. Les noms peuvent varier entre documents. |
| **Richesse sémantique** | Limitée : les données publiées sont tabulaires, sans liens vers d'autres ressources. |
| **Facilité d'utilisation** | Utile pour valider les noms officiels, mais non exploitable directement en LOD. |

### Conclusion comparative – Universités

> Wikidata constitue le seul référentiel externe réellement utilisable pour l'alignement des universités. Le portail MESRSI est une source de validation des noms officiels, mais ne peut pas jouer le rôle d'un référentiel LOD.

---

## 4. Benchmark des référentiels – Type : Ville

### Référentiel A : GeoNames

| Critère | Observation |
|---|---|
| **Couverture** | Exhaustive : toutes les villes marocaines sont référencées avec identifiant numérique stable. |
| **Précision apparente** | Excellente : noms alternatifs (arabe, amazigh, français), coordonnées géographiques, région administrative, code pays. |
| **Qualité des identifiants** | Excellente : chaque lieu possède un `geonames_id` numérique stable et déréférençable via `http://sws.geonames.org/{id}/`. |
| **Richesse sémantique** | Bonne : classe `gn:Feature`, propriétés `gn:name`, `gn:alternateName`, `gn:countryCode`, `gn:population`. Compatible avec des ontologies géospatiales. |
| **Facilité d'utilisation** | API simple disponible. Recherche par nom de ville possible. Données téléchargeables librement. |

### Référentiel B : Wikidata

| Critère | Observation |
|---|---|
| **Couverture** | Très bonne pour les villes marocaines du dataset (Rabat, Casablanca, Fès, Marrakech, etc.). |
| **Précision apparente** | Riche : coordonnées, population, région, statut administratif, images. |
| **Qualité des identifiants** | Excellente (QIDs stables). Wikidata inclut également des liens vers GeoNames (`P1566`). |
| **Richesse sémantique** | Supérieure à GeoNames sur le plan encyclopédique, mais les deux sont complémentaires. |
| **Facilité d'utilisation** | Bonne. Peut aussi servir de pont vers GeoNames via `P1566`. |

### Conclusion comparative – Villes

> **GeoNames est le référentiel de référence** pour les données géographiques et l'obtention de coordonnées. Wikidata est complémentaire et permet des enrichissements contextuels. Pour un graphe LOD, il est recommandé d'utiliser les deux : GeoNames comme ancre géospatiale, Wikidata pour le contexte.

---

## 5. Cas d'appariement manuel (6 cas documentés)

### Cas 1 – ENSIAS → Wikidata

| Champ | Valeur |
|---|---|
| **Entité locale** | `Ecole Nationale Superieure d'Informatique et d'Analyse des Systemes` |
| **Abréviation** | ENSIAS |
| **Ville** | Rabat |
| **Référentiel cible** | Wikidata |
| **URI proposée** | `https://www.wikidata.org/wiki/Q2963869` |
| **Indices utilisés** | Nom complet, sigle ENSIAS, ville Rabat, site web `ensias.um5.ac.ma` |
| **Niveau de confiance** | ★★★★★ – Très élevé |
| **Ambiguïté** | Aucune : l'entité est unique et bien documentée sur Wikidata |
| **Décision finale** | Appariement confirmé |

### Cas 2 – Université Mohammed V → Wikidata

| Champ | Valeur |
|---|---|
| **Entité locale** | `Universite Mohammed V in Rabat` |
| **Abréviation** | UM5 |
| **Ville** | Rabat |
| **Référentiel cible** | Wikidata |
| **URI proposée** | `https://www.wikidata.org/wiki/Q2064250` |
| **Indices utilisés** | Nom officiel, sigle UM5, année 1957, ville Rabat |
| **Niveau de confiance** | ★★★★★ – Très élevé |
| **Ambiguïté** | Aucune : seule université publique de ce nom à Rabat |
| **Décision finale** | Appariement confirmé |

### Cas 3 – Ville de Rabat → GeoNames

| Champ | Valeur |
|---|---|
| **Entité locale** | `Rabat` (champ `city`) |
| **Pays** | Morocco |
| **Référentiel cible** | GeoNames |
| **URI proposée** | `http://sws.geonames.org/2538475/` |
| **Indices utilisés** | Nom de la ville, code pays MA |
| **Niveau de confiance** | ★★★★★ – Très élevé |
| **Ambiguïté** | Potentielle avec d'autres villes nommées Rabat dans le monde, levée par le code pays Morocco |
| **Décision finale** | Appariement confirmé avec filtre `countryCode=MA` |

### Cas 4 – Université Cadi Ayyad → Wikidata

| Champ | Valeur |
|---|---|
| **Entité locale** | `Cadi Ayyad University` |
| **Abréviation** | UCA |
| **Ville** | Marrakesh |
| **Référentiel cible** | Wikidata |
| **URI proposée** | `https://www.wikidata.org/wiki/Q928670` |
| **Indices utilisés** | Nom en anglais (`Cadi Ayyad University`), sigle UCA, ville Marrakech, année 1978 |
| **Niveau de confiance** | ★★★★☆ – Élevé |
| **Ambiguïté** | Légère : la transcription du nom (Cadi Ayyad / Qadi Ayyad) peut varier |
| **Décision finale** | Appariement probable ; vérification par le site web `uca.ma` recommandée |

### Cas 5 – EHTP → Wikidata

| Champ | Valeur |
|---|---|
| **Entité locale** | `Ecole Hassania des Travaux Publics` |
| **Abréviation** | EHTP |
| **Ville** | Casablanca |
| **Référentiel cible** | Wikidata |
| **URI proposée** | `https://www.wikidata.org/wiki/Q3057282` |
| **Indices utilisés** | Nom complet, sigle EHTP, ville Casablanca, domaine `civil engineering` |
| **Niveau de confiance** | ★★★★☆ – Élevé |
| **Ambiguïté** | Aucune ambiguïté notable ; entité rare et spécifique |
| **Décision finale** | Appariement probable |

### Cas 6 – Ville de Marrakesh → GeoNames

| Champ | Valeur |
|---|---|
| **Entité locale** | `Marrakesh` (champ `city`) |
| **Pays** | Morocco |
| **Référentiel cible** | GeoNames |
| **URI proposée** | `http://sws.geonames.org/2542997/` |
| **Indices utilisés** | Nom de ville, code pays MA |
| **Niveau de confiance** | ★★★★★ – Très élevé |
| **Ambiguïté** | Variation orthographique (`Marrakesh` vs `Marrakech`) ; GeoNames référence les deux formes comme noms alternatifs |
| **Décision finale** | Appariement confirmé ; normaliser le champ local en `Marrakech` (forme officielle française) |

---

## 6. Analyse critique des appariements

- **Liens les plus fiables** : Wikidata pour ENSIAS, UM5, et GeoNames pour Rabat — entités uniques, bien documentées, sans ambiguïté de nom.
- **Liens incertains** : établissements récents (post-2005) dont la fiche Wikidata est incomplète ou absente (ex. ENSA Kénitra, créée en 2008).
- **Informations manquantes** pour automatisation : identifiant officiel MESRSI, numéro d'accréditation, coordonnées GPS directement dans le dataset source.
- **Risques de faux positifs** : confusion entre homonymes (ex. plusieurs établissements nommés "Faculté des Sciences" dans des villes différentes).
- **Entités prioritaires pour identifiant stable** : les universités (12 entités stables) et les villes (10 entités géographiques fixes).
