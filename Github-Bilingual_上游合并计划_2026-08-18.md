# Github-Bilingual 上游更新合并计划书

> 日期：2026-08-18  
> 本仓库：`GitRuozhi/Github-Bilingual`  
> 上游：`maboloshi/github-chinese`

## 1. 目标

在**不破坏中英双语显示逻辑**、不引入上游无关工程结构的前提下，将当前 Github-Bilingual 从现有基线更新到上游 `1.9.4.4-2026-08-16` 的有效运行时改动。

本次不采用整仓 merge / rebase，也不直接 cherry-pick 全部上游提交，而是继续沿用当前项目的维护方式：

- `locals.js`：选择性同步词库与忽略规则。
- `main.user.js`：只手工移植对运行行为有价值的修复。
- 保留 Github-Bilingual 自有的中英双语格式、元数据、GreasyFork 词库引用方式与默认行为。
- 不引入 VS Code 扩展、CI、AI Review、Dependabot 等与本项目无关的内容。

---

## 2. 当前状态与目标版本

### 当前本仓库

- HEAD：`d508af36a0287e8ff82f10c7a683a9a9ea03af54`
- 当前脚本版本：`1.9.4.4-2026-07-26a`
- README 标注上游基线：`1.9.4.4-2026-07-26`

当前 HEAD：
<https://github.com/GitRuozhi/Github-Bilingual/commit/d508af36a0287e8ff82f10c7a683a9a9ea03af54>

### 上游目标

- 上游目标提交：`9be6573a444e1b83acf264252edaf5ee9fe78f0a`
- 上游版本：`1.9.4.4-2026-08-16`

目标提交：
<https://github.com/maboloshi/github-chinese/commit/9be6573a444e1b83acf264252edaf5ee9fe78f0a>

### 版本范围

从上游 `1.9.4.4-2026-07-26` 到 `1.9.4.4-2026-08-16` 共包含 15 个提交。

其中真正需要本项目处理的运行时变化，主要只有：

1. React 顶部搜索按钮 DOM 保护修复。
2. 可编辑区域禁止翻译。
3. `locals.js` 新词条。
4. 最终版本号与缓存刷新参数。

---

## 3. 上游提交处理矩阵

| 上游提交 | 内容 | 处理建议 | 优先级 |
|---|---|---|---|
| `7e15a149` | `.highlight`、`.notranslate`、`translate=no` 全局跳过 | **无需重复合并**，当前仓库已实际包含 | 已完成 |
| `e77d1c86` | 修复 React 搜索按钮翻译时破坏内部 `kbd` 节点 | **手工适配合入** | P0 |
| `6b2f1409` | 新增 GitHub UI 词条 | **合入 `locals.js`** | P1 |
| `13ce010f` | 禁止翻译 `input`、`textarea`、`contenteditable` | **合入 `locals.js`** | P0 |
| `85453043` | 版本生成更新到 2026-07-29 | 不单独合入 | 跳过 |
| `0eac0da1` | 版本生成更新到 2026-08-04 | 不单独合入 | 跳过 |
| `9be6573a` | 版本生成更新到 2026-08-16 | 只同步最终版本信息 | P1 |
| `264c4be2` | 上游回归测试调整 | 不引入测试工程 | 跳过 |
| `1db77726` | README / 贡献者文档 | 与本项目文档体系无关 | 跳过 |
| `4952fe43` | VS Code 扩展打包依赖 | 无关 | 跳过 |
| `b1c8da30` | 移除上游根目录 VS Code userscript | 无关 | 跳过 |
| `59c9ee91` | VS Code 集成浏览器重构 | 无关 | 跳过 |
| `70e1f8dd` | CI / 构建流程升级 | 无关 | 跳过 |
| `77f0da1a` | GitHub Actions 依赖升级 | 无关 | 跳过 |
| `bf852c09` | AI Code Review 工作流 | 无关 | 跳过 |

---

