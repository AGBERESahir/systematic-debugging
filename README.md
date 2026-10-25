# systematic-debugging

Fait suivre à Claude une **démarche de débogage méthodique** : prouver la cause avant de corriger. Le piège principal est de proposer un correctif *plausible* à partir du message d'erreur sans avoir confirmé la cause ; il fait perdre du temps et peut masquer le vrai problème.

Skill Claude : un dossier d'instructions que Claude charge automatiquement quand ta demande correspond. Tu n'as rien à invoquer : décris ton problème normalement.

## Quand il se déclenche

- tu signales un bug, une erreur, une stack trace, un crash ou un comportement inattendu ;
- tu écris « ça ne marche pas », « pourquoi ça plante », « it works on my machine » ;
- un test échoue ou un résultat est incorrect, quel que soit le langage.

Exemples de messages :

- « J'ai cette erreur TypeError: cannot read properties of undefined, voici la stack trace. »
- « Mon test passe en local mais échoue dans la CI. »
- « Le total de ma facture est faux de temps en temps, je ne comprends pas pourquoi. »

## Ce que le skill fait faire à Claude

La boucle de débogage en sept étapes :

1. **Cadrer le symptôme** : comportement attendu, observé, message d'erreur complet (la stack trace entière), environnement, ce qui a changé récemment. Une seule question à la fois si une information décisive manque.
2. **Reproduire** avec le plus petit cas possible ; pour un bug intermittent, chercher ce qui varie (timing, données, état partagé).
3. **Isoler** : bissection, `git bisect`, entrées minimales, composants désactivés ; remonter du point où l'erreur *apparaît* vers celui où l'état devient *faux*.
4. **Formuler 2 à 4 hypothèses classées**, chacune avec le test qui la confirmerait ou l'éliminerait.
5. **Tester une hypothèse à la fois** : un seul changement par essai, avec des faits (logs ciblés, débogueur, exécution du code) plutôt que des suppositions.
6. **Confirmer la cause racine** : pouvoir dire « le bug se produit parce que X, ce qui entraîne Y ». Si l'explication ne couvre pas tous les symptômes, la cause n'est pas trouvée.
7. **Corriger et vérifier** : correctif minimal sur la cause, relance du cas de reproduction, puis un test qui aurait détecté le bug.

Règles de conduite : distinguer ce qui est **confirmé** de ce qui est **hypothèse**, et après trois hypothèses infirmées, reculer d'un pas pour revérifier les bases (bonne version du code exécutée, bon fichier de config lu, cache à jour).

## Format de la réponse

```
## Ce que je comprends
## Hypothèses (par probabilité)
   1. ... → test : ...
## Diagnostic (ce qui est confirmé, avec les preuves)
## Correctif (code + explication de la cause)
## Pour éviter la récidive (test, garde-fou, log)
```

Si la cause n'est pas confirmée, Claude le dit et propose l'étape de diagnostic suivante plutôt qu'un correctif présenté comme certain.

## Installation

**Claude Code**

```bash
git clone https://github.com/AGBERESahir/systematic-debugging.git ~/.claude/skills/systematic-debugging
```

Le dossier doit s'appeler `systematic-debugging` et contenir `SKILL.md` à sa racine. Relance Claude Code si une session était ouverte.

**Claude.ai**

Zippe le dossier `systematic-debugging` (avec `SKILL.md` dedans), puis ajoute l'archive dans **Paramètres > Skills**.

## Vérifier qu'il fonctionne

Envoie l'un des messages d'exemple ci-dessus. La réponse doit suivre la structure décrite plus haut. Si Claude répond de façon libre, vérifie que le skill est activé dans tes paramètres et que `SKILL.md` n'est pas dans un sous-dossier.

## Limites

- Plus tu donnes d'éléments (stack trace complète, version, cas minimal), plus le diagnostic est rapide : sans cela, Claude te demande des précisions avant de conclure.
- Claude ne peut exécuter ton code que si l'environnement le permet ; sinon il te donne les commandes ou les logs à lancer et attend le résultat.
- Un bug qu'on ne sait pas reproduire ne peut pas être confirmé corrigé : le skill le dit plutôt que de promettre une solution.
- Le skill ne couvre pas la sécurité du code (voir ci-dessous).

## Voir aussi

- [infra-deploy-diagnosis](https://github.com/AGBERESahir/infra-deploy-diagnosis) : quand le bug vient de l'environnement ou du déploiement.
- [safe-refactoring-migration](https://github.com/AGBERESahir/safe-refactoring-migration) : après le correctif, pour nettoyer le code en sécurité.
- [code-security-review](https://github.com/AGBERESahir/code-security-review) : pour la revue de sécurité.

## Fichiers

| Fichier | Rôle |
|---|---|
| `SKILL.md` | Les instructions : nom, description de déclenchement, démarche et format de sortie. |
| `README.md` | Cette page, pour les humains (Claude ne la lit pas). |

## Licence

Aucune licence n'est encore indiquée dans ce dépôt ; ajoute un fichier `LICENSE` avant de le réutiliser ou de le partager.
