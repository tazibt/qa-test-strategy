# 3. Smoke tests

Le smoke test répond à une seule question : **la livraison est-elle assez stable pour qu'on commence à tester ?**
S'il échoue, on ne lance pas la campagne : on renvoie la livraison.

## 3.1 Smoke, sanity, non-régression : la différence

Le glossaire ISTQB traite smoke test et sanity test comme synonymes. En pratique, les équipes les
distinguent souvent comme ci-dessous, et c'est l'usage que je retiens.

| | Smoke test | Sanity check | Non-régression |
|---|---|---|---|
| Question | Peut-on tester ? | La correction / l'évolution marche-t-elle ? | Rien d'autre n'est cassé ? |
| Quand | Après chaque déploiement | Après une livraison ciblée (correctif) | P1 à chaque livraison, P2 et plus avant la mise en production |
| Largeur | Large mais superficiel | Étroit et ciblé | Large et approfondi |
| Durée | 10 à 15 min max | Quelques minutes | Heures, voire jours |
| Si KO | Livraison rejetée, campagne bloquée | Correctif rejeté | Anomalies, décision au Go / No Go |

## 3.2 Comment je choisis les smoke tests

Je pars de la grille de risques ([voir 1](01-strategie-de-test.md)) et je ne garde un test que s'il passe
ces quatre filtres :

1. Il couvre un parcours vital. Si ce parcours est KO, plus rien n'a de sens : se connecter, chercher,
   ajouter au panier, payer. Pas les cas particuliers.
2. Son échec bloque d'autres tests. Si le login est KO, 80 % de la campagne est inexécutable :
   il est dans le smoke. Si un filtre de recherche avancé est KO, on peut tester le reste : il n'y est pas.
3. Il est rapide et fiable. Un test instable (*flaky*) dans le smoke fait rejeter de bonnes livraisons
   et l'équipe finit par l'ignorer. Il en sort tant qu'il n'est pas stabilisé.
4. Il vérifie aussi l'environnement. Base, services tiers et configuration : beaucoup de « bugs »
   en début de campagne sont en fait des problèmes de déploiement.

Le résultat tient en une dizaine de tests au plus. Si la liste s'allonge, c'est qu'on est en train de faire
de la non-régression.

## 3.3 Exemple ShopDemo

| # | Smoke test | Pourquoi il est là |
|---|---|---|
| 1 | Les services répondent (health checks front, API, recherche, PSP bouchonné) | Vérifie le déploiement et l'environnement |
| 2 | La page d'accueil s'affiche avec des produits | Catalogue et base de données accessibles |
| 3 | Connexion d'un client de test | Bloque tous les parcours connectés |
| 4 | Recherche d'un produit connu → résultats | Nouveau moteur (risque 6) et accès au catalogue |
| 5 | Ajout au panier et affichage du total | Prérequis du paiement |
| 6 | Paiement par carte accepté → commande créée | Parcours à risque 9, de bout en bout |
| 7 | La commande apparaît dans le back-office | Vérifie l'intégration front → OMS |

Non retenus : le paiement en 3 fois (testé en campagne, son échec n'empêche pas les autres tests),
les cas d'erreur de paiement, les pages éditoriales (risque 1).

## 3.4 Exécution

- Automatisés et lancés automatiquement après chaque déploiement sur l'environnement de recette.
- En CI, la campagne complète ne démarre que si le smoke est au vert.
- Résultat communiqué tout de suite : « livraison acceptée » ou « livraison rejetée + raison ».
- Rejouables à la main en moins de 15 minutes si l'automatisation est indisponible.

## 3.5 Maintenance

À chaque version, je relis la liste avec la grille de risques à jour : une nouvelle fonctionnalité vitale
peut entrer, un parcours devenu secondaire peut sortir. Le nombre de tests ne doit pas grossir avec le temps.

## 3.6 Mise en pratique

Voir le pipeline de [mock-payment-api](https://github.com/tazibt/mock-payment-api) : job `smoke`, puis tests d'acceptation.
