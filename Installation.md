#### Téléchargement du dépôt git

```bash
git clone https://github.com/cea-hpc/MaHyCo.git
```

#### Compilation

- Modifier le fichier `ArcaneCompilerConfig.cmake` avec le chemin d'installation d'Arcane que vous choisissez.

```cmake
set(Arcane_DIR "/path/to/Arcane/installation" FORCE)
```

```bash
rm -rf build
mkdir build
cd build
```

- Configuration et compilation

Modifier le chemin d'installation d'Arcane dans la commande suivante.

```bash
# Build with makefiles in parallel
cmake .. -DCMAKE_BUILD_TYPE=Release \
         -DArcane_ROOT=/path/to/arcane/installation
         -G Ninja

#cmake --build .
ninja
```
La compilation produit l'exécutable `./src/Mahyco`.

Si une erreur de compilation sur des includes Arcane non trouvés apparaît, vérifier que le fichier en question a bien été installé avec Arcane. Il se peut qu'une partie des sources Arcane n'ait été compilée, par exemple si le GCC-11 ou C++ 20 n'était pas activé (cela ne produit pas d'erreur de compilation mais des fichiers peuvent être manquants dans l'installation).

