# qa-test-strategy

Ma façon de construire et piloter une démarche de test, de la stratégie jusqu'à la décision de mise en production.

Les exemples s'appuient sur ShopDemo, une boutique en ligne fictive (catalogue, recherche, panier,
paiement via un prestataire externe, compte client, back-office des commandes). Les principes sont ceux
que j'applique en mission, sur des contextes e-commerce, bancaires, assurance ou flux de données.

## Sommaire

| # | Document | En bref |
|---|---|---|
| 1 | [Stratégie de test](docs/01-strategie-de-test.md) | Périmètre, analyse de risques, niveaux et types de test, critères d'entrée / sortie |
| 2 | [ATDD](docs/02-atdd.md) | Écrire les tests d'acceptation avec le métier avant le développement |
| 3 | [Smoke tests](docs/03-smoke-tests.md) | Comment je choisis les quelques tests qui bloquent ou non une campagne |
| 4 | [Non-régression](docs/04-non-regression.md) | Prioriser, automatiser et maintenir la TNR |
| 5 | [Performance](docs/05-performance.md) | Du besoin au tir : modèle de charge, loi de Little, types de tir, analyse |
| 6 | [Données de test](docs/06-donnees-de-test.md) | Jeux de données maîtrisés, sans données de production |
| 7 | [Gestion des défauts](docs/07-defauts.md) | Rédiger, qualifier, prioriser, confirmer |
| 8 | [KPI et Go / No Go](docs/08-kpi-go-no-go.md) | Les indicateurs suivis et la grille de décision de mise en production |

## Mise en pratique

Plusieurs points sont appliqués dans mes autres dépôts :

| Dépôt | Ce qu'il illustre |
|---|---|
| [mock-payment-api](https://github.com/tazibt/mock-payment-api) | ATDD (scénarios Gherkin), smoke tests tagués `@smoke` qui conditionnent la campagne en CI, isolation d'un prestataire externe |
| [Generateur-Data](https://github.com/tazibt/Generateur-Data) | Données de test fictives et reproductibles, sans aucune donnée personnelle réelle |
| [gatling-performance-tests](https://github.com/tazibt/gatling-performance-tests) | Modèles de charge ouvert / fermé, loi de Little, assertions de SLA en CI |

## Vocabulaire

J'utilise le vocabulaire ISTQB : base de test, condition de test, cas de test, critères d'entrée et de sortie,
test de confirmation, test de régression.
