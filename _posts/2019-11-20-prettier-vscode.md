---
layout: post
title: VSCode・Prettierでコードの自動整形する
categories: tech
---

何番煎じかわからないけど Vistual Studio Code と Prettier でコードの自動整形をする環境を構築したので雑に紹介します。

https://github.com/rhythm191/eslint-prettier-sample

## HTML

VSCode のデフォルトのフォーマットを使っています。

## JavaScript のフォーマット

ESLint でフォーマットを行います。

## CSS/SCSS のフォーマット

Prettier で CSS/SCSS のフォーマットができなくなったので VSCode の拡張機能(stylelint-plus)でフォーマットします。
stylelint のルールは Twitter Bootstrap のもの(stylelint-config-twbs-bootstrap)を使っています。
