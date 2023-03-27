
> 1-6节使用到的指令和变量的汇总，方便查看

## 指令

#### *PROJECT 指令*

```cmake
PROJECT(projectname [CXX] [C] [Java])
```

你可以用这个指令定义工程名称，并可指定工程支持的语言，支持的语言列表是可以忽略的，默认情况表示支持所有语言。

#### *SET指令*

```cmake
SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
```

SET 指令可以用来显式的定义变量。

#### *MESSAGE 指令*

```cmake
MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] "message to display" ...) 
```

这个指令用于向终端输出用户定义的信息，包含了三种类型：

1. `SEND_ERROR`，产生错误，生成过程被跳过
2. `STATUS`，输出前缀为`--`的信息
3. `FATAL_ERROR`，立即终止所有cmake过程

#### *ADD_EXECUTABLE 指令*

```cmake
ADD_EXECUTABLE(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               [source1] [source2 ...])
```

使用 `source1` 、`source2` 等源文件构建一个 `name` 的可执行程序。

在本书例子中:

```cmake
ADD_EXECUTABLE(hello ${SRC_LIST})
```

定义了这个工程会生成一个文件名为 hello 的可执行文件，相关的源文件是 SRC_LIST 中定义的源文件列表， 本例中你也可以直接写成`ADD_EXECUTABLE(hello main.c)`。

#### *ADD_LIBRARY 指令*

```cmake
ADD_LIBRARY(libname [SHARED|STATIC|MODULE]
 [EXCLUDE_FROM_ALL]
 source1 source2 ... sourceN
)
```

添加一个名为<name>的库目标，从命令调用中列出的源文件(`source1` 、`source2` )中构建。

类型有三种：

1. **SHARED**，动态库
2. **STATIC**，静态库
3. **MODULE**，在使用 dyld 的系统有效，如果不支持 dyld，则被当作 SHARED 对待。

#### *ADD_SUBDIRECTORY 指令*

```cmake
ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```

这个指令用于向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置。

`EXCLUDE_FROM_ALL` 参数的含义是将这个目录从编译过程中排除，比如，工程的 example，可能就需要工程构建完成后，再进入 example 目录单独进行构建。

在本书例子中

```cmake
ADD_SUBDIRECTORY(src bin)
```

定义了将 `src` 子目录加入工程，并指定编译输出(包含编译中间结果)路径为 `bin `目录。如果不进行 `bin` 目录的指定，那么编译结果(包括中间结果)都将存放在 `build/src` 目录(这个目录跟原有的 src 目录对应)，指定 `bin` 目录后，相当于在编译时将 src 重命名为 bin，所有的中间结果和目标二进制都将存放在 `bin` 目录。

#### *INSTALL 指令*

INSTALL 指令用于定义安装规则，安装的内容可以包括目标二进制、动态库、静态库以及文件、目录、脚本等。

INSTALL 指令包含了各种安装类型，详细请翻回[第四节、更好一点的Hello World](better-hello-world) 查看。

#### *SET_TARGET_PROPERTIES 指令*

```cmake
SET_TARGET_PROPERTIES(target1 target2 ...
 PROPERTIES prop1 value1
 prop2 value2 ...
)
```

这条指令可以用来设置输出的名称，对于动态库，还可以用来指定动态库版本和 API 版本。

#### *GET_TARGET_PROPERTY 指令*

```cmake
GET_TARGET_PROPERTY(VAR target property)
```

与 SET_TARGET_PROPERTIES 指令相对应。具体用法如下例：

```cmake
GET_TARGET_PROPERTY(OUTPUT_VALUE hello_static OUTPUT_NAME)
MESSAGE(STATUS “This is the hello_static OUTPUT_NAME:” ${OUTPUT_VALUE})
```

如果没有这个属性定义，则返回 NOTFOUND。

#### *INCLUDE_DIRECTORIES 指令*

```cmake
INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)
```

这条指令可以用来向工程添加多个特定的头文件搜索路径，路径之间用空格分割，如果路径中包含了空格，可以使用双引号将它括起来，默认的行为是追加到当前的头文件搜索路径的后面，你可以通过两种方式来进行控制搜索路径添加的方式：

1. `CMAKE_INCLUDE_DIRECTORIES_BEFORE`，通过 SET 这个 cmake 变量为 on，可以将添加的头文件搜索路径放在已有路径的前面。
2. 通过 `AFTER` 或者 `BEFORE` 参数，也可以控制是追加还是置前。

#### *LINK_DIRECTORIES 指令*

```cmake
LINK_DIRECTORIES(directory1 directory2 ...)
```

这个指令非常简单，添加非标准的共享库搜索路径，比如，在工程内部同时存在共享库和可执行二进制，在编译时就需要指定一下这些共享库的路径。

#### *TARGET_LINK_LIBRARIES 指令*

```cmake
TARGET_LINK_LIBRARIES(target library1
 <debug | optimized> library2
 ...)
```

这个指令可以用来为 target 添加需要链接的共享库，在书中的例子是一个可执行文件，但是同样可以用于为自己编写的共享库添加共享库链接。

## 变量

#### *\<projectname\>_BINARY_DIR*

由 PROJECT 指令隐式定义的两个 cmake 变量之一，当使用 PROJECT 指令时就会定义。该变量表示编译目录的路径。

#### *\<projectname\>_SOURCE_DIR*

由 PROJECT 指令隐式定义的两个 cmake 变量之一，当使用 PROJECT 指令时就会定义。该变量表示源码工程目录的路径。

#### *PROJECT_BINARY_DIR*

cmake 系统预定义的变量，与<projectname>_BINARY_DIR 一样，该变量表示编译目录的路径。

#### *PROJECT_SOURCE_DIR*

cmake 系统预定义的变量，与<projectname>_SOURCE_DIR 一样，该变量表示源码工程目录的路径。

#### *EXECUTABLE_OUTPUT_PATH*

指定最终的目标二进制（可执行文件）的位置。通过 SET 指令定义该变量，可指定最终生成的可执行文件的位置。（不包含编译生成的中间文件）

```cmake
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
```

#### *LIBRARY_OUTPUT_PATH*

指定最终的目标二进制（动、静态库文件）的位置。通过 SET 指令定义该变量，可指定最终生成的动态库或静态库文件的位置。（不包含编译生成的中间文件）

```cmake
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
```

#### *CMAKE_INSTALL_PREFIX*

`CMAKE_INSTALL_PREFIX` 变量类似于 configure 脚本的 `--prefix`，常见的使用方法看起来是这个样子：`cmake -DCMAKE_INSTALL_PREFIX=/usr`。

#### *CMAKE_INCLUDE_DIRECTORIES_BEFORE*

通过 SET 这个 cmake 变量为 on，可以将添加的头文件搜索路径放在已有路径的前面。

## 环境变量

#### *CMAKE_INCLUD_PATH* 和 *CMAKE_LIBRARY_PATH*

使用方法是要在 bash 中用 export 或者在 csh 中使用 set 命令设置或者 `CMAKE_INCLUDE_PATH=/home/include cmake ..`等方式。

这两个变量主要是用来解决以前 autotools 工程中 `--extra-include-dir` 等参数的支持的。

也就是，如果头文件没有存放在常规路径(`/usr/include`, `/usr/local/include` 等)，则可以通过这些变量就行弥补。

这两个变量需要结合FIND指令一起使用。详细用法请翻回[第六节、如何使用外部共享库和头文件](the-use-of-lib-and-header-file) 查看。