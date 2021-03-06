cmake_minimum_required(VERSION 3.1)
project(SofaMiscCollision VERSION 1.0)

set(SOFA_HAVE_SOFAMISCCOLLISION 1)

find_package(SofaSphFluid QUIET)

set(HEADER_FILES
    config.h
    initMiscCollision.h
    )

set(SOURCE_FILES
    initMiscCollision.cpp
    )

set(EXTRA_FILES
    README.md
    )

list(APPEND HEADER_FILES
    BarycentricStickContact.h
    BarycentricStickContact.inl
    DefaultCollisionGroupManager.h
    RuleBasedContactManager.h
    SolverMerger.h
    TetrahedronDiscreteIntersection.h
    TetrahedronModel.h
    TriangleModelInRegularGrid.h
    )

list(APPEND SOURCE_FILES
    BarycentricStickContact.cpp
    DefaultCollisionGroupManager.cpp
    RuleBasedContactManager.cpp
    SolverMerger.cpp
    TetrahedronBarycentricPenalityContact.cpp
    TetrahedronDiscreteIntersection.cpp
    TetrahedronFrictionContact.cpp
    TetrahedronModel.cpp
    TetrahedronRayContact.cpp
    TriangleModelInRegularGrid.cpp
    TetrahedronBarycentricDistanceLMConstraintContact.cpp
    )
message(STATUS "SofaMiscModule: build all maintained sofa components.")

if(PLUGIN_SOFASPHFLUID AND SofaSphFluid_FOUND)
    list(APPEND HEADER_FILES SpatialGridPointModel.h)
    list(APPEND SOURCE_FILES SpatialGridPointModel.cpp)
    message(STATUS "SofaMiscCollision: optional dependency SofaSphFluid found activing: <SpatialGridPointModel>")
else()
    message(STATUS "SofaMiscCollision: optional dependency SofaSphFluid not found de-activing: <SpatialGridPointModel>")
endif()

if(SOFA_HAVE_SMP)
    list(APPEND HEADER_FILES ParallelCollisionPipeline.h)
    list(APPEND SOURCE_FILES ParallelCollisionPipeline.cpp)
endif()

if(SOFA_HAVE_MINIFLOWVR)
    list(APPEND SOURCE_FILES FrictionContact_DistanceGrid.cpp)
    list(APPEND SOURCE_FILES BarycentricDistanceLMConstraintContact_DistanceGrid.cpp)
endif()

if(NOT SOFA_NO_OPENGL)
    list(APPEND HEADER_FILES RayTriangleVisitor.h)
    list(APPEND SOURCE_FILES RayTriangleVisitor.cpp)
endif()

add_library(${PROJECT_NAME} SHARED ${HEADER_FILES} ${SOURCE_FILES} ${EXTRA_FILES})
target_link_libraries(${PROJECT_NAME} PUBLIC SofaBaseTopology SofaGeneralMeshCollision SofaGeneralDeformable SofaConstraint SofaExplicitOdeSolver SofaGeneralExplicitOdeSolver SofaImplicitOdeSolver SofaBaseCollision)
target_link_libraries(${PROJECT_NAME} PUBLIC SofaEigen2Solver)

if(PLUGIN_SOFASPHFLUID AND SofaSphFluid_FOUND)
    target_link_libraries(${PROJECT_NAME} PUBLIC SofaSphFluid)
endif()

set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-DSOFA_BUILD_MISC_COLLISION")
set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${HEADER_FILES}")

target_include_directories(${PROJECT_NAME} PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/..>")
sofa_generate_package(
    NAME ${PROJECT_NAME}
    TARGETS ${PROJECT_NAME}
    VERSION ${PROJECT_VERSION}
    RELOCATABLE "plugins"
    )
