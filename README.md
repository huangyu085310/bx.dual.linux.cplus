## 目录说明

lib:

- lib64.tar.gz：64位、(GNU libc) 2.17、所有库文件及Demo可执行程序
- lib32.tar.gz：32位、(GNU libc) 2.5、所有库文件及Demo可执行程序

- lib-aarch64-himix100.tar：64bits、for aarch64-himix100-linux、gcc version 6.3.0(HC&C V100)


demo_source：demo源码:

- allfonts： 存放字体文件；在调用发送动态区函数时，可以使用字体文件作为参数；
- DynamicAreaDemo: 动态区示例源码；

include：依赖头文件:

- bx_dual_sdk.h ：所有接口函数的声明和注释；

- bx_sdk_dual.h ：所有接口函数的别名，不是必须要包含的；

- Obasic_types.h：基本数据类型的定义



## linux下Demo示例程序的编译和运行

### 示例程序依赖的库和头文件

- #### onbon SDK库

所在目录：./lib

主要包含onbon sdk库；cairo、iconv等第三方开源库；如果本地有相同的库，建议优先使用/链接到我们提供的这些库；

- #### 头文件

有3个，分别放在：

./include/bx_dual_sdk.h ：所有接口函数的声明和注释；

./include/bx_sdk_dual.h ：所有接口函数的别名，不是必要要包含的；

./include/Obasic_types.h：基本数据类型的定义

- ### 动态区Demo源代码


参考：[动态区接口调用说明](https://github.com/onbonlab/bx.dual.linux.cplus/blob/master/doc/动态区接口调用说明.md)



### 动态区Demo编译和运行

- 从lib目录中解压相应平台的lib库，如下以64位linux操作系统为例：

  ```
  tar -zxvf lib64.tar.gz
  ```

- 进入库目录

  ```
  cd ./lib64
  或
  cd ./lib32
  ```

- 编译demo源码：

```
g++ -I. -L. testmain8.c -lbx_sdkDual -o testmain8
```

- 运行Demo

```C++
./testmain8 192.168.1.100
```

*192.168.1.100为LED控制卡的IP；