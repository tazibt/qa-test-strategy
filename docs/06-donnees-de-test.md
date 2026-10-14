# 6. Données de test

Une bonne partie des anomalies « non reproductibles » et des campagnes bloquées vient des données :
compte déjà utilisé, produit en rupture, commande dans un état inattendu. Les données se préparent
comme le reste de la campagne.

## 6.1 Pas de données de production brutes

Copier la production en recette pose un problème RGPD et de confidentialité (noms, emails,
adresses, données bancaires). Trois options, par ordre de préférence :

1. Données générées : fictives dès le départ, aucun risque.
2. Données anonymisées : copie de production dont les données personnelles sont remplacées,
   utile quand on a besoin de volumes et de cas réels (historiques, cas complexes).
3. Données de production : seulement sur un environnement au même niveau de sécurité que la
   production, avec accord explicite.

Règles simples pour les données générées : emails sur un domaine réservé (`example.com`), numéros de
téléphone dans des plages réservées à la fiction, aucun vrai numéro de carte (cartes de test du PSP).

## 6.2 Des données isolées par usage

| Usage | Principe |
|---|---|
| Tests automatisés | Chaque test crée ses données ou utilise un jeu qui lui est réservé ; il ne dépend pas de l'ordre d'exécution |
| Recette manuelle | Jeux nommés par testeur ou par thème pour éviter les collisions |
| Performance | Assez de comptes pour les utilisateurs simultanés ; si le fichier est rejoué en boucle, vérifier que la réutilisation ne fausse ni les caches ni les règles métier (un panier actif par compte...) |
| Démo / formation | Jeu stable, jamais utilisé pour les tests |

## 6.3 Couvrir les cas, pas seulement le nominal

Pour chaque règle de la stratégie, le jeu de données doit contenir les cas qui la mettent à l'épreuve.
Pour ShopDemo :

- clients : nouveau, fidèle, compte bloqué, adresse à l'étranger ;
- produits : en stock, stock à 1, rupture, prix à 0,01 €, produit lourd (frais de port) ;
- paniers aux bornes du paiement en 3 fois (99,99 €, 100 €, 2 000 €, 2 000,01 €) ;
- cartes de test du prestataire : acceptée, refusée, expirée, 3-D Secure.

## 6.4 Reproductibilité

Un jeu de données doit pouvoir être recréé à l'identique : scripts SQL ou d'API versionnés,
générateurs avec une graine fixe. Une anomalie trouvée avec un jeu reproductible se rejoue en
quelques minutes ; sans, on perd des heures à recréer le contexte.

## 6.5 Mise en pratique

[Generateur-Data](https://github.com/tazibt/Generateur-Data) génère des utilisateurs fictifs
(emails `example.com`, téléphones en plage de fiction) avec une graine optionnelle pour obtenir le même
fichier à chaque exécution. Il alimente les simulations de
[gatling-performance-tests](https://github.com/tazibt/gatling-performance-tests).
