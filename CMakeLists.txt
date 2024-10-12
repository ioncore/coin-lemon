CMAKE_MINIMUM_REQUIRED(VERSION 3.22)

project(lemon}
        LANGUAGES CXX
        VERSION 1.3.1)

IF(CMAKE_COMPILER_IS_GNUCXX)
    SET(CXX_WARNING "-Wall -W -Wunused -Wformat=2 -Wctor-dtor-privacy -Wnon-virtual-dtor -Wno-char-subscripts -Wwrite-strings -Wno-char-subscripts -Wreturn-type -Wcast-qual -Wcast-align -Wsign-promo -Woverloaded-virtual -fno-strict-aliasing -Wold-style-cast -Wno-unknown-pragmas")
    SET(CMAKE_CXX_FLAGS_DEBUG CACHE STRING "-ggdb")
    SET(CMAKE_C_FLAGS_DEBUG CACHE STRING "-ggdb")
ELSEIF(MSVC)
    # This part is unnecessary 'casue the same is set by the lemon/core.h.
    # Still keep it as an example.
    SET(CXX_WARNING "/wd4250 /wd4355 /wd4503 /wd4800 /wd4996")
    # Suppressed warnings:
    # C4250: 'class1' : inherits 'class2::member' via dominance
    # C4355: 'this' : used in base member initializer list
    # C4503: 'function' : decorated name length exceeded, name was truncated
    # C4800: 'type' : forcing value to bool 'true' or 'false'
    #        (performance warning)
    # C4996: 'function': was declared deprecated
ELSE()
    SET(CXX_WARNING "-Wall")
ENDIF()
SET(LEMON_CXX_WARNING_FLAGS ${CXX_WARNING} CACHE STRING "LEMON warning flags.")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${LEMON_CXX_WARNING_FLAGS}")

IF(MSVC)
    SET( CMAKE_CXX_FLAGS_MAINTAINER "/WX ${CMAKE_CXX_FLAGS_DEBUG}" CACHE STRING
            "Flags used by the C++ compiler during maintainer builds."
    )
    SET( CMAKE_C_FLAGS_MAINTAINER "/WX ${CMAKE_CXX_FLAGS_DEBUG}" CACHE STRING
            "Flags used by the C compiler during maintainer builds."
    )
    SET( CMAKE_EXE_LINKER_FLAGS_MAINTAINER
            "${CMAKE_EXE_LINKER_FLAGS_DEBUG}" CACHE STRING
            "Flags used for linking binaries during maintainer builds."
    )
    SET( CMAKE_SHARED_LINKER_FLAGS_MAINTAINER
            "${CMAKE_SHARED_LINKER_FLAGS_DEBUG}" CACHE STRING
            "Flags used by the shared libraries linker during maintainer builds."
    )
ELSE()
    SET( CMAKE_CXX_FLAGS_MAINTAINER "-Werror -ggdb -O0" CACHE STRING
            "Flags used by the C++ compiler during maintainer builds."
    )
    SET( CMAKE_C_FLAGS_MAINTAINER "-Werror -O0" CACHE STRING
            "Flags used by the C compiler during maintainer builds."
    )
    SET( CMAKE_EXE_LINKER_FLAGS_MAINTAINER
            "${CMAKE_EXE_LINKER_FLAGS_DEBUG}" CACHE STRING
            "Flags used for linking binaries during maintainer builds."
    )
    SET( CMAKE_SHARED_LINKER_FLAGS_MAINTAINER
            "${CMAKE_SHARED_LINKER_FLAGS_DEBUG}" CACHE STRING
            "Flags used by the shared libraries linker during maintainer builds."
    )
ENDIF()

MARK_AS_ADVANCED(
        CMAKE_CXX_FLAGS_MAINTAINER
        CMAKE_C_FLAGS_MAINTAINER
        CMAKE_EXE_LINKER_FLAGS_MAINTAINER
        CMAKE_SHARED_LINKER_FLAGS_MAINTAINER )

INCLUDE(CheckTypeSize)
CHECK_TYPE_SIZE("long long" LONG_LONG)
SET(LEMON_HAVE_LONG_LONG ${HAVE_LONG_LONG})

INCLUDE(FindThreads)

IF(NOT LEMON_THREADING)
    IF(CMAKE_USE_PTHREADS_INIT)
        SET(LEMON_THREADING "Pthread")
    ELSEIF(CMAKE_USE_WIN32_THREADS_INIT)
        SET(LEMON_THREADING "Win32")
    ELSE()
        SET(LEMON_THREADING "None")
    ENDIF()
ENDIF()

SET( LEMON_THREADING "${LEMON_THREADING}" CACHE STRING
        "Choose the threading library, options are: Pthread Win32 None."
        FORCE )

IF(LEMON_THREADING STREQUAL "Pthread")
    SET(LEMON_USE_PTHREAD TRUE)
ELSEIF(LEMON_THREADING STREQUAL "Win32")
    SET(LEMON_USE_WIN32_THREADS TRUE)
ENDIF()

ENABLE_TESTING()

CONFIGURE_FILE(
        "${CMAKE_CURRENT_SOURCE_DIR}/lemon/config.h.in"
        "${CMAKE_CURRENT_BINARY_DIR}/lemon/config.h"
)

CONFIGURE_FILE(
        "${CMAKE_CURRENT_SOURCE_DIR}/lemon/lemon.pc.in"
        "${CMAKE_CURRENT_BINARY_DIR}/lemon/lemon.pc"
        @ONLY
)

