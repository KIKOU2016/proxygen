add_library(
    proxygenhttpserver STATIC
    RequestHandlerAdaptor.cpp
    SignalHandler.cpp
    HTTPServerAcceptor.cpp
    HTTPServer.cpp
)
target_compile_options(
    proxygenhttpserver
    PRIVATE
        ${_PROXYGEN_COMMON_COMPILE_OPTIONS}
)
target_link_libraries(
    proxygenhttpserver
    PUBLIC
        proxygen
)
install(
    TARGETS proxygenhttpserver
    EXPORT proxygen-exports
    DESTINATION lib
)

add_executable(proxygen_push
    samples/push/PushServer.cpp
    samples/push/PushRequestHandler.cpp
)
target_compile_options(
    proxygen_push
    PRIVATE
        ${_PROXYGEN_COMMON_COMPILE_OPTIONS}
)
target_link_libraries(
    proxygen_push
    PUBLIC
        proxygen
        proxygenhttpserver
)
install(
    TARGETS proxygen_push
    EXPORT proxygen-exports
    DESTINATION bin
)

add_executable(proxygen_proxy
    samples/proxy/ProxyServer.cpp
    samples/proxy/ProxyHandler.cpp
)
target_compile_options(
    proxygen_proxy
    PRIVATE
        ${_PROXYGEN_COMMON_COMPILE_OPTIONS}
)
target_link_libraries(
    proxygen_proxy
    PUBLIC
        proxygen
        proxygenhttpserver
)
install(
    TARGETS proxygen_proxy
    EXPORT proxygen-exports
    DESTINATION bin
)

add_executable(proxygen_static
    samples/static/StaticServer.cpp
    samples/static/StaticHandler.cpp
)
target_compile_options(
    proxygen_static
    PRIVATE
        ${_PROXYGEN_COMMON_COMPILE_OPTIONS}
)
target_link_libraries(
    proxygen_static
    PUBLIC
        proxygen
        proxygenhttpserver
)
install(
    TARGETS proxygen_static
    EXPORT proxygen-exports
    DESTINATION bin
)

add_executable(proxygen_echo
    samples/echo/EchoServer.cpp
    samples/echo/EchoHandler.cpp
)
target_compile_options(
    proxygen_echo
    PRIVATE
        ${_PROXYGEN_COMMON_COMPILE_OPTIONS}
)
target_link_libraries(
    proxygen_echo
    PUBLIC
        proxygen
        proxygenhttpserver
)
install(
    TARGETS proxygen_echo
    EXPORT proxygen-exports
    DESTINATION bin
)

if (BUILD_QUIC)
  add_executable(hq
      samples/hq/main.cpp
      samples/hq/SampleHandlers.cpp
      samples/hq/PartiallyReliableCurlClient.cpp
  )
  target_compile_options(
      hq
      PRIVATE
          ${_PROXYGEN_COMMON_COMPILE_OPTIONS}
  )
  target_link_libraries(
      hq
      PUBLIC
          proxygen
          proxygencurl
          proxygenhttpserver
          mvfst::mvfst_transport
          mvfst::mvfst_client
          mvfst::mvfst_server
  )
  install(
      TARGETS hq
      EXPORT proxygen-exports
      DESTINATION bin
  )
endif()

file(
    GLOB_RECURSE PROXYGEN_HTTPSERVER_HEADERS_TOINSTALL
    RELATIVE ${CMAKE_CURRENT_SOURCE_DIR}
    *.h
)
list(FILTER PROXYGEN_HTTPSERVER_HEADERS_TOINSTALL EXCLUDE REGEX tests/)
list(FILTER PROXYGEN_HTTPSERVER_HEADERS_TOINSTALL EXCLUDE REGEX Mocks.h)
list(FILTER PROXYGEN_HTTPSERVER_HEADERS_TOINSTALL EXCLUDE REGEX samples/)
foreach(header ${PROXYGEN_HTTPSERVER_HEADERS_TOINSTALL})
    get_filename_component(header_dir ${header} DIRECTORY)
    install(FILES ${header} DESTINATION include/proxygen/httpserver/${header_dir})
endforeach()

add_subdirectory(tests)
add_subdirectory(filters/tests)
#add_subdirectory(samples/echo/test)
