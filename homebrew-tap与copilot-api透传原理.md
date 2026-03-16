# Homebrew Tap 与 copilot-api 透传原理

## 一、Homebrew Tap 是什么？为什么需要 GitHub 仓库？

### 先理解 Homebrew 的工作原理

Homebrew 本质上就是一个**包管理器**，它需要知道三件事：
1. **这个软件叫什么**
2. **从哪里下载**
3. **怎么安装**

这三个信息写在一个叫 **Formula**（配方）的 Ruby 文件里，例如 `Formula/copilot-api.rb`：

```ruby
# 叫什么 → copilot-api
class CopilotApi < Formula
  # 从哪下载 → GitHub Release 的 tar.gz
  url "https://github.com/xxx/xxx/releases/download/vX.X.X/copilot-api-darwin-arm64.tar.gz"
  # 怎么装 → 把二进制放到 bin 目录
  def install
    bin.install "copilot-api"
  end
end
```

### 官方仓库 vs 自定义 Tap

Homebrew 自带一个**官方 Formula 仓库**，叫 `homebrew-core`，里面收录了几千个主流软件（比如 git、node、python）。你 `brew install git` 的时候，Homebrew 就去 `homebrew-core` 里找 `git.rb` 这个 Formula。

但是——你自己的小工具不可能被收录到官方仓库（太小众了）。

**Tap 就是解决这个问题的。** Tap 的本质就是：

> **一个符合特定命名规则的 GitHub 仓库，里面放着你自己写的 Formula。**

命名规则是 `<用户名>/homebrew-<tap名>`，例如 `sq-lai/homebrew-tap`。

### 整个流程是这样的

```
brew tap sq-lai/tap
```

↓ Homebrew 做了什么？

```
git clone https://github.com/sq-lai/homebrew-tap.git
→ 保存到 /opt/homebrew/Library/Taps/sq-lai/homebrew-tap/
```

它就是把你的 GitHub 仓库 **clone 到本地**，这样 Homebrew 就能找到你写的 Formula 了。

```
brew install copilot-api
```

↓ Homebrew 做了什么？

```
1. 在所有已添加的 Tap 里找 copilot-api.rb
2. 找到了 → 读取 Formula
3. 根据 url 字段下载 tar.gz（从你的 GitHub Release）
4. 校验 sha256（防篡改）
5. 解压，执行 install 方法（把二进制放到 /opt/homebrew/bin/）
6. 完成 → 用户就能直接在终端用 copilot-api 命令了
```

### 所以为什么需要 GitHub 仓库？

因为 **Tap 就是一个 Git 仓库**。Homebrew 用 `git clone` 来获取你的 Formula，用 `git pull` 来更新（`brew update` 时）。GitHub 仓库只是一个存放 Formula 的地方，不存放软件本身——软件本身在 Release 的附件里。

### 总结各部分的关系

```
主项目仓库（存放源码）
├── 源码（TypeScript）
└── Releases/vX.X.X
    └── copilot-api-darwin-arm64.tar.gz  ← 真正的二进制在这里

homebrew-tap 仓库（存放配方）
└── Formula/copilot-api.rb  ← 只是一个"配方"，告诉 brew 去哪下载

用户电脑
├── /opt/homebrew/Library/Taps/sq-lai/homebrew-tap/  ← brew tap 拉下来的配方
└── /opt/homebrew/bin/copilot-api                     ← brew install 装好的二进制
```

---

## 二、透传是什么？完整的传输流程

### 先说"透传"这个词

透传 = **透明传递**，意思是中间人**不看、不改、原样转发**。

就像快递中转站：包裹从 A 发到 B，中间经过中转站 C。如果 C 原封不动地把包裹转给 B，这就是透传。如果 C 拆开包裹重新包装再发，那就不是透传。

### 完整请求流程

当你在终端输入 `codex "帮我写个排序算法"` 时，发生了以下事情：

