if(CONFIG_ZENOH_PICO)

zephyr_compile_definitions(ZENOH_ZEPHYR)
zephyr_include_directories(../include)
zephyr_library()

# Feature/config defaults (mirrors upstream root CMakeLists cache vars).
# Kept out of the host build so it can drive config.h generation directly.
include(../features.cmake)

# Generate config.h into the build tree from the CMake feature cache, matching
# how the upstream host build produces it. Avoids Kconfig/Z_FEATURE double
# definitions.
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/../include/zenoh-pico/config.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/include/zenoh-pico/config.h
  @ONLY
)
zephyr_library_include_directories(${CMAKE_CURRENT_BINARY_DIR}/include)
zephyr_include_directories(${CMAKE_CURRENT_BINARY_DIR}/include)

file(GLOB_RECURSE Sources
  "../src/api/*.c"
  "../src/collections/*.c"
  "../src/link/*.c"
  "../src/net/*.c"
  "../src/protocol/*.c"
  "../src/runtime/*.c"
  "../src/session/*.c"
  "../src/transport/*.c"
  "../src/utils/*.c"
  "../src/system/common/*.c"
)

file (GLOB Sources_Zephyr "../src/system/zephyr/*.c")
list(APPEND Sources ${Sources_Zephyr})

# Link against mbedTLS when the TLS link is enabled.
if(Z_FEATURE_LINK_TLS)
  zephyr_link_libraries(mbedTLS)
endif()

zephyr_library_sources(${Sources})
endif()
