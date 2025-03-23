---
title: 打包Java项目过程
date: 2025-03-24 10:35:45
tags:
---

## 1. 在 `IDEA` 中使用Maven构建工具构建项目

- #### 在项目根目录下创建`pom.xml`文件，配置项目依赖及打包插件（例如`maven-jar-plugin`）


- #### 根据图片指引进行项目构建

<img src="https://s2.loli.net/2025/03/23/DfCT9q3tziaAlkp.png" alt="packaging-java-project-workflow-01.png" style="width:100%; height:100%;">

---

## 2. 整理构建好的 JAR 文件到指定目录

- #### 创建目标目录

  在你的系统上创建一个文件夹用于存放你的 JAR 文件和其他相关文件。

- #### 复制构建好的 JAR 包

  - 将通过 `mvn clean package` 生成的主 JAR 文件（例如 `outboundmanagement-1.0-SNAPSHOT.jar` 或带依赖的 `outboundmanagement-1.0-SNAPSHOT-jar-with-dependencies.jar`）复制到刚刚创建的目录中。
  - 如果你的项目生成了多个 JAR 文件，确保所有需要的 JAR 文件都被复制到这个目录内。

- #### 确认依赖是否完整

  - 如果你使用了带依赖打包（jar-with-dependencies），一个 JAR 文件中通常包含了所有运行所需的类库，这种情况下只需要一个 JAR 文件。
  - 如果你采用了独立 JAR 的方式，那么除了主 JAR，还需要将依赖的 JAR 文件也放入目录中。

<img src="https://s2.loli.net/2025/03/23/GeZ6fX4wFAYazlc.png" alt="packaging-java-project-workflow-02.png" style="width:100%; height:100%;">

---

## 3. 添加 JRE 到项目目录

为了确保用户在没有预装 JRE 的系统上也能运行你的应用程序，我们需要将一个兼容的 JRE 放到项目目录内。具体步骤如下：

