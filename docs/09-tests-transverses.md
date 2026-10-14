# 9. Tests transverses

Chaque application peut passer sa recette et le processus échouer quand même : un champ mal transcodé
entre deux systèmes, un traitement de nuit lancé dans le mauvais ordre, une évolution livrée d'un côté
et pas de l'autre. Les tests transverses suivent une donnée métier (une commande, un contrat, un dossier) de bout en bout,
à travers toutes les applications qu'elle traverse.

## 9.1 Dessiner le parcours de la donnée

Je commence par un schéma du processus, construit avec les experts métier à partir des spécifications,
des contrats d'interface et de ce qu'on observe en production.

Pour ShopDemo, une commande passe par six systèmes :

```
Site / API  ──>  PSP (paiement)  ──>  OMS (commandes)  ──>  WMS (entrepôt)  ──>  Transporteur
   │                                       │
   └──> CRM (emails client)                └──> ERP (facturation, compta)
```

Pour chaque échange, je note le mode (appel temps réel, traitement planifié, fichier), la fréquence et
les données qui doivent arriver intactes de l'autre côté : numéro de commande, montant, statut.

## 9.2 Quels scénarios transverses

Je pars des processus, pas des applications, et je garde en priorité :

- les processus qui pèsent le plus (chiffre d'affaires, obligations légales, image) ;
- les échanges modifiés par le projet et ceux qui en dépendent ;
- les chemins secondaires, peu empruntés donc peu testés : annulation, retour, refus de paiement,
  rupture constatée en entrepôt ;
- les problèmes déjà signalés en production.

| Parcours ShopDemo | Systèmes | Pourquoi |
|---|---|---|
| Commande payée, livrée, facturée | Site, PSP, OMS, WMS, transporteur, ERP | Parcours principal |
| Paiement en 3 fois | Site, PSP, OMS, ERP | Nouveau, l'échéancier doit se retrouver en comptabilité |
| Rupture en entrepôt après paiement | OMS, WMS, PSP, CRM | Remboursement partiel et email client, chemin rare |
| Retour client | Site, OMS, WMS, ERP, PSP | Avoir et remboursement, source de réclamations |

## 9.3 Décrire un scénario transverse

Un scénario transverse se joue souvent à plusieurs : chaque testeur connaît son application et vérifie son étape.
Celui qui a écrit le scénario suit le déroulé complet et consolide le résultat.

En-tête du scénario :

| Rubrique | Exemple |
|---|---|
| Référence | TT-03 – Rupture en entrepôt après paiement |
| Exigences couvertes | EX-112, EX-118 |
| Versions | Site 2.4.0, OMS 7.2, WMS 3.1, bouchon PSP 1.0, CRM 5.0 |
| Prérequis | Article `REF-2001` à 1 unité dans l'OMS et à 0 dans le WMS ; synchronisation des stocks suspendue |
| Données | Client `test.e2e03@example.com`, carte de test « acceptée » |

Puis une ligne par étape, avec le contrôle à faire **dans le système concerné**, pas seulement sur
l'écran final :

| # | Étape | Système | Vérification | Qui |
|---|---|---|---|---|
| 1 | Commande et paiement de `REF-2001` | Site, PSP | Commande créée, paiement autorisé, numéro noté | Testeur front |
| 2 | Réception de la commande | OMS | Statut « À préparer », même montant que sur le site | Testeur OMS |
| 3 | Préparation | WMS | Rupture signalée, ligne « Non préparable » | Testeur logistique |
| 4 | Traitement de retour WMS → OMS | OMS | Statut « Annulée partiellement » | Testeur OMS |
| 5 | Remboursement | PSP | Montant de la ligne remboursé, pas toute la commande | Testeur paiement |
| 6 | Email client | CRM | Un seul email « article indisponible » | Testeur CRM |
| 7 | Comptabilité | ERP | Avoir créé, du même montant que le remboursement | Testeur finance |

## 9.4 Avant de lancer la campagne

- toutes les applications aux versions prévues, sur le même environnement : un système en retard de
  version génère de fausses anomalies ;
- le planning des traitements planifiés connu, ou la possibilité de les déclencher à la main ;
- les mêmes données dans tous les systèmes (même client, même article) ;
- un créneau commun avec les testeurs de chaque application et un canal de discussion ouvert pendant
  la campagne, pour enchaîner les étapes sans attendre.

## 9.5 Quand un scénario échoue

Les vérifications par système montrent à quelle étape la donnée a divergé. L'anomalie part alors
directement à l'équipe concernée, pas à celle qui gère le dernier écran consulté.

Ce qui ne relève pas du projet (traitement trop lent, écart déjà présent en production, évolution
manquante dans une autre version) est noté à part et transmis aux équipes concernées.
