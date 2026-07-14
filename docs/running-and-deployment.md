# yanxu-webblog 运行与部署边界

## 本地运行

默认监听`127.0.0.1:8787`，只暴露在本机回环接口。修改端口时使用`YANXU_WEBBLOG_ADDR`：

```sh
YANXU_WEBBLOG_ADDR=127.0.0.1:9000 yanbao --manifest-path yanxu-webblog run
```

地址必须符合`言序.toml`中的`TCP监听`权限。不要为方便开发把权限扩展为不必要的外部接口。

## 检查与测试

从总工作区根目录运行：

```sh
YANXU_BIN=yanxu-language-new/target/debug/yanxu yanbao/target/debug/yanbao --manifest-path yanxu-webblog install
yanxu-language-new/target/debug/yanxu 查 yanxu-webblog/src/主.yx
yanxu-language-new/target/debug/yanxu 试 yanxu-webblog/tests --json
```

`--once`适合真实连接冒烟测试：先在后台启动一次性服务器，再发出一个请求。集成规格测试本身不需要端口。

## 为什么不是生产服务器

当前开发服务器是串行 HTTP/1.1，一条连接只处理一个请求，然后发送`Connection: close`。项目没有 TLS、并发工作池、长连接、流式请求、速率限制、可信代理配置、结构化访问日志、指标、优雅重启或会话存储。

安全首部中间件和目录穿越防护是应用设计示例，不是生产认证。把服务直接绑定公网会扩大拒绝服务、慢请求和运维风险。

## 若要进行受控演示

临时演示至少应保持应用绑定回环地址，由成熟反向代理终止 HTTPS、限制请求大小与超时，并把进程置于受限账户或容器中。代理应覆盖或清理不受信任的转发首部，并配置访问日志与健康检查。

这仍然不改变后端串行处理的容量上限。真正的生产支持需要等待`yanxu-http`和`yanxu-web`完成并发、连接生命周期、背压、优雅关闭与代理信任模型。最新范围以[言序 Web 路线图](https://docs.yanxu.dev/web/security-roadmap/)为准。

## 静态资产

示例由应用进程读取`static/`并设置`no-cache`，便于开发时立即看到变化。稳定部署应为带内容摘要的资产设置长期缓存，并优先交由反向代理、CDN 或对象存储提供。
