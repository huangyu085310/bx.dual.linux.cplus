#  BX-G5/G6 C++ SDK

## 文档适用者

通过仰邦SDK对仰邦控制卡向LED显示屏发送显示内容的软件开发者；

SDK中包含较多可调用的函数接口；

本文档主要讨论发布显示内容的相关SDK接口



## 软件开发环境

x86_64 GNU/Linux

(GNU libc) 2.17及以上版本

SDK相关依赖库

onbon SDK



## 适用控制卡类型

仰邦5代、6代单双色图文控制卡;



## 发送内容的二种接口

节目接口和动态区接口：

需要频繁更新的显示内容，使用动态区接口来发送；反之，不需要频繁更新的显示内容，推荐使用节目接口来实现。

节目和动态区的内在区别：

节目：节目内容可以长期保存在控制卡的可记忆可擦除Flash中，所以控制卡掉电后，再重启，节目内容仍可以重新显示；但是Flash擦写次数是有限制的。

动态区：动态区内容保存在掉电后不保存数据的RAM中，所以显示内容掉电不保存，即再次对控制卡上电后，动态区的内容就没有了。但是RAM对于刷新次数没有限制，主要应用于更新频次高，更新速度比较快的场合。



## 动态区接口的使用

### 6代卡动态区的使用：

#### 发送动态区内容

步骤：

1.配置控制卡IP及端口

2.配置屏幕类型：单色、双色、三色

3.配置动态区参数：显示区的位置、大小、内容等

4.配置动态区内显示内容的属性：显示效果（移动速度等）、字体名称和大小等

5.调用发送动态区接口:[bxDual_dynamicArea_AddAreaTxtDetails_6G]()



代码实例：

```c++
/*1.配置控制卡IP及端口*/
	Ouint8* pIP = (Ouint8*)g_IP;//"192.168.0.181";
	Ouint32 nPort = 5005;

/*2.配置屏幕类型：单色、双色、三色*/
	E_ScreenColor_G56 color = eSCREEN_COLOR_DOUBLE;

/*3.配置动态区参数：显示区的位置、大小、内容等*/	
	{
		EQareaHeader_G6 aHeader1;
		aHeader1.AreaType = 0x00;
		aHeader1.AreaX = 0;// 16;
		aHeader1.AreaY = 0;
		aHeader1.AreaWidth = 64;
		aHeader1.AreaHeight = 32;
		aHeader1.BackGroundFlag = 0x00;
		aHeader1.Transparency = 101;
		aHeader1.AreaEqual = 0x00;
		
/*4.配置动态区内显示内容的特性：显示效果（移动速度等）、字体名称和大小等*/         	            		EQpageHeader_G6 pheader1;
		pheader1.PageStyle = 0x00;
		pheader1.DisplayMode = 0x04;
		pheader1.ClearMode = 0x01;
		pheader1.Speed = 10;
		pheader1.StayTime = 500;
		pheader1.RepeatTime = 1;
		pheader1.ValidLen = 128;
		pheader1.CartoonFrameRate = 0x00;
		pheader1.BackNotValidFlag = 0x00;
		pheader1.arrMode = eMULTILINE;//eSINGLELINE;//
		pheader1.fontSize = 12;
		pheader1.color = 0xff00ff;
		pheader1.fontBold = false;
		pheader1.fontItalic = false;
		pheader1.tdirection = pNORMAL;
		pheader1.txtSpace = 0;
		pheader1.Valign = 2;
		pheader1.Halign = 3;

/*5.调用发送动态区接口；*/        
        Ouint8 nAreaID = 0;
        Ouint8 str[] = "汉字测试123456789";
		bxDual_dynamicArea_AddAreaTxtDetails_6G(pIP, 5005, eSCREEN_COLOR_DOUBLE, nAreaID, &aHeader1, &pheader1, (Ouint8*)"./allfonts/1.ttf", (Ouint8*)str);
	}
```



#### 动态区涉及的数据结构说明

##### 动态区参数/动态区域属性

