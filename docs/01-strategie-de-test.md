# 1. Stratégie de test

La stratégie répond à une question simple : **avec le temps et les moyens disponibles, où met-on l'effort de test
pour réduire au maximum le risque de mise en production ?** Elle tient sur quelques pages et doit être lue
par le chef de projet, le PO et les développeurs, pas seulement par l'équipe QA.

## 1.1 Contexte et périmètre

À écrire en premier, avec le PO :

- ce qui change dans la version (fonctionnalités, migrations, dette technique) ;
- ce qui est dans le périmètre de test et ce qui n'y est pas (et pourquoi) ;
- les dépendances externes : prestataire de paiement, transporteur, ERP, APIs partenaires.

> ShopDemo – version 2.4 : nouveau tunnel de paiement en une page, ajout du paiement en 3 fois,
> migration du moteur de recherche. Hors périmètre : back-office marketing (inchangé).

## 1.2 Analyse de risques

Chaque fonctionnalité est notée sur deux axes, de 1 à 3 :

- impact si elle tombe en production (chiffre d'affaires, image, réglementaire) ;
- probabilité de défaut (code nouveau ou modifié, complexité, dépendance externe, historique de bugs).

| Fonctionnalité ShopDemo | Impact | Probabilité | Risque | Effort de test |
|---|---|---|---|---|
| Paiement (nouveau tunnel) | 3 | 3 | **9** | Fonctionnel complet, intégration PSP, performance, automatisation |
| Paiement en 3 fois | 3 | 3 | **9** | Fonctionnel complet, cas limites (montants, refus) |
| Recherche (nouveau moteur) | 3 | 2 | **6** | Fonctionnel, comparaison avant / après, performance |
| Panier | 3 | 1 | 3 | Non-régression automatisée |
| Compte client | 2 | 1 | 2 | Non-régression automatisée |
| Pages éditoriales | 1 | 1 | 1 | Exploratoire rapide |

Risque élevé = score de 6 ou 9.

Cette grille sert ensuite à tout le reste : choix des smoke tests, priorisation de la TNR, ordre d'exécution
de la campagne, et argumentaire lors du Go / No Go.

## 1.3 Niveaux et types de test

| Niveau | Qui | Ce qui est vérifié chez ShopDemo |
|---|---|---|
| Composant | Développeurs | Règles de calcul (frais de port, échéancier du paiement en 3 fois) |
| Intégration | QA + devs | Échanges avec le PSP, le moteur de recherche, l'OMS (contrats d'API, cas d'erreur) |
| Système | QA | Parcours complets sur un environnement intégré |
| Acceptation | Métier + QA | Critères d'acceptation des user stories, recette métier |

Types de test retenus selon le risque : fonctionnel, API, non-régression, performance, exploratoire,
compatibilité navigateurs / mobiles sur les parcours critiques.

## 1.4 Environnements et outillage

- environnement de recette, puis préproduction pour la répétition de mise en production ;
- recette aussi proche que possible de la production : versions, données, intégrations ;
- les tiers instables ou payants sont bouchonnés (mock du PSP) pour ne pas bloquer la campagne ;
- outillage : gestion des tests et des campagnes (Xray / Squash), anomalies (Jira), automatisation
  (Playwright / Selenium, API), performance (JMeter / Gatling), CI (Jenkins / GitLab CI / GitHub Actions).

## 1.5 Critères d'entrée et de sortie

Critères d'entrée d'une campagne :

- livraison déployée et smoke tests au vert ([voir 3](03-smoke-tests.md)) ;
- notes de livraison disponibles (contenu, anomalies corrigées) ;
- jeux de données en place ([voir 6](06-donnees-de-test.md)).

Critères de sortie (validés avec le PO au début du projet, pas à la fin) :

- 100 % des tests des fonctionnalités à risque élevé exécutés, ou écarts justifiés et acceptés par le PO ;
- aucune anomalie bloquante ou majeure ouverte sans contournement accepté ;
- objectifs de performance atteints ;
- rapport de synthèse transmis pour le Go / No Go ([voir 8](08-kpi-go-no-go.md)).

## 1.6 Ce que je fais valider

Je fais valider trois points par le projet : le périmètre (et le hors périmètre), la grille de risques,
et les critères de sortie. Les seuils de la grille Go / No Go ([voir 8](08-kpi-go-no-go.md)) en découlent.
