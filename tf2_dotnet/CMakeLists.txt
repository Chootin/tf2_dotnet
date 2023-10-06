cmake_minimum_required(VERSION 3.5)

project(tf2_dotnet)

find_package(ament_cmake_export_assemblies REQUIRED)

find_package(ament_cmake REQUIRED)
find_package(rcl REQUIRED)
find_package(rcl_interfaces REQUIRED)
find_package(rmw REQUIRED)
find_package(rmw_implementation REQUIRED)
find_package(rmw_implementation_cmake REQUIRED)
find_package(rosidl_generator_c REQUIRED)

find_package(rclcpp REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_msgs REQUIRED)
find_package(std_msgs REQUIRED)

find_package(dotnet_cmake_module REQUIRED)
find_package(DotNETExtra REQUIRED)
find_package(rcldotnet REQUIRED)
find_package(rcldotnet_common REQUIRED)

if(NOT WIN32)
  if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--no-undefined")
  elseif(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,-undefined,error")
  endif()
endif()

set(CSHARP_TARGET_FRAMEWORK "netstandard2.0")

set(CS_SOURCES
  Exceptions.cs
  Interop.cs
  SafeBufferCoreHandle.cs
  Tf2Conversions.cs
  Tf2ExceptionHelper.cs
  Transform.cs
  TransformBroadcaster.cs
  TransformBuffer.cs
  TransformListener.cs
)

add_dotnet_library(${PROJECT_NAME}_assemblies
  SOURCES
  ${CS_SOURCES}
  INCLUDE_DLLS
  ${rcldotnet_common_ASSEMBLIES_DLL}
  ${rcldotnet_ASSEMBLIES_DLL}
  ${std_msgs_ASSEMBLIES_DLL}
  ${geometry_msgs_ASSEMBLIES_DLL}
  ${tf2_msgs_ASSEMBLIES_DLL}
  ${test_msgs_ASSEMBLIES_DLL}
  ${builtin_interfaces_ASSEMBLIES_DLL}
  ${rcldotnet_ASSEMBLIES_DLL}
)

install_dotnet(${PROJECT_NAME}_assemblies DESTINATION lib/${PROJECT_NAME}/dotnet)
ament_export_assemblies_dll("lib/${PROJECT_NAME}/dotnet/${PROJECT_NAME}_assemblies.dll")

add_library(${PROJECT_NAME} SHARED
  tf2_dotnet.cpp
)

set_target_properties(${PROJECT_NAME}
  PROPERTIES
  OUTPUT_NAME ${PROJECT_NAME}_native
)

ament_target_dependencies(${PROJECT_NAME}
  "builtin_interfaces"
  "rcl"
  "rclcpp"
  "rosidl_generator_c"
  "rosidl_typesupport_c"
  "tf2"
  "tf2_ros"
  "std_msgs"
  "geometry_msgs"
  "builtin_interfaces"
)

ament_export_libraries(${PROJECT_NAME}_native)

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

ament_export_dependencies(ament_cmake)
ament_export_dependencies(builtin_interfaces)
ament_export_dependencies(geometry_msgs)
ament_export_dependencies(tf2_msgs)
ament_export_dependencies(rcl)
ament_export_dependencies(rosidl_generator_c)
ament_export_dependencies(rosidl_typesupport_c)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