## 4. 已经合入、不要重复处理的内容

### `7e15a149`：全局跳过翻译区域

上游提交：

<https://github.com/maboloshi/github-chinese/commit/7e15a1492eb5be9dbdb75b479eeb540c034e5eba>

该提交新增：

```js
'.highlight',
'.notranslate',
'[translate="no"]',
```

并同时加入：

- `ignoreMutationSelectorPage['*']`
- `ignoreSelectorPage['*']`

虽然 Github-Bilingual 的版本号仍写作 `2026-07-26a`，但当前 HEAD `d508af36` 实际已经包含这组规则。

**结论：本次不要再次添加。**

这也说明后续同步不能只看 README 里的“基于版本”，还需要看实际文件内容和最近同步提交。

---

## 5. P0：合入 React 顶部搜索按钮 DOM 保护修复

### 上游提交

`e77d1c864e16daf0bd986dc46d66942cdeeee4ca`

<https://github.com/maboloshi/github-chinese/commit/e77d1c864e16daf0bd986dc46d66942cdeeee4ca>

### 问题

Github-Bilingual 当前的：

```js
function translateReactGlobalNavSearchButton()
```

最终使用：

```js
placeholder.textContent = label;
```

这种写法会把 React 搜索按钮占位文字内部的子节点整体替换掉。

GitHub 当前搜索按钮文字中可能包含：

```html
Type <kbd>/</kbd> to search
```

直接写 `textContent` 会删除 React 管理的 `<kbd>` 节点，从而可能破坏快捷键标记和按钮行为。

### 上游修复思路

上游不再替换整个 `placeholder`，而是：

1. 遍历已有 DOM。
2. 将普通文本节点分组。
3. 将 `kbd` 等 `unsafeTextSelector` 节点视为受保护节点。
4. 只更新文本节点内容。
5. 保留 React 原有节点结构。

### Github-Bilingual 的适配要求

**不能直接照抄上游函数。**

上游使用：

```js
translateReactGlobalNavText(...)
```

Github-Bilingual 使用：

```js
formatReactGlobalNavText(...)
```

后者会生成双语格式，例如：

```text
Type / to search | 输入 / 搜索
```

因此需要保留上游“按文本节点分段替换”的算法，但继续使用 Github-Bilingual 自己的：

- `formatReactGlobalNavText`
- `normalizeReactGlobalNavText`
- `unsafeTextSelector`
- 双语检测逻辑

### 验收标准

修复后必须同时满足：

- 顶部搜索按钮仍显示双语。
- `<kbd>/</kbd>` 节点仍存在。
- 点击搜索按钮仍能打开 GitHub 搜索弹层。
- `/` 快捷键提示不丢失。
- 搜索输入框中用户正在输入的内容不被修改。
- 搜索建议中的仓库名、用户名等动态内容不被双语化。

---

## 6. P0：合入“可编辑区域禁止翻译”

### 上游提交

`13ce010f6ab18ef2c2d0e8f0ca4057889fdf4045`

<https://github.com/maboloshi/github-chinese/commit/13ce010f6ab18ef2c2d0e8f0ca4057889fdf4045>

### 需要加入 `locals.js` 的规则

同时加入：

```js
ignoreMutationSelectorPage['*']
```

和：

```js
ignoreSelectorPage['*']
```

内容为：

```js
'input',
'textarea',
'[contenteditable=""]',
'[contenteditable="true"]',
'[contenteditable="plaintext-only"]',
```

### 为什么值得合入

Github-Bilingual 比上游更容易暴露这类问题，因为双语模式会主动重写文本为：

```text
English | 中文
```

一旦 MutationObserver 误处理输入区、Issue 编辑器、评论框或其他 contenteditable 区域，影响比单语替换更明显。

这项修改属于**输入安全边界**，优先级应高于普通词库更新。

### 注意

Github-Bilingual 的 React GlobalNav 自定义逻辑中已经有单独的：

```js
unsafeTextSelector
```