```C++
typedef struct
{
Ouint8	AreaType;		//区域类型；动态区时，固定为0x10;
Ouint16	AreaX;			//区域左上角横坐标
Ouint16	AreaY;			//区域左上角纵坐标
Ouint16	AreaWidth;		//区域宽度
Ouint16	AreaHeight;		//区域高度
Ouint8  BackGroundFlag; //是否有背景
Ouint8  Transparency;   //透明度
Ouint8  AreaEqual;      //前景、背景区域大小是否相同

/*下面的这个语音结构体EQSound_6G仅在动态区时使用；图文分区播放语音请使用单独的结构体：EQPicAreaSoundHeader_G6;*/
//使用语音功能时：部分卡需要配置串口为语音模式！！！
//不使用语音时，不需要考虑这个结构
EQSound_6G stSoundData;

}BXareaHeader_G6,EQareaHeader_G6;
```

##### 动态区内显示内容的属性：

```C++
typedef struct
{
Ouint8   PageStyle;			//数据页类型
Ouint8   DisplayMode;		//显示方式:0x00 –随机显示; 0x01–静止显示; 0x02–快速打出; 0x03–向左移动; 0x04 –向左连移； 0x05 –向上移动； 0x06 –向上连移；...0x25 –向右移动  0x26 –向右连移  0x27 –向下移动  0x28 –向下连移 其它参考6代卡动态区协议文档
Ouint8   ClearMode;			//退出方式/清屏方式
Ouint8   Speed;				//速度等级
Ouint16  StayTime;			//停留时间
Ouint8   RepeatTime;		//重复次数
Ouint16  ValidLen;			//此字段只在左移右移方式下有效
Ouint8   CartoonFrameRate;  //特技为动画方式时，该值代表其帧率
Ouint8   BackNotValidFlag;  //背景无效标志
//字体信息----------------------------------------------------------------------------
E_arrMode arrMode;			//排列方式--单行多行
Ouint16  fontSize;			//字体大小
Ouint32  color;				//字体颜色 E_Color_G56此通过此枚举值可以直接配置七彩色，如果大于枚举范围使用RGB888模式
Obool    fontBold;			//是否为粗体
Obool    fontItalic;		//是否为斜体
E_txtDirection tdirection;	//文字方向
Ouint16   txtSpace;			//文字间隔    
Ouint8 Halign; //横向对齐方式（0系统自适应、1左对齐、2居中、3右对齐）
Ouint8 Valign; //纵向对齐方式（0系统自适应、1上对齐、2居中、3下对齐）
//字体信息 结束
}BXpageHeader_G6,EQpageHeader_G6;
```



#### 动态区的删除

删除指定动态区：

```c++
	/*配置控制卡IP及端口*/
	Ouint8* pIP = (Ouint8*)g_IP;//"192.168.0.181";
	Ouint32 nPort = 5005;		

	/*指定要删除的动态区ID*/
	const Oint8 cnst_nAreaCount = 4;
	Oint8 pAreaID[cnst_nAreaCount];
	pAreaID[0] = 0;
	pAreaID[1] = 1;
	pAreaID[2] = 2;
	pAreaID[3] = 3;
	/*调用删除动态区的接口*/
	bxDual_dynamicArea_DelAreas_6G(pIP, 5005, cnst_nAreaCount, pAreaID);
```

删除所有动态区：

```c++
	/*配置控制卡IP及端口*/
	Ouint8* pIP = (Ouint8*)g_IP;//"192.168.0.181";
	Ouint32 nPort = 5005;		

	/*动态区ID==0xff时，表示要删除所有动态区*/
	Oint8 nAllID = 0xff;

	/*调用删除动态区的接口*/
	/*与指定删除动态区的接口相比，此接口函数名称中是DelArea，而不是DelAreas*/
	bxDual_dynamicArea_DelArea_6G(pIP, 5005, cnst_nAreaCount, nAllID);
```



## linux下Demo示例程序的编译和运行

