# InternetFashionedInstaller
一个用InnoSetup仿好压安装程序的脚本模板，改一改背景图片就可以一键打包属于自己的美观的互联网风格的安装程序

## 效果
* 窗口无边框
* 自定义按钮、标题栏以及窗口背景
* 点住窗口中除按钮和输入框以外的地方均可拖动窗口
* 自定义取消安装时弹出的那个提示框，并可拖动
* 点击*许可协议*按钮，便会跳转到许可协议的网址
* 窗口可伸缩，点击*自定义安装*按钮，窗口变长并显示安装路径输入框以及其他按钮
* 安装前判断是否已经安装将要安装的软件，若已经安装，则将目标安装路径切换为之前的安装路径，并禁止用户修改安装路径
* 自定义安装进度条
* 安装界面显示安装百分比
* 安装程序和应用程序互斥
* 限制只能运行一个安装实例
* 若已安装新版本，则禁止安装旧版本
* 可控制安装程序运行在特定版本的 Windows 操作系统上
* 可控制安装程序是否支持运行在32位/64位操作系统上
* 可制作绿色安装包，即只释放文件（也可以进行创建快捷方式和读写INI文件等不需要管理员权限的操作），而不写入任何注册表条目，也不生成卸载程序，但同时也不能获取以前安装的版本了，也不能获取以前安装的路径了，因为没有写注册表，绿色安装模式下，运行安装程序不会弹出UAC提示框
* 其他安装程序应该有的功能

## 截图
![01](/Snapshot/01.PNG)
![02](/Snapshot/02.PNG)
![03](/Snapshot/03.PNG)
![04](/Snapshot/04.PNG)

## 支持平台
* 此脚本打包的安装程序已在**Microsoft Windows 7 SP1、Windows 8.1 with Update 1 以及 Windows 10 1703 32位&64位操作系统**上测试通过，目标功能全部完美实现，无任何BUG
* 若要使安装程序支持更早版本的操作系统，请注释掉代码中的`#define Windows7SP1AndNewer`，但不能保证安装程序的最终效果

## 编译
1. 下载我提供的这个脚本模板（包含所有资源文件）并解压
2. [下载最新版的**Unicode**版**Inno Setup**](http://jrsoftware.org/isdl.php)并安装
3. 在代码文件（**Setup.iss**）所在的目录下新建**App**文件夹
4. 把您要打包的所有文件及文件夹都放到上一步新建的**App**下
5. 打开**Setup.iss**，根据需求，修改编译器指令，并修改**MyAppID**、**MyAppName**和**MyAppVersion**等涉及到具体项目的信息
6. 若有创建快捷方式和向注册表中写入条目的需求，请取消代码中**Icons**和**Registry**区段的注释，并自行添加相关脚本
7. 修改**tmp**文件夹下的图片素材，替换代码目录下的**Setup.ico**
8. 编译

## 注意事项
1. 此脚本支持**官方原版编译器、SkyGZ（风铃夜思雨）增强版编译器以及Restools增强版编译器**
2. 一定要使用**Unicode**版编译器，尽量使用**最新版**，最低不能低于 5.5.0 版，若您的编译器版本低于 5.5.9 ，请将代码中的`#IF VER < EncodeVer(5,5,9)`宏注释掉
3. 安装程序所有用到的UI图片都在**tmp**文件夹下，在不改变图片尺寸的前提下，您可以自由修改图片素材，以此来打造属于您自己的安装程序
4. 如果您改变了图片素材的尺寸，请注意**一并修改代码**，否则安装程序的外观会出现异常
5. 若要生成绿色版安装程序（不向注册表中写入任何条目且不生成卸载程序），请将代码中的`#define PortableBuild`取消注释
6. 若您要打包的项目为64位项目，请将代码中的`#define x64Build`取消注释
7. 若您要使程序注册相关后缀名，请将代码中的`#define RegisteAssociations`取消注释
8. 推荐使用[**Inno Script Studio**](https://www.kymoto.org/products/inno-script-studio/downloads)

## 心得体会
1. Inno Setup 给安装程序提供了丰富的接口，搭配 botva2.dll ，可以实现任何你想要实现的界面和功能，可定制程度很高，但 Inno Setup 给卸载程序提供的接口很少，搭配 botva2.dll ，也只能做到换换窗口背景，改改按钮的文字及位置等，可定制程度很低。因此，若需要完全自定义的卸载程序，我的建议还是用其他的语言或工具（比如 C# 啦、 Qt 啦）专门写一个卸载程序，只写一个界面就可以了，可以通过使用参数`/SILENT`调用卸载程序的“静默卸载”模式，来完成具体的卸载工作。当然，如果采用这种方法，一是要改写注册表的卸载条目，让注册表指向你的卸载程序，而不是原来那个，二是要注意把你自己的卸载程序复制到临时文件夹再启动，不然卸载程序删不掉你那个卸载程序，会有残留的文件，岂不很尴尬。
2. 使用 botva2.dll 有一个要注意的地方，那就是在安装程序窗口关闭前，一定要调用其提供的`gdipShutdown()`函数，然后再调用 Inno Setup 提供的`WizardForm.Release()`函数释放窗口资源（不必再调用`WizardForm.Close()`，它会重复释放资源的动作），并且保证整个安装过程这两个函数只执行一遍，而且调用顺序一定不能搞错，此时安装程序的窗口就会正常关闭，安装程序的退出代码也是正常的，否则安装程序的窗口就会在关闭时卡住好一会，退出代码也很诡异，在 Windows 7 操作系统上还会直接报错。
3. 不知为什么，在32位安装模式下，读取注册表条目时， Windows 7 操作系统不会自动重定向，要单独指定64位条目的路径，而在更新的系统上，则会自动重定向，不用专门指定64位条目的路径，经过我的测试，在64位安装模式下（脚本中指定`#define x64Build`的意思，不只是单纯的在64位操作系统中运行的意思），没有这个问题。
4. 其实 NSIS 也能实现这种界面，同样也是借助 botva2.dll ，但我感觉 NSIS 对新手不太友好啊，关键字很多，而且官方没有提供任何编辑器，只有一个编译器，写脚本只能用记事本，也真是够了，不过还好有第三方的编辑器，我用过，还挺好用的，但我还是推荐用 Inno Setup ，确实很好上手，而且默认的压缩算法是 7-Zip 固实压缩，压缩率很高。
5. `#ifdef`、`#ifndef`、`#else`和`#endif`等编译器指令还是很好用的，尤其是经常需要修改功能或者模块时，超级方便的。
