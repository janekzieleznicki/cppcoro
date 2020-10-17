list(APPEND TEST_SOURCES_LIST 
"main.cpp"
"counted.cpp"
"generator_tests.cpp"
"recursive_generator_tests.cpp"
"async_generator_tests.cpp"
"async_auto_reset_event_tests.cpp"
"async_manual_reset_event_tests.cpp"
"async_mutex_tests.cpp"
"async_latch_tests.cpp"
"cancellation_token_tests.cpp"
"task_tests.cpp"
"sequence_barrier_tests.cpp"
"shared_task_tests.cpp"
"sync_wait_tests.cpp"
"single_consumer_async_auto_reset_event_tests.cpp"
"single_producer_sequencer_tests.cpp"
"multi_producer_sequencer_tests.cpp"
"when_all_tests.cpp"
"when_all_ready_tests.cpp"
"ip_address_tests.cpp"
"ip_endpoint_tests.cpp"
"ipv4_address_tests.cpp"
"ipv4_endpoint_tests.cpp"
"ipv6_address_tests.cpp"
"ipv6_endpoint_tests.cpp"
"static_thread_pool_tests.cpp"
)

include(ExternalProject)
find_package(Git REQUIRED)

ExternalProject_Add(
    doctest
    PREFIX ${CMAKE_BINARY_DIR}/doctest
    GIT_REPOSITORY https://github.com/onqtam/doctest.git
    TIMEOUT 10
    UPDATE_COMMAND ${GIT_EXECUTABLE} pull
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
    INSTALL_COMMAND ""
    LOG_DOWNLOAD ON
)

# Expose required variable (DOCTEST_INCLUDE_DIR) to parent scope
ExternalProject_Get_Property(doctest source_dir)
set(DOCTEST_INCLUDE_DIR ${source_dir}/doctest CACHE INTERNAL "Path to include folder for doctest")

add_executable(${PROJECT_NAME}_test ${TEST_SOURCES_LIST})
target_link_libraries(${PROJECT_NAME}_test ${PROJECT_NAME})
# or per target
target_include_directories(${PROJECT_NAME}_test PUBLIC ${DOCTEST_INCLUDE_DIR})

include(${PROJECT_SOURCE_DIR}/cmake/doctest.cmake)
doctest_discover_tests(${PROJECT_NAME}_test)