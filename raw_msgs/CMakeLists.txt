cmake_minimum_required(VERSION 2.8.7)
project(raw_msgs)
find_package(catkin REQUIRED)

find_package(Protobuf REQUIRED)

include_directories(${PROTOBUF_INCLUDE_DIRS})

set(CFH_ROOT_DIR ${PROJECT_SOURCE_DIR}/../at_work_central_factory_hub)

file(GLOB CFH_PROTOS ${CFH_ROOT_DIR}/raw/msgs/*.proto)

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS ${CFH_PROTOS})

add_custom_target(copy ALL
  DEPENDS ${PROTO_HDRS}
)


add_library(raw_msgs SHARED ${PROTO_SRCS})
target_link_libraries(raw_msgs ${PROTOBUF_LIBRARIES})


catkin_package(
  LIBRARIES
    raw_msgs
)



file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_INCLUDE_DESTINATION}/${PROJECT_NAME} )

foreach(File ${PROTO_HDRS})
  add_custom_command(TARGET copy POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy ${File} ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_INCLUDE_DESTINATION}/${PROJECT_NAME} 
  )
endforeach()

install(FILES ${PROTO_HDRS}
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(TARGETS raw_msgs
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## else: using cmake
#else()
#  install(
#    TARGETS raw_msgs
#    LIBRARY DESTINATION lib
#  )
#  install(
#    FILES ${PROTO_HDRS}
#    DESTINATION include/${PROJECT_NAME}
#  )
#
#endif(DEFINED _CATKIN_ALL_INCLUDED_)

