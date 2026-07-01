# ChatServer 项目全量介绍

## 一、项目介绍

ChatServer 是一个轻量级的聊天服务器项目，旨在提供基础的网络通信能力，支持客户端与服务端、客户端之间的消息交互。该项目采用 C/C\+\+ 开发，具备跨平台编译特性（依托 CMake 构建），代码结构清晰，适合作为网络编程学习、轻量级即时通讯服务搭建的基础框架。

项目核心目标：

- 实现基础的 TCP/UDP 网络通信（典型的 C/S 架构）；

- 提供可扩展的代码结构，便于后续增加消息加密、多房间聊天、用户认证等功能；

- 依托跨平台构建工具，支持 Linux/macOS/Windows 等系统部署。

## 二、项目框架

### 1\. 目录结构详解

```Plain Text
ChatServer-main/
├── autobuild.sh          # 自动化构建脚本（Linux/macOS 一键编译）
├── README.md             # 项目说明文档
├── CMakeLists.txt        # CMake 构建配置文件（核心构建入口）
├── src/                  # 源代码目录（核心业务逻辑）
│   ├── server/           # 服务端代码（网络监听、连接管理、消息处理）
│   ├── client/           # 客户端代码（连接服务端、消息收发）
│   ├── common/           # 公共模块（工具函数、数据结构、协议定义）
│   └── main.cpp          # 程序入口（服务端/客户端启动）
├── thirdparty/           # 第三方依赖库（如网络库、日志库、JSON 解析库等）
├── build/                # 编译产物目录（cmake 编译的中间文件、可执行文件临时存放）
├── bin/                  # 最终可执行文件输出目录（编译完成后生成的 server/client 二进制文件）
└── include/              # 头文件目录（公共头文件、模块接口头文件）
```

### 2\. 核心模块职责

|模块|核心职责|
|---|---|
|网络层|基于 socket 实现 TCP/UDP 通信，处理连接建立、断开、消息收发|
|连接管理|维护客户端连接池，处理连接超时、异常断开，支持高并发连接（可选 epoll/kqueue）|
|消息处理|定义消息协议（如自定义二进制协议 / JSON），解析、转发、存储消息|
|客户端模块|提供命令行 / 简易 GUI 界面，实现消息发送、接收、服务端连接|
|公共工具模块|日志打印、配置解析、数据加密、内存管理等通用功能|

## 三、技术选型

### 1\. 核心开发语言

- **C/C\+\+**：底层开发语言，兼顾性能与灵活性，适合网络服务器开发，支持直接操作系统调用（如 socket、epoll）。

### 2\. 构建工具

- **CMake**：跨平台构建工具，替代 Makefile 手动编写，支持一键生成编译脚本、适配不同编译器（GCC/Clang/MSVC）。

- **Shell 脚本（\[autobuild\.sh\]\(autobuild\.sh\)）**：封装编译流程，简化一键编译、清理、部署操作。

### 3\. 网络编程

- **原生 Socket API**：基于 POSIX socket 实现基础网络通信（TCP 为主，UDP 可选扩展）；

- **IO 多路复用**（可选）：Linux 下 epoll、macOS/BSD 下 kqueue、Windows 下 IOCP，提升高并发处理能力；

- （可选）第三方网络库：如 libevent/asio，简化异步网络开发（若 thirdparty 目录包含则启用）。

### 4\. 辅助技术

- **日志**：自定义日志模块或第三方库（如 spdlog，存放于 thirdparty）；

- **数据序列化**：自定义二进制协议 / JSON（如 cJSON，存放于 thirdparty）；

- **编译环境**：GCC 9\+/Clang 10\+/MSVC 2019\+（保证 C\+\+11 及以上特性支持）。

## 四、项目依赖

### 1\. 系统级依赖（必须安装）

