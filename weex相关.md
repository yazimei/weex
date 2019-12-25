# weex

## 起步

环境：java、Android studio、node.js、安卓模拟器(雷电模拟器等)

打开Android studio点击Configure选择SDK Manage，添加sdk-26以及26以上的sdk.

### Java安装及环境配置

下载地址([<https://www.oracle.com/technetwork/java/javase/downloads/jdk13-downloads-5672538.html>])点击同意协议，选择Windows环境对应的jdk下载(exe安装或者zip解压缩安装)。
环境变量设置：电脑 -> 控制面板 -> 系统和安全 -> 系统，选择高级系统设置 -> 环境变量， 在系统变量(S)新建两个变量，分别是:(变量名大小写不影响)

* 变量名：JAVA_HOME
 变量值：*指向jdk安装地址*如：C:\Program Files\Java\jre1.8.0_231
* 变量名：CLASSPATH
变量值： .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;

编辑系统变量Path，新增值：%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;

验证Java环境变量是否修改成功：
在cmd命令板输入

```cmd
java -version
```

修改成功会打印下列字符：

```String
java version "1.8.0_231"
Java(TM) SE Runtime Environment (build 1.8.0_231-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.231-b11, mixed mode)
```

### ADB校验

windows环境搜索cmd，命令行进入adb.exe存放的文件夹，（一般放在\AppData\Local\Android\Sdk\platform-tools）

```vim
// 如下
cd C:\Users\zxy\AppData\Local\Android\Sdk\platform-tools
C:\Users\zxy\AppData\Local\Android\Sdk\platform-tools> adb devices
```

如果打印的形式如下：

```String
adb server version (31) doesn't match this client (41); killing...
* daemon started successfully
List of devices attached
emulator-5554   device
```

用Android的adb.exe替换模拟器的adb.exe

将adb.exe的文件路径如：C:\Users\zxy\AppData\Local\Android\Sdk\platform-tools添加到用户环境变量，不用进入adb.exe文件夹就可以使用adb命令。

adb的命令功能参考连接[<https://github.com/mzlogin/awesome-adb>]

如果已经连接模拟器或者测试机，打印不出设备列表，可以尝试从SDK Manager窗口的Extras文件夹中安装Google USB驱动程序，或者从手机生产商官网下载驱动设备。一般安卓10以上设备可以通过连续点击设备版本号七次以上进入开发者模式。

### 编译器vscode

插件：vscode-weex、weex-run、weeex-lang、weex-debugger
ctrl+shift+P(具体看个人设置)进入命令行输入Weex: New Project根据提示写入文件名等
工程新建完成后要运行命令：npm i ，以安装相关依赖。

## 编译

编译页面

```npm
weex compile src build
// weex compile src/index.vue build
```

## 问题

### weex 命令出错

 weex : 无法加载文件 C:\Users\AppData\Roamingzhan\npm\weex.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go
.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess

window 解决：搜索Windows PoweShell 以管理员身份运行，cd到被禁止脚本所在文件夹，执行命令

set-ExecutionPolicy RemoteSigned

选择“Y”

### async和await支持问题

weex 默认支持JavaScript、ES6、ES7，对于async和await需要对.babelrc文件进行配置
首先执行命令：`npm install --save-dev babel-plugin-transform-runtime`

其次加入：

```json
"plugins": [
    [
      "transform-runtime",
      {
        "helpers": false,
        "ployfill": false,
        "regenerator": true,
        "moduleName": "babel-runtime"
      }
    ]
  ]
```

### weex run android问题

打开模拟器或连接真机，执行命令：`weex run android`
如果出现下列问题

```String
* What went wrong: Execution failed for task ':app:compileDebugJavaWithJavac'. > Could not find tools.jar. Please check that C:\Program Files\Java\jre1.8.0_231 contains a valid JDK installation.
```

解决方法（仅对windows环境）：
首先按照*Java安装及环境配置*校验是否完成环境变量修改
其次打开Android Studio安装文件夹 -> jre文件夹 -> lib文件夹 -> 复制tools.jar到java -> jre1.8.0_231 -> lib文件夹

* 如果下载安装的sdk是zip压缩包的文件可以是jdk-13.0.1文件夹，同时环境变量home指向也应当是对应的jdk位置，jre或jdk后面跟随的数字为当前版本。

### weex默认只支持单页面

想要app原生效果，weex需要能支持多页面