- #### 选择并下载合适的 JRE 和 javafx-jmods

  请下载与你的 Java 应用兼容的 JRE 版本（例如 OpenJDK 或 AdoptOpenJDK），确保版本与项目编译使用的 Java 版本相匹配（例如 JDK 17 对应的 JRE）。

  > [javafx-jmods 点击这里下载](https://gluonhq.com/products/javafx/)
  >
  > [jdk 点击这里下载](https://jdk.java.net/archive/)

- #### 解压并复制 JRE 和 javafx-jmods

  解压下载的 JRE 和 javafx-jmods 压缩包。将整个 JRE 文件夹和 javafx-jmods 文件夹复制到你的项目主目录中。

- #### 验证文件结构

  确保在 `H:\Code\obm.exe\output` 目录下有如下结构：

  - `dist` 文件夹，内含你的 JAR 文件
  - `jre17` 文件夹，内含完整的 `JRE` 和 `javafx-jmods`

<img src="https://s2.loli.net/2025/03/23/EeQ1KGmFJjC92np.png" alt="packaging-java-project-workflow-03.png" style="width:100%; height:100%;">

---

## 4. 使用 jlink 生成自定义运行时镜像

- #### 理解 jlink 工具

  - **作用**：jlink 根据你指定的模块创建一个自定义的运行时镜像。这样你只需要打包应用程序需要的部分，而不是整个 JDK。
  - **模块来源**：模块位于 JDK 安装目录的 `bin` 目录下。

- #### 确定应用程序需要哪些模块

  - 检查你的应用是否依赖特定的 Java 模块。比如，一个典型的桌面应用可能至少需要：
    - `java.base`（所有 Java 应用必需）
    - `java.desktop`（如果使用 Swing、AWT 或 JavaFX 等）
  - 根据项目使用的功能和第三方库，可能还需要其他模块（例如 `java.logging`、`java.sql` 等）。

- #### 使用 jlink 生成自定义运行时镜像

  假设你的 JDK 安装目录为 `C:\path\to\jdk-17`，在命令行中执行类似下面的命令（请根据你的需求调整模块列表）：

  ~~~bash
  jlink --module-path "C:\path\to\jdk-17\jmods" --add-modules java.base,java.desktop --output C:\MyJavaApp\jre-custom
  ~~~

  如果要在 `IDEA` 终端进行打包，则推荐使用以下命令：

  ~~~bash
  & "C:\path\to\jdk-17\bin\jlink" --% --module-path "C:\path\to\jre17\jdk-17\jmods;C:\path\to\jre17\javafx-jmods-17.0.14" --add-modules java.base,java.desktop --output "C:\path\to\output\jre-custom"
  ~~~

  我根据我的项目内容和之前步骤的调整的命令如下：

  ~~~bash
  & "H:\Code\obm.exe\output\jre17\jdk-17\bin\jlink" --% --module-path "H:\Code\obm.exe\output\jre17\jdk-17\jmods;H:\Code\obm.exe\output\jre17\javafx-jmods-17.0.14" --add-modules java.base,java.logging,java.xml,java.naming,java.sql,java.desktop,java.management,javafx.controls,javafx.fxml --output "H:\Code\obm.exe\output\jre-custom"
  ~~~

<img src="https://s2.loli.net/2025/03/23/C3TBXGaSkJeOH5D.png" alt="packaging-java-project-workflow-04.png" style="width:100%; height:100%;">
<img src="https://s2.loli.net/2025/03/23/4Xx8UlMZf1D6Br5.png" alt="packaging-java-project-workflow-05.png" style="width:100%; height:100%;">

---

## 5. 使用 Launch4j 创建 .exe 文件

- #### 下载并安装 Launch4j

  安装过程就暂时不做展示了。

  > [Launch4j 点击这里下载](http://launch4j.sourceforge.net)

- #### 启动 Launch4j 并创建新配置

  打开 Launch4j 的图形界面，点击“新建配置”或直接在空白配置中开始设置。

- #### 配置基本参数

  **Output file**：设置生成的 .exe 文件的路径，这里的文件路径使用我们之前的打包目录。

  **Jar**：指定我们之前的主 JAR 文件路径，这里使用的是相对路径。

  这里要勾选"Don't wrap the jar, launch only"

<img src="https://s2.loli.net/2025/03/23/MLHCqDhipwEcNWS.png" alt="packaging-java-project-workflow-06.png" style="width:100%; height:100%;">

- #### 设置主类

  在“Header”选项卡中，确保“Main class”字段填写了你的应用主类全限定名

<img src="https://s2.loli.net/2025/03/23/ydnfgBToCucSiFm.png" alt="packaging-java-project-workflow-07.png" style="width:100%; height:100%;">

- #### 配置 JRE 参数

  转到 “JRE” 选项卡，做如下设置：

  - **Min JRE version**：填写 `17`（与你的应用编译版本对应）。
  - **JRE paths**：指定生成的自定义运行时镜像路径。

<img src="https://s2.loli.net/2025/03/23/vCh819PpVXuFWO6.png" alt="packaging-java-project-workflow-08.png" style="width:100%; height:100%;">

- #### 构建 .exe 文件

  点击“Build Wrapper”按钮，Launch4j 将生成 .exe 文件。如果构建过程中没有错误，你将会在指定的输出目录下看到生成的 .exe 文件。

<img src="https://s2.loli.net/2025/03/23/3TY9hujQWvE8olV.png" alt="packaging-java-project-workflow-09.png" style="width:100%; height:100%;">

- #### 检查可执行程序是否能像在 `IDEA` 中一样正常运行，最好把所有功能都测试一遍，以防在前面使用 jlink 生成运行时镜像时少加了某些模块

<img src="https://s2.loli.net/2025/03/23/lPVWNQ8ETyk5Dau.png" alt="packaging-java-project-workflow-10.png" style="width:100%; height:100%;">

- #### 在这里可以将 `jre17` 文件夹删除，因为在 `jlink` 生成自定义运行时镜像时就已使用完毕，如果保留会导致下一步生成的安装程序包含多余的运行时文件

---

## 6. 通过 Inno Setup 生成一个基本的安装程序

- #### 下载并安装 Inno Setup

  安装过程就暂时不做展示了。

  > [Inno Setup 点击这里下载](https://jrsoftware.org/isdl.php)

  下载最新版本的 Inno Setup 并安装，我这里使用的是 **innosetup-6.4.2** 。

- #### 启动 Inno Setup 向导

  打开 Inno Setup 后，选择“Create a new script file using the Script Wizard”（使用脚本向导创建新脚本）。

<img src="https://s2.loli.net/2025/03/24/tfSZC6BrToIVi32.png" alt="packaging-java-project-workflow-11.png" style="width:80%; height:80%;">
<img src="https://s2.loli.net/2025/03/24/WQzsjGEZBNwD1go.png" alt="packaging-java-project-workflow-12.png" style="width:80%; height:80%;">

​	向导会一步步引导你设置安装程序的基本信息。

- #### Application Information

  在此步骤中，需要填写应用程序的基本信息：

  - **Application name**：应用程序的名称，例如 “Outbound Management”。
  - **Application version**：应用程序的版本号，例如 “1.0.0”。
  - **Application publisher**：发布者名称，可填公司名或个人名。
  - **Application website**：如果有官网或相关链接，可在此处填写。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/WgiTowFA1tbJklK.png" alt="packaging-java-project-workflow-13.png" style="width:80%; height:80%;">

- #### Application Folder

  在此步骤中，需要指定应用程序的默认安装目录：

  - **Application destination base folder**：通常选择 “Program Files folder”，即默认安装在 `C:\Program Files\` 下。
  - **Application folder name**：指定安装后文件夹的名称，例如 “Outbound Management”。
  - 如果希望用户可修改安装路径，勾选 **Allow user to change the application folder**。
  - 如果应用程序不需要安装到特定文件夹（例如仅部署几个独立文件），可勾选 **The application doesn't need a folder**。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/bE8YIgwXScTWkFr.png" alt="packaging-java-project-workflow-14.png" style="width:80%; height:80%;">

- #### Application Files

  在此步骤中，主要是指定要打包进安装程序的文件。

  - **Application main executable file**：指定应用程序的主可执行文件路径。通常是你打包后生成的 `.exe` 文件，例如 `H:\Code\obm.exe\output\obm.exe`。
  - 如果希望安装结束后让用户可立即运行程序，可以勾选 **Allow user to start the application after Setup has finished**。
  - 若应用程序没有主可执行文件（例如只是一些数据文件或库文件），可以勾选 **The application doesn't have a main executable file**。
  - **Other application files**：可以使用通配符（如 `H:\Code\obm.exe\output\*`）来包含此文件夹下所有文件，或者根据需要手动添加文件或文件夹。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/XL1AdN5xnWie4Ct.png" alt="packaging-java-project-workflow-15.png" style="width:80%; height:80%;">

- #### Application File Association

  如果你的应用并不需要在系统中关联特定的文件扩展名（例如双击某个文件就自动用你的程序打开），可以**取消勾选** “Associate a file type to the main executable”，或在该向导页保留默认设置（不填写任何扩展名）。

    若你确实需要为应用配置文件关联（例如扩展名 `.myp`），可在此处填写：

    - **Application file type name**：如 *Outbound Management File*
    - **Application file type extension**：如 `.myp`

  然后在脚本中进一步添加 `[Registry]` 或 `[Tasks]` 配置来实现更完整的文件关联逻辑。

  > 但就你提供的脚本而言，当前并没有相关条目，因此一般建议**留空或取消勾选**。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/hpgsIMtxEOnqdYQ.png" alt="packaging-java-project-workflow-16.png" style="width:80%; height:80%;">

- #### Application Shortcuts

  在此步骤中，主要设置要为应用程序创建的快捷方式：
  - **Create a shortcut to the main executable in the Start Menu Programs folder**：是否在开始菜单中创建快捷方式，通常勾选。
  - **Application Start Menu folder name**：自定义开始菜单文件夹名称，例如 “Outbound Management”。
  - 其他可选项：
    - **Allow user to change the Start Menu folder name**：允许用户自定义开始菜单文件夹名。
    - **Allow user to disable Start Menu folder creation**：允许用户选择不创建开始菜单文件夹。
    - **Create an Internet shortcut in the Start Menu folder**：在开始菜单中创建一个指向网站的快捷方式。
    - **Create an Uninstall shortcut in the Start Menu folder**：在开始菜单中创建卸载程序的快捷方式。
  - **Other shortcuts to the main executable**：可勾选 **Allow user to create a desktop shortcut** 来在桌面上创建快捷方式。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/oPA5lf2bD1CtdRF.png" alt="packaging-java-project-workflow-17.png" style="width:80%; height:80%;">

- #### Application Documentation

  该向导用于指定安装过程要显示的文档：

    - **License file**：如果有软件许可协议文本文件（例如 `license.txt`），可在此处指定；脚本中并未引用此项，因此可以留空。
    - **Information file shown before installation** / **after installation**：如果想在安装前/后显示额外说明文件（TXT、RTF等），可指定路径；脚本中未配置，可留空。

  如果你没有这些文档要展示，**全部留空**即可。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/d6Hmozx3Ffbwsjt.png" alt="packaging-java-project-workflow-18.png" style="width:80%; height:80%;">

- #### Setup Install Mode

  脚本中并未显式指定安装模式，一般可以选择：

    - **Administrative install mode (install for all users)**：默认选项，表示以管理员方式安装，供所有用户使用。
    - 也可视需求选择“Non administrative install mode (install for current user only)”或让用户自行选择。

  在多数情况下，**保留默认的 “Administrative install mode”** 即可。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/rUCTNhYp3J4bQ2H.png" alt="packaging-java-project-workflow-19.png" style="width:80%; height:80%;">

- #### Application Registry Keys And Values

  如果没有需要写入或修改注册表的特殊需求，或者脚本中未配置 `[Registry]` 条目，就可以**不导入任何 .reg 文件**，并保留默认勾选（删除空键值等）即可。

    - **Windows registry file (.reg) to import**：留空
    - 下面的删除选项默认勾选即可（表示卸载时清理空键值）。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/E3H8L5aq7tD2Nv4.png" alt="packaging-java-project-workflow-20.png" style="width:80%; height:80%;">

  - #### Setup Languages

      脚本中未显式指定语言，但示例中有提到可能需要中文或英文：

      - 如果只需要英文，**只勾选 “English”**。
      - 若要支持中文，请勾选 “Chinese (Simplified)” 或 “Chinese (Traditional)”（Inno Setup 默认可能未内置中文，需要额外放置语言文件或在安装时勾选）。
      - 其他语言可按需要自行勾选或保留默认。

    点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/DNk3aw4BMtFi7mE.png" alt="packaging-java-project-workflow-21.png" style="width:80%; height:80%;">

- #### Compiler Settings

  与脚本中 `[Setup]` 段落对应的配置：

    - **Custom compiler output folder**：对应 `OutputDir=Output`

      > 这里可填写 `.\Output` 或直接 `Output`，确保和脚本中一致。

    - **Compiler output base file name**：对应 `OutputBaseFilename=OutboundManagementSetup`

      > 即最终生成的安装包名称为 `OutboundManagementSetup.exe`。

    - **Custom Setup icon file**：如需定制安装程序图标，可在此指定 ICO 文件路径；脚本未配置则留空。

    - **Setup password**：如果需要给安装程序加密码可填；脚本中无此配置，一般留空。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/NUEW129pTbBRrmk.png" alt="packaging-java-project-workflow-22.png" style="width:80%; height:80%;">

- #### Inno Setup Preprocessor

  该选项是**是否在脚本中启用 #define 等预处理指令**，对脚本本身的功能影响不大，纯粹看你是否需要使用预处理指令简化脚本编写：

    - **Yes, use #define compiler directives**：如果你希望后续在脚本中使用 `#define` 变量。
    - **不勾选**则脚本不会自动生成预处理指令模板。

  通常保留默认勾选也无妨；若不需要则可以取消。

  点击“Next”继续。

<img src="https://s2.loli.net/2025/03/24/QaeMrXY8CSwUov6.png" alt="packaging-java-project-workflow-23.png" style="width:80%; height:80%;">

- #### 当向导执行到最后一步，会显示“**You have successfully completed the Inno Setup Script Wizard**”

  说明脚本向导已结束，可点击“**Finish**”退出向导。

  退出后，可在 Inno Setup Compiler 中查看或修改生成的脚本。

  如需编译，点击“**Compile**”或使用菜单栏中的相应选项进行打包。

<img src="https://s2.loli.net/2025/03/24/gP9bHJvCWMFLDU8.png" alt="packaging-java-project-workflow-24.png" style="width:80%; height:80%;">

- #### Would you like to compile the new script now?

  在此对话框中，Inno Setup 会询问是否要立即编译新生成的脚本。

  - 如果选择“**是(Y)**”，则会直接开始编译，生成安装程序。
  - 如果选择“**否(N)**”，则暂不编译，可稍后再手动执行编译操作。
  - 也可以选择“**取消**”以中断当前操作。
  - 这里建议选择“**是(Y)**”。

<img src="https://s2.loli.net/2025/03/24/cpWvk8C6OM5jQfs.png" alt="packaging-java-project-workflow-25.png" style="width:100%; height:100%;">

- #### Would you like to save the script before compiling?

  在开始编译前，Inno Setup 会询问是否先保存脚本：

  - 选择“**是(Y)**”可先保存 `.iss` 脚本文件，再开始编译。
  - 选择“**否(N)**”则会在不保存脚本的情况下继续编译，此时生成的安装文件会默认存放在 “My Documents” 文件夹下。
  - 若选择“**取消**”，则会中断编译流程。

  建议在编译前先保存脚本，便于后续维护和修改。

<img src="https://s2.loli.net/2025/03/24/K6BI5ngb2dkiMt1.png" alt="packaging-java-project-workflow-26.png" style="width:100%; height:100%;">

- #### 选择“是(Y)”之后，会弹出另存为对话框

  此时会弹出“**另存为**”对话框，指定脚本文件的保存路径与名称：

  - 在“**文件名(N)**”中输入脚本文件名，例如 “`01.iss`”。
  - 确认“**保存类型(T)**”为 “Inno Setup Script files (*.iss)”。
  - 选择合适的保存位置（如项目的 `output` 文件夹），然后点击“**保存(S)**”。

<img src="https://s2.loli.net/2025/03/24/C6BJHdOUrZmyxuA.png" alt="packaging-java-project-workflow-27.png" style="width:100%; height:100%;">

- #### 保存成功后，会自动返回编译界面进行后续操作。

  此时可以在 Inno Setup Compiler 的下方看到编译进度与输出日志：

  - 编译器会逐一压缩指定的文件，如 `.exe`、`.dll`、`.jar` 或其他资源文件。
  - 编译完成后，输出窗口会显示 “Finished.” 字样，并给出编译耗时信息。
  - 若编译过程中出现错误或警告，会在此处提示，需根据日志信息进行排查。

  编译结束后，生成的安装程序可在脚本中指定的 `OutputDir` 文件夹中找到。

<img src="https://s2.loli.net/2025/03/24/LYv9DwXWe3nxuOS.png" alt="packaging-java-project-workflow-28.png" style="width:100%; height:100%;">

- #### 生成后，可以在一个干净的环境下（或者模拟没有预装 Java 的环境）测试安装，确认安装目录中包含你的所有文件，并且双击启动 EXE 能够正确运行。

<img src="https://s2.loli.net/2025/03/24/MsOInKPJqUzSgiw.png" alt="packaging-java-project-workflow-29.png" style="width:100%; height:100%;">

---

## 7. 如果没什么问题，那么至此你已经成功完成了 `Java项目` 打包成安装程序的成就！
