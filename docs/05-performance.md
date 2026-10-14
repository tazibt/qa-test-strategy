# 5. Tests de performance

L'outil (JMeter, Gatling) ne représente qu'une petite partie du travail. L'essentiel se joue avant le tir
(comprendre et modéliser la charge) et après (analyser et trouver le goulot).

## 5.1 Recueillir le besoin

Sans objectif chiffré, un test de performance ne peut ni réussir ni échouer. Je cherche à obtenir :

- les volumes : visites par jour, commandes à l'heure de pointe, pic attendu (soldes, campagne) ;
- la répartition des parcours : part de visiteurs qui cherchent, ajoutent au panier, paient ;
- les exigences : temps de réponse acceptables par transaction (en percentile, pas en moyenne),
  taux d'erreur maximum ;
- l'environnement : proportion par rapport à la production, services tiers bouchonnés ou non.

Sources : logs et outils d'analyse de la production, APM (Dynatrace...), métier pour les prévisions.

> ShopDemo : 1 200 commandes à l'heure de pointe en période de soldes, soit 0,33 commande par seconde.
> Ratio observé : 1 commande pour 25 visites → 8,33 visites par seconde. Exigence : p95 < 2 s
> sur la recherche et le panier, p95 < 3 s sur le paiement, moins de 1 % d'erreurs.

## 5.2 Modéliser la charge : la loi de Little

**N = X × (R + Z)**

- N : nombre d'utilisateurs simultanés
- X : débit, dans la même unité que le parcours mesuré par R + Z (ici des visites par seconde)
- R : temps de réponse cumulé du parcours
- Z : temps de réflexion (pauses de l'utilisateur)

> ShopDemo : 8,33 visites / s, une visite dure en moyenne 3 min (180 s, pauses comprises)
> → N = 8,33 × 180 ≈ **1 500 utilisateurs simultanés** au pic (en dimensionnement, on arrondit vers le haut).

C'est cette valeur qui dimensionne le test :

- en modèle fermé (Thread Group JMeter, `injectClosed` Gatling), on injecte ~1 500 utilisateurs en
  respectant les temps de réflexion ;
- en modèle ouvert (plugin JMeter Arrivals Thread Group, `injectOpen` Gatling), on injecte
  8,33 arrivées par seconde et
  la concurrence se construit d'elle-même. C'est le modèle le plus fidèle pour un site public :
  si le site ralentit, les visiteurs continuent d'arriver.

Une erreur fréquente est de confondre le nombre total de sessions sur la durée du test et le nombre
de sessions simultanées. 10 000 sessions en une heure, ce n'est pas 10 000 utilisateurs en même temps.

## 5.3 Les types de tir

| Tir | Objectif | Profil ShopDemo |
|---|---|---|
| Validation | Vérifier les scripts et l'environnement | 1 utilisateur, quelques itérations |
| Charge | Tenir la charge de pointe attendue | Paliers jusqu'à 100 % du pic, plateau de 1 h |
| Stress | Trouver la limite et le comportement au-delà | Paliers jusqu'à la rupture (150 %, 200 %…) |
| Pic | Encaisser un afflux brutal | De 100 % à 300 % du pic en quelques secondes (lancement des soldes) |
| Endurance | Détecter fuites mémoire et dérives | Charge nominale pendant 8 à 12 h |

## 5.4 Préparer les scripts

- corrélation de toutes les valeurs dynamiques (jetons de session, identifiants de panier, CSRF) ;
- jeux de données suffisants pour toute la durée du tir, sans réutilisation qui fausserait les caches
  ([voir 6](06-donnees-de-test.md)) ;
- temps de réflexion réalistes ;
- checks sur le contenu des réponses, pas seulement sur le code HTTP (une page d'erreur peut
  renvoyer 200) ;
- paramètres externalisés (URL, charge, durée) pour rejouer le même script en validation, charge
  ou endurance ;
- tiers bouchonnés si besoin, avec une latence réaliste pour ne pas fausser les temps de bout en bout.

## 5.5 Pendant le tir

- monitoring de l'application (CPU, mémoire, GC, pools de connexions, base de données) et de l'APM ;
- monitoring des injecteurs : un injecteur saturé donne de faux mauvais résultats ;
- arrêt immédiat si le taux d'erreur explose ou si l'environnement est mis en danger.

## 5.6 Analyser

Dans cet ordre :

1. les erreurs : nature et moment d'apparition ;
2. les temps de réponse par transaction en p95 / p99, pas en moyenne ;
3. le comportement dans le temps : à partir de quel palier ça se dégrade, le débit plafonne-t-il
   alors que la charge augmente ?
4. la corrélation avec le monitoring pour localiser le goulot (application, base, réseau, tiers) ;
5. la comparaison avec le tir de référence de la version précédente.

Le rapport conclut par une réponse claire (objectifs atteints ou non), les goulots identifiés
et des recommandations.

## 5.7 Mise en pratique

Voir [gatling-performance-tests](https://github.com/tazibt/gatling-performance-tests).
