# SPDX-FileCopyrightText: NVIDIA CORPORATION & AFFILIATES
# Copyright (c) 2024 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.22.1)
project(isaac_ros_rtdetr LANGUAGES C CXX)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake_auto REQUIRED)
ament_auto_find_build_dependencies()

# RtDetrPreprocessorNode
ament_auto_add_library(rtdetr_preprocessor_node SHARED src/rtdetr_preprocessor_node.cpp)
rclcpp_components_register_nodes(rtdetr_preprocessor_node "nvidia::isaac_ros::rtdetr::RtDetrPreprocessorNode")
set(node_plugins "${node_plugins}nvidia::isaac_ros::rtdetr::RtDetrPreprocessorNode;$<TARGET_FILE:rtdetr_preprocessor_node>\n")

# RtDetrDecoderNode
ament_auto_add_library(rtdetr_decoder_node SHARED src/rtdetr_decoder_node.cpp)
rclcpp_components_register_nodes(rtdetr_decoder_node "nvidia::isaac_ros::rtdetr::RtDetrDecoderNode")
set(node_plugins "${node_plugins}nvidia::isaac_ros::rtdetr::RtDetrDecoderNode;$<TARGET_FILE:rtdetr_decoder_node>\n")

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()

  # The FindPythonInterp and FindPythonLibs modules are removed
  if(POLICY CMP0148)
    cmake_policy(SET CMP0148 OLD)
  endif()

  find_package(launch_testing_ament_cmake REQUIRED)
  add_launch_test(test/isaac_ros_rtdetr_pol_test.py TIMEOUT "600")
endif()

# Visualizer python scripts
ament_python_install_package(${PROJECT_NAME})

install(PROGRAMS
  scripts/isaac_ros_rtdetr_visualizer.py
  DESTINATION lib/${PROJECT_NAME}
)


# Embed versioning information into installed files
ament_index_get_resource(ISAAC_ROS_COMMON_CMAKE_PATH isaac_ros_common_cmake_path isaac_ros_common)
include("${ISAAC_ROS_COMMON_CMAKE_PATH}/isaac_ros_common-version-info.cmake")
generate_version_info(${PROJECT_NAME})

ament_auto_package(INSTALL_TO_SHARE launch)
