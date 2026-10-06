# Optimisation : PL, Glouton et Programmation Dynamique

## Tables des matières

- [Contexte](#-contexte)
- [Fonctionnalités](#-fonctionnalités)
- [Technologies utilisées](#-technologies-utilisées)
- [Installation](#-installation)
- [Le fonctionnement](#-Le-fonctionnement)


---
## Contexte

J’ai été mandaté par une ligue de basketball amateur pour automatiser la gestion de leurs tournois. L’organisation m’a expliqué que chaque saison, ceci doit résoudre plusieurs problèmes d’optimisation : qui inviter ? Quelle est la meilleure composition d’équipe ? Le système doit être rapide et trouver la meilleure solution possible.

Ma mission est de construire un système complet d’optimisation en Python en appliquant trois algorithmes: la **programmation linéaire** (PuLP), l'**algorithme glouton** et la **programmation dynamique** (récursivité avec mémoïsation).

Voici les données données par la ligue :

| Joueur | Score | Salaire ($) | Poids (kg) |
|--------|------:|------------:|-----------:|
| Alice  | 88 | 1 200 | 72 |
| Bob    | 91 | 1 800 | 85 |
| Clara  | 84 |   950 | 68 |
| David  | 93 | 2 100 | 90 |
| Emma   | 79 |   800 | 65 |
| Frank  | 87 | 2 400 | 95 |
| Grace  | 85 | 1 050 | 70 |
| Hugo   | 89 | 1 600 | 80 |

Voici les contraintes données par la ligue :

- Budget total de **8 500 $/semaine** pour les 6 joueurs
- Poids maximum de **250 kg par équipe**
- Exactement **2 équipes de 3 joueurs**
- Chaque joueur dans **une seule équipe**

Branches:
- partie1_pl.py : Programmation linéaire avec PuLP (solution optimale) 
- partie2_glouton.py : Stratégies gloutonnes et tableau comparatif avec PuLP
- partie3_recursion.py : Score cumulé récursif et Fibonacci naïf vs mémoïsé
- Graphiques-d’analyse : Graphiques d'analyse (matplotlib)

---
## Fonctionnalités

- **Sélection optimale des équipes** avec la programmation linéaire (PuLP) : forme 2 équipes de 3 joueurs qui maximisent le score total tout en respectant le budget, le poids et la répartition des joueurs.
- **Quatre stratégies gloutonnes** : meilleur score absolu, meilleur ratio score/salaire, meilleur ratio score/poids et alternance score/ratio.
- **Vérification des contraintes** : message d'erreur lorsqu'une équipe ne peut pas être complétée avec les joueurs restants.
- **Tableau comparatif** : score total, budget utilisé et écart (en points et en pourcentage) de chaque stratégie par rapport à la solution optimale.
- **Score cumulé récursif** : calcul du score des k meilleurs joueurs avec affichage de chaque étape.
- **Fibonacci des scores** : comparaison entre la version naïve et la version mémoïsée, avec mesure du temps d'exécution.
- **Graphiques d'analyse** : comparaison des algorithmes, utilisation du budget et du poids par équipe, profil des joueurs sélectionnés.


---
## Technologies utilisées

| Technologie | Utilisation |
|-------------|-------------|
| **Python** | Langue du projet |
| **PuLP** | Modélisation et résolution du problème de programmation linéaire |
| **Matplotlib** | Création des graphiques d'analyse |
| **NumPy** | Positionnement des barres groupées (`np.arange`) dans les graphiques |
| **time** (bibliothèque standard) | Mesure du temps d'exécution avec `time.perf_counter()` |
| **Git / GitHub** | Versionnement du code|

---
## Installations

```bash
git clone https://github.com/BarbaraEstimable/AI_TP2.git
cd AI_TP2
pip install pulp matplotlib numpy
```
---
## Le fonctionnement

### Partie 1 — Programmation linéaire (PuLP)
 
**Variables de décision :** $x_{i,e} \in \{0, 1\}$ vaut 1 si le joueur $i$ est sélectionné dans l'équipe $e \in \{A, B\}$.
 
**Fonction objective :**
 
$$\max Z = \sum_i score_i \cdot (x_{i,A} + x_{i,B})$$
 
**Contraintes :**
 
- 3 joueurs par équipe : $\sum_i x_{i,e} = 3$ pour chaque équipe $e$
- Un joueur dans une seule équipe : $x_{i,A} + x_{i,B} \leq 1$ pour chaque joueur $i$
- Budget total : $\sum_i \sum_e salaire_i \cdot x_{i,e} \leq 8500$
- Poids par équipe : $\sum_i poids_i \cdot x_{i,e} \leq 250$ pour chaque équipe $e$
**Résultat (statut : Optimal)**
 
| Équipe | Joueurs | Score | Salaire | Poids |
|--------|---------|------:|--------:|------:|
| A | Clara, Emma, Hugo | 252 | 3 350 $ | 213 kg |
| B | Alice, Bob, David | 272 | 5 100 $ | 247 kg |
| **Total** | | **524** | **8 450 $** | 460 kg |
 
---
 
### Partie 2 — Algorithme glouton
 
Quatre stratégies ont été implémentées :
 
1. **Meilleur score absolu** : choisit le joueur au score le plus élevé qui respecte encore les contraintes.
2. **Meilleur ratio score/salaire** : favorise les joueurs performants à petit salaire.
3. **Meilleur ratio score/poids** : favorise les joueurs performants parmi les plus légers.
4. **Alternance score / ratio** : alterne entre le meilleur score et le meilleur ratio score/salaire selon la parité du compteur de sélection.
| Stratégie | Score total | Budget utilisé | Écart vs optimal |
|-----------|------------:|---------------:|-----------------:|
| 1 — Score absolu | 446 pts | 7 750 $ | -78 pts (-14,9 %) |
| 2 — Ratio score/salaire | 516 pts | 7 400 $ | -8 pts (-1,5 %) |
| 3 — Ratio score/poids | 516 pts | 7 400 $ | -8 pts (-1,5 %) |
| 4 — Alternance | 521 pts | 8 300 $ | -3 pts (-0,6 %) |
| **PuLP (optimal)** | **524 pts** | **8 450 $** | — |
 
La stratégie 1 ne parvient pas à compléter l'équipe B : après avoir placé David, Bob et Alice dans l'équipe A, les joueurs restants font dépasser les contraintes, et le programme affiche un message expliquant la contrainte en cause.
 
Le glouton ne trouve pas toujours l'optimum parce qu'il prend la meilleure décision locale à chaque étape sans jamais revenir en arrière : un choix avantageux sur le moment peut bloquer une meilleure combinaison plus tard.
 
---
 
### Partie 3 — Récursivité et programmation dynamique
 
#### Score cumulé récursif
 
```
score_cumule(joueurs, 0) = 0
score_cumule(joueurs, 1) = 0 + 93 (David) = 93
score_cumule(joueurs, 2) = 93 + 91 (Bob) = 184
score_cumule(joueurs, 3) = 184 + 89 (Hugo) = 273
score_cumule(joueurs, 4) = 273 + 88 (Alice) = 361
score_cumule(joueurs, 5) = 361 + 87 (Frank) = 448
score_cumule(joueurs, 6) = 448 + 85 (Grace) = 533
```
 
#### Fibonacci des scores — naïf vs mémoïsé
 
Avec `fib(0) = 93` (David) et `fib(1) = 91` (Bob) :
 
| Version | Résultat `fib(35)` | Temps | Complexité |
|---------|-------------------:|------:|-----------:|
| `fib_naif` | 1 370 067 806 | 2,401 s | O(2ⁿ) |
| `fib_memo` | 1 370 067 806 | 0,000095 s | O(n) |
 
La version mémoïsée stocke chaque résultat la première fois qu'il est calculé et le réutilise ensuite, alors que la version naïve recalcule les mêmes valeurs un nombre exponentiel de fois. La fonction `score_cumule` est en O(n), puisqu'elle ne fait qu'un seul appel récursif par niveau.
 
---
 
### Partie 4 — Graphiques
 
- Comparaison des scores obtenus par chaque approche, avec une ligne de référence au score optimal
- Répartition du budget et du poids par équipe par rapport aux maximums autorisés
- Profil des joueurs sélectionnés par PuLP (valeurs normalisées)
---
 
### Conclusion
 
PuLP est l'approche recommandée pour un problème de cette taille, puisqu'elle garantit la solution optimale en respectant toutes les contraintes. Pour un très grand nombre de joueurs, un algorithme glouton bien choisi (comme l'alternance ou le ratio score/salaire) offre un bon compromis entre rapidité et qualité du résultat.

---

