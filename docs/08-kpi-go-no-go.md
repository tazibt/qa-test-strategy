# 8. KPI qualité et Go / No Go

Au Go / No Go, la QA ne décide pas : elle donne une vision argumentée du risque pour que le projet
décide en connaissance de cause.

## 8.1 Les indicateurs que je suis

| Indicateur | Ce qu'il dit |
|---|---|
| Avancement de la campagne (exécutés / prévus) | Où en est-on par rapport au plan |
| Taux de réussite (OK / exécutés) | Qualité perçue de la version |
| Couverture des exigences, en priorité celles à risque élevé | A-t-on testé ce qui compte |
| Défauts ouverts par sévérité | Le risque restant |
| Tendance ouverts / fermés | La version se stabilise-t-elle ? |
| Taux de réouverture | Qualité des corrections |
| Résultats de performance vs objectifs | La version tiendra-t-elle la charge ? |
| Défauts échappés en production (après coup) | Efficacité de la démarche de test |

« 95 % de réussite » peut cacher 5 % d'échecs tous concentrés sur le paiement : je présente donc
les indicateurs par niveau de risque.

## 8.2 Le rapport de synthèse

Une page, lisible en deux minutes :

1. Recommandation en tête : Go, Go avec réserves, ou No Go.
2. Périmètre testé et non testé (avec la raison).
3. Indicateurs clés par niveau de risque.
4. Défauts ouverts bloquants et majeurs, avec leurs contournements.
5. Risques résiduels et actions proposées (correctif rapide, surveillance renforcée, désactivation
   d'une fonctionnalité).

## 8.3 La grille de décision

| | Go | Go avec réserves | No Go |
|---|---|---|---|
| Défauts bloquants | 0 | 0 | ≥ 1 |
| Défauts majeurs | 0 | Contournement accepté par le métier, correctif planifié | Sans contournement sur un parcours critique |
| Couverture des risques élevés | 100 % | ≥ 95 %, écarts justifiés | < 95 % ou parcours critique non testé |
| Performance | Objectifs atteints | Écart léger, accepté et sous surveillance | Objectifs non atteints au pic attendu |
| Smoke en préproduction | OK | OK | KO |

Les seuils sont fixés au début du projet avec le PO, dans les critères de sortie de la stratégie
([voir 1](01-strategie-de-test.md)), pas le jour de la décision.

## 8.4 Exemple ShopDemo 2.4

> **Recommandation : Go avec réserves.**
>
> - 100 % des tests à risque élevé exécutés, hors un cas bloqué par l'environnement (voir risque résiduel).
>   97 % de réussite, les échecs restants sont des anomalies mineures. Tous les tests exécutés sur le paiement sont OK.
> - 0 bloquant. 1 majeur ouvert : le tri par prix de la nouvelle recherche ignore les promotions.
>   Contournement : tri par pertinence par défaut. Correctif prévu en 2.4.1 sous 10 jours, accepté par le PO.
> - Performance : objectifs atteints à 100 % du pic (p95 paiement 1,8 s pour 3 s exigées).
>   À 150 %, la recherche dépasse 2 s : surveillance renforcée pendant les soldes.
> - Risque résiduel : paiement en 3 fois non testé avec la carte d'un prestataire secondaire
>   (environnement indisponible) → vérification en production sur une commande test le jour J.

## 8.5 Après la mise en production

- smoke tests rejoués en production (sur des comptes dédiés) ;
- surveillance renforcée des premiers jours : erreurs, temps de réponse, taux de conversion (APM, logs) ;
- les défauts échappés sont analysés : pourquoi la campagne ne les a pas trouvés, et quel test ajouter.
