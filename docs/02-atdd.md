# 2. ATDD – Acceptance Test-Driven Development

L'ATDD consiste à **écrire les tests d'acceptation avec le métier avant le développement**.
Le test devient la spécification : si tout le monde est d'accord sur les exemples, il reste peu de place
pour l'interprétation.

Ce qui change concrètement pour l'équipe : les ambiguïtés sont levées en atelier, au début du sprint,
au lieu d'être découvertes en recette sous forme d'anomalies.

## 2.1 Le cycle

```
  Atelier 3 amigos  →  Exemples en Gherkin  →  Développement  →  Automatisation  →  Démo / validation
  (PO, dev, QA)        (critères d'acceptation)                    (tests au vert)      (le PO rejoue)
```

1. Atelier « 3 amigos » (30 min par story) : le PO explique la règle, le dev pose les questions
   techniques, le QA cherche les cas limites et les cas d'erreur.
2. Rédaction des exemples en Given / When / Then, dans le langage du métier.
3. Validation des scénarios par le PO avant que la story entre en développement.
4. Développement, puis automatisation des scénarios avec Cucumber ; les scénarios sont stockés
   dans Xray et les résultats y remontent par import du rapport Cucumber JSON.
5. La story est terminée quand ses scénarios d'acceptation passent.

## 2.2 Exemple ShopDemo : paiement en 3 fois

Règle exprimée par le PO : *« Le paiement en 3 fois est proposé pour les paniers entre 100 € et 2 000 €. »*

L'atelier fait apparaître les questions que la phrase ne règle pas :

- 100 € et 2 000 € sont-ils inclus ?
- le montant s'entend-il avec ou sans frais de port ?
- que se passe-t-il si un code promo fait passer le panier sous 100 € ?
- comment arrondir quand le montant n'est pas divisible par 3 ?

Les réponses deviennent des exemples :

```gherkin
# language: fr
Fonctionnalité: Paiement en 3 fois

  Règle: proposé pour un total TTC, frais de port inclus, entre 100 € et 2 000 € (bornes incluses)

  Plan du scénario: Éligibilité selon le montant du panier
    Étant donné un panier d'un total de <total> € frais de port inclus
    Quand j'arrive à l'étape de paiement
    Alors le paiement en 3 fois est "<proposé>"

    Exemples:
      | total    | proposé     |
      | 99,99    | non proposé |
      | 100,00   | proposé     |
      | 2000,00  | proposé     |
      | 2000,01  | non proposé |

  Scénario: Un code promo fait passer le panier sous le seuil
    Étant donné un panier de 110,00 € frais de port inclus
    Et le paiement en 3 fois sélectionné
    Quand j'applique un code promo de 20 €
    Alors le paiement en 3 fois n'est plus proposé
    Et un message m'invite à choisir un autre moyen de paiement

  Scénario: Le reste de la division est porté par la première échéance
    Étant donné un panier de 100,00 € frais de port inclus
    Quand je paie en 3 fois
    Alors l'échéancier est 33,34 € puis 33,33 € puis 33,33 €
```

Sans atelier, chacune de ces questions aurait été tranchée par le développeur, et découverte en recette.

## 2.3 Bonnes pratiques de rédaction

- Un scénario = une règle. S'il faut plusieurs « Quand », c'est souvent deux scénarios.
- Langage métier, pas d'interface : « je valide ma commande » plutôt que « je clique sur le bouton #submit ».
- Données explicites dans le scénario quand elles portent la règle (montants, bornes).
- Plan du scénario pour les valeurs aux limites, au lieu de copier dix fois le même scénario.
- Les scénarios vivent dans le dépôt de code ou dans l'outil de test (Xray gère le Gherkin) et sont
  versionnés avec la story.

## 2.4 Ce qui fait réussir (ou échouer) la démarche

- Le PO doit être présent aux ateliers. Si le QA écrit les scénarios seul après coup, on retombe sur
  des cas de test classiques.
- Ateliers courts, story par story. Une demi-journée de spécification fatigue tout le monde.
- Commencer par les stories à risque plutôt que de tout passer en ATDD dès le premier sprint.

## 2.5 Mise en pratique

Le dépôt [mock-payment-api](https://github.com/tazibt/mock-payment-api) applique cette démarche :
les règles d'acceptation du mock sont écrites en Gherkin (`paiement.feature`, `robustesse.feature`)
et automatisées avec Cucumber et RestAssured.
