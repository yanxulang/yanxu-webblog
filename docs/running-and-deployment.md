# 言枢博客运行与部署

## 本地运行

```sh
yanbao install --manifest-path yanxu-webblog
yanbao run --manifest-path yanxu-webblog
```

默认监听`127.0.0.1:8787`。可在清单允许范围内设置：

```sh
YANXU_WEBBLOG_ADDR=127.0.0.1:9000 yanbao run --manifest-path yanxu-webblog
```

## 检查与测试

```sh
yanxu 查 yanxu-webblog/src/主.yx
yanxu 试 yanxu-webblog/tests --并发 1 --json
```

并发设为 1 是为了避免多个测试进程同时检出共享 Git 包缓存；应用行为本身由无端口客户端快速验证。真实连接可用`yanbao run --manifest-path yanxu-webblog -- --once`进行一次请求冒烟测试。

## 生产边界

当前开发服务器串行处理 HTTP/1.1，每连接只读取一个请求并发送`Connection: close`。它没有 TLS、并发工作池、长连接、流式请求、可信代理、速率限制、结构化日志、指标、优雅关闭或会话存储。

默认 CSP、`nosniff`、同源 referrer policy、模板自动转义和目录穿越防护只是应用安全基线。身份、授权、CSRF、输入校验、密码与密钥、备份和运维仍由生产系统负责。

受控演示至少应绑定回环地址，由成熟反向代理终止 HTTPS、施加超时与请求大小限制、清理转发首部，并将进程放入受限账户或容器。

## 静态与模板文件

模板在每次请求时从`templates/`读取，静态文件默认`Cache-Control: no-cache`，都以开发反馈速度为目标。生产部署应预编译或缓存模板，并让带内容摘要的静态资产由代理、CDN 或对象存储提供。
