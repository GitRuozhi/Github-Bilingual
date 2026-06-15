# Github Bilingual

Github Bilingual is a userscript that displays GitHub interface translations in bilingual form.

[中文说明](./Readme_ZH.md)

## Source

This project is based on [maboloshi/github-chinese](https://github.com/maboloshi/github-chinese). The original project provides the translation dictionary and matching rules.

## What Is Different

- Displays translated interface text as `English|中文` for short text.
- Displays longer text on two lines: original text first, translated text below it.
- Keeps one userscript version only.
- Keeps the original dictionary and matching rules unchanged.
- Disables remote description translation by default.
- Does not modify the page `<html lang>` attribute.
- Adds a search hotfix based on upstream issue #702. The new React header/search area may stay in English so the search box keeps working.
- Compatibility is aimed at GitHub official sites matched by the script. Non-GitHub official products or third-party GitHub-like sites are not guaranteed to work.

## Install

1. Install a userscript manager such as [Tampermonkey](https://www.tampermonkey.net/) or [Violentmonkey](https://violentmonkey.github.io/).
2. Install [`main.user.js`](https://github.com/GitRuozhi/Github-Bilingual/raw/gh-pages/main.user.js).
3. Refresh GitHub pages.

## License

This project follows the original project's GPL-3.0 license.
