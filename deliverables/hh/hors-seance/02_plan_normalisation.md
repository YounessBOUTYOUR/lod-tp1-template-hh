# Plan de normalisation et d'identification

## Informations générales

- **Module** : Linked Open Data
- **TP** : TP1 – Travail hors séance
- **Jeu de données** : Établissements de l'enseignement supérieur universitaire public au Maroc (2024-2025)
- **Producteur** : MESRSI – data.gov.ma

---

## 1. Entités prioritaires

| Type d'entité | Pourquoi est-il prioritaire ? | Identifiant actuel | Stratégie proposée |
|---|---|---|---|
| **Établissement** | Entité centrale du dataset ; 165 enregistrements ; porteur de tous les attributs descriptifs | `record_id` (séquentiel, non stable) | Construire un URI local basé sur le sigle et la ville : `http://data.education.ma/etablissement/{short_name}-{city}` |
| **Université** | Entité hiérarchique regroupant plusieurs établissements ; 12 universités distinctes | Nom textuel uniquement | Aligner sur Wikidata QID ; construire URI local : `http://data.education.ma/universite/{short_name}` |
| **Ville** | Entité géographique transversale ; 10+ villes présentes dans le dataset | Nom textuel brut, non normalisé | Aligner sur GeoNames ID ; utiliser les URIs GeoNames comme identifiants déréférençables |

---

## 2. Champs à normaliser

| Champ | Problème observé | Règle de normalisation proposée | Impact attendu |
|---|---|---|---|
| `institution_name` | Absence d'accents français (ex. `Ecole` au lieu de `École`) ; mélange français/anglais (ex. `Cadi Ayyad University` au lieu du nom officiel français) | Standardiser en français officiel avec accents ; utiliser la langue arabe comme label alternatif (`skos:altLabel`) | Améliore l'appariement avec Wikidata et DBpedia ; cohérence inter-enregistrements |
| `short_name` | Certains sigle incluent des espaces ou des descriptifs géographiques (ex. `ENSA Kenitra`) ; instabilité de format | Normaliser en sigle pur sans espace : `ENSAKenitra` ou avec tiret `ENSA-Kenitra` ; documenter la règle | Utilisable comme composant d'URI stable |
| `city` | Variations orthographiques : `Marrakesh` vs `Marrakech` ; `Tetouan` vs `Tétouan` | Adopter la forme française officielle avec accents ; créer une table de correspondance nom local → GeoNames URI | Cohérence géographique ; alignement GeoNames fiable |
| `institution_type` | Valeurs hétérogènes : `public school`, `faculty`, `university`, `engineering school`, `public institute` | Mapper vers une ontologie contrôlée (ex. TEACH, schema.org `EducationalOrganization`, ou valeurs SKOS locales) | Interopérabilité sémantique ; requêtes SPARQL cohérentes |
| `main_field` | Champ en anglais, valeurs non normalisées (`computer science`, `data science`, `applied sciences`) | Aligner vers un référentiel disciplinaire (ex. ISCED-F 2013, EuroVoc) ; URI pour chaque discipline | Lien vers des thésaurus disciplinaires internationaux |
| `website` | URL existant mais sans vérification de déréférencement ; format `http` vs `https` non uniformisé | Vérifier accessibilité des URLs ; forcer le schéma `https` ; utiliser comme `foaf:homepage` | Interopérabilité et dereferencabilité dans le graphe RDF |
| `year_created` | Valeur numérique simple, pas en format date ISO | Convertir en `xsd:gYear` (ex. `"1992"^^xsd:gYear`) pour une compatibilité RDF complète | Requêtes temporelles précises dans SPARQL |
| `country` | Valeur textuelle `Morocco` uniquement | Remplacer par un URI pays stable (ex. Wikidata `Q1028` ou ISO 3166-1 `MA`) | Alignement international ; suppression de l'ambiguïté linguistique |

---

## 3. Stratégie d'identifiants

| Entité cible | Base de construction envisagée | Risque | Recommandation |
|---|---|---|---|
| **Établissement** | `http://data.education.ma/etablissement/{short_name}` (ex. `/etablissement/ENSIAS`) | Collision si deux établissements ont le même sigle dans des villes différentes | Ajouter la ville en suffixe : `/etablissement/ENSIAS-Rabat` ; vérifier l'unicité sur l'ensemble du dataset réel (165 enregistrements) |
| **Université** | `http://data.education.ma/universite/{short_name}` (ex. `/universite/UM5`) | Sigle potentiellement instable ou renommable dans le futur | Combiner avec l'alignement Wikidata : utiliser le QID comme identifiant externe de référence (`owl:sameAs`) |
| **Ville** | URI GeoNames déréférençable (ex. `http://sws.geonames.org/2538475/` pour Rabat) | Dépendance à un service tiers | Utiliser GeoNames comme identifiant de référence + créer un miroir local si nécessaire |
| **Discipline** | URI vers ISCED-F 2013 ou EuroVoc | Couverture incomplète pour certaines disciplines très spécialisées | Créer des identifiants locaux pour les disciplines non couvertes : `http://data.education.ma/discipline/{label}` |

### Schéma URI proposé (récapitulatif)

```
Namespace de base : http://data.education.ma/

Établissements : http://data.education.ma/etablissement/{sigle}-{ville}
Universités    : http://data.education.ma/universite/{sigle}
Villes         : http://sws.geonames.org/{geonames_id}/        (externe)
Disciplines    : http://data.education.ma/discipline/{label-normalise}
```

---

## 4. Risques et limites

### Champs trop faibles pour servir d'identifiant

- **`institution_name`** : trop verbeux, sujet aux variations orthographiques et linguistiques ; ne peut pas constituer un identifiant stable.
- **`city`** (seul) : insuffisant pour distinguer deux établissements dans la même ville (Rabat en compte au moins 5 dans le dataset sample).
- **`main_field`** : valeurs non contrôlées, en anglais, avec des approximations sémantiques (`data science` vs `statistics`).

### Collisions et ambiguïtés possibles

- Deux établissements peuvent partager un sigle similaire s'il n'existe pas de registre officiel centralisé. La combinaison `{sigle}-{ville}` réduit ce risque mais ne l'élimine pas totalement.
- Les universités qui ont changé de nom dans le temps (ex. lors des réformes de l'enseignement supérieur) peuvent générer des doublons dans Wikidata.
- Le champ `institution_type` présente des chevauchements sémantiques : une `public school` peut aussi être une `engineering school`.

### Informations supplémentaires nécessaires

- **Identifiant officiel MESRSI** : un code administratif national attribué par le ministère constituerait un identifiant stable et non ambigu ; son absence est la principale lacune du dataset.
- **Région administrative** : la région (ex. Rabat-Salé-Kénitra) est absente du dataset mais essentielle pour l'alignement géographique précis.
- **Coordonnées GPS** : aucune coordonnée géographique n'est fournie pour les établissements, ce qui limite l'enrichissement via GeoNames ou Wikidata.
- **Langue arabe** : les noms officiels en arabe sont absents, ce qui rend l'alignement avec des sources arabophones difficile.

### Limites de la stratégie URI locale

- Les URIs construits localement (`http://data.education.ma/...`) ne sont pas déréférençables en l'état si aucune infrastructure Web n'est déployée pour les exposer.
- La stabilité des URIs dépend de la pérennité du domaine choisi et de la gouvernance des données.
