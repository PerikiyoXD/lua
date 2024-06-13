# Set the Lua version and hash
set(LUA_VERSION "5.4.4")
set(LUA_VERSION_HASH "03C27684B9D5D9783FB79A7C836BA1CDC5F309CD")
set(LUA_EXTRACT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lua")
set(LUA_DIR "${LUA_EXTRACT_DIR}/lua-${LUA_VERSION}")
set(LUA_ZIP "lua-${LUA_VERSION}.tar.gz")

# Download and extract Lua if it is not already present
if(NOT IS_DIRECTORY "${LUA_DIR}")
    if(NOT EXISTS ${LUA_ZIP})
        message("Downloading Lua ${LUA_VERSION}...")
        file(DOWNLOAD "https://www.lua.org/ftp/lua-${LUA_VERSION}.tar.gz" ${LUA_ZIP} SHOW_PROGRESS EXPECTED_HASH SHA1=${LUA_VERSION_HASH})
    endif()

    file(MAKE_DIRECTORY ${LUA_EXTRACT_DIR})

    message(STATUS "Extracting Lua...")
    execute_process(
        COMMAND ${CMAKE_COMMAND} -E tar xzf "${CMAKE_BINARY_DIR}/${LUA_ZIP}"
        WORKING_DIRECTORY ${LUA_EXTRACT_DIR}
    )
endif()

# Get the Lua source files and remove the Lua compiler source
file(GLOB_RECURSE lua_SOURCES "${LUA_DIR}/src/*.c")
list(REMOVE_ITEM lua_SOURCES "${LUA_DIR}/src/luac.c")

# Create the Lua library
add_library(lua SHARED ${lua_SOURCES})
target_include_directories(lua PUBLIC "${LUA_DIR}/src")
set_target_properties(lua PROPERTIES LINKER_LANGUAGE CXX)

# Ensure your project uses the local Lua library
# Assuming you have an executable or another target that depends on Lua

add_executable(my_project main.cpp)  # Replace 'main.cpp' with your actual source files
target_link_libraries(my_project PRIVATE lua)
target_include_directories(my_project PRIVATE "${LUA_DIR}/src")

# Optionally, add a message to confirm the include directories and linked libraries
message(STATUS "Lua include directories: ${LUA_DIR}/src")
message(STATUS "Lua library: lua")
