cmake_minimum_required(VERSION "3.21")

project("placeholder")

set(CMAKE_CXX_STANDARD 17)

set(HEADERS "")
set(SOURCES "main.cpp")

set(HEADER_DIR "include/")
set(SOURCE_DIR "src/")

#list(TRANSFORM HEADERS PREPEND ${HEADER_DIR})
list(TRANSFORM SOURCES PREPEND ${SOURCE_DIR})

add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})
target_include_directories(${PROJECT_NAME} PRIVATE ${HEADER_DIR})
target_sources(${PROJECT_NAME} PRIVATE ${SOURCES} ${HEADERS})

#[[ 

# uncomment this block to enable google test
# how to fetch googletest:
# $ git submodule add https://github.com/google/googletest.git googletest

set(TEST_SUBPROJECT_NAME "unit_tests")

enable_testing()

add_subdirectory(
	"${CMAKE_CURRENT_SOURCE_DIR}/googletest"
	"googletest" # place output files here
)

add_executable(
	${TEST_SUBPROJECT_NAME}
	test/test.cpp
)

# add source files to the include dirs for the test target
target_include_directories(
	${TEST_SUBPROJECT_NAME} PRIVATE
	"${CMAKE_CURRENT_SOURCE_DIR}/${SOURCE_DIR}"
)
target_include_directories(
	${TEST_SUBPROJECT_NAME} PRIVATE
	"${CMAKE_CURRENT_SOURCE_DIR}/${HEADER_DIR}"
)

# link test target with gtest
target_link_libraries(
	${TEST_SUBPROJECT_NAME} PRIVATE
	gtest_main
)

# see https://google.github.io/googletest/quickstart-cmake.html
include(GoogleTest) # enable gtest CMake module
gtest_discover_tests(${TEST_SUBPROJECT_NAME}) # discover tests included in the binary

if (MSVC)
	# see https://github.com/google/googletest/blob/master/googletest/README.md
	set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
endif()

]]

# project compiler flags
if (MSVC)
	target_compile_options(
		${PROJECT_NAME} PRIVATE
		"/permissive-"
		"/W4"
		"/w14242" "/w14254" "/w14263" "/w14265"
		"/w14287" "/we4289" "/w14296" "/w14311"
		"/w14545" "/w14546" "/w14547" "/w14549"
		"/w14555" "/w14619" "/w14640" "/w14826"
		"/w14905" "/w14906" "/w14928"
	)
else() # gcc or clang
	target_compile_options(
		${PROJECT_NAME} PRIVATE
		"-Wall" "-Wshadow" "-Wextra" "-Wpedantic"
		"-Wunused" "-Wconversion" "-Wsign-conversion"
		"-Wnull-dereference" "-Wdouble-promotion"
		"-Wnon-virtual-dtor" "-Woverloaded-virtual"
		"-Wcast-align" "-Wformat=2"
	)
endif()

if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
	target_compile_options(
		${PROJECT_NAME} PRIVATE
		"-Wmisleading-indentation"
		"-Wduplicated-cond"
		"-Wduplicated-branches"
		"-Wlogical-op"
		"-Wuseless-cast"
	)
endif()
