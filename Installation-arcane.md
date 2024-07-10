## Compilation Arcane

### Prérequis:
* cmake
* dotnet
* ninja
* glib2
* mpich
* libxml
* ccache

Sur alma linux:
```sh
sudo dnf install cmake\
                 dotnet-sdk\
                 ninja-build\
                 glib2-devel\
                 mpich-devel\
                 mpich\
                 libxml2-devel\
                 ccache
```

### Compilation
Lancer les commandes suivantes en adaptant le `/path/to/Arcane/installation`.

```sh
cd framework
rm -rf build
mkdir build
cd build

# Build with makefiles in parallel
export CCACHE_COMPRESS=true
export CCACHE_COMPRESSLEVEL=6
export CCACHE_DIR=../ccache_dir
export CCACHE_MAXSIZE=20G

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

# Tests
ctest -j 16

# Install
cmake --build . --target install
```

Actuellement, si C++20 est bien reconnu sur le PC, Arcane lance environ 960 tests unitaires. S'il y a moins de tests, vérifier que la compilation se lance bien avec GCC-11 et C++20 (regarder le listing de la configuration cmake).

Il faut avoir dans la configuration CMake :

```
-- CXX Compiler             = /ccc/products/gcc-11.2.0/system/default/bin/c++
-- CXX CompilerId           = GNU
-- CXX Compiler Version     = 11.2.0
```
Avec un chemin éventuellement différent vers gcc-11.

Si la compilation se lance en GCC-8 alors que GCC-11 a été installé sur l'ordinateur, pensez à vérifier les liens symboliques g++, gcc, c++, cxx.


