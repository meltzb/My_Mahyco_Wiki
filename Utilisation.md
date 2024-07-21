# Premier exemple

Se placer dans le dossier `TESTS`.
Exécuter la commande

```bash
../build/src/MaHyCo Donnees_sodlag_maillage_msh.arc
```

Un dossier `output` est produit avec les résultats de la simulations.
On peut utiliser Paraview pour ouvrir `output/depouillement/ensight.case`.

# Initialisation

## Jeu de donnée

Les paramètres de la simulation sont définis dans un fichier XML ayant l'extension `.arc`.
Les différents paramètres sont décrits dans les différents fichiers `.axl` du dossier `src`.
Ce fonctionnement découle directement d'Arcane, vous pouvez consulter la documentation Arcane

* [des jeux de données .ARC](https://arcaneframework.github.io/arcane/userdoc/html/d5/dcd/arcanedoc_core_types_casefile.html),
* [des descripteurs de services/modules .AXL](https://arcaneframework.github.io/arcane/userdoc/html/db/d4e/arcanedoc_core_types_axl.html),
* [des options](https://arcaneframework.github.io/arcane/userdoc/html/d4/d00/arcanedoc_core_types_axl_caseoptions.html).

Plusieurs exemples sont données dans les dossiers TESTS et NONREGRESSION.

## Depuis un cas test

Voir le dossier `src/casTest`. On remplit le maillage cartésien avec une boucle sur tout les éléments.

Dans le jeu de donnée, est spécifié une balise `<cas-test>` qui permet de donner le nom du cas.

## Depuis un maillage

On renseigne un maillage avec la balise `<mesh>` qui est chargée par arcane. Dans ce cas on ne met pas de balise `<cas-test>`, ce qui revient à mentionner `<cas-test>EXTERNAL</cas-test>`.

On donne ensuite les valeur initiales des variables dans la partie `<initialisation>`, en donnant une initialisation par zone de maillage (aussi appelé groupe de maillage, définie dans le fichier de maillage)

Logiciels de visualisation/édition du maillage:
* Gmsh (nécessite le paquet mesa-libGLU sur Alma Linux)

# Visualisation des sorties (dépouillement)

Elles se trouves dans le fichier  `output/depouillement/ensight.case`

On peut utiliser les logiciels
* [Paraview](https://www.paraview.org/download/)
* [Visit](https://visit-dav.github.io/visit-website/index.html) (lit bien le fichier mais n'affiche rien)

## Options

Sorties paramétrées via la balise `<arcane-post-processing>` documentée [ici](https://arcaneframework.github.io/arcane/userdoc/html/d1/db1/axldoc_module_ArcanePostProcessing_arcane_std.html)

Attention les noms des options ne seront pas acceptés dans le jeu de donnée, il faut utiliser les noms anglais. Si on a une version compilée d'arcane on peut les trouver dans `share/axl/ArcanePostProcessing_arcane_std.axl`, sinon dans les source sur [github](https://github.com/arcaneframework/framework/blob/main/arcane/src/arcane/std/ArcanePostProcessing.axl).
J'ai trouvé cela avec `grep -ri post-processing | grep axl`.

## Précision des sorties

Fixé par défaut en simple précision.
On peut passer en double précision avec `<format name="UCDPostProcessor" />`.

# Options en ligne de commande

Vous pouvez ajouter les options suivantes pour chaque exemple. Les
options doivent être ajoutées avant le jeu de données (qui doit
toujours être le dernier argument).
```bash
    -arcane_opt max_iteration N  
```
avec N le nombre d'iterations a effectuer.
On peut également piloter le nombre d'iterations a effectuer de cette manière:
```bash
    -A,MaxIteration=N  
```
Enfin, pour faire une reprise (continuer une exécution précédente):
```bash
    -arcane_opt continue
```


# Lancer MaHyCo en parallèle

Pour lancer un cas en parallèle, il faut specifier le service
de parallélisme via la variable d'environnement `ARCANE_PARALLEL_SERVICE`.
Les valeurs possibles sont: `Mpi`, `Thread`, `MpiThread`.
Dans le cas ou on utilise des threads, il faut spécifier leur nombre
via la variables d'environnement `ARCANE_NB_THREAD`.

Par exemple, pour 3 process MPI:
```bash
    export ARCANE_PARALLEL_SERVICE=Mpi
    mpiexec -n 3 ./Mahyco Mahyco.arc
```
pour 4 threads:
```bash
    export ARCANE_PARALLEL_SERVICE=Thread
    export ARCANE_NB_THREAD=4
    ./Mahyco Mahyco.arc
```
pour 3 process MPI et 4 threads (soit 12 sous-domaines)
```bash
    export ARCANE_PARALLEL_SERVICE=MpiThread
    export ARCANE_NB_THREAD=4
    mpiexec -n 3 ./Mahyco Mahyco.arc
```
