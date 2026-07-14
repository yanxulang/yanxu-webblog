# yanxu-webblog

`yanxu-webblog` 是言序 Web 开发的最小可运行博客、入门模板和跨库参考实现。它综合使用：

- `yanxu-html`：页面节点、组件、自动转义与显式原始 HTML；
- `yanxu-http`：HTTP 请求与响应、Cookie/查询参数基础；
- `yanxu-web`：应用、路由、路径参数、中间件、错误处理、静态文件和开发服务器；
- `yanju`：言据 API 序列化。

## 运行

当前言序 1.1.4 的包权限要求依赖源码位于显式文件根内，因此模板用递归 Git submodule 固定 `yanxu-web`、其下层库和 `yanju`。先递归克隆：

```sh
git clone --recurse-submodules https://github.com/yanxulang/yanxu-webblog.git
```

安装言序 1.1.4 后，在仓库根目录执行：

```sh
yanxu 包 锁 .
yanxu 包 运行 .
```

默认地址是 [http://127.0.0.1:8787](http://127.0.0.1:8787)。可通过清单允许的 `YANXU_WEBBLOG_ADDR` 覆盖：

```sh
YANXU_WEBBLOG_ADDR=127.0.0.1:9000 yanxu 包 运行 .
```

自动化或冒烟测试可只处理一个请求：

```sh
yanxu 包 运行 . -- --once
```

## 路由

| 路径 | 内容 |
| --- | --- |
| `/` | 博客首页 |
| `/posts/:id` | 文章详情与路径参数示例 |
| `/api/posts` | JSON API |
| `/api/posts.yj` | 言据 API |
| `/static/app.css` | 静态文件服务 |
| `/health` | 文字健康检查 |

页面数据中的 `<标签>` 作为普通文字节点自动转义；页脚的 `&copy;` 则用名称明确的原始节点创建，展示两条信任路径的区别。

从言序总工作区根目录验收：

```sh
yanxu-language-new/target/debug/yanxu 包 锁 yanxu-webblog
yanxu-language-new/target/debug/yanxu 查 yanxu-webblog/src/主.yx
yanxu-language-new/target/debug/yanxu 试 yanxu-webblog/tests --json
```

当前版本是 `0.1.0`，按 MIT License 发布。
