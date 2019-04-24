Git Commit Format Guide
===

## Introduction

Git commit 的 comment 主要分为两个部分：title 和 description。

后来人们为了能在 commit 的 comment 内包含更多的信息，以便于和日益丰富的 CI 系统做集成，
人们约定俗成的在 descrption 里定义了不同的段落格式。

后面分别介绍 title 和 description 的格式。

---

## Commitizen Style

### Title

title 的规范五花八门，commitizen 和 pbr 各搞了一套。

不过格式基本固定为：`<commit_type>: <commit description title>`，
也就是 `<本次提交针对的问题类型>: <简要的描述>`。

本文主要介绍 commitizen。
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)

在 commitizen 里，提交的类型被分为：

```
? Select the type of change that you're committing: (Use arrow keys)
❯ feat:     A new feature
  fix:      A bug fix
  docs:     Documentation only changes
  style:    Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
  refactor: A code change that neither fixes a bug nor adds a feature
  perf:     A code change that improves performance
  test:     Adding missing tests or correcting existing tests
```

### Description

Description 一般被分为两段：详细描述和关联操作，已空行隔开。

一个完整提交信息形如：

```
feat: add xxx features

This commit will add some features.

closes #11
```

可以看出这是一次 feat 类型的提交，并且附带了描述，这次提交的生效还会自动关闭 #11 号 issue。


### Install

commitizen 提供了方便的基于 node 的命令行工具 `git cz`。

可参考官方文档：[https://github.com/commitizen/cz-cli](https://github.com/commitizen/cz-cli)。

如果你已安装有 nodejs，可简单的在项目根路径执行：

```sh
$ npm init
$ sudo npm --registry=https://registry.npm.taobao.org \
    --cache=$HOME/.npm/.cache/cnpm \
    --disturl=https://npm.taobao.org/dist \
    --userconfig=$HOME/.cnpmrc install -g commitizen

# 然后就可运行
$ git cz
```