它目前包含 `textarea` 和 `[contenteditable="true"]`。

本次不要顺手大规模重构这一部分。先严格同步上游的全局 `locals.js` 忽略规则；React GlobalNav 的 `unsafeTextSelector` 只有在实际测试发现问题时再单独扩展。

---

## 7. P1：同步 `locals.js` 新词条

### 上游提交

`6b2f1409396de46ec68e1528a99a52a000bfd46d`

<https://github.com/maboloshi/github-chinese/commit/6b2f1409396de46ec68e1528a99a52a000bfd46d>

### 推荐同步

主要包括：

#### Copilot / 用量页面

新增：

- `Included usage`
- `Additional usage`
- `No usage yet`
- `Not enabled`
- 新版 AI credits 描述

同时移除已经被新版 GitHub UI 替代的部分旧 Premium Requests 文案。

#### 仓库页面

新增：

- `See your forks of this repository`
- `Add this repository to one or more lists`
- `Star lists`
- `Star ...`
- `Unstar ...`

#### Projects

新增：

- `No projects were found`
- `Please try a different search query.`

#### Issues 新版侧栏

新增：

- `Assigned to me`
- `Created by me`
- `Mentioned`
- `Recent activity`
- `Collapse sidebar`
- `Expand sidebar`
- `Maintainers only`

### 合并原则

这一提交基本属于纯词库变化，和 Github-Bilingual 的双语逻辑没有结构性冲突。

建议：

- 按上游 patch 原样同步对应词条。
- 不直接覆盖整个 `locals.js`。
- 保留 Github-Bilingual 已有的自定义配置和前期选择性同步内容。

---

## 8. 版本号与元数据更新

代码与词库全部验证通过后，再统一更新版本。

### 推荐版本

上游基线：

```text
1.9.4.4-2026-08-16
```

Github-Bilingual：

```text
1.9.4.4-2026-08-16a
```

### `main.user.js`

更新：

```js
// @version      1.9.4.4-2026-08-16a
```

Github-Bilingual 当前使用 GreasyFork 词库地址，因此继续保留自己的 `@require`，只更新缓存版本参数：

```text
v1.9.4.4-2026-08-16a
```

**不要改回上游 raw.githubusercontent.com 的 `@require`。**

### README

更新：

```text
修改基于版本：1.9.4.4-2026-08-16
本分支版本号：1.9.4.4-2026-08-16a
```

---

## 9. 明确不合入的内容

### VS Code 扩展

不合入：

- `4952fe43`
- `b1c8da30`
- `59c9ee91`

Github-Bilingual 当前定位是轻量 userscript，没有对应的 VS Code Extension 工程。

### CI / GitHub Actions / AI Review

不合入：

- `70e1f8dd`
- `77f0da1a`
- `bf852c09`

这些提交不会改善用户脚本运行行为，反而会引入额外工程维护成本。

### 上游测试目录

不直接引入：

- `264c4be2`
- `e77d1c86` 随附的 Node 回归测试
- `13ce010f` 随附的 Node 回归测试

当前 Github-Bilingual 仓库没有对应的 Node 测试基础设施。

本次采用**人工回归测试清单**代替整套测试工程迁移。

---

## 10. 推荐实施顺序

### 阶段 A：建立同步分支

建议新建：

```text
sync/upstream-2026-08-16
```

不要直接修改 `gh-pages`。

### 阶段 B：输入安全规则

先修改 `locals.js`：

1. 加入 `input`。
2. 加入 `textarea`。
3. 加入三种 `contenteditable` 形式。
4. 同时更新 MutationObserver 和初次 DOM 遍历两个忽略列表。

先验证输入区域不再被翻译。

### 阶段 C：词库更新

应用 `6b2f1409` 的 `locals.js` 词条变更。

验证：

- 仓库 Star/Fork UI。
- Issues 左侧栏。
- Copilot 用量页面。
- Projects 空状态。

