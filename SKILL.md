---
name: systematic-debugging
description: Démarche méthodique de débogage - reproduire, isoler, formuler des hypothèses, les tester une par une, confirmer la cause racine avant de proposer un correctif. À utiliser dès que l'utilisateur signale un bug, une erreur, une stack trace, un crash, un comportement inattendu, "ça ne marche pas", "pourquoi ça plante", "it works on my machine", un test qui échoue ou un résultat incorrect, quel que soit le langage. Use for any debugging, error, traceback or unexpected-behavior request.
---

# Débogage méthodique

Le piège principal : proposer un correctif *plausible* à partir du message d'erreur, sans avoir confirmé la cause. Un correctif plausible mais faux fait perdre du temps et peut masquer le vrai problème. Cette démarche impose de prouver la cause avant de corriger.

## Boucle de débogage

### 1. Cadrer le symptôme
Établir précisément : comportement **attendu**, comportement **observé**, message d'erreur complet (stack trace entière, pas seulement la dernière ligne), environnement (OS, version du langage/framework), et ce qui a changé récemment (code, dépendances, configuration, données). Si une information décisive manque, la demander (une question à la fois) plutôt que deviner.

### 2. Reproduire
Chercher le plus petit cas qui déclenche le bug. Un bug qu'on ne sait pas reproduire ne peut pas être confirmé corrigé. S'il est intermittent, chercher ce qui varie (timing, données, concurrence, état partagé).

### 3. Isoler
Réduire la zone suspecte : bissection (commenter la moitié du code, `git bisect` pour trouver le commit fautif), entrées minimales, désactivation de composants. Remonter la chaîne depuis le point où l'erreur *apparaît* vers le point où l'état devient *faux*. Les deux sont souvent distants.

### 4. Formuler des hypothèses classées
Lister 2 à 4 causes possibles, de la plus probable à la moins probable, avec pour chacune **le test qui la confirmerait ou l'éliminerait**. Exemple :
- H1 : la variable est `None` à cause d'un appel asynchrone non attendu → ajouter un log avant l'usage
- H2 : version de la bibliothèque incompatible → comparer avec le lockfile

### 5. Tester une hypothèse à la fois
Changer une seule chose par essai, observer, conclure. Modifier plusieurs choses à la fois rend impossible de savoir ce qui a agi. Utiliser logs ciblés, assertions, débogueur, ou exécution du code (outil bash disponible) pour obtenir des faits plutôt que des suppositions.

### 6. Confirmer la cause racine
Pouvoir énoncer : "Le bug se produit parce que X, ce qui entraîne Y." Si l'explication ne couvre pas tous les symptômes observés, la cause n'est pas encore trouvée. Se méfier des correctifs qui "font disparaître" l'erreur sans l'expliquer.

### 7. Corriger et vérifier
Appliquer le correctif minimal qui traite la cause (pas le symptôme), relancer le cas de reproduction, puis ajouter un test qui aurait détecté le bug. Vérifier qu'aucun autre comportement ne régresse.

## Règles de conduite

- Si le code peut être exécuté ici, l'exécuter pour reproduire plutôt que raisonner dans le vide.
- Distinguer clairement dans la réponse ce qui est **confirmé** et ce qui est **hypothèse**.
- Si la cause n'est pas confirmée, le dire et proposer l'étape de diagnostic suivante plutôt qu'un correctif présenté comme certain.
- Après trois hypothèses infirmées, reculer d'un pas : revérifier les hypothèses de départ (la bonne version du code s'exécute-t-elle ? le bon fichier de config est-il lu ? le cache est-il à jour ?).

## Format de réponse

```
## Ce que je comprends
[symptôme, contexte]

## Hypothèses (par probabilité)
1. ... → test : ...
2. ... → test : ...

## Diagnostic
[ce qui est confirmé, avec les preuves]

## Correctif
[code + explication de la cause]

## Pour éviter la récidive
[test, garde-fou, log]
```
