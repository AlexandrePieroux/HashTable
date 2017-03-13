cmake_minimum_required (VERSION 2.8.2)
project (Hash_table)

# File variables
# Libraries
set (MEMORY_MANAGEMENT
		 libs/memory_management/memory_management.c
		 libs/memory_management/memory_management.h)

set (LINKED_LIST
		 libs/linked_list/linked_list.c
		 libs/linked_list/linked_list.h)

set (HASH_TABLE
		 libs/hash_table/FNV-1.c
		 libs/hash_table/FNV-1.h
		 libs/hash_table/murmur3.c
		 libs/hash_table/murmur3.h
		 libs/hash_table/hash_table.c
		 libs/hash_table/hash_table.h)

set (THREAD_POOL
		 libs/thread_pool/job_queue.c
		 libs/thread_pool/job_queue.h
		 libs/thread_pool/thread_pool.c
		 libs/thread_pool/thread_pool.h)


# add the Libraries
add_library (memory_management ${MEMORY_MANAGEMENT})
add_library (linked_list ${LINKED_LIST})
add_library (hash_table ${HASH_TABLE})
add_library (thread_pool ${THREAD_POOL})


# link Libraries
target_link_libraries (linked_list memory_management)
target_link_libraries (hash_table linked_list)
target_link_libraries (thread_pool memory_management)


# Tests
# Download and unpack googletest at configure time
configure_file (CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process (COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download)
if (result)
  message (FATAL_ERROR "CMake step for googletest failed: ${result}")
endif ()
execute_process (COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download)
if (result)
  message (FATAL_ERROR "Build step for googletest failed: ${result}")
endif ()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set (gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest and gtest_main targets.
add_subdirectory (${CMAKE_BINARY_DIR}/googletest-src
                  ${CMAKE_BINARY_DIR}/googletest-build)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
  include_directories ("${gtest_SOURCE_DIR}/include")
endif ()


set (LINKED_LIST_TEST tests/linked_list_test.cpp)
set (HASH_TABLE_TEST tests/hash_table_test.cpp)


add_executable (linked_list_test ${LINKED_LIST_TEST})
add_executable (hash_table_test ${HASH_TABLE_TEST})


target_link_libraries (linked_list_test linked_list thread_pool gtest gtest_main)
target_link_libraries (hash_table_test hash_table thread_pool gtest gtest_main)


add_test(NAME linked_list_test COMMAND linked_list_test)
add_test(NAME hash_table_test COMMAND hash_table_test)

