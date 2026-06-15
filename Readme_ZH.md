# Github Bilingual

Github Bilingual 是一个用户脚本，用于把 GitHub 界面翻译显示为双语形式。

[English README](./README.md)

## 来源

本项目源自 [maboloshi/github-chinese](https://github.com/maboloshi/github-chinese)。源项目提供翻译词库和匹配规则。

## 和源项目不一样的地方

- 短文本显示为 `English|中文`。
- 较长文本显示为两行：第一行原文，第二行译文。
- 只保留一个用户脚本版本。
- 不修改原词库，不修改原翻译匹配规则。
- 默认禁用远程描述翻译，需要时可在脚本菜单中手动开启。
- 不修改页面的 `<html lang>` 属性。
- 根据源项目 issue #702 增加搜索框修复。新版 React 顶部导航和搜索区域可能保持英文，以保证搜索框可正常使用。
- 兼容目标是脚本匹配的 GitHub 官方站点。非 GitHub 官方产品或第三方 GitHub 类站点不保证可用。

## 安装

1. 安装用户脚本管理器，例如 [Tampermonkey](https://www.tampermonkey.net/) 或 [Violentmonkey](https://violentmonkey.github.io/)。
2. 安装 [`main.user.js`](https://github.com/GitRuozhi/Github-Bilingual/raw/gh-pages/main.user.js)。
3. 刷新 GitHub 页面。

## 许可证

本项目沿用源项目的 GPL-3.0 许可证。
