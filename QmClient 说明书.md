# QmClient 当前版本功能说明书

## 一、文档范围

本文档基于当前仓库代码整理 QmClient 的现状功能，用于替换旧版说明中已经过时的描述。

- 统计范围：`src/game/client/components/qmclient/`、`src/game/client/components/tclient/`、`src/game/client/QmUi/` 以及相关配置/UI 入口
- 明确排除：语音模块（`voice_*`）及其配套文档
- 文档目标：优先回答「当前代码里已经有什么」，不把历史规划稿与现状实现混写

---

## 二、当前整体定位

QmClient 是构建在 DDNet / TClient 之上的定制客户端，当前代码的功能结构可以概括为 4 条主线：

1. `QmClient` 自有功能模块
2. 继承并扩展的 `TClient` 功能
3. 新版 `QmClient Settings` 配置界面
4. `QmUi` 现代化 UI 基础设施

当前版本已经明显不是单点功能集合，而是一个带有独立配置体系、独立功能页签、独立客户端识别与服务端配套能力的完整客户端分支。

---

## 三、当前核心功能概览

### 3.1 配置系统与设置入口

当前客户端同时维护 3 个配置域：

| 配置域 | 典型前缀 | 说明 |
|--------|----------|------|
| DDNet | 无 | 上游原生设置 |
| TClient | `tc_` | TClient 继承功能 |
| QmClient | `qm_` | 栖梦自有功能 |

当前主要设置入口分为两套：

- `TClient` 传统设置页，覆盖配置列表、Profiles、Chat Binds、Bind Wheel 等
- `QmClient Settings` 新页面，按标签页组织功能模块，支持模块搜索、卡片布局、折叠状态与使用记录持久化

相关代码：

- `src/game/client/components/qmclient/menus_qmclient.cpp`
- `src/game/client/components/tclient/menus_tclient.cpp`
- `src/engine/shared/config_variables_qmclient_extra.h`
- `src/engine/shared/config_variables_tclient.h`

### 3.2 QmClient 自有功能模块

当前 `qmclient` 侧已经落地的用户可见功能，主要包括：

| 分类 | 当前功能 |
|------|----------|
| 功能辅助 | Gores 自动切枪、Axiom 自动登录、自动队伍锁定、分身小窗 |
| 沉浸与隐私 | 专注模式、Streamer 模式、名称板坐标显示 |
| 聊天与社交 | 聊天气泡、复读、关键词回复、屏蔽词、好友上线提醒、进图好友播报/自动问候、饼菜单 |
| 翻译 | 入站自动翻译、出站自动翻译、主动翻译命令、LLM/腾讯云/LibreTranslate/FTAPI 后端配置 |
| 视觉与表现 | Jelly Tee、增强激光、碰撞体积可视化、彩虹名字、脚底粒子 |
| 镜头与 HUD | 相机漂移、动态 FOV、纵横比预设、玩家统计 HUD、地图进度条 |
| 其他集成 | 歌词显示、SMTC（系统媒体控制）、QmClient 用户识别与在线分布同步 |

其中一些功能虽然 UI 入口在 `menus_qmclient.cpp`，但实际运行逻辑跨越多个组件与 `TClient` 主体，需要结合具体源码理解。

### 3.3 翻译系统

翻译系统是当前代码中最完整的 QmClient 自有模块之一，和旧文档相比已有明显演进：

- 配置前缀已经切换到 `qm_`，不再以旧版 `tc_translate_*` 为主
- 默认设计已包含 LLM Provider 分流，而不只是传统 HTTP 翻译 API
- 当前可配置 Provider 包括智谱、DeepSeek、OpenAI、自定义端点
- 支持入站自动翻译、出站自动翻译、本地目标语言识别阈值、并发控制、思考模式、系统提示词覆盖
- 仍保留命令式触发：`translate`、`translate_id`

相关代码：

- `src/game/client/components/qmclient/translate.cpp`
- `src/game/client/components/qmclient/translate.h`
- `src/game/client/components/qmclient/translate_parse.cpp`
- `src/game/client/components/qmclient/translate_ui_settings.cpp`

### 3.4 聊天与社交增强

当前聊天/社交增强已经不仅是单个功能点，而是由多项小模块构成：

- 聊天气泡与气泡动画
- 草稿保留
- 复读
- 关键词自动回复
- 屏蔽词列表与替换策略
- 好友上线提醒、自动刷新服务器列表
- 好友进图自动问候、大字播报
- 饼菜单快捷交互

这部分大多集中在 `QmClient Settings -> Function` 页签中。

### 3.5 Gores / Axiom / 玩法辅助

当前版本对特定社区与玩法场景有明显定制：

- `qm_gores` 相关自动切枪与联动配置
- Gores 场景下的 fast input 联动
- Axiom 主号/分身自动登录
- 分身相关快捷功能与小窗视图
- 自动锁队

这部分功能跨越：

- `src/game/client/components/qmclient/menus_qmclient.cpp`
- `src/game/client/components/tclient/tclient.cpp`

### 3.6 HUD、镜头与可视化

当前已存在并可配置的 HUD / 视图增强包括：

