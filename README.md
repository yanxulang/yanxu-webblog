# yanxu-webblog

[![CI](https://github.com/yanxulang/yanxu-webblog/actions/workflows/ci.yml/badge.svg)](https://github.com/yanxulang/yanxu-webblog/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Yanxu 1.1.4-A](https://img.shields.io/badge/言序-1.1.4--A-b33.svg)](https://github.com/yanxulang/yanxu/releases/tag/v1.1.4-A)

`yanxu-webblog` 是言序 Web 开发的最小可运行博客、入门模板和跨库参考实现。它不隐藏框架细节：页面节点、HTTP 响应、路由、中间件、言据序列化和静态文件都可以沿源码逐层阅读。

## 它验证了什么

| 项目 | 在博客中的用途 |
| --- | --- |
| [`yanxu-html`](https://github.com/yanxulang/yanxu-html) | 页面布局、文章卡组件、文字自动转义、显式原始实体。 |
| [`yanxu-http`](https://github.com/yanxulang/yanxu-http) | HTTP 响应、响应首部、请求测试与媒体类型。 |
| [`yanxu-web`](https://github.com/yanxulang/yanxu-web) | 应用、路由、路径参数、中间件、错误处理、静态文件和开发服务器。 |
| [`yanju`](https://github.com/yanxulang/yanju) | 言据 API 序列化与集成测试解析。 |

## 运行

需要言序、言包和 Git。仓库只包含博客源码，四个包由言包安装并按锁文件固定：

```sh
git clone https://github.com/yanxulang/yanxu-webblog.git
yanbao --manifest-path yanxu-webblog install
yanbao --manifest-path yanxu-webblog run
```

`yanbao install`验证`言序.lock`中的精确 Git 提交和内容校验；只有主动升级依赖时才执行`yanbao update`。无需递归克隆或初始化 submodule。

默认地址是[http://127.0.0.1:8787](http://127.0.0.1:8787)。通过清单已允许的环境变量修改地址：

```sh
YANXU_WEBBLOG_ADDR=127.0.0.1:9000 yanbao --manifest-path yanxu-webblog run
```

自动化或冒烟测试可让服务器只处理一个请求：

```sh
yanbao --manifest-path yanxu-webblog run -- --once
```

## 路由

| 方法 | 路径 | 响应 | 展示能力 |
| --- | --- | --- | --- |
| GET | `/` | HTML 博客首页 | 文档、组件、文章列表。 |
| GET | `/posts/:id` | HTML 文章页或 HTML 404 | 路径参数、自定义状态。 |
| GET | `/api/posts` | JSON | 结构化 JSON 响应。 |
| GET | `/api/posts.yj` | 言据 | `yanju`序列化与言据媒体类型。 |
| GET | `/static/app.css` | CSS 字节 | 静态文件、媒体类型、路径安全。 |
| GET | `/health` | `ok` | 纯文字响应。 |

其他路径返回 404；路径存在但方法不匹配时返回 405 和`Allow`。

## 源码导览

```text
src/
├── 主.yx       # 地址配置、开发服务器和 --once
├── 应用.yx     # 路由、中间件、错误处理与应用装配
├── 页面.yx     # 布局、文章组件和 HTML 页面
└── 数据.yx     # 最小内存文章数据
static/
└── app.css     # 静态文件示例
tests/
└── 集成.yx     # 不打开 TCP 的端到端应用测试
```

推荐阅读顺序是`数据.yx → 页面.yx → 应用.yx → 主.yx → tests/集成.yx`。详见[项目结构](docs/project-structure.md)。

## 安全示例

文章数据中的`<标签>`进入普通文字节点，页面输出为`&lt;标签&gt;`；页脚固定的`&copy;`通过名称明确的`HTML.原始`创建。`应用.yx`的中间件统一添加 CSP、`X-Content-Type-Options`和`Referrer-Policy`，静态文件路由拒绝目录穿越。

这些措施用于展示各层职责，不把开发服务器变成生产服务器。部署边界见[运行与部署说明](docs/running-and-deployment.md)。

## 文档

- [从零理解博客](docs/tutorial.md)
- [项目结构与扩展位置](docs/project-structure.md)
- [运行、测试与部署边界](docs/running-and-deployment.md)
- [言序文档站：完整博客教程](https://docs.yanxu.dev/web/webblog/)

## 验收

始终从言序总工作区根目录运行：

```sh
YANXU_BIN=yanxu-language-new/target/debug/yanxu yanbao/target/debug/yanbao --manifest-path yanxu-webblog install
yanxu-language-new/target/debug/yanxu 查 yanxu-webblog/src/主.yx
yanxu-language-new/target/debug/yanxu 试 yanxu-webblog/tests --json
```

当前版本是 `0.1.0`，按 [MIT License](LICENSE) 发布。
