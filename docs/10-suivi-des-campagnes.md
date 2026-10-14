# 10. Estimation, équipe et suivi des campagnes

## 10.1 Estimer la charge

J'estime à partir de la grille de risques ([voir 1](01-strategie-de-test.md)), du nombre de cas et de
charges unitaires observées sur les projets précédents :

| Activité | Calcul ShopDemo 2.4 | Charge |
|---|---|---|
| Stratégie et plan de test | Forfait | 3 j |
| Écriture des cas | 60 cas × 0,25 j + 6 scénarios transverses × 1 j | 21 j |
| Préparation des données | 2 j par campagne × 2 campagnes | 4 j |
| Exécution | (60 cas × 0,15 j + 6 transverses × 0,5 j) × 1,3 pour les rejeux | 15,6 j |
| Tests de confirmation et de régression | 30 % de l'exécution | 4,7 j |
| Suivi, reporting, bilan | 15 % des activités ci-dessus (48,3 j) | 7,2 j |
| **Total** | | **≈ 55,5 j** |

Le coefficient de rejeu et la part de confirmation viennent de l'historique. Sans historique, je les
présente comme des hypothèses et je les recale après la première campagne.

## 10.2 L'équipe pendant les campagnes

Je répartis le travail par application ou par processus plutôt que par liste de cas : chaque testeur
devient le spécialiste d'une zone, connaît ses données et ses développeurs.

- un point de 15 minutes chaque matin : où on en est, ce qui bloque, les priorités du jour ;
- les anomalies bloquantes sont signalées tout de suite, pas au point du lendemain ;
- un nouvel arrivant est accompagné sur les processus et les outils, et ses premiers cas sont relus.

Le chef de projet et moi faisons le point chaque semaine : il suit le planning et le budget, je suis
l'avancement, les anomalies et le risque.

## 10.3 Le point d'avancement hebdomadaire

Une page envoyée chaque semaine au même moment :

> **Tests ShopDemo 2.4 – semaine 41 – tendance : 🟠**
>
> | Campagne | Prévus | Exécutés | OK | KO | Bloqués |
> |---|---|---|---|---|---|
> | Fonctionnel | 60 | 52 (87 %) | 48 | 4 | 2 |
> | Transverse | 6 | 3 (50 %) | 2 | 1 | 0 |
> | Régression automatisée | 240 | 240 (100 %) | 236 | 4 | 0 |
>
> **Anomalies** : 9 ouvertes (0 bloquante, 2 majeures, 7 mineures). Cette semaine : 5 nouvelles,
> 6 corrigées, 1 réouverte.
>
> **À surveiller**
> - TT-03 (rupture en entrepôt) KO à l'étape 5 : la commande entière est remboursée au lieu de la ligne.
>   Majeure, correctif annoncé pour le 15/10.
> - 2 cas bloqués par l'indisponibilité du WMS pendant 2 jours, rattrapés lundi.
>
> **Besoin d'une décision** : décaler la 2e campagne transverse au 20/10 (dépend du correctif TT-03).
>
> **Fin prévue** : 22/10 si le correctif arrive le 15/10, sinon 3 jours de retard.

La couleur résume, les chiffres justifient. Et chaque point d'avancement dit clairement ce qu'on
attend des destinataires : sans demande explicite, les blocages restent en l'état.

## 10.4 Le bilan de fin de projet

1. Prévu contre réalisé : périmètre, charge, délais.
2. Indicateurs finaux par niveau de risque ([voir 8](08-kpi-go-no-go.md)).
3. Anomalies : nombre, répartition par application et par origine (spécification, code,
   environnement, données).
4. Anomalies découvertes en production dans les semaines suivantes.
5. Ce qu'on change pour le prochain projet, avec un responsable et une date.

> Pour ShopDemo 2.4 :
> - 30 % des anomalies transverses venaient d'écarts de version entre applications → vérification
>   automatique des versions avant chaque campagne ;
> - 2 jours par campagne pour créer les données dans trois systèmes → script de création ;
> - 4 cas de régression encore joués à la main à chaque version → à automatiser (critères en [4.3](04-non-regression.md)).

Ces actions sont reprises au lancement du projet suivant ; sinon on retrouve les mêmes constats au bilan d'après.
