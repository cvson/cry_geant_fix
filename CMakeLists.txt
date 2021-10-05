cmake_minimum_required(VERSION 3.6)
project(cry_geant_test)

set(CMAKE_CXX_STANDARD 11)

option(WITH_GEANT4_UIVIS "Build example with Geant4 UI and Vis drivers" ON)
if(WITH_GEANT4_UIVIS)
    find_package(Geant4 REQUIRED ui_all vis_all)
else()
    find_package(Geant4 REQUIRED)
endif()

include(${Geant4_USE_FILE})
include_directories(${PROJECT_SOURCE_DIR}/include)

file(GLOB sources ${PROJECT_SOURCE_DIR}/src/*.cc)
file(GLOB headers ${PROJECT_SOURCE_DIR}/include/*.hh)

set(CRYHOME /Users/cvson/meOffline_nosync/geant4test/krakow2019/cry_v1.7/)

file(GLOB cry_sources ${CRYHOME}/src/*.cc)
set(CRY_LIBRARIES ${CRYHOME}/lib/libCRY.a)
include_directories(${CRYHOME}/src)

find_package(ROOT)
include_directories(${ROOT_INCLUDE_DIRS})

add_executable(cosmic cosmic.cc ${sources} ${headers} ${cry_sources} ${cry_headers})
target_link_libraries(cosmic ${Geant4_LIBRARIES} ${ROOT_LIBRARIES} ${CRY_LIBRARIES})

set(COPY_SCRIPTS
        setup.file
        cmd.file
        cmd2.file 
        )

foreach(_script ${COPY_SCRIPTS})
    configure_file(
            ${PROJECT_SOURCE_DIR}/${_script}
            ${PROJECT_BINARY_DIR}/${_script}
            COPYONLY
    )
endforeach()

install(TARGETS cosmic DESTINATION bin)

#add_custom_command(TARGET cosmic PRE_BUILD
#        COMMAND ${CMAKE_COMMAND} -E copy_directory
#        ${PROJECT_SOURCE_DIR}/input
#        ${PROJECT_BINARY_DIR}/input)