```
┌─────────────────────────────────────────────────────────────────┐
│  第一步：Codex CLI 组装请求                                       │
│                                                                 │
│  Codex 读取 ~/.codex/config.toml：                               │
│    wire_api = "responses"  →  用 Responses API 格式              │
│    base_url = "http://localhost:4141"  →  发到本地反代            │
│    model = "gpt-5.4"                                            │
│                                                                 │
│  组装出一个 HTTP 请求：                                            │
│    POST http://localhost:4141/responses                          │
│    Body: {                                                      │
│      "model": "gpt-5.4",                                       │
│      "stream": true,                                            │
│      "input": [{"role":"user","content":"帮我写个排序算法"}],       │
│      ...                                                        │
│    }                                                            │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  第二步：copilot-api（反代工具）收到请求                            │
│                                                                 │
│  路由匹配：POST /responses → handleResponses()                   │
│                                                                 │
│  做了什么？                                                      │
│    1. 检查速率限制                                                │
│    2. 读取请求体（payload）                                       │
│    3. 加上认证信息（Copilot Token，来自之前 copilot-api auth）     │
│    4. 把请求【原样】转发给 GitHub Copilot 后端                     │
│       ┌──────────────────────────────────────┐                  │
│       │  这一步就是"透传"：                     │                  │
│       │  - 不改 model 字段                     │                  │
│       │  - 不改 input 内容                     │                  │
│       │  - 不做格式转换                        │                  │
│       │  - 只是加了认证头（Token）              │                  │
│       └──────────────────────────────────────┘                  │
│                                                                 │
│  发出的请求：                                                     │
│    POST https://api.githubcopilot.com/responses                 │
│    Headers: { Authorization: "Bearer <copilot-token>", ... }    │
│    Body: { 和 Codex CLI 发来的一模一样 }                          │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  第三步：GitHub Copilot 后端处理                                   │
│                                                                 │
│  1. 验证 Token（Copilot 订阅是否有效）                             │
│  2. 检查模型权限（gpt-5.4 是否允许使用）                            │
│  3. 调用底层的 OpenAI gpt-5.4 模型                                │
│  4. 返回响应（流式 SSE）                                          │
│                                                                 │
│  流式响应长这样（一个字一个字往回吐）：                               │
│    event: response.created                                      │
│    data: {"type":"response.created","response":{...}}           │
│                                                                 │
│    event: response.output_text.delta                            │
│    data: {"type":"response.output_text.delta","delta":"下"}      │
│                                                                 │
│    event: response.output_text.delta                            │
│    data: {"type":"response.output_text.delta","delta":"面"}      │
│                                                                 │
│    event: response.output_text.delta                            │
│    data: {"type":"response.output_text.delta","delta":"是"}      │
│    ...                                                          │
│                                                                 │
│    event: response.completed                                    │
│    data: {"type":"response.completed","response":{完整响应}}      │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  第四步：copilot-api 把响应【原样】传回 Codex CLI                   │
│                                                                 │
│  又是透传：                                                      │
│    - Copilot 后端吐一个 SSE 事件                                  │
│    - copilot-api 立刻原样转发给 Codex CLI                         │
│    - 不解析、不修改、不缓存                                        │
│                                                                 │
│  代码里就是这段：                                                  │
│    for await (const rawEvent of response) {                     │
│      await stream.writeSSE(rawEvent)  // 收到什么，发什么          │
│    }                                                            │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  第五步：Codex CLI 收到流式响应                                    │
│                                                                 │
│  逐字显示在你的终端上：                                             │
│  > 下面是一个快速排序的实现...                                      │
└─────────────────────────────────────────────────────────────────┘
```

### 为什么需要这个反代？直接连 Copilot 后端不行吗？

不行，因为：

1. **认证问题**：Copilot 后端需要专门的 Copilot Token（通过 OAuth 设备流获取），不是普通的 API Key。Codex CLI 不知道怎么获取这个 Token，它只会用标准的 OpenAI API Key。

2. **反代 = 自动补认证**：copilot-api 帮你做了 `copilot-api auth` 获取 Token，然后每次请求自动带上，Codex CLI 完全不用操心认证的事。

### 为什么是透传而不是格式转换？

最开始其实试过格式转换方案：

```
Codex CLI（Responses 格式）
    → copilot-api 转换成 Chat Completions 格式
    → Copilot 后端的 /chat/completions

结果：Copilot 后端拒绝了，返回 "unsupported_api_for_model"
原因：gpt-5.4 只允许走 /responses 端点，不接受 /chat/completions
```

后来发现 Copilot 后端本身就有 `/responses` 端点，所以根本不需要转换格式，直接透传就完事了：

```
Codex CLI（Responses 格式）
    → copilot-api 原样转发
    → Copilot 后端的 /responses（原生支持 Responses 格式）

结果：成功 ✅
```

透传方案更简单、更可靠——中间不做任何格式转换，出问题的概率也更小。