### 阶段 D：React 搜索按钮修复

手工移植 `e77d1c86`。

关键要求：

- 保留 `formatReactGlobalNavText()`。
- 不再使用 `placeholder.textContent = label`。
- 只更新普通文本节点。
- 保留 `<kbd>` 等 React 子节点。

### 阶段 E：完整回归

确认双语模式核心行为没有退化。

### 阶段 F：版本更新

最后才修改：

- `@version`
- `@require` 缓存参数
- README 上游基线
- README 本分支版本号

---

## 11. 回归测试清单

### 顶部导航 / 搜索

- [ ] 顶部导航正常显示双语。
- [ ] 搜索按钮显示双语。
- [ ] 搜索按钮内部 `<kbd>/</kbd>` 节点存在。
- [ ] 点击搜索按钮可以正常打开弹层。
- [ ] `/` 快捷键可以正常调用搜索。
- [ ] 搜索输入内容不会被修改。
- [ ] 搜索动态建议不会被翻译。
- [ ] 搜索弹层静态标题仍正常双语显示。

### 可编辑区域

- [ ] Issue 标题输入框不翻译。
- [ ] Issue / PR 评论编辑框不翻译。
- [ ] Markdown 编辑区域不翻译用户输入。
- [ ] `contenteditable="true"` 不翻译。
- [ ] `contenteditable=""` 不翻译。
- [ ] `contenteditable="plaintext-only"` 不翻译。
- [ ] 普通 input / textarea 的输入值不被 MutationObserver 改写。

### 原有跳过规则

- [ ] `.notranslate` 区域不翻译。
- [ ] `[translate="no"]` 区域不翻译。
- [ ] `.highlight` 代码高亮区域不翻译。
- [ ] README / 代码树相关忽略规则无回退。

### 新词条

- [ ] Star / Unstar 新 UI 翻译正常。
- [ ] Fork 浮窗翻译正常。
- [ ] Issues 新侧栏翻译正常。
- [ ] Copilot Included / Additional usage 翻译正常。
- [ ] Projects 无结果提示翻译正常。

### Github-Bilingual 自有功能

- [ ] 英文原文仍保留。
- [ ] 短文本仍为 `English | 中文`。
- [ ] 长文本仍按项目现有双语格式显示。
- [ ] 不出现上游“只显示中文”的回退。
- [ ] 远程描述翻译仍默认关闭。
- [ ] GreasyFork 词库引用仍有效。

---

## 12. 推荐提交拆分

建议不要一次性提交全部修改。

### Commit 1

```text
fix: skip editable regions during translation
```

内容：

- `input`
- `textarea`
- contenteditable 全局忽略规则

### Commit 2

```text
sync: update upstream translation entries to 2026-08-16
```

内容：

- `6b2f1409` 对应词库更新

### Commit 3

```text
fix: preserve React search shortcut markup
```

内容：

- 手工适配 `e77d1c86`

### Commit 4

```text
chore: bump upstream base to 1.9.4.4-2026-08-16
```

内容：

- `@version`
- `@require` 缓存参数
- README 版本说明

这种拆分方式便于出现问题时单独回退。

---

## 13. 最终结论

### 必须合入

1. `e77d1c86` — React 搜索按钮 DOM 保护。
2. `13ce010f` — 可编辑区域禁止翻译。

### 建议合入

3. `6b2f1409` — 最新 GitHub UI 词条。

### 已经存在，不重复合入

4. `7e15a149` — `.highlight` / `.notranslate` / `translate=no`。

### 只做版本同步，不照搬提交

5. `85453043`
6. `0eac0da1`
7. `9be6573a`

### 全部跳过

VS Code 扩展、CI、AI Review、Dependabot、上游文档与测试工程相关提交。

---

## 14. 建议最终版本

完成上述修改并通过回归测试后：

```text
Github-Bilingual 1.9.4.4-2026-08-16a
```

这是本次最合理的同步目标。
