# ROS理解——CMakelists.txt

## 主要部分（不一定都有且顺序不敏感）

1. **Required CMake Version** (`cmake_minimum_required`)
2. **Package Name** (`project()`)
3. **Find other CMake/Catkin packages needed for build** (`find_package()`)
4. **Enable Python module support** (`catkin_python_setup()`)
5. **Message/Service/Action Generators** (`add_message_files(), add_service_files(), add_action_files()`)
6. **Invoke message/service/action generation** (`generate_messages()`)
7. **Specify package build info export** (`catkin_package()`)
8. **Libraries/Executables to build** (`add_library()/add_executable()/target_link_libraries()`)
9. **Tests to build** (`catkin_add_gtest()`)
10. **Install rules** (`install()`)

## find_package()

使用`find_package(<NAME>)`之后会自动定义如下变量

- <NAME>_FOUND - Set to true if the library is found, otherwise false
- <NAME>\_INCLUDE_DIRS or <NAME>_INCLUDES - The include paths exported by the package
- <NAME>\_LIBRARIES or <NAME>_LIBS - The libraries exported by the package

一般推荐使用

```cmake
find_package(catkin REQUIRED COMPONENTS nodelet)
```

这样用的好处是可以将==COMPONENTS==后面的包的内容加入到==catkin==的内容，添加头文件不用再单独加新包，库文件也是

If using C++ and Boost, you need to invoke `find_package()` on Boost and specify which aspects of Boost you are using as components. For example, if you wanted to use Boost threads, you would say:

```cmake
find_package(Boost REQUIRED COMPONENTS thread)
```

## catkin_package()

是catkin提供的宏，必须在`add_library()` 或者 `add_executable()`之前使用

- `INCLUDE_DIRS` - The exported include paths (i.e. cflags) for the package（生成的文件的nclude路径）
- `LIBRARIES` - The exported libraries from the project（生成的文件的库）
- `CATKIN_DEPENDS` - Other catkin projects that this project depends on（本package的catkin依赖）
- `DEPENDS` - Non-catkin CMake projects that this project depends on. For a better understanding, see [this explanation](http://answers.ros.org/question/58498/what-is-the-purpose-of-catkin_depends/).（本package的非catkin依赖）
- `CFG_EXTRAS` - Additional configuration options

```cmake
catkin_package(
   INCLUDE_DIRS include
   LIBRARIES ${PROJECT_NAME}
   CATKIN_DEPENDS roscpp nodelet
   DEPENDS eigen opencv)
```

第三个和第四个主要是为了当其他ros程序使用本程序作为引用的时候，可以将本程序的依赖直接导入而不会造成依赖缺失问题

## Specifying Build Targets

CMake要求统一文件夹下不论runtime文件还是library文件都要有唯一名称，但是可以使用以下语句重命名

```cmake
set_target_properties(rviz_image_view
                      PROPERTIES OUTPUT_NAME image_view
                      PREFIX "")
```

这段话可以将`rviz_image_view`重命名为`image_view`

```
set_target_properties(python_module_library
  PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_PYTHON_DESTINATION})
```

这段话则可以将library的输出位置改变

## 引用顺序

```cmake
find_package(catkin REQUIRED COMPONENTS message_generation ...)
add_message_files(...)
add_service_files(...)
add_action_files(...)
generate_messages(...)
catkin_package(
	...
	CATKIN_DEPENDS message_runtime ...
	...)
...
```

同时需要注意在package.xml文件

```xml
<exec_depend>message_runtime</exec_depend>
<build_depend>message_generation</build_depend>
```

## 例子

```cmake
  # Get the information about this package's buildtime dependencies
  find_package(catkin REQUIRED
    COMPONENTS message_generation std_msgs sensor_msgs)

  # Declare the message files to be built
  add_message_files(FILES
    MyMessage1.msg
    MyMessage2.msg
  )

  # Declare the service files to be built
  add_service_files(FILES
    MyService.srv
  )

  # Actually generate the language-specific message and service files
  generate_messages(DEPENDENCIES std_msgs sensor_msgs)

  # Declare that this catkin package's runtime dependencies
  catkin_package(
   CATKIN_DEPENDS message_runtime std_msgs sensor_msgs
  )

  # define executable using MyMessage1 etc.
  add_executable(message_program src/main.cpp)
  add_dependencies(message_program ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

  # define executable not using any messages/services provided by this package
  add_executable(does_not_use_local_messages_program src/main.cpp)
  add_dependencies(does_not_use_local_messages_program ${catkin_EXPORTED_TARGETS})
```