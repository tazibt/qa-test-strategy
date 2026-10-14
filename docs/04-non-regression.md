# 4. Non-régression (TNR)

La TNR vérifie que ce qui marchait avant marche toujours. Son vrai problème, c'est sa taille :
une TNR qui prend trois jours n'est plus jouée en entier.

## 4.1 Prioriser

Chaque cas de test du patrimoine reçoit une priorité, tirée de la grille de risques ([voir 1](01-strategie-de-test.md)) :

| Priorité | Contenu | Quand on la joue |
|---|---|---|
| P1 | Parcours critiques (risque 6 à 9) | À chaque livraison, automatisée en CI |
| P2 | Fonctionnalités importantes, règles de gestion principales | Avant chaque mise en production |
| P3 | Cas secondaires, écrans peu utilisés | Par rotation, ou si l'analyse d'impact les désigne |

## 4.2 Analyse d'impact

Avant chaque version, je regarde avec les développeurs ce qui a réellement été modifié : composants,
tables, APIs, configuration. On ajoute alors à la campagne les P2 / P3 qui touchent ces zones.

> ShopDemo 2.4 : la migration du moteur de recherche touche aussi les suggestions de la page d'accueil
> et le tri des catégories. Les tests P3 de ces deux écrans sont ajoutés à la campagne, ce qu'une TNR
> « par défaut » n'aurait pas fait.

## 4.3 Manuel ou automatisé ?

J'automatise un test quand il coche la plupart de ces cases :

- il sera rejoué souvent (P1, P2) ;
- le comportement est stable (pas une fonctionnalité qui change à chaque sprint) ;
- le résultat attendu est vérifiable par une machine ;
- les données peuvent être préparées automatiquement.

Ce qui reste manuel : les fonctionnalités encore instables, les vérifications visuelles ou d'ergonomie,
et le test exploratoire, que je garde volontairement en fin de campagne sur les zones à risque.

Pour l'automatisation, je privilégie les tests API quand la règle peut y être vérifiée (plus rapides
et plus stables) et je réserve les tests d'interface (Playwright, Selenium) aux parcours de bout en bout.

## 4.4 Entretenir le patrimoine

- Un test cassé est corrigé ou supprimé, jamais laissé en échec « connu » : sinon plus personne
  ne regarde les résultats.
- Les tests instables sont mis en quarantaine, analysés, puis réintégrés ou supprimés.
- Une anomalie corrigée en production donne lieu à un nouveau test de régression.
- Revue du patrimoine à chaque version majeure : les tests qui n'ont jamais trouvé de défaut sur une
  zone qui ne bouge plus peuvent descendre en P3.

## 4.5 Indicateurs propres à la TNR

Part automatisée de la TNR P1 et P2, durée de la campagne automatisée, nombre de tests instables.
Les indicateurs de campagne et de défauts sont en [8.1](08-kpi-go-no-go.md).
