---
title: "TypeScriptにてカスタムフックの作成について学ぶ"
last_modified_at: 2025-04-25T00:00:00+0900
tags:
  - TypeScript
---

## 学んだこと

### ChakraUI を使ってアプリを作成

- 今回は ChakraUI を使って小さいアプリを作成してみる。

### 環境構築

- 今回 Docker にて TypeScript ＋ React の環境構築を行った。
- 教材が react17 系統のため環境をあわせる。
- Docker を使って開発したかったので、Docker も使えるようにした。

### 環境構築の流れ

- コマンドで react と typescript を使ったプロジェクトを作成

```
mkdir typescript-react-app
npx create-react-app . --template typescript
```

- 依存関係を無視して、各種ライブラリをインストール
  - 依存関係を考えてインストールするのは大変だったので、一旦無視してインストールするようにした。
  - TypeScript4.1 にアップグレードした意味は、`error TS6046: Argument for '--jsx' option must be: 'preserve', 'react-native', 'react'.`のエラーを解決するため。
    - アップグレードしないと、`--jsx react-jsx`を TypeScript 側が認識しない。

```
# プロジェクト作成時に入ったライブラリを一旦削除
Remove-Item -Recurse -Force node_modules, package-lock.json

# 本番環境構築、衝突防止として--legacy-peer-depsをつける
npm install --save-exact `
  react@17.0.1 react-dom@17.0.1 react-scripts@4.0.0 `
  @chakra-ui/react@1.2.1 @chakra-ui/icons@1.0.17 `
  @emotion/react@11.1.4 @emotion/styled@11.0.0 `
  framer-motion@4.1.17 react-router-dom@5.2.0 axios@0.21.1 `
  --legacy-peer-deps

# 開発環境構築、衝突防止として--legacy-peer-depsをつける
npm install --save-dev --save-exact `
  typescript@3.9.10 `
  @types/react@17.0.0 @types/react-dom@17.0.0 `
  @types/react-router-dom@5.1.7 `
  --legacy-peer-deps

# TypeScript4.1に上げる
npm install -E -D typescript@4.1.6 --legacy-peer-deps
```

- tsconfig.json を React17 用で動かしたい為、compilerOptions の jsx を react に変更

```
{
  "compilerOptions": {
    ...
    "jsx": "react"
  },
  "include": ["src"]
}
```

- index.tsx を React17 で動かせるように変更

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

// React 17 まで の標準描画メソッド
// React 18 は、createRootを使用する
ReactDOM.render(
  <React.StrictMode><App /></React.StrictMode>,
  document.getElementById('root')
);
```

- Dockerfile

```
FROM node:14

WORKDIR /app

# npmをv8系統にアップグレード
# --legacy-peer-depsでpeer依存を許容
# peer依存: 並走の依存関係を宣言する仕組み
COPY package*.json ./
RUN npm install -g npm@8 && npm install --legacy-peer-deps

COPY . .

EXPOSE 3000
CMD ["npm", "start"]
```

- docker-compose-yml

```
version: "3.8"
services:
  react:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - CHOKIDAR_USEPOLLING=true
    stdin_open: true
    tty: true
```

#### Peer 依存

- 例えば ChakraUI と React
  - ChakraUI 自身は React を同梱していないが、導入するプロジェクトに React が入っていることを前提に動作する。
- なぜ必要か
  - 重複を防ぐ
    - 各 UI ライブラリが React を持つと、データ自体膨れ上がる
  - バージョン整合性
    - React17 React18 が同時ロードしてクラッシュするかもしれない。
    - そのため、peerDependencies 内でサポート範囲を宣言し、ズレを検知する。
