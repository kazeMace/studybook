# 在 windows7 与Visual Studio 2015 上搭建3DSlicer 方法（Debug）

>  推荐配置 （我的系统配置与环境）
>
>  * windows7 64bit
>  * Visual Studio 2015 （with update 3）
>  * Git GUI （最好是新版本）
>  * Cmake 3.12.3 win64版本（当前最新）

## Step 1（两种方法下载3DSlicer源码）

#### 方法1：使用```git clone``` 下载源码（这种方法克隆的源码为Nightly版本）

使用```git clone``` 命令克隆 ```github``` 上的 3DSlicer 源码

```bash
cd projectdir
git clone git://github.com/Slicer/Slicer.git
```

> 克隆时间因网速而已，克隆下来的文件夹大约 550 Mb
>
> ```projectdir``` 为项目文件夹
>
> 截止当前，```github``` 上最新的Slicer版本为4.9.0 nightly版本

#### 方法2 直接在github的Slicer仓库下载```master```分支源码（这种方法下载的源码版本为4.8.1正式版）

> https://github.com/Slicer/Slicer

## Step 2

克隆结束后会自动创建 Slicer 源代码文件夹，使用```git bash``` 进入slicer文件夹

```bash
cd Slicer
./Utilities/SetupForDevelopment.sh
```

> 为什么要使用```git bash``` 而不使用 ```cmd``` ？
>
> 因为```git bash```能够运行```.sh```文件	

在命令行中输入昵称和邮箱，回车完成

## Step 3

打开 Cmake-GUI

![微信截图_20181011124306](C:\Users\zhangbohang\Desktop\微信截图_20181011124306.png)

> 其中```Where is the source code``` 填 源码的路径
>
> ```where to build the binaries``` 填 编译的目标路径

然后点击Add Entry 按钮：

![微信截图_20181011123753](C:\Users\zhangbohang\Desktop\微信截图_20181011123753.png)

在弹出的对话框中输入```QT5_DIR``` , Type 选择 ```PATH``` , Value 设置为qt5的文件夹路径，如图所示：

![微信截图_20181011125654](C:\Users\zhangbohang\Desktop\微信截图_20181011125654.png)

在选择路径时需要注意：

* 我所用的Qt版本是5.10，其他版本没有测试，不能保证成功

* 进入到5.10.0文件夹下，有以下文件

  ![1539233170686](C:\Users\zhangbohang\AppData\Roaming\Typora\typora-user-images\1539233170686.png)

  其中我的Visual Studio版本为2015 Update3 64位版本，因此要进入```winrt_x64_msvc2015```文件夹中选择```qt5```文件夹。

  ![微信截图_20181011125742](C:\Users\zhangbohang\Desktop\微信截图_20181011125742.png)

  之后点击```Configure``` 按钮

  在弹出的对话框中

  ![1539233532292](C:\Users\zhangbohang\AppData\Roaming\Typora\typora-user-images\1539233532292.png)

选择对应的Visual Studio 版本， 其他选项都设置为默认。

点击Finsh完成

![微信截图_20181011125816](C:\Users\zhangbohang\Desktop\微信截图_20181011125816.png)

> 此时如果出错，则是大多是因为Entry的名字输入有误

当出现Configuring done时，点击```Generate``` 按钮完成生成

![微信截图_20181011130012](C:\Users\zhangbohang\Desktop\微信截图_20181011130012.png)

## Step 3

进入目标文件夹找到```Slicer.sln```文件

![1539234124165](C:\Users\zhangbohang\AppData\Roaming\Typora\typora-user-images\1539234124165.png)

打开Visual Studio 2015， 点击```文件``` --> ```打开``` -->```项目/解决方案```，选择Slicer.sln文件。

在导入了解决方案后，会发现有56个项目

![微信截图_20181011130507](C:\Users\zhangbohang\Desktop\微信截图_20181011130507.png)

注意：这个地方一定是56个，不然一定会出错

右键点击```ALL_BUILD```，选择```生成```

此时需要漫长的等待，依据不同的计算机的性能，时间不同，我等待了大约6个小时

但是，因为源码存在BUG，第一次编译一定会出错的，报错提示是缺少```vtkSlicerBaseCLIExport.h``` 文件，但是实际上是有的，在目标文件夹中搜索```vtkSlicerBaseCLIExport.h```可以找到，然后将这个文件扔进源码文件夹的```Base/CLI```路径下。

重读上边的操作，在此编译，这次应该就能成功了。

## Step 4

接下来就是启动。

在slicer_build文件夹下，我们可以看到一个Slicer.exe 文件，以及Slicer.sln解决方案文件，

![微信截图_20181011131920](C:\Users\zhangbohang\Desktop\微信截图_20181011131920.png)

按住shift键，右击选择```在此处打开命令窗口```

输入Slicer.exe --VisualStudio 命令启动VisualStudio

> 这里一定要这么做，不然会报各种各样的DLL缺失错误。

右键选择SlicerApp，选择设置为启动项

![微信截图_20181011132317](C:\Users\zhangbohang\Desktop\微信截图_20181011132317.png)

点击菜单栏的调试按钮

![微信截图_20181011132404](C:\Users\zhangbohang\Desktop\微信截图_20181011132404.png)

选择```开始调试```，差不多需要20分钟，项目就能启动了。

## 参数

- SlicerApp_APPLICATION_NAME: Custom application name to be used, instead of default "Slicer". The name is used in installation package name, window title bar, etc.
- Slicer_DISCLAIMER_AT_STARTUP: String that is displayed to the user after first startup of Slicer after installation (disclaimer, welcome message, etc).
- Slicer_DEFAULT_HOME_MODULE: Module name that is activated automatically on application start.
- Slicer_DEFAULT_FAVORITE_MODULES: Modules that will be added to the toolbar by default for easy access. List contains module names, separated by space character.
- Slicer_CLIMODULES_DISABLED: Built-in CLI modules that will be removed from the application. List contains module names, separated by semicolon character.
- Slicer_QTLOADABLEMODULES_DISABLED: Built-in Qt loadable modules that will be removed from the application. List contains module names, separated by semicolon character.
- Slicer_QTSCRIPTEDMODULES_DISABLED: Built-in scripted loadable modules that will be removed from the application. List contains module names, separated by semicolon character.
- Slicer_USE_PYTHONQT_WITH_OPENSSL: enable/disable building the application with SSL support (ON/OFF)
- Slicer_USE_SimpleITK: enable/disable SimpleITK support (ON/OFF)
- Slicer_BUILD_SimpleFilters: enable/disable building SimpleFilters. Requires SimpleITK. (ON/OFF)
- Slicer_BUILD_EMSegment: enable/disable building EM segmenter (ON/OFF)
- Slicer_USE_PYTHONQT_WITH_TCL: TCL support (ON/OFF)
- Slicer_EXTENSION_SOURCE_DIRS: Defines additional extensions that will be included in the application package as built-in modules. Full paths of extension source directories has to be specified, separated by semicolons.
- QT_QMAKE_EXECUTABLE
- Slicer_USE_SimpleITK  : OFF
- Qt5_DIR ：D:/software/Qt/5.10.0/msvc2015_64/lib/cmake/
- Subversion_SVN_EXECUTABLE

---

版本记录

Slicer4D3：slicer4.8.1；

Slicer4D4：slicer4.9nightly；按照官网操作








