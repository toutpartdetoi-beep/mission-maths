# Contrôle qualité — Math Quest

Ce document récapitule les vérifications effectuées sur le contenu et le
code de `index.html` avant livraison.

## 1. Contenu mathématique

- **Calculs et exemples relus à la main.** Chaque exemple résolu (champ
  `example` de chaque notion) et chaque exemple de correction (champ
  `correction.example` de chaque exercice) a été recalculé pour
  confirmer son exactitude. Exemples vérifiés : priorités opératoires
  (5+3×4=17, 8+2×(5-3)=12, 30-4×(6-4)=22...), nombres relatifs
  ((-5)+3=-2, (-6)×(-2)=12, (-9)×3=-27...), fractions (8/12=2/3,
  1/4+1/2=3/4, 2/3-1/6=1/2...), proportionnalité et pourcentages
  (200×6/4=300, 90×2,5=225, 45×0,70=31,5...), calcul littéral et
  distributivité (2×3+5=11, 4×(x-3)=4x-12 vérifié pour x=5, 6x+9=3(2x+3)
  vérifié pour x=2...), équations (3x+4=19 → x=5, vérifié par
  substitution), grandeurs et conversions (2,5km=2500m, 3h15min=195min,
  50×30=1500m²=15 000 000cm²...), aires et volumes (5×3×4=60cm³,
  12×7=84m²...), théorème de Pythagore (3²+4²=25→BC=5 ; 5²+12²=169→
  BC=13 ; contre-exemple 6/7/9 : 9²=81 ≠ 6²+7²=85, donc non rectangle),
  statistiques (moyenne de 12,14,8,16,10 = 60÷5=12), probabilités
  (2/8=1/4=25%), fonctions (f(x)=0,10x+15 : f(50)=20€, antécédent de
  25€ = 100min), algorithmique (déroulé pas à pas des boucles, ex. x=1
  répété ×2 quatre fois → 16), et méthode LIRE (45×0,80-5=31€).

- **Une seule réponse correcte par QCM.** Pour chaque exercice de type
  `mcq`, l'indice `answer` a été vérifié comme correspondant à l'unique
  bonne réponse parmi les choix proposés, et l'absence de choix
  dupliqués ou ambigus a été confirmée par un contrôle automatisé (voir
  section 4).

- **Correction complète pour les exercices QCM, à trous et ouverts.**
  Chaque exercice de type `mcq`, `fill` et `open` comporte un bloc
  `correction` avec :
  - `rule` (la règle ou méthode générale),
  - `why` (l'explication du pourquoi, affichée en cas d'erreur),
  - `example` (un exemple chiffré supplémentaire),
  - `errorType` (une catégorie d'erreur, utilisée dans le tableau de
    bord et le mode parent pour repérer les schémas d'erreurs
    récurrents).
  Les exercices de type `order` (remise en ordre des étapes) utilisent
  volontairement un mécanisme différent : un système d'indice progressif
  intégré (`showHint`) qui pointe vers la première étape mal placée,
  plutôt qu'un bloc de correction figé — ce choix pédagogique a été
  vérifié comme cohérent avec le fonctionnement du moteur de session.

- **4 niveaux par notion, 2 exercices par niveau.** Un contrôle
  automatisé (voir section 4) confirme que les 18 notions possèdent
  chacune exactement 4 niveaux, et que chaque niveau contient exactement
  2 exercices, soit 144 exercices au total.

## 2. Cohérence pédagogique du parcours

- **Prérequis valides.** Chaque identifiant de prérequis (`prereqs`)
  référence bien une notion existante du tableau `NOTIONS` ; aucun
  prérequis orphelin n'a été détecté.
- **Progression logique.** L'ordre des notions suit une progression
  standard du programme de cycle 4 (nombres → calcul littéral →
  grandeurs → géométrie → statistiques/probabilités → fonctions →
  algorithmique → résolution de problèmes transversale), avec des
  prérequis qui respectent cette logique (ex. les équations nécessitent
  la distributivité, les fonctions nécessitent les équations).