### 相关库和头文件的说明

- #### onbon SDK依赖库

文件所在目录：./bx.dual.linux64.libs/lib64bxdualsdk.tar.gz 

主要包含cairo、iconv等第三方开源库；如果本地有相同的库，建议优先使用/链接到我们提供的这些库；

- #### onbon SDK库

文件所在目录：./bx.dual.linux64.libs/bx_dual_sdk.tar.gz

- #### 头文件

有3个，分别放在：

./bx.dual.linux64.libs/bx_dual_sdk.h ：所有接口函数的声明和注释；

./bx.dual.linux64.libs/bx_sdk_dual.h ：所有接口函数的别名，不是必要要包含的；

./bx.dual.linux64.libs/Obasic_types.h：基本数据类型的定义

### 动态区Demo源代码文件

./bx.dual.linux64.libs/testmain8.c

使用一个简单的main函数，再将上面介绍的 [发送动态区内容](#### 发送动态区内容) 中的函数放到这个main函数中进行编译、运行即可；完整的代码参考如下：

```C++
#include <stdio.h>

#include "bx_dual_sdk.h"
#include "bx_sdk_dual.h"

int testDynamic6G();

int main(int argc, char *arg[])
{
    
    testDynamic6G();
	
	return 0;
}

int testDynamic6G()
{
	/*1.配置控制卡IP及端口*/
	Ouint8* pIP = (Ouint8*)g_IP;//"192.168.0.181";
	Ouint32 nPort = 5005;

/*2.配置屏幕类型：单色、双色、三色*/
	E_ScreenColor_G56 color = eSCREEN_COLOR_DOUBLE;

/*3.配置动态区参数：显示区的位置、大小、内容等*/	
	{
		EQareaHeader_G6 aHeader1;
		aHeader1.AreaType = 0x00;
		aHeader1.AreaX = 0;// 16;
		aHeader1.AreaY = 0;
		aHeader1.AreaWidth = 64;
		aHeader1.AreaHeight = 32;
		aHeader1.BackGroundFlag = 0x00;
		aHeader1.Transparency = 101;
		aHeader1.AreaEqual = 0x00;
		//aHeader1.SoundFlag = 0x00;
		
/*4.配置动态区内显示内容的特性：显示效果（移动速度等）、字体名称和大小等*/         	            		EQpageHeader_G6 pheader1;
		pheader1.PageStyle = 0x00;
		pheader1.DisplayMode = 0x04;
		pheader1.ClearMode = 0x01;
		pheader1.Speed = 10;
		pheader1.StayTime = 500;
		pheader1.RepeatTime = 1;
		pheader1.ValidLen = 128;
		pheader1.CartoonFrameRate = 0x00;
		pheader1.BackNotValidFlag = 0x00;
		pheader1.arrMode = eMULTILINE;//eSINGLELINE;//
		pheader1.fontSize = 12;
		pheader1.color = 0xff00ff;
		pheader1.fontBold = false;
		pheader1.fontItalic = false;
		pheader1.tdirection = pNORMAL;
		pheader1.txtSpace = 0;
		pheader1.Valign = 2;
		pheader1.Halign = 3;

/*5.调用发送动态区接口；*/        
        Ouint8 nAreaID = 0;
        Ouint8 str[] = "汉字测试123456789";
		bxDual_dynamicArea_AddAreaTxtDetails_6G(pIP, 5005, eSCREEN_COLOR_DOUBLE, nAreaID, &aHeader1, &pheader1, (Ouint8*)"./allfonts/1.ttf", (Ouint8*)str);
	}
}
```

### 动态区Demo编译和运行

- 生成Demo可执行程序

将  [onbon SDK依赖库]() 和 [onbon SDK库]() 解压到 Demo源码 testmain8.c 文件所在目录，使用编译命令：

```
g++ -I. -L. testmain8.c -lbx_sdkDual -o testmain8
```

- 运行Demo

在当前目录下输入如下命令，运行Demo程序：

```C++
./testmain8
```

