# Nocturne（夜曲）

DSH Web GUI 的深色皮肤。左侧导航自右上向左下倾泻蓝—墨—紫三段渐变，主区是近黑三色微渐变，边框统一一道冷白。

`skin.json` 声明 v2 清单，`skin.css` 承载 token 重映射与语义面规则，`patches.css` 承载三处自由选择器修补。纯资产目录，无构建步骤，无依赖。

## 配色

| 区域 | 渐变（`to bottom left`） |
| --- | --- |
| 左侧导航 | `rgba(21, 85, 154, 1)` → `rgba(28, 28, 28, 1)` → `rgba(74, 34, 93, 1)` |
| 主区 | `rgba(15, 20, 35, 1)` → `rgba(10, 20, 17, 1)` → `rgba(19, 17, 36, 1)` |

边框：`rgba(226, 225, 228, 1)`，统一覆盖 `border-l1` 至 `border-l4`。

全部 token 写在 `body, body[data-ds-dark-theme]` 同一块内，亮暗两态同值——深色配色不受应用内明暗切换影响。

## 安装

把三个文件放进用户皮肤目录，目录名须与 `skin.json` 的 `id` 一致：

```sh
git clone git@github.com:LostAbaddon/dsh-theme-nocturne.git
cp dsh-theme-nocturne/{skin.json,skin.css,patches.css} ~/.dsh/skins/nocturne/
```

随后在 DSH 的 **设置 → 皮肤中心** 中选择「夜曲」。目录被 desktop 与 web profile 共用，装一次两端通用；无需重启，刷新页面即生效。

皮肤目录必须是实际目录。皮肤中心以 `readdirSync(root, { withFileTypes: true }).filter(d => d.isDirectory())` 扫描，而 `Dirent.isDirectory()` 对软链接返回 `false`，软链目录不会出现在清单里。

删除或改名皮肤目录前，先把激活态切到其它皮肤。active 指向的皮肤一旦不在清单中，皮肤中心会将激活态重置为 `null`。

## 文件

| 文件 | 作用 |
| --- | --- |
| `skin.json` | v2 清单：id、名称、accent、`contributes.stylesheet` 与 `contributes.patches` |
| `skin.css` | `--dsw-*` token 全量重映射 + L2 语义面规则（页面画布、左栏、右侧详情列、composer 席位遮罩、卡片高光） |
| `patches.css` | 3 条 L3 修补：左栏 hash-class 兜底、代码块吸顶横幅补色、焦点环强调色 |

## 三处外壳约束

**token 定义在 `body` 而非 `:root`。** 外壳把每一个 `--dsw-*` 都声明在 `body` 上。皮肤加载器只把 `:root` 里的 `--dsw-alias-*` 与 `--dsw-specific-*` 克隆到 `body`，其余前缀不克隆——写在 `:root` 会被 `body` 自身的定义覆盖。

**`color-mix()` 与 `background-color` 不接受渐变值。** 主区渐变放在 `--dsw-alias-bg-base` 上，而外壳有两处用 `color-mix()` 读取该 token：composer 席位遮罩与 plugin dock 遮罩。渐变值使整条声明在计算值阶段失效，结果是 `unset` 而非回退到次高优先级的声明，因此席位遮罩在 `skin.css` 中以 `!important` 接管。另有两处通过 `background-color` 消费同一 token（`body` 画布、代码块横幅），分别由显式声明与 `patches.css` 补齐。

**macOS 的 `--dsw-specific-sidebar-fill` 只能是实色。** 官方在 `[data-platform=darwin]` 下用 `color-mix()` 包裹该 token 绘制整列，填入渐变会使整条 `background` 失效、侧栏透明。左栏渐变改由 `[data-dsh-surface="sidebar"]` 选择器绘制，`patches.css` 另有一条 hash-class 兜底。

## 已知取舍

- `--dsw-specific-sidebar-nav-item-hover` 与 `--dsw-specific-sidebar-nav-item-active` 在外壳中仅被设置对话框左侧菜单消费，左栏主导航的行状态走通用的 `--dsw-alias-interactive-bg-hover`。
- 边框 4 级同值，官方设计的弱／强分隔层次不再体现。
- 输入框链路上不挂 `backdrop-filter`。席位与卡片内含 `position: fixed` 的 tooltip，一旦成为包含块会造成对话区滚动跳动。
- 皮肤不带 `preview`，皮肤中心卡片以 `accent` 色块呈现。
- 未设 `dsh-market.provenance.json`，hooks 不可用（`hooks.mjs` 仅对通过官方市场字节校验的皮肤执行）。

## 许可

Apache-2.0，见 `skin.json` 的 `license` 字段。