- **Seuil de validation.** Le seuil de 70% de bonnes réponses pour
  valider un niveau (et débloquer le suivant, ou débloquer la notion
  suivante au niveau 1) a été vérifié dans le code (`endSession`).

## 3. Vérifications techniques (JavaScript)

- **Syntaxe.** Le bloc `<script>` a été extrait du fichier HTML et
  vérifié avec `node --check` : aucune erreur de syntaxe détectée.
- **Exécution sans erreur.** Le script a été exécuté dans un
  environnement Node.js simulant les API du navigateur (DOM minimal,
  `localStorage`) : aucune erreur d'exécution au chargement (génération
  des notions, construction de l'index d'exercices, rendu de l'écran
  d'accueil).
- **Générateurs d'automatismes.** Les 7 générateurs de calcul mental
  (tables, doubles/moitiés, ×÷10-100-1000, fractions usuelles,
  pourcentages usuels, carrés, signes des produits) ont été exécutés 200
  fois chacun : aucune réponse `NaN`, `undefined` ou incohérente n'a été
  produite. Chaque formule est déterministe et ne peut générer qu'une
  réponse exacte et non ambiguë.
- **Pas de dépendance externe critique.** Le site ne dépend d'aucune
  bibliothèque JavaScript externe ; la seule ressource externe est le
  chargement des polices Google Fonts (dégradation propre vers une
  police système si hors ligne).

## 4. Contrôle automatisé exécuté

Un script de contrôle (Node.js, environnement `vm` avec DOM simulé) a
été utilisé pour valider automatiquement l'intégrité des données :

- Chargement du fichier `index.html`, extraction et exécution du script.
- Pour chacune des 18 notions et chacun des 4 niveaux : vérification de
  la présence d'exactement 2 exercices.
- Pour chaque exercice `mcq` : vérification que l'indice de réponse
  (`answer`) est dans la plage des choix proposés, et qu'il n'y a pas de
  choix dupliqués.
- Pour chaque exercice `fill` : vérification qu'au moins une réponse
  acceptée (`accepted`) est renseignée.
- Pour chaque exercice `order` : vérification d'au moins 2 étapes à
  remettre en ordre.
- Pour chaque exercice `open` : vérification qu'au moins un mot-clé
  attendu (`requiredAny`) est renseigné.
- Vérification que chaque `prereqs` référence un identifiant de notion
  existant.
- Exécution répétée des 7 générateurs d'automatismes pour détecter toute
  valeur incohérente.

**Résultat :** 18 notions, 144 exercices, structure conforme sur
l'ensemble du contenu ; aucune anomalie bloquante détectée.

## 5. Sauvegarde et persistance

- La fonction `save()` (écriture dans `localStorage`) et `load()`
  (lecture avec repli sur un état par défaut en cas d'absence ou de
  corruption des données) ont été relues pour confirmer la présence
  d'un `try/catch` protégeant contre les erreurs de stockage (quota
  dépassé, navigation privée, etc.), avec un comportement de repli sûr
  (état par défaut) dans ces cas.
- Le mode parent permet une réinitialisation complète et volontaire de
  la progression, avec demande de confirmation explicite avant toute
  suppression de données.

## 6. Limites connues (non-bloquantes)

- La sauvegarde étant locale au navigateur, elle ne se synchronise pas
  entre appareils (voir LISEZ_MOI.txt).
- Les exercices `fill` utilisent une comparaison textuelle normalisée
  (espaces retirés, point converti en virgule) : une réponse
  mathématiquement équivalente mais très différemment formulée (ex.
  "0,5" au lieu de "1/2" quand seule la fraction est acceptée) pourrait
  être signalée comme incorrecte. Le lien "🚩 Signaler cette question"
  permet de faire remonter ce type de cas si Louise le rencontre.
