cmake_minimum_required(VERSION 3.10)
project(CalculatriceProject)

# Configuration de la version C
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Options de compilation
set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra")

# Configuration des répertoires
set(SRC_DIR src)
set(LIB_STAT_DIR ${SRC_DIR}/lib/calculatriceStat)
set(LIB_DYN_DIR ${SRC_DIR}/lib/calculatriceDyn)
set(BUILD_DIR ${SRC_DIR}/build)
set(BIN_DIR ${SRC_DIR}/bin)

# Création de la bibliothèque statique
add_library(calculatriceStat STATIC
    ${LIB_STAT_DIR}/calculatriceStat.c
)

# Définition des propriétés de la bibliothèque statique
set_target_properties(calculatriceStat PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    OUTPUT_NAME calculatriceStat
)

# Inclure le répertoire d'en-têtes de la bibliothèque statique
target_include_directories(calculatriceStat PUBLIC
    ${LIB_STAT_DIR}
)

# Création de la bibliothèque dynamique
add_library(calculatriceDyn SHARED
    ${LIB_DYN_DIR}/calculatriceDyn.c
)

# Définition des propriétés de la bibliothèque dynamique
set_target_properties(calculatriceDyn PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    OUTPUT_NAME calculatriceDyn
    SOVERSION 1
)

# Inclure le répertoire d'en-têtes de la bibliothèque dynamique
target_include_directories(calculatriceDyn PUBLIC
    ${LIB_DYN_DIR}
)

# Création de l'exécutable principal
add_executable(exe
    ${SRC_DIR}/app/main.c
)

# Liaison des bibliothèques à l'exécutable
target_link_libraries(exe
    calculatriceStat
    calculatriceDyn
)

# Inclure les répertoires d'en-têtes pour l'exécutable
target_include_directories(exe PRIVATE
    ${LIB_STAT_DIR}
    ${LIB_DYN_DIR}
)

# Définition du répertoire de sortie pour l'exécutable
set_target_properties(exe PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR}
)

# Définition des répertoires de sortie pour les bibliothèques
set_target_properties(calculatriceStat PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY ${LIB_STAT_DIR}
)

set_target_properties(calculatriceDyn PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY ${LIB_DYN_DIR}
)

# Configuration du RPATH pour les bibliothèques dynamiques
if(UNIX AND NOT APPLE)
    set_target_properties(exe PROPERTIES
        INSTALL_RPATH "$ORIGIN/../lib/calculatriceDyn"
        BUILD_WITH_INSTALL_RPATH ON
    )
endif()

# Options d'installation (optionnel)
install(TARGETS exe
    RUNTIME DESTINATION bin
)

install(TARGETS calculatriceDyn
    LIBRARY DESTINATION lib
)

# Cible pour exécuter le programme
add_custom_target(run
    DEPENDS exe
    COMMAND ./${BIN_DIR}/exe
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)

# Cible pour nettoyer
add_custom_target(clean-all
    COMMAND ${CMAKE_COMMAND} -E remove_directory ${BIN_DIR}
    COMMAND ${CMAKE_COMMAND} -E remove_directory ${BUILD_DIR}
    COMMAND ${CMAKE_COMMAND} -E remove ${LIB_STAT_DIR}/libcalculatriceStat.a
    COMMAND ${CMAKE_COMMAND} -E remove ${LIB_DYN_DIR}/libcalculatriceDyn.so
)

# Affichage d'informations de débogage
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "Binary output directory: ${BIN_DIR}")
message(STATUS "Static library directory: ${LIB_STAT_DIR}")
message(STATUS "Dynamic library directory: ${LIB_DYN_DIR}")
