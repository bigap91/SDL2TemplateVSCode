# Add executable called "SDLProject_exe" that is built from the source files
# The extensions are automatically found.

file(GLOB_RECURSE sources
    "*.c"
    "*.cpp"
)

add_executable(SDLProject_exe ${sources})

# Link to math library if compiler is gnu
if ("${CMAKE_C_COMPILER_ID}" STREQUAL "GNU")
    list(APPEND libraries m)
endif()

# Link to user libraries (if there are any)
if(libraries)
    target_link_libraries(SDLProject_exe ${libraries})
endif()

target_link_libraries(SDLProject_exe ${Boost_LIBRARIES})

install( TARGETS SDLProject_exe
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
)

add_test(NAME test_SDLProject_exe COMMAND SDLProject_exe)
