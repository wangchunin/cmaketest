cmake_minimum_required (VERSION 3.10)
project (Tutorial)
set(CMAKE_INSTALL_PREFIX "${PROJECT_BINARY_DIR}/install")


set(INSTALL_LIB_DIR $ORIGIN "${CMAKE_INSTALL_PREFIX}/lib") # 假设安装目录在编译目录的lib子目录内和当前文件夹，这里写成了列表的形式
set(CMAKE_SKIP_BUILD_RPATH FALSE)
set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
set(CMAKE_INSTALL_RPATH "${INSTALL_LIB_DIR}")
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH FALSE) # 存在上面一句，此句好像没有用


# should we use our own math functions?
option (USE_MYMATH 
        "Use tutorial provided math implementation" ON) 


# The version number.
set (Tutorial_VERSION_MAJOR 5)
set (Tutorial_VERSION_MINOR 0)


message (">>> value = ${PROJECT_SOURCE_DIR}")
message (">>> value = ${PROJECT_BINARY_DIR}")
# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/Tutorial.version"
  "${PROJECT_SOURCE_DIR}/Tutorial.h"
  )


# add the MathFunctions library?
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
  message (">>> value = ${EXTRA_LIBS}")
endif (USE_MYMATH)



include_directories ("${PROJECT_SOURCE_DIR}")
add_executable (Tutorial tutorial.cxx)

target_link_libraries (Tutorial PRIVATE  ${EXTRA_LIBS})

# add the install targets

install (TARGETS Tutorial DESTINATION bin)
install (FILES "Tutorial.h" DESTINATION include)

enable_testing()
#define a macro to simplify adding tests, then use it
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)
 
# do a bunch of result based tests
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")