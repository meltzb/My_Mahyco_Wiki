Ce guide est pensé une installation sur un ordinateur linux, sur les distributions Ubuntu ou Alma Linux.
Les précisions pour d'autres distributions sont les bienvenues (Installation sur Inti à venir).

MaHyCo utilise la bibliothèque [Arcane](http://github.com/arcane-framework/framework). Il est donc nécessaire de l'installer au préalable pour pouvoir utiliser MaHyCo.

# Installation d'Arcane


## Logiciels/bibliothèques requis

La compilation d'Arcane nécessite les logiciels suivants. Il sont également nécessaires pour compiler MaHyCo (peut-être pas tous ?).

* cmake
* dotnet
* ninja
* glib2
* mpich
* libxml
* ccache

Sur alma linux, avec une installation nue, on peut installer tous ces logiciels avec la commande

```bash
sudo dnf install cmake\
                 dotnet-sdk\
                 ninja-build\
                 glib2-devel\
                 mpich-devel\
                 mpich\
                 libxml2-devel\
                 ccache
```

## Compilation

Lancer les commandes suivantes en adaptant le `/path/to/Arcane/installation`.
C'est dans ce dossier que seront placés les `bin/`, `lib/`, `include/` ainsi que les
fichiers cmake nécessaires au fonctionnement de MaHyCo.

```sh
cd framework
mkdir build
cd build
```


```bash
# Build with Ninja (natively parallel)
cmake .. -DARCANE_WANT_CUDA=FALSE\
         -DCMAKE_INSTALL_PREFIX="/path/to/Arcane/installation"\
         -DARCANE_BUILD_TYPE=Release\
         -DARCANEFRAMEWORK_BUILD_COMPONENTS=Arcane\
         -DARCCORE_CXX_STANDARD=20\
         -DCMAKE_CXX_COMPILER_LAUNCHER=ccache\
         -DCMAKE_C_COMPILER_LAUNCHER=ccache\
         -G Ninja

#cmake --build .
ninja
```

## Tests

```bash
ctest -j 16
```

## Installation

Cet opération déplace les `bin/`, `lib/`, `include/`, et les fichier cmake dans dossier
spécifié par `CMAKE_INSTALL_PREFIX`.

```bash
cmake --build . --target install
```

## Note sur le standard C++

Actuellement, si C++20 est bien reconnu sur le PC, Arcane lance environ 960 tests unitaires. S'il y a moins de tests, vérifier que la compilation se lance bien avec GCC-11 et C++20 (regarder le listing de la configuration cmake).

Il faut avoir dans la configuration CMake :

```
-- CXX Compiler             = /ccc/products/gcc-11.2.0/system/default/bin/c++
-- CXX CompilerId           = GNU
-- CXX Compiler Version     = 11.2.0
```
Avec un chemin éventuellement différent vers gcc-11.

Si la compilation se lance en GCC-8 alors que GCC-11 a été installé sur l'ordinateur, pensez à vérifier les liens symboliques g++, gcc, c++, cxx.


# Installation de MaHyCo


## Téléchargement du dépôt git

```bash
git clone https://github.com/cea-hpc/MaHyCo.git
```

## Compilation

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

La compilation produit l'exécutable `./build/src/MahyCo`.

Si une erreur de compilation sur des includes Arcane non trouvés apparaît, vérifier que le fichier en question a bien été installé avec Arcane. Il se peut qu'une partie des sources Arcane n'ait été compilée, par exemple si le GCC-11 ou C++ 20 n'était pas activé (cela ne produit pas d'erreur de compilation mais des fichiers peuvent être manquants dans l'installation).

