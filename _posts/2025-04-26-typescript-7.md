---
title: "TypeScriptとReactでChakraUIを使いアプリを作成(スタイル設定・ルーティング)"
last_modified_at: 2025-04-26T00:00:00+0900
tags:
  - TypeScript
  - React
---

## 学んだこと

### グローバルなスタイル設定

- theme.ts
  - グローバルなテーマを作成
  - 色名の後に設定されている値が高いほど色の濃さが違う

```
import { extendTheme } from "@chakra-ui/react";

export const theme = extendTheme({
  styles: {
    global: {
      body: {
        backgroundColor: "gray.100",
        color: "gray.800",
      },
    },
  },
});

```

### ルーティングページ

- App.tsx
  - 上記で作成したテーマを適用

```
import React from "react";
import "./App.css";
import { Button, ChakraProvider } from "@chakra-ui/react";
import { theme } from "./theme/theme";

export default function App() {
  return (
    <ChakraProvider theme={theme}>
      <Button colorScheme="teal">ボタン</Button>
      <p>ああああああ</p>
    </ChakraProvider>
  );
}

```

### ルーティングの作成

- ルーティングは以下の通り
  - ログインページ
    - ホームページ
    - 設定ページ
    - ユーザ管理ページ

```
import { Route, Switch } from "react-router-dom";
import { Login } from "../component/pages/Login";
import { homeRoutes } from "./HomeRoutes";
import React, { memo, VFC } from "react";
import { Page404 } from "../component/pages/Page404";

export const Router: VFC = memo(() => {
  return (
    <Switch>
      <Route exact path="/">
        <Login />
      </Route>
      <Route
        path="/home"
        render={({ match: { url } }) => (
          <Switch>
            {homeRoutes.map((route) => (
              <Route
                key={route.path}
                exact={route.exact}
                path={`${url}${route.path}`}
              >
                {route.children}
              </Route>
            ))}
          </Switch>
        )}
      />
      <Route path="*">
        <Page404 />
      </Route>
    </Switch>
  );
});

```

- HomeRoutes.tsx
  - Home 配下のルーティングを設定。

```
import { Home } from "../component/pages/Home";
import { Setting } from "../component/pages/Setting";
import { UserManagement } from "../component/pages/UserManagement";
import React from "react";

export const homeRoutes = [
  {
    path: "/",
    exact: true,
    children: <Home />,
  },
  {
    path: "/user_management",
    exact: false,
    children: <UserManagement />,
  },
  {
    path: "/setting",
    exact: false,
    children: <Setting />,
  },
];

```
