# CCF — Composantes connexes avec Apache Spark

Projet M2 IASD — *Machine Learning sur le Big Data* (Université Paris-Dauphine, campus de Tunis).

Implémentation du calcul des composantes connexes d'un graphe inspirée du papier
**CCF: Fast and Scalable Connected Component Computation in MapReduce** (Kardes et al., 2014),
avec les deux API Spark (**RDD** et **DataFrame**) en **Scala** et en **Python (PySpark)**,
puis comparaison expérimentale.

## Structure

```
.
├── notebooks/
│   ├── python/
│   │   ├── ccf_pyspark_baseline.ipynb    # 1re version (run_ccf_rdd / run_ccf_dataframe)
│   │   └── ccf_pyspark_optimized.ipynb   # version utilisée pour les benchmarks Python (*_fast)
│   └── scala/
│       └── ccf_spark_scala.ipynb         # notebook Zeppelin (%spark) exporté en .ipynb
└── docs/
    ├── paper/Kardes2014_CCF_MapReduce.pdf
    └── report/rapport_composantes_connexes_spark.pdf
```

## Exécution

**Python** (Colab / Kaggle / local) : Spark 3.5.1, Java 17 ou 21, Python ≤ 3.12.

```bash
pip install pyspark==3.5.1 pandas matplotlib
```

Adapter dans le notebook `JAVA_HOME` et le dossier de checkpoint (`/content/...` = Colab) à votre environnement,
puis exécuter toutes les cellules. Utiliser **`ccf_pyspark_optimized.ipynb`** pour les benchmarks.

**Scala** : notebook Zeppelin (interpréteur `%spark`). Hors Zeppelin, le code peut être lancé dans `spark-shell`
(retirer les lignes `%spark`, puis `:paste fichier.scala`).

## Algorithme

Chaque nœud démarre avec son propre identifiant comme label ; à chaque itération il prend le minimum
entre son label et ceux de ses voisins, jusqu'à ce qu'aucun label ne change. Résultat attendu sur le graphe
de test : `A–B–C–D–E → A` et `F–G–H → F`.

## Résultats (rapport)

| nœuds | itérations | Scala RDD | Scala DF | Python RDD | Python DF |
|------:|-----------:|----------:|---------:|-----------:|----------:|
| 50    | 10         | 8 454 ms  | 17 572 ms | 17 415 ms | 15 259 ms |
| 400   | 40         | 33 209 ms | 74 742 ms | 61 048 ms | 35 131 ms |

Scala : RDD ≈ 2× plus rapide. Python : DataFrame plus rapide (coût de la frontière Python/JVM pour les RDD).