add_library(lemon STATIC)
target_include_directories(lemon
        PUBLIC
        ${CMAKE_CURRENT_BINARY_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR})

target_sources(lemon
        PUBLIC
        FILE_SET HEADERS
        BASE_DIRS
        ${CMAKE_CURRENT_BINARY_DIR}/lemon
        FILES
        ${CMAKE_CURRENT_BINARY_DIR}/lemon/config.h)

target_sources(lemon
        PUBLIC
        FILE_SET HEADERS
        BASE_DIRS
        ${CMAKE_CURRENT_SOURCE_DIR}/lemon
        FILES
        lemon/bits/enable_if.h
        lemon/bits/traits.h
        lemon/adaptors.h
        lemon/arg_parser.h
        lemon/assert.h
        lemon/bellman_ford.h
        lemon/bfs.h
        lemon/bin_heap.h
        lemon/binomial_heap.h
        lemon/bucket_heap.h
        lemon/capacity_scaling.h
        lemon/christofides_tsp.h
        lemon/circulation.h
        lemon/color.h
        lemon/concept_check.h
        lemon/connectivity.h
        lemon/core.h
        lemon/cost_scaling.h
        lemon/counter.h
        lemon/cycle_canceling.h
        lemon/dfs.h
        lemon/dheap.h
        lemon/dijkstra.h
        lemon/dim2.h
        lemon/dimacs.h
        lemon/edge_set.h
        lemon/edmonds_karp.h
        lemon/elevator.h
        lemon/error.h
        lemon/euler.h
        lemon/fib_heap.h
        lemon/fractional_matching.h
        lemon/full_graph.h
        lemon/glpk.h
        lemon/gomory_hu.h
        lemon/graph_to_eps.h
        lemon/greedy_tsp.h
        lemon/grid_graph.h
        lemon/grosso_locatelli_pullan_mc.h
        lemon/hao_orlin.h
        lemon/hartmann_orlin_mmc.h
        lemon/howard_mmc.h
        lemon/hypercube_graph.h
        lemon/insertion_tsp.h
        lemon/karp_mmc.h
        lemon/kruskal.h
        lemon/lgf_reader.h
        lemon/lgf_writer.h
        lemon/list_graph.h
        lemon/lp.h
        lemon/lp_base.h
        lemon/lp_skeleton.h
        lemon/maps.h
        lemon/matching.h
        lemon/math.h
        lemon/max_cardinality_search.h
        lemon/min_cost_arborescence.h
        lemon/nagamochi_ibaraki.h
        lemon/nauty_reader.h
        lemon/nearest_neighbor_tsp.h
        lemon/network_simplex.h
        lemon/opt2_tsp.h
        lemon/pairing_heap.h
        lemon/path.h
        lemon/planarity.h
        lemon/preflow.h
        lemon/quad_heap.h
        lemon/radix_heap.h
        lemon/radix_sort.h
        lemon/random.cc
        lemon/smart_graph.h
        lemon/static_graph.h
        lemon/suurballe.h
        lemon/time_measure.h
        lemon/tolerance.h
        lemon/unionfind.h
        PRIVATE
        lemon/bits/windows.cc
        lemon/arg_parser.cc
        lemon/base.cc
        lemon/color.cc
        lemon/lp_base.cc
        lemon/lp_skeleton.cc
        lemon/random.cc)

IF(LEMON_HAVE_GLPK)
    target_sources(lemon
            PUBLIC
            FILE_SET HEADERS FILES
            lemon/glpk.h
            PRIVATE
            lemon/glpk.cc)
    target_include_directories(lemon ${GLPK_INCLUDE_DIRS})
ENDIF()

IF(LEMON_HAVE_CPLEX)
    target_sources(lemon
            PUBLIC
            FILE_SET HEADERS FILES
            lemon/cplex.h
            PRIVATE
            lemon/cplex.cc)
    target_include_directories(lemon ${ILOG_INCLUDE_DIRS})
ENDIF()

IF(LEMON_HAVE_CLP)
    target_sources(lemon
            PUBLIC
            FILE_SET HEADERS FILES
            lemon/clp.h
            PRIVATE
            lemon/clp.cc)
    target_include_directories(lemon ${COIN_INCLUDE_DIRS})
ENDIF()

IF(LEMON_HAVE_CBC)
    target_sources(lemon
            PUBLIC
            FILE_SET HEADERS FILES
            lemon/cbc.h
            PRIVATE
            lemon/cbc.cc)
    target_include_directories(lemon ${COIN_INCLUDE_DIRS})
ENDIF()

IF(LEMON_HAVE_SOPLEX)
    target_sources(lemon
            PUBLIC
            FILE_SET HEADERS FILES
            lemon/soplex.h
            PRIVATE
            lemon/soplex.cc)
    target_include_directories(lemon ${SOPLEX_INCLUDE_DIRS})
ENDIF()

target_link_libraries(lemon
        ${GLPK_LIBRARIES} ${COIN_LIBRARIES} ${ILOG_LIBRARIES} ${SOPLEX_LIBRARIES}
)

IF(UNIX)
    SET_TARGET_PROPERTIES(lemon PROPERTIES OUTPUT_NAME lemon VERSION 1.3.1 SOVERSION 1.3.1)
ENDIF()


