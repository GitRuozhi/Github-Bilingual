# Github Bilingual

Github Bilingual 是一个用户脚本，用于把 GitHub 界面翻译显示为双语形式。

[English README](./README.md)

## 安装

1. 安装用户脚本管理器，例如 [Tampermonkey](https://www.tampermonkey.net/) 或 [Violentmonkey](https://violentmonkey.github.io/)。
2. 安装 [`main.user.js`](https://github.com/GitRuozhi/Github-Bilingual/raw/gh-pages/main.user.js)。
3. 刷新 GitHub 页面。

![Github Bilingual 预览图](https://raw.githubusercontent.com/GitRuozhi/Github-Bilingual/gh-pages/Preview/01.png)

## 来源

本项目源自 [maboloshi/github-chinese](https://github.com/maboloshi/github-chinese)。源项目提供翻译词库和匹配规则。

## 描述

- 修改基于版本：`1.9.4-2026-06-10`，本分支版本号：`1.9.4-2026-06-15-d`
- 短文本显示为 `English | 中文`，长文本显示为两行：第一行原文，第二行译文。
- 默认禁用远程描述翻译，需要时可在脚本菜单中手动开启。
- 不修改页面的 `<html lang>` 属性。
- 2026.06.12新版搜索框临时修复。顶部导航可能保持为英文，后期会跟随主分支改动。
