# Github Bilingual

Github Bilingual is a userscript that displays GitHub interface translations in bilingual form.

[中文说明](./Readme_ZH.md)

## Install

1. Install a userscript manager, such as [Tampermonkey](https://www.tampermonkey.net/) or [Violentmonkey](https://violentmonkey.github.io/).
2. Install [`main.user.js`](https://github.com/GitRuozhi/Github-Bilingual/raw/gh-pages/main.user.js).
3. Refresh GitHub pages.

![Github Bilingual preview](https://raw.githubusercontent.com/GitRuozhi/Github-Bilingual/gh-pages/Preview/01.png)

## Source

This project is based on [maboloshi/github-chinese](https://github.com/maboloshi/github-chinese). The original project provides the translation dictionary and matching rules.

## Description

- Modified from version: `1.9.4-2026-06-10`; this branch version: `1.9.4-2026-06-15-d`.
- Short text is displayed as `English | Chinese`; long text is displayed on two lines: original text first, translated text second.
- Remote description translation is disabled by default. You can enable it manually from the userscript menu when needed.
- The page `<html lang>` attribute is not modified.
- Temporary fix for the new search box introduced on 2026-06-12. The top navigation may stay in English and will follow future upstream changes.
