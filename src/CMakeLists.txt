add_executable(jit_tutorial
    main.cpp
)
target_include_directories(jit_tutorial PUBLIC .)
target_compile_options(jit_tutorial PRIVATE ${JT_CXX_FLAGS})
target_link_libraries(jit_tutorial PRIVATE Catch2::Catch2WithMain)

include(CTest)
include(Catch)
catch_discover_tests(jit_tutorial)
