# MacTools - macOS 动态库注入与逆向辅助工具箱

**MacTools** 是一款现代化的 macOS 原生应用程序，专为安全研究人员、逆向工程师和开发者设计。它提供了一站式的可视化界面，用于自动化管理 macOS 应用的动态库注入（Dylib/Framework Injection）流程，并集成了多种实用的二进制分析与处理工具。

## 截图
<img width="300" height="200" alt="e7fcf85056f1df696b5cf8cd73f52241" src="https://github.com/user-attachments/assets/0a117c0b-7ff8-4032-9077-e9b24f548344" />
<img width="300" height="200" alt="46df958d6680577c99832376d2334f57" src="https://github.com/user-attachments/assets/3b8f139b-b479-4f2b-a999-43189c033761" />
<img width="300" height="200" alt="eb3c5949be965846b9c1c100508d238d" src="https://github.com/user-attachments/assets/05d4eb2d-31d2-41d2-b87f-d131fd17e857" />
<img width="300" height="200" alt="eb7590386455db2a0a8985de327aa917" src="https://github.com/user-attachments/assets/b7febbc9-4043-4978-bf4f-f2120581a9cf" />
<img width="300" height="200" alt="fad8e8e506257692dec7431fb8691500" src="https://github.com/user-attachments/assets/651b7c2d-8dcb-47ba-aefa-ff0c2e2cff20" />
<img width="300" height="200" alt="476b428ffce8b7a7b5ec3d35f7d30808" src="https://github.com/user-attachments/assets/44f2900f-93c8-445e-b24e-facaacd24b04" />
<img width="300" height="200" alt="16643f733d3996e37603006422f0ebbf" src="https://github.com/user-attachments/assets/a7b4bc84-330e-4d2b-8075-ea3b94c96f8e" />
<img width="300" height="200" alt="8d825c6612fd0d657d704b66688b59a2" src="https://github.com/user-attachments/assets/82752fe7-77f4-4aeb-9eb8-24b088307ab5" />
<img width="300" height="200" alt="7aace1d4f9a9ec39132250d4c7f8b10b" src="https://github.com/user-attachments/assets/348dd058-2a54-47ea-bd51-df8d2c8f2a52" />


## 核心功能详解

### 1. 动态库注入 (Injector)

这是 MacTools 的核心模块，旨在将繁琐的命令行注入流程及其自动化。它不仅仅是简单的文件复制，通过内置的 Mach-O 解析引擎，能够智能地处理注入过程中的每一个环节。

**功能特性：**
*   **智能路径处理**：自动处理 App 包结构，将库文件安全复制到 `Contents/Frameworks` 目录，若目录不存在会自动创建。
*   **二进制补丁**：直接解析并修改目标 App 主程序的 Load Commands，插入 `LC_LOAD_DYLIB` 指令。
*   **权限与签名修复**：
    *   自动检测文件权限，必要时请求管理员权限（Sudo）进行操作。
    *   移除文件隔离属性（Quarantine/Gatekeeper），防止系统拦截。
    *   注入完成后执行 Ad-hoc 重签名（Codesign），确保 App 修改后仍能正常启动。

**使用指南：**
1.  启动 MacTools，等待应用列表加载完成（支持列表模式和网格模式切换）。
2.  在左侧导航栏选择你想要注入的目标应用程序，可以使用顶部的搜索框快速定位。
3.  确保顶部选项卡处于 **“注入”** 模式。
4.  将编译好的 `.dylib` 动态库或 `.framework` 文件夹拖拽到右侧的“注入配置”区域（支持一次性拖拽多个文件）。
5.  点击底部的 **“开始注入”** 按钮。
6.  如果目标 App 需要更高权限（如系统应用或从 App Store 下载的应用），弹窗提示时请输入管理员密码。
7.  观察下方“执行终端”的日志输出，等待提示“注入全部完成”。
8.  现在你可以直接从 Finder 或 Launchpad 启动修改后的 App。

### 2. 逆向工具箱 (Toolbox)

工具箱模块集合了一系列针对 Mach-O 二进制文件（可执行文件、动态库）的常用逆向分析工具。

**操作对象：**
工具箱支持两种操作模式：
*   **直接拖拽**：将任意文件（.o, .dylib, .deb, 可执行文件）拖拽到工具箱顶部的区域。
*   **应用联动**：如果在左侧选择了 App，工具箱默认会将该 App 的主程序作为操作对象。

