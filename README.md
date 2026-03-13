# Development Guide

このリポジトリは **pnpm workspace + Changesets** を利用した monorepo 構成です。
Node / pnpm のバージョン管理には **Volta** を使用します。

* macOS / Linux / Windows 対応
* corepack は使用しません

---

# Table of Contents

* [1. Environment Setup](#1-environment-setup)
* [2. Volta Configuration](#2-volta-configuration)
* [3. Node / pnpm Setup](#3-node--pnpm-setup)
* [4. Install Dependencies](#4-install-dependencies)
* [5. Workspace Structure](#5-workspace-structure)
* [6. Development](#6-development)
* [7. Dependency Management](#7-dependency-management)
* [8. Versioning](#8-versioning)
* [9. Release / Publish](#9-release--publish)
* [10. 新規 Package の Publish](#10-新規-package-の-publish)
* [11. なぜ Volta を使用するのか](#11-なぜ-volta-を使用するのか)
* [12. Troubleshooting](#12-troubleshooting)

---

# 1. Environment Setup

## Volta のインストール

### macOS / Linux

```bash
curl https://get.volta.sh | bash
```

### Windows (PowerShell)

```powershell
iwr https://get.volta.sh -UseBasicParsing | iex
```

確認

```bash
volta --version
```

---

# 2. Volta Configuration

## PATH 設定

Volta を利用するためには `VOLTA_HOME` を設定します。

```bash
export VOLTA_HOME="$HOME/.volta"
export PATH="$VOLTA_HOME/bin:$PATH"
```

`.zshrc` / `.bashrc` などに追加してください。

---

## corepack の無効化

このプロジェクトでは pnpm を **Volta で管理**します。
corepack が有効な場合、pnpm と競合する可能性があります。

```bash
corepack disable
```

必要に応じて

```bash
rm -rf ~/.local/share/corepack
```

---

# 3. Node / pnpm Setup

## Node

```bash
volta install node@20
volta pin node@20
```

確認

```bash
node -v
```

---

## pnpm

```bash
volta install pnpm@9.1.0
```

確認

```bash
pnpm -v
```

---

## Volta shim 確認（重要）

macOS / Linux

```bash
which pnpm
```

Windows

```powershell
where pnpm
```

以下のようになっている必要があります。

```
~/.volta/bin/pnpm
```

---

## 環境確認

```bash
node -v
pnpm -v
volta list
```

---

# 4. Install Dependencies

**必ずリポジトリの root で実行してください。**

```bash
pnpm install
```

pnpm workspace では package ディレクトリで `pnpm install` を実行すると
依存関係が壊れる可能性があります。

---

# 5. Workspace Structure

このリポジトリは **pnpm workspace** で構成されています。

```
packages/
  pkg-a
  pkg-b
  pkg-c
```

各ディレクトリは独立した npm package です。

---

# 6. Development

全パッケージ build

```bash
pnpm build
```

特定 package の build

```bash
pnpm --filter <package> build
```

例

```bash
pnpm --filter engine-client build
```

---

# 7. Dependency Management

依存関係の追加は以下のルールに従ってください。

## 全 package で必要な依存

**workspace root に追加**

```bash
pnpm add <package> -w
```

例

```bash
pnpm add typescript -D -w
```

---

## 特定 package のみで使用する依存

対象 package 内で追加します。

```bash
cd packages/<package>

pnpm add <package>
```

例

```bash
cd packages/engine-client
pnpm add three
```

---

# 8. Versioning

このリポジトリでは **Changesets** を使用してバージョン管理を行います。

変更を加えた場合は changeset を作成してください。

```bash
pnpm changeset
```

質問に従って

* 対象 package
* version bump
* summary

を入力してください。

---

# 9. Release / Publish

main ブランチにマージされると
GitHub Actions が以下を自動実行します。

* version bump
* changelog 更新
* npm publish

通常の変更では **手動 publish は不要です。**

---

# 10. 新規 Package の Publish

新しい package を追加した場合は
**最初の publish のみ手動で行う必要があります。**

```
cd packages/<new-package>
pnpm publish --access public
```

一度 publish されると
以降のリリースは Changesets workflow が自動で処理します。

---

# 11. なぜ Volta を使用するのか

このプロジェクトでは Node / pnpm のバージョン管理に **Volta** を採用しています。

## 自動バージョン切り替え

`package.json` に指定された Node / pnpm のバージョンが
リポジトリに入るだけで **自動的に適用**されます。

## OS に依存しないツールチェーン

Volta は

* macOS
* Linux
* Windows

すべてで同一の動作をします。

## CI / ローカル差異の防止

Node / pnpm のバージョン差異は

* lockfile 不整合
* install エラー
* build 失敗

などの原因になります。

Volta によりツールチェーンを固定することで
**環境差異を最小化できます。**

---

# 12. Troubleshooting

## pnpm が Volta 経由にならない

```bash
which pnpm
```

が

```
~/.volta/bin/pnpm
```

でない場合、PATH を確認してください。

```bash
export VOLTA_HOME="$HOME/.volta"
export PATH="$VOLTA_HOME/bin:$PATH"
```

---

## corepack が干渉する

```bash
corepack disable
```
