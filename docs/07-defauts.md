# 7. Gestion des défauts

Un rapport d'anomalie est lu par quelqu'un qui n'était pas là au moment du test. S'il doit poser une
question avant de pouvoir agir, le rapport est incomplet.

## 7.1 Le contenu d'un rapport

| Champ | Exemple ShopDemo |
|---|---|
| Titre | Paiement en 3 fois proposé pour un panier de 99,99 € |
| Environnement / version | Recette – ShopDemo 2.4.0-rc2 |
| Prérequis / données | Client `test.recette03@example.com`, produit `REF-1042` à 89,99 € + 10 € de port |
| Étapes | 1. Ajouter REF-1042 au panier 2. Aller au paiement 3. Observer les moyens de paiement |
| Résultat obtenu | Le paiement en 3 fois est proposé |
| Résultat attendu | Non proposé : seuil à 100 € frais de port inclus (critère d'acceptation, scénario « Éligibilité ») |
| Preuves | Capture, requête / réponse API, logs, horodatage |
| Sévérité / priorité | Majeure / Haute (voir ci-dessous) |
| Lien | Story et cas de test concernés |

Le résultat attendu cite sa référence (critère d'acceptation, spécification, règle de gestion),
sinon l'anomalie risque d'être requalifiée en évolution.

## 7.2 Sévérité et priorité

La sévérité mesure l'impact technique ou fonctionnel, la priorité l'urgence de correction.
Elles ne vont pas toujours ensemble.

| Sévérité | Définition |
|---|---|
| Bloquante | Empêche un parcours critique ou bloque la campagne, sans contournement |
| Majeure | Fonction importante incorrecte, contournement possible mais pénalisant |
| Mineure | Fonction secondaire incorrecte, impact limité |
| Cosmétique | Libellé, alignement, sans impact fonctionnel |

La priorité se note Haute, Moyenne ou Basse.

> Une faute d'orthographe dans le nom de la marque sur la page d'accueil : sévérité cosmétique,
> mais priorité haute. Un plantage dans un écran d'administration utilisé une fois par an :
> sévérité majeure, priorité basse.

## 7.3 Le cycle de vie

```
Nouveau → Qualifié → En correction → Livré → Test de confirmation → Clos
              ↘ Rejeté (doublon, non reproductible, comportement attendu)       ↘ Réouvert
```

- Qualification : je reproduis, je vérifie que ce n'est ni un doublon ni un problème de données ou
  d'environnement, puis je fixe sévérité et priorité.
- Test de confirmation : on rejoue le cas d'origine sur la version corrigée. On complète par un
  test de régression ciblé autour (même écran, même règle) pour détecter un effet de bord de la correction.
- Un rejet est toujours argumenté ; un défaut rejeté « non reproductible » plusieurs fois mérite une
  analyse plus poussée (données, concurrence, environnement).

## 7.4 Le suivi

En comité de suivi (ou au daily), la liste des défauts est triée par priorité : bloquants et majeurs
ouverts, leur âge, défauts réouverts. Les indicateurs de tendance sont en [8.1](08-kpi-go-no-go.md).
