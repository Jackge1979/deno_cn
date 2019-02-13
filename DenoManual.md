# deno 中文手册
## 1 免责声明
需要注意的是：Deno正处于开发阶段。 我们鼓励勇敢的早期开发者，但是需要说明的是它可能存在很多的错误，并且API可能没有任何通知的时候随时更改。

您通过 https://github.com/denoland/deno/issues 报告错误，这将对我们有很大帮助！

## 2 介绍
### 2.1 设计原则
Deno旨在成为现代程序员有用的工具。

它将始终作为单个可执行文件分发 - 并且该可执行文件完全可以运行任何deno程序。 给定一个deno程序的URL，您应该能够用不超过50M deno的可执行文件来执行它。

Deno明确地承担了运行时和包管理器的角色。 它使用标准的浏览器兼容协议来加载URL模块。

Deno提供有关程序如何访问系统的安全保证，默认情况下是最严格的安全沙箱。

Deno提供了一组经过审查（审计）的标准模块，可以保证与Deno一起使用。

Deno是固执己见的，定义了样式指南并具有自动格式化程序。

### 2.2 设计目标

* 支持TypeScript开箱即用。
* 没有package.json。 没有。 未与Node明确兼容。
* 与浏览器一样，允许从URL导入：
  
  ```
  import * as log from "https://deno.land/x/std/log/mod.ts";
  ```

* 远程代码在首次执行时被提取和缓存，并且在使用 --reload 标志运行代码之前永远不会更新。 （所以，这仍然适用于飞机。有关缓存的详细信息，请参阅 ~/.deno/src。）
* 使用“ES模块”并且不支持 require()。
* 可以控制文件系统和网络访问以运行沙盒代码。 V8（非特权）和 Rust（特权）之间的访问，只能通过此 flatbuffer 中定义的序列化消息完成。 这使审计变得容易。 例如，要启用写访问，请使用标志 --allow-write 或网络访问 --allow-net。
* 只发送一个可执行文件。
* 永远死于未捕获的错误。
* 旨在支持顶级等待。

### 2.3 浏览器兼容性
Deno程序的子集完全用 JavaScript 编写，不会导入特殊的“deno”模块，也应该能够在现代Web浏览器中运行而不需要更改。


## 3 安装
### 3.1 使用二进制安装
Deno适用于OSX，Linux和Windows。 Deno是一个单独的二进制可执行文件，它没有外部依赖。

deno_install提供了下载和安装二进制文件的便捷脚本。

* 使用Shell：

```
curl -fL https://deno.land/x/install/install.sh | sh
```

* 使用PowerShell：

```
iex (iwr https://deno.land/x/install/install.ps1)
```
注意：根据您的安全设置，您可能必须首先运行Set-ExecutionPolicy RemoteSigned -Scope Current User以允许执行下载的脚本。

* 使用Scoop：

```
scoop install deno
```

也可以通过下载 https://github.com/denoland/deno/releases 上的 tarball 或 zip 文件手动安装Deno。 这些包只包含一个可执行文件。 您必须在Mac和Linux上设置为可执行。

一旦安装并添加到$PATH中，可以命令行中请尝试：

```
deno https://deno.land/welcome.js
```

### 3.2 从源文件编译安装

```
# Fetch deps.
git clone --recurse-submodules https://github.com/denoland/deno.git
cd deno
./tools/setup.py

# You may need to ensure that sccache is running.
# (TODO it's unclear if this is necessary or not.)
# prebuilt/mac/sccache --start-server

# Build.
./tools/build.py

# Run.
./target/debug/deno tests/002_hello.ts

# Test.
./tools/test.py

# Format code.
# TODO: set PYTHONPATH in format.ts when run API has env option.
PYTHONPATH=third_party/python_packages deno ./tools/format.ts --allow-read --allow-run
```

#### 先决条件
为了确保可重现的构建，deno在git子模块中具有大部分依赖性。 但是，您需要单独安装：

1. Rust >= 1.31.1
2. Node
3. Python 2 而非 Python 3

Mac用户的额外步骤：

1. XCode
2. (可选) Openssl 1.1: brew install openssl@1.1

Windows用户的额外步骤：

将 python.exe 添加到 PATH（例如，设置 PATH=%PATH%;C:\Python27\python.exe）
使用 C++ 工具包获取 VS Community 2017 with Desktop 开发，并确保选择下面列出的以下所需工具以及所有 C++工具。

* Windows 10 SDK >= 10.0.17134
* 用于 x86 和 x64 的 Visual C++ ATL
* 用于 x86 和 x64 的 Visual C++ MFC
* C++分析工具

1. 启用Windows调试工具。 转到控制面板→程序→程序和功能→选择Windows软件开发工具包-Windows 10→更改→更改→检查Windows调试工具→更改→完成。

其他有用的命令：
```
# Call ninja manually.
./third_party/depot_tools/ninja -C target/debug

# Build a release binary.
DENO_BUILD_MODE=release ./tools/build.py :deno

# List executable targets.
./third_party/depot_tools/gn ls target/debug //:* --as=output --type=executable

# List build configuration.
./third_party/depot_tools/gn args target/debug/ --list

# Edit build configuration.
./third_party/depot_tools/gn args target/debug/

# Describe a target.
./third_party/depot_tools/gn desc target/debug/ :deno
./third_party/depot_tools/gn help

# Update third_party modules
git submodule update
```

环境变量：DENO_BUILD_MODE，DENO_BUILD_PATH，DENO_BUILD_ARGS，DENO_DIR。

## 4 API 参考
### deno --types
要获得deno运行时API的参考，请在命令行中运行以下命令：

```
deno --types
```

（未完待续）