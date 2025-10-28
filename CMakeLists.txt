# Configuration minimale requise
cmake_minimum_required(VERSION 3.10)

# Définition du projet
project(Calculatrice C)

# Configuration des flags de compilation
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g")
set(CMAKE_BUILD_TYPE Debug)

# Définition des répertoires
set(SRC_DIR src)
set(LIB_DIR ${SRC_DIR}/lib)
set(BUILD_DIR ${SRC_DIR}/build)
set(BIN_DIR ${SRC_DIR}/bin)
set(INCLUDE_VAL ${SRC_DIR}/include)

# Configuration des outputs
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${LIB_DIR})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${BUILD_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR})


# Création des bibliothèques statiques et dynamique

# 1. Bibliothèque statique
add_library(calculatriceStat STATIC ${INCLUDE_VAL}/calculatrice.c)
set_target_properties(calculatriceStat PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY ${LIB_DIR}
        ARCHIVE_OUTPUT_DIRECTORY ${BUILD_DIR})
target_include_directories(calculatriceStat PRIVATE
        ${INCLUDE_VAL}
    )

# 1. Bibliothèque dynamique
add_library(calculatriceDyn SHARED ${INCLUDE_VAL}/calculatrice.c)
# Définir le répertoire de sortie spécifique
set_target_properties(calculatriceDyn PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY ${LIB_DIR}
        ARCHIVE_OUTPUT_DIRECTORY ${BUILD_DIR})
target_include_directories(calculatriceDyn PUBLIC
        ${INCLUDE_VAL}
    )
# Explication : Crée une lib statique avec le flag -g implicite via CMAKE_C_FLAGS
# L'include directory est privé car interne à la bibliothèque



# Création de l'exécutable principal
add_executable(exeWithStatLib
    ${SRC_DIR}/app/main.c
)
# Configuration des includes pour l'exécutable
target_include_directories(exeWithStatLib PRIVATE
    ${LIB_DIR}
)
# Liaison des bibliothèques
target_link_libraries(exeWithStatLib
    calculatriceStat
)
# Définition du répertoire de sortie
set_target_properties(exeWithStatLib PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR}
)



# Création de l'exécutable principal
add_executable(exeWithDynLib
    ${SRC_DIR}/app/main.c
)
# Configuration des includes pour l'exécutable
target_include_directories(exeWithDynLib PRIVATE
    ${LIB_DIR}
)
# Liaison des bibliothèques
target_link_libraries(exeWithDynLib
    calculatriceDyn
)
# Définition du répertoire de sortie
set_target_properties(exeWithDynLib PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR}
)

