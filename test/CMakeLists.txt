#Add the new file name under "add_executable" to include any new test files.

cmake_minimum_required(VERSION 3.20.1)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0 -fprofile-arcs -ftest-coverage")


if(BUILD_TESTING)
    #Add Dependencies for test. These are the actual test files.
    add_executable(unit_tests
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/MessageTranslator.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/SomeipHandler.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/SomeipRouter.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/MessageTranslator.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/SomeipWrapper.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/UMsg.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/../src/VsomeipUTransport.cpp
        unit_tests/ExampleTest.cpp
    )

    find_package(GTest REQUIRED)

    #To include another cmake for dependencies:
    #    Include in other cmake: set(this_lib variablename). Ex: set(this_lib ustreamer)
    #    Include in this cmake: variablename. Ex: ustreamer
    target_link_libraries(unit_tests PUBLIC
        GTest::GTest
        up-cpp::up-cpp
        rapidjson
        protobuf::libprotobuf
        fmt::fmt
        spdlog::spdlog
        vsomeip3::vsomeip3
        Boost::boost
        Boost::system
        Boost::thread
        Boost::filesystem
    )

    #Directories for any required files.
    target_include_directories(unit_tests PRIVATE
        ${CMAKE_CURRENT_LIST_DIR}
        ${PROJECT_SOURCE_DIR}/src
    )

    #Set for Test Filtering
    #Format: "Test Suite"."TestName"
    #Example: StreamerTest.AssertInitSuccess , StreamerTest.* (Runs all tests in this suite)
    set (TEST_FILTERING false)

    #Run tests and gcovr when building
    add_custom_command(
        TARGET unit_tests
        COMMENT "Run tests and gcovr"
        POST_BUILD
        COMMAND unit_tests
        COMMAND gcovr -r ${CMAKE_SOURCE_DIR} --html --html-details -o index.html -e '.*test.*'-e'.*hpp.*'
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    )

endif()