|系统|依赖项|安装命令（示例）|
|---|---|---|
|Linux（Ubuntu/Debian）|cmake、g\+\+、make、libssl\-dev（可选，加密用）|sudo apt update \&\& sudo apt install cmake g\+\+ make libssl\-dev|
|Linux（CentOS/RHEL）|cmake、gcc\-c\+\+、make|sudo yum install cmake gcc\-c\+\+ make|
|macOS|Xcode Command Line Tools、cmake|xcode\-select \-\-install; brew install cmake|
|Windows|Visual Studio 2019\+（带 C\+\+ 开发组件）、CMake|下载安装 VS 和 CMake（添加到系统环境变量）|

### 2\. 第三方库依赖（可选，视 thirdparty 内容）

若 thirdparty 目录包含以下库，无需额外安装（已内置）：

- 网络库：libevent、boost\.asio；

- 日志库：spdlog、glog；

- 数据解析：cJSON、nlohmann/json；

- 加密库：OpenSSL（系统级已装则复用）。

## 五、项目运行步骤

### 环境准备

确保已安装上述「系统级依赖」（核心是 cmake、C\+\+ 编译器）。

### 步骤 1：克隆 / 下载项目

将项目文件解压到本地，进入项目根目录：

```bash
cd ChatServer-main
```

### 步骤 2：编译项目（两种方式）

#### 方式 1：使用自动化脚本（Linux/macOS 推荐）

```bash
# 赋予脚本执行权限
chmod +x autobuild.sh
# 执行自动构建（编译、生成可执行文件到 bin 目录）
./autobuild.sh
```

> 若 \[autobuild\.sh\]\(autobuild\.sh\) 包含清理逻辑，可执行 `./autobuild.sh clean` 清理编译产物。
> 
> 

#### 方式 2：手动 CMake 编译（跨平台通用）

```bash
# 创建 build 目录（若不存在）并进入
mkdir -p build && cd build
# 生成编译配置（Debug/Release 可选，Release 性能更高）
cmake -DCMAKE_BUILD_TYPE=Release ..
# 编译项目（-j 后接CPU核心数，加速编译，如 -j4）
make -j4
# 编译完成后，将可执行文件拷贝到 bin 目录
cp server client ../bin/
```

### 步骤 3：运行项目

#### 1\. 启动服务端

```bash
# 进入 bin 目录
cd ../bin
# 启动服务端（默认端口可自定义，如 8888）
./server 8888
```

输出类似 `Server start on port 8888, waiting for client...` 表示服务端启动成功。

#### 2\. 启动客户端（新终端 / 新窗口）

```bash
# 进入 bin 目录
cd ChatServer-main/bin
# 连接服务端（IP 为服务端所在地址，本地用 127.0.0.1，端口与服务端一致）
./client 127.0.0.1 8888
```

客户端启动后，即可输入消息，实现与服务端 / 其他客户端的交互。

### 步骤 4：停止项目

- 客户端：按 `Ctrl + C` 退出；

- 服务端：按 `Ctrl + C` 停止监听，释放端口。

## 六、常见问题排查

1. **编译失败**：

    - 检查 C\+\+ 编译器版本（需支持 C\+\+11\+），执行 `g++ --version` 验证；

    - 检查 CMake 版本（需 3\.10\+），执行 `cmake --version` 验证；

    - 确保 build 目录为空后重新执行 cmake 命令。

2. **服务端启动失败**：

    - 检查端口是否被占用（如 `netstat -tulpn | grep 8888`），更换未占用端口；

    - 确保防火墙放行对应端口（Linux：`sudo ufw allow 8888`）。

3. **客户端无法连接服务端**：

    - 检查服务端是否启动、IP / 端口是否输入正确；

    - 跨机器连接时，确保服务端机器可访问（ping 测试），且防火墙放行端口。

## 七、扩展建议

1. 功能扩展：增加用户注册 / 登录、房间管理、文件传输、消息加密；

2. 性能优化：引入线程池处理消息、优化 IO 多路复用逻辑；

3. 监控运维：增加服务端监控（连接数、消息量）、日志持久化；

4. 跨平台适配：完善 Windows 下的编译脚本、适配不同终端交互逻辑。
