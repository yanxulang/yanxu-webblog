# yanxu-webblog：言枢博客

[![CI](https://github.com/yanxulang/yanxu-webblog/actions/workflows/ci.yml/badge.svg)](https://github.com/yanxulang/yanxu-webblog/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

这是言枢 0.2 与言标的完整参考项目。它展示配置化应用、命名路由、反向 URL、路由组、模板继承、自动转义、自定义 404、JSON/言据 API、静态文件和无端口集成测试。

## 运行

```sh
git clone https://github.com/yanxulang/yanxu-webblog.git
yanbao --manifest-path yanxu-webblog install
yanbao --manifest-path yanxu-webblog run
```

项目通过 Git `main + ^0.2`使用已发布的言枢 0.2，`言序.lock`固定经过验证的精确修订；普通克隆后即可由言包复现依赖。

默认地址为[http://127.0.0.1:8787](http://127.0.0.1:8787)。可用清单允许的环境变量调整地址：

```sh
YANXU_WEBBLOG_ADDR=127.0.0.1:9000 yanbao --manifest-path yanxu-webblog run
```

自动化真实连接可使用一次请求模式：

```sh
yanbao --manifest-path yanxu-webblog run -- --once
```

## 项目结构

```text
src/
├── 主.yx       # 配置地址、项目路径与开发服务器
├── 应用.yx     # 言枢配置、处理器、命名路由组与错误边界
├── 页面.yx     # 言标模板上下文与反向 URL
└── 数据.yx     # 示例领域数据
templates/
├── base.yb.html
├── home.yb.html
├── post.yb.html
├── 404.yb.html
└── partials/   # head、header、footer
static/app.css
tests/集成.yx
```

推荐阅读顺序是`数据.yx → 页面.yx → templates/ → 应用.yx → 主.yx → tests/集成.yx`。

## 路由

| 方法 | 路径 | 名称 | 响应 |
| --- | --- | --- | --- |
| GET | `/` | `首页` | 言标首页 |
| GET | `/posts/:id` | `文章详情` | 言标文章页或 404 |
| GET | `/api/posts` | `api:文章列表` | JSON |
| GET | `/api/posts.yj` | `api:文章言据` | 言据 |
| GET | `/health` | `健康` | 纯文字 |
| GET | `/static/*path` | — | 静态文件 |

页面数据层通过`上下文.反向`生成链接，不把业务路由重复写进模板。

## 言标用法

子页面继承`base.yb.html`并覆盖`主体`块：

```html
{% 承 base.yb.html %}
{% 块 主体 %}
  {% 逐 文章 于 文章列 %}
    <a href="{{ 文章.url }}">{{ 文章.title }}</a>
  {% 终逐 %}
{% 终块 %}
```

文章正文中的`<标签>`由普通插值输出为`&lt;标签&gt;`。项目没有动态可信 HTML 使用点；页脚版权符号直接写在静态模板源码中。

## 验收

从言序总工作区根目录运行：

```sh
YANXU_BIN=yanxu-language-new/target/debug/yanxu yanbao/target/debug/yanbao --manifest-path yanxu-webblog install
yanxu-language-new/target/debug/yanxu 查 yanxu-webblog/src/主.yx
yanxu-language-new/target/debug/yanxu 试 yanxu-webblog/tests --并发 1 --json
```

测试使用`言枢.测试客户端`覆盖页面、API、中间件、404/405、静态文件与目录穿越；它不占用 TCP 端口。

## 部署边界

言枢开发服务器仍为串行 HTTP/1.1、一连接一请求模型。默认安全首部和静态路径防护不是生产认证；TLS、并发、速率限制、认证授权、CSRF、日志、指标和优雅关闭仍需独立实现或由成熟前置系统承担。

详细说明见[教程](docs/tutorial.md)、[项目结构](docs/project-structure.md)和[运行与部署](docs/running-and-deployment.md)。当前版本为`0.2.0`。