**功能列表与使用：**

*   **生成 Tweak 项目 (智能模式)**
    *   **用途**：基于动态库快速构建 Theos 开发环境与 Frida 调试脚本。
    *   **深度分析**：工具会自动解析二进制文件中的 Objective-C 类结构。
    *   **代码生成**：
        1.  **Tweak.xm**：自动生成包含所有识别类的 Logos Hook 模版，省去手动编写 `%hook` 的繁琐工作。
        2.  **trace.js**：自动生成配套的 Frida 追踪脚本。该脚本已内置好所有类的方法 Hook 逻辑，可直接用于追踪该动态库的所有方法调用与参数返回。

*   **签名信息检查**
    *   **用途**：查看二进制文件的代码签名详情、Team ID、Entitlements 权限声明。
    *   **操作**：点击按钮，详细信息将输出到底部的控制台日志中。

*   **修改 TeamID**
    *   **用途**：用于绕过基于 Team ID 的校验机制（如某些 App 检测插件是否由同一开发者签名）。
    *   **操作**：点击按钮，在弹出的对话框中输入新的 10 位 Team ID（如 `A1B2C3D4E5`），确认后将原地修改二进制文件。

*   **ldid 签名**
    *   **用途**：为二进制文件进行伪签名（Pseudo-signing），使其能在越狱设备或允许的调试环境下运行。
    *   **操作**：点击即执行 `ldid -S` 命令。

*   **头文件提取**
    *   **用途**：从无源码的 App 中还原 Objective-C 类头文件，辅助分析类结构与方法。
    *   **操作**：点击按钮，工具会解析二进制文件，并在文件同级目录下生成一个 `_Headers` 文件夹，其中包含所有还原出的 `.h` 文件。

*   **简单混淆 (Strip)**
    *   **用途**：剥离二进制文件中的调试符号表（Symbol Table）。
    *   **操作**：点击按钮，工具会移除为了调试而保留的函数名和变量名信息，减小文件体积并增加静态分析难度。

## 技术栈

*   **UI 框架**: SwiftUI (macOS)
*   **核心逻辑**: Swift 5.0+
*   **底层操作**: C 语言
    *   直接操作 Mach-O Load Command
    *   实现 `insert_dylib` 核心逻辑
    *   封装命令行工具调用接口
*   **架构模式**: MVVM (Model-View-ViewModel)

## 快速开始

### 环境要求
*   macOS 12.0 或更高版本
*   Xcode 13.0+ (用于编译)

### 运行
1. 打开DMG安装软件：
2. 给应用授权环境，设置->隐私->app管理
3. 点击运行

## 更新日志 (Update Log)

### v2.0 - 2026-01-07
**核心功能升级**
-   **在线资源库 (Online Library)**：
    -   集成官方插件库，支持一键下载注入。
    -   **自定义仓库**：支持添加任意 GitHub 公开仓库 (`User/Repo`)，自动同步与持久化。
    -   **远程加载**：支持通过直链 (`URL`) 直接下载并注入动态库。
-   **应用分身 (App Cloning)**：
    -   新增“制作分身”功能，支持为任意 App 创建独立运行的副本 (Coexisting Bundle)。
    -   自动处理 Bundle ID 修改与签名剥离，确保分身共存。
-   **应用瘦身 (App Slimming)**：
    -   逆向工具箱新增“瘦身”工具，一键提取当前系统架构 (如 arm64)，剔除冗余架构，大幅减小体积。

**体验与交互优化**
-   **实时文件监听**：应用列表现在会自动感知 `/Applications` 目录的变化（安装/删除/重命名），实现秒级自动刷新，无需手动重启。
-   **内置使用教程**：
    -   新增“用法”窗口，以图文并茂的方式引导新用户使用各项功能。
    -   注入界面新增“地球图标”作为在线库入口。

## 免责声明

本工具仅供安全研究、调试分析及学习交流使用。
请勿将本工具用于任何非法用途（如制作作弊软件、侵犯版权软件的分发等）。
使用者需自行承担因不当使用本工具而产生的一切法律责任与后果。

## 许可证

本项目采用 MIT 许可证。详情请参阅 [LICENSE](LICENSE) 文件。
