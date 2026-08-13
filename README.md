# 视频生成监控台 · MiniMax H3

一款为 **MiniMax H3 视频生成（私有网关接入）** 打造的轻量个人工作台。把原本只能靠 `curl` 看 `queued / running / succeeded` 的技术化输入输出，变成一张直观的看板：状态徽章、提示词、参数、生成完直接内嵌视频预览、一键复制 / 下载，链接超 24h 自动预警。

> 本项目面向 **个人自用 / 小团队分发**，只做一件事：让你对自己视频内容的生成全流程看得清、管得顺。

---

## 功能特性

- **三种生成模式**：文生视频 / 图生视频（首帧 + 尾帧）/ 多模态参考（参考图、参考视频、参考音频任意组合）
- **任务看板**：自动轮询网关查询接口，状态实时刷新（`queued → running → succeeded / failed`）
- **视频预览与下载**：生成成功直接内嵌播放器预览，支持复制链接、本地下载
- **24h 有效期预警**：视频直链过期后行内橙色提醒，避免拿到失效链接
- **失败原因透出**：轮询到 `failed` 时显示网关返回的具体原因（如内容审核拦截）
- **本地持久化**：任务静默存于浏览器 / 客户端本地存储，无需后端
- **零依赖桌面端**：可打包为 macOS DMG，双击安装即用，无需装 Node 或任何运行时

---

## 快速开始

### 方式一：网页版（最简单）

直接用浏览器打开根目录的 `video-monitor.html` 即可使用。首次打开后：

1. 点击右上角「接入设置」展开
2. 填入你的 **API Key** 与 **Base URL**（已预填 `https://tokendance.space/gateway/minimax/v2`）
3. 提交一个测试任务，观察状态能否从「排队中」自动变为「成功」

### 方式二：macOS 桌面版（DMG）

已提供 Universal 通用安装包（`electron-app/dist/*.dmg`），**Intel 与苹果芯片均可安装**，无需任何前置依赖。

安装步骤：

1. 双击 DMG，将「视频生成监控台」拖入「应用程序」
2. 首次打开若被 Gatekeeper 拦截（未签名），请 **右键 → 打开**，或在终端执行一次：
   ```bash
   sudo xattr -dr com.apple.quarantine /Applications/视频生成监控台.app
   ```
3. 打开后同样在「接入设置」中填入你自己的 API Key 即可

> 注：当前分发包为**未签名**版本（无 Apple 开发者证书）。如需他人「双击即装、零警告」，可自行购买 Apple Developer 证书做公证后重打包。

---

## 用法说明

| 模式 | 说明 | 必填参数 |
|---|---|---|
| 文生视频 | 仅传 `text` 提示词 | `ratio` 必填（不可为 `adaptive`） |
| 图生视频 | 传一张图默认按首帧；可传首帧 + 尾帧 | 宽高比由图片决定，无需传 `ratio` |
| 多模态参考 | 参考图 / 视频 / 音频任意组合 | 至少一个参考视频或图片 |

可用分辨率：**`2K`**、**`768P`**（MiniMax-H3 不支持 `1080P`，选中会报错）。

任务提交后，看板会自动轮询 `query/video_generation/{task_id}`，状态流转无需手动刷新。

---

## 项目结构

```
.
├── video-monitor.html        # 网页版（单文件，可直接浏览器打开）
├── electron-app/             # macOS 桌面端工程
│   ├── main.js               # Electron 主进程（加载 HTML 的极薄壳）
│   ├── package.json          # 含 electron-builder 的 universal dmg 配置
│   ├── app/
│   │   └── index.html        # 由 video-monitor.html 复制而来
│   └── dist/                 # 构建产物（DMG，已 gitignore）
├── assets/
│   └── qrcode.jpg            # 作者公众号二维码
├── LICENSE                   # MIT
└── README.md
```

重新打包桌面端：

```bash
cd electron-app
npm install
npm run dist          # 常规构建
# 若遇到批量删除守卫拦截，使用：
npm run dist:safe
```

---

## 特别鸣谢

本项目的诞生，建立在以下团队与产品扎实的基础工作之上。在此致以诚挚感谢。

### 词元跳动 · TokenDance（[tokendance.space](https://tokendance.space/)）

词元跳动打造的 TokenDance 是一套**多协议兼容的 AI 网关**，原生支持 OpenAI / Claude / Gemini 文本协议，覆盖图像、视频、文本转语音等生成能力，并提供智能路由、统一计费与容错降级。本项目正是通过其私有网关，以极小改造成本接入了 MiniMax H3 的视频生成能力。

### MiniMax 与 MiniMax H3（[官方博文](https://www.minimaxi.com/blog/minimax-h3)）

MiniMax H3 是一款**通用全模态生成模型**，支持对文本、图像、视频、声音组成的多模态上下文的统一理解与生成，可输出具备原生双声道的音视频，最高支持 15s / 2K 分辨率。其核心技术包括 Contextual Omni Representation、H3-VAE、H3-Omni Transformer 与 In-context Regeneration，并在 2K 分辨率下提供了行业领先的性价比（每秒价格不到主流模型的 1/3）。MiniMax 亦明确表示将在合规前提下开放模型权重，以推动开源社区发展与国产芯片适配。

**词元跳动与 MiniMax 作为中国人工智能领域的重要创新力量，在视频生成、全模态理解与开发者基础设施等方向持续突破，既为国内创作者与开发者提供了自主可控、高性价比的能力底座，也为全球人工智能生态的开放与繁荣作出了积极贡献。谨此致谢。**

### WorkBuddy 与 Hy3 模型

感谢 **WorkBuddy 开发环境**与 **Hy3 模型**在本项目开发过程中提供的全程协助，使从需求评估、接口联调到界面打磨得以高效完成。

---

## 关于作者

作者公众号：**算力白肉**

> 一个年更博主，很懒的博主，偶尔发发广子，偶尔发发心得体会，泛 AI 圈的，不是专业的自媒体。大家有兴趣就关注，无兴趣就算了。

![算力白肉公众号二维码](assets/qrcode.jpg)

---

## 开源协议

本项目基于 **MIT 协议**开源：

- ✅ 全部授权**免费**
- ✅ 可用于**商用**
- ✅ 欢迎**二次魔改**

魔改完之后，欢迎给作者看看。如果没问题，作者会为你点赞（并可能转发）。

---

*Intelligence with Everyone.*
