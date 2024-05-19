# rust-Pingora-quickstart
Pingora是一个用Rust编写的框架，主要用于构建快速、可靠且可编程的网络系统。以下是Pingora的主要用途和功能：

### 1. HTTP代理服务
Pingora可以用来构建HTTP代理服务，支持HTTP/1和HTTP/2的端到端代理，还支持gRPC和WebSocket代理[1][2][3][4]。

### 2. 负载均衡
Pingora提供了多种负载均衡算法，如轮询（Round Robin）和哈希（Hashing），并允许用户自定义负载均衡和故障转移策略[1][3][4]。

### 3. 安全通信
Pingora支持通过OpenSSL或BoringSSL进行TLS加密，确保通信的安全性，并且符合FIPS标准和后量子加密技术[1][2][3][4]。

### 4. 高性能和多线程
Pingora是一个异步多线程框架，能够高效地处理大量并发请求，节省CPU和内存资源[2][3][4][5]。

### 5. 可编程性和定制化
Pingora提供了高度可编程的API，允许用户自定义服务的处理、转换和转发请求的方式。这使得Pingora非常适合构建高级和个性化的网关或负载均衡器[1][2][3][4]。

### 6. 零停机重启
Pingora支持零停机的优雅重启，允许在不丢失任何请求的情况下升级服务[1][3][4]。

### 7. 观察性工具
Pingora集成了多种观察性工具，如Syslog、Prometheus、Sentry和OpenTelemetry，方便监控和调试[1][3][4]。

### 8. 内存缓存
Pingora提供了异步的内存缓存功能，带有缓存锁以防止缓存雪崩[1]。

### 9. 连接复用
Pingora通过共享连接来提高网络连接的利用效率，减少新连接的建立，从而节省时间和资源[2][5]。

### 10. 合规性和安全性
Pingora通过与Internet Security Research Group (ISRG)的Prossimo项目合作，推动在互联网关键基础设施中采用内存安全的框架[3][7][11]。

总的来说，Pingora是一个功能强大且灵活的框架，适用于构建各种高性能、安全且可定制的网络服务。

Citations:
[1] https://github.com/cloudflare/pingora
[2] https://www.infoq.com/news/2024/03/cloudflare-open-sources-pingora/
[3] https://blog.cloudflare.com/pingora-open-source
[4] https://www.phoronix.com/news/Cloudflare-Pingora-Open-Source
[5] https://blog.cloudflare.com/how-we-built-pingora-the-proxy-that-connects-cloudflare-to-the-internet
[6] https://www.youtube.com/watch?v=BnLSNf_KiPc
[7] https://linuxiac.com/cloudflare-pingora-framework-gets-open-source/
[8] https://www.phoronix.com/news/Cloudflare-Pingora-0.1
[9] https://news.itsfoss.com/cloudflare-pingora/
[10] https://news.ycombinator.com/item?id=39535969
[11] https://www.memorysafety.org/blog/introducing-river/
[12] https://github.com/cloudflare/pingora/blob/main/README.md?plain=1
[13] https://docs.rs/crate/pingora-core/latest

以下是一个使用Rust语言的Pingora框架构建基本负载均衡器的快速入门示例：

### 创建一个新的Cargo项目

首先，创建一个新的Cargo项目，命名为`load_balancer`：

```bash
cargo new load_balancer
cd load_balancer
```

### 添加Pingora依赖

在项目的`Cargo.toml`文件中添加Pingora和其他必要的依赖：

```toml
[dependencies]
async-trait = "0.1"
pingora = { version = "0.1", features = ["lb"] }
```

### 创建Pingora服务器

在`main.rs`文件中，编写代码以创建一个Pingora服务器。Pingora服务器是一个可以托管一个或多个服务的进程。以下是一个基本的示例：

```rust
use async_trait::async_trait;
use pingora::prelude::*;
use std::sync::Arc;

fn main() {
    let mut my_server = Server::new(None).unwrap();
    my_server.bootstrap();
    my_server.run_forever();
}
```

### 添加命令行选项

通过修改`main`函数，使服务器能够接受命令行参数：

```rust
fn main() {
    let mut my_server = Server::new(Some(Opt::default())).unwrap();
    my_server.bootstrap();
    my_server.run_forever();
}
```

### 运行服务器

编译并运行服务器：

```bash
cargo run -- -h
```

你应该会看到一个帮助菜单，列出可用的命令行参数。

### 后台运行

使用`-d`或`--daemon`参数使程序在后台运行：

```bash
cargo run -- -d
```

要停止服务，可以发送`SIGTERM`信号以进行优雅的关闭：

```bash
pkill -SIGTERM load_balancer
```

### 配置文件

Pingora的配置文件帮助定义如何运行服务。以下是一个示例配置文件`conf.yaml`，定义了服务的线程数、pid文件位置、错误日志文件和升级协调套接字：

```yaml
---
version: 1
threads: 2
pid_file: /tmp/load_balancer.pid
error_log: /tmp/load_balancer_err.log
upgrade_sock: /tmp/load_balancer.sock
```

使用此配置文件运行服务：

```bash
RUST_LOG=INFO cargo run -- -c conf.yaml -d
```

### 添加功能

Pingora提供了许多有用的功能，可以通过几行代码启用和配置。这些功能包括简单的对等健康检查以及无缝更新运行中的二进制文件而不间断服务。

### 优雅升级服务（仅限Linux）

假设我们更改了负载均衡器的代码并重新编译了二进制文件。现在我们可以优雅地升级服务：

```bash
# 重新编译二进制文件
cargo build --release

# 发送升级信号
pkill -SIGHUP load_balancer
```

以上是一个使用Pingora框架构建基本负载均衡器的快速入门示例。更多详细信息和高级配置可以参考Pingora的官方文档和用户指南[1][2][5]。

Citations:
[1] https://github.com/cloudflare/pingora
[2] https://blog.cloudflare.com/pingora-open-source
[3] https://docs.rs/pingora/latest/pingora/
[4] https://www.youtube.com/watch?v=WpMwuo13-7w
[5] https://github.com/cloudflare/pingora/blob/main/docs/quick_start.md
[6] https://www.youtube.com/watch?v=BnLSNf_KiPc
[7] https://news.itsfoss.com/cloudflare-pingora/
[8] https://github.com/randommm/pingora-reverse-proxy
[9] https://blog.cloudflare.com/how-we-built-pingora-the-proxy-that-connects-cloudflare-to-the-internet
[10] https://news.ycombinator.com/item?id=39535969
[11] https://wiki.nikiv.dev/updates
[12] https://www.reddit.com/r/rust/comments/1bi26l2/a_reverse_proxy_with_pingora/
[13] https://www.reddit.com/r/rust/comments/12jhxi2/which_web_framework_do_people_recommend_for_rust/
[14] https://news.ycombinator.com/item?id=39536560
[15] https://hub.qovery.com/guides/tutorial/create-a-blazingly-fast-api-in-rust-part-1/
[16] https://www.memorysafety.org/blog/introducing-river/
[17] https://dl.fullcirclemagazine.org/issue204_en.pdf
[18] https://github.com/tokio-rs/axum
[19] https://glenbradford.com/files/Stocks/list.txt
[20] https://www.youtube.com/watch?v=hzSsOV2F7-s
