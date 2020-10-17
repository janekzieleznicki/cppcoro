cmake_minimum_required(VERSION 3.15.0)

project(cppcoro VERSION 1.0.0 LANGUAGES CXX)

list(APPEND SOURCES_LIST 
"lib/async_auto_reset_event.cpp"
"lib/async_manual_reset_event.cpp"
"lib/async_mutex.cpp"
"lib/cancellation_state.cpp"
"lib/cancellation_token.cpp"
"lib/cancellation_source.cpp"
"lib/cancellation_registration.cpp"
"lib/lightweight_manual_reset_event.cpp"
"lib/ip_address.cpp"
"lib/ip_endpoint.cpp"
"lib/ipv4_address.cpp"
"lib/ipv4_endpoint.cpp"
"lib/ipv6_address.cpp"
"lib/ipv6_endpoint.cpp"
"lib/static_thread_pool.cpp"
"lib/auto_reset_event.cpp"
"lib/spin_wait.cpp"
"lib/spin_mutex.cpp"
)

message(STATUS "Sources list: ${SOURCES_LIST}")

message(STATUS "Header list: ${HEADER_LIST}")
add_library(${PROJECT_NAME} STATIC ${SOURCES_LIST})

target_compile_options(${PROJECT_NAME} PUBLIC
-Wall
-Wextra
)
find_package(Threads)
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_20)

target_link_libraries(${PROJECT_NAME} PUBLIC Threads::Threads)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    target_link_options(${PROJECT_NAME} PUBLIC -lc++)
    target_compile_options(${PROJECT_NAME} PUBLIC
    -stdlib=libc++
    -fcoroutines-ts
    )
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(${PROJECT_NAME} PUBLIC
    -fcoroutines
    )
endif()

target_include_directories(${PROJECT_NAME} PUBLIC 
    $<INSTALL_INTERFACE:include>    
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Targets
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    PUBLIC_HEADER DESTINATION "${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}"
)

set(CMAKE_INSTALL_INCLUDEDIR "${CMAKE_INSTALL_PREFIX}/include")

install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME} DESTINATION include)

set(CMAKECONFIG_INSTALL_DIR lib/cmake/${PROJECT_NAME})

include(CMakePackageConfigHelpers)
configure_package_config_file(${PROJECT_NAME}Config.cmake.in ${PROJECT_NAME}Config.cmake
    INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR}
    INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}/bin
    PATH_VARS CMAKE_INSTALL_INCLUDEDIR
)

write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    VERSION "${PROJECT_VERSION}" COMPATIBILITY AnyNewerVersion)

install(EXPORT ${PROJECT_NAME}Targets FILE ${PROJECT_NAME}Targets.cmake NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKECONFIG_INSTALL_DIR})
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    DESTINATION ${CMAKECONFIG_INSTALL_DIR})
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION ${CMAKECONFIG_INSTALL_DIR})

enable_testing()
add_subdirectory(test)