- 玩家统计 HUD
- 地图进度条（含嵌入式样式与调试路线）
- 碰撞体积可视化
- 相机漂移
- 动态 FOV
- 自定义纵横比
- 分身迷你视图

这部分属于当前版本较新的现状能力，旧版文档对它们的覆盖并不完整。

### 3.7 QmClient 用户识别与中心服务配套

`qmclient.cpp` 当前包含了一条独立于语音模块之外的服务链路：

- 生命周期标记写入与上报
- 在线时长记录与查询
- QmClient Auth Token 获取
- QmClient 用户数据同步
- QmClient 用户列表拉取
- 客户端识别状态同步
- DDNet 玩家统计请求

这说明当前 QmClient 已经不仅是纯本地 UI 功能集合，而是带有中心服务协同的客户端分支。

相关代码：

- `src/game/client/components/qmclient/qmclient.cpp`

---

## 四、TClient 继承能力的当前状态

除了 QmClient 自有模块，当前仓库依然保留了大量可直接面向用户的 TClient 功能：

| 分类 | 代表功能 |
|------|----------|
| 输入与预测 | Fast Input、改进版 AntiPing、冻结场景预测控制 |
| 指示与信息 | 玩家指示器、状态栏、Mini Debug、存活提醒、跳跃提示 |
| 视觉表现 | Rainbow、Tee Trails、Outlines、Ghost、Tiny Tees、白脚底 |
| 聊天与交互 | Chat Binds、Bind Wheel、自动回复、聊天过滤 |
| 实用工具 | Profiles、Warlist、Fast Practice、宠物、移动图块显示 |

这些功能并不是「旧功能残留」，而是当前用户体验的重要组成部分。QmClient 现在的功能报告如果只写 `qmclient/` 目录，会遗漏大量实际可用能力。

---

## 五、QmUi 基础设施现状

`src/game/client/QmUi/` 当前已经形成一套独立的 UI 基础设施：

| 模块 | 作用 |
|------|------|
| `QmLayout.*` | Flex 风格布局能力 |
| `QmAnim.*` | 动画轨道与过渡能力 |
| `QmTree.*` | UI 节点组织 |
| `QmRender.*` | 渲染桥接 |
| `QmLegacy.*` | 新旧 UI 适配 |
| `QmRt.*` | 运行时总管 |

结合 `menus_qmclient.cpp` 当前的大量卡片、过渡、搜索、折叠与性能埋点，QmUi 已经是现行设置页面的重要基础设施，不应继续只被描述成「未来现代化方向」。

---

## 六、旧版报告中最需要纠正的点

相较旧版说明和旧版分模块报告，当前最需要统一修正的是下面几类问题：

### 6.1 把规划稿写成了现状

旧目录中有不少文档本质上是在做方案设计，例如：

- `01-功能模块/自动更新系统功能报告.md`
- `01-功能模块/截图管理器功能报告.md`
- `01-功能模块/观战导演模式功能报告.md`

这些文档对实现方向有价值，但不能直接代表当前代码状态。

### 6.2 翻译系统描述已明显过时

旧文档里仍然大量以旧配置前缀和旧后端结构为中心；当前代码已经转向 `qm_` 前缀 + LLM Provider 架构，必须按现代码重写。

### 6.3 总说明书中的文件结构已不准确

旧版说明把不少 `tclient` 文件错误地写进了 `qmclient` 目录，也混用了过时的配置头文件名，需要整体修正。

### 6.4 语音文档与非语音文档边界需要明确

本次更新明确不覆盖语音模块，因此语音相关描述不应继续出现在总说明书的主体功能清单中。

---

## 七、建议作为“当前版本现状报告”优先维护的文档

如果后续继续细化，建议优先维护以下文档，使其成为“当前代码现状”的主文档：

1. `QmClient 说明书.md`
2. `01-功能模块/翻译功能报告.md`
3. `01-功能模块/聊天框翻译按钮功能报告.md`
4. `01-功能模块/资源页面功能报告.md`
5. `01-功能模块/Bind指令系统功能报告.md`
6. `01-功能模块/好友分类与全局聊天功能报告.md`
7. `01-功能模块/UI和动画效果现代化报告.md`
8. `01-功能模块/汉化完善功能报告.md`
9. `01-功能模块/BC端精华功能移植报告.md`

其他文档更适合作为：

- 规划稿
- 调研稿
- 待实现设计案

而不是“当前功能说明”。

---

## 八、当前结论

当前仓库中的 QmClient 已具备以下特征：

- 有独立的 `QmClient Settings` 功能页与模块化设置结构
- 有持续扩展中的 `qm_` 配置体系
- 有成熟的翻译系统和多后端能力
- 有明显面向 Gores / Axiom / 社交使用场景的定制功能
- 有相对完整的 HUD / 镜头 / 可视化增强
- 有以 `QmUi` 为基础的现代化 UI 运行时
- 有与中心服务协同的客户端识别、在线时长与用户同步机制

因此，后续所有功能报告都应以「当前代码现状 + 明确标注未实现规划」的方式维护，避免再把历史构想、移植方案和已上线能力混成一份文档。

---

**版本**：2026-04-29 当前仓库快照  
**维护说明**：本版明确排除语音模块
