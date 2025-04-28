---
title: "TypeScriptとReactでChakraUIを使いアプリを作成(ログイン機能・メッセージ表示機能)"
last_modified_at: 2025-04-28T00:00:00+0900
tags:
  - TypeScript
  - React
---

## 学んだこと

### ログイン画面作成

- Login.tsx
  - ログインボタンを押して、ユーザ情報と一致すれば Home ページヘ遷移する。
  - ログインボタンは入力がない限り有効化しない。
  - ログイン処理は、`useAuth.ts`で行うようにした
    - カスタムフックに分けることで、処理を追いやすくした。

```
import { Box, Divider, Flex, Heading, Input, Stack } from "@chakra-ui/react";
import React, { ChangeEvent, memo, useState, VFC } from "react";
import { PrimaryButton } from "../atoms/button/PrimaryButton";
import { useAuth } from "../../hooks/useAuth";

export const Login: VFC = memo(() => {
  const { login, loading } = useAuth();
  const [userId, setUserId] = useState("");
  const onChangeUserId = (e: ChangeEvent<HTMLInputElement>) =>
    setUserId(e.target.value);
  const onClickLogin = () => login(userId);
  return (
    <Flex align="center" justify="center" height="100vh">
      <Box bg="white" w="sm" p={4} borderRadius="md" shadow="md">
        <Heading as="h1" size="lg" textAlign="center">
          ユーザー管理アプリ
        </Heading>
        <Divider my={4} />
        <Stack spacing={6} py={4} px={10}>
          <Input
            placeholder="ユーザーID"
            value={userId}
            onChange={onChangeUserId}
          />
          <PrimaryButton
            onClick={onClickLogin}
            loading={loading}
            disabled={userId === ""}
          >
            ログイン
          </PrimaryButton>
        </Stack>
      </Box>
    </Flex>
  );
});

```

- useAuth.ts
  - ログイン時の処理を記述したコンポーネント。
    - jsonplaceholder を使い、ユーザデータを取得
    - 取得したデータとログイン時のデータがあっていれば home ページへ遷移する。
    - ローディング用の state を用意し、ローディング中かどうか判断できるようになる。

```
import axios from "axios";
import { useCallback, useState } from "react";
import { User } from "../types/api/user";
import { useHistory } from "react-router-dom";

export const useAuth = () => {
  const history = useHistory();
  const [loading, setLoading] = useState(false);
  const login = useCallback(
    (id: string) => {
      setLoading(true);
      axios
        .get<User>(`https://jsonplaceholder.typicode.com/users/${id}`)
        .then((res) => {
          if (res.data) {
            history.push("/home");
          } else {
            alert("ユーザーが見つかりませんでした。");
          }
        })
        .catch(() => alert("ログイン出来ません"))
        .finally(() => setLoading(false));
    },
    [history]
  );

  return { login, loading };
};

```

- user.ts
  - jsonplaceholder 側から取得するデータ一覧の型を宣言している。

```
export type User = {
  id: number;
  name: string;
  username: string;
  email: string;
  address: {
    street: string;
    suite: string;
    city: string;
    zipcode: string;
    geo: {
      lat: string;
      lng: string;
    };
  };
  phone: string;
  website: string;
  company: {
    name: string;
    catchPhrase: string;
    bs: string;
  };
};

```

- PrimaryButton.tsx
  - ログインボタンで使用しているコンポーネント
  - ローディングしてるか、有効化されているかなどのステータスを取得できるようにした。

```
import React, { memo, ReactNode, VFC } from "react";
import { Button } from "@chakra-ui/react";

type Props = {
  children: ReactNode;
  disabled?: boolean;
  loading?: boolean;
  onClick: () => void;
};

export const PrimaryButton: VFC<Props> = memo((props) => {
  const { children, disabled = false, loading = false, onClick } = props;
  return (
    <Button
      bg="teal.400"
      color="white"
      _hover={{ opacity: 0.8 }}
      isLoading={loading}
      disabled={disabled || loading}
      onClick={onClick}
    >
      {children}
    </Button>
  );
});

```

### メッセージ表示機能作成

- useMessage.ts
  - ChakraUI の toast を使ってメッセージ表示機能を作成。
  - Props の Status の｜表現は、どれかの文字列しか受け取れないようになる。

```
import { useToast } from "@chakra-ui/react";
import { useCallback } from "react";

type Props = {
  title: string;
  status: "info" | "warning" | "success" | "error";
};
export const useMessage = () => {
  const toast = useToast();
  const showMessage = useCallback(
    (props: Props) => {
      const { title, status } = props;
      toast({
        title,
        status,
        position: "top",
        duration: 2000,
        isClosable: true,
      });
    },
    [toast]
  );

  return { showMessage };
};

```

- useAuth.ts
  - 上記で作成したメッセージ表示機能を各種エラーメッセージを出力する箇所で使用。
  - ログイン成功時にも機能を使ってメッセージを表示。

```
import axios from "axios";
import { useCallback, useState } from "react";
import { User } from "../types/api/user";
import { useHistory } from "react-router-dom";
import { useMessage } from "./useMessage";

export const useAuth = () => {
  const history = useHistory();
  const { showMessage } = useMessage();
  const [loading, setLoading] = useState(false);
  const login = useCallback(
    (id: string) => {
      setLoading(true);
      axios
        .get<User>(`https://jsonplaceholder.typicode.com/users/${id}`)
        .then((res) => {
          if (res.data) {
            showMessage({ title: "ログインしました", status: "success" });
            history.push("/home");
          } else {
            showMessage({
              title: "ユーザーが見つかりませんでした",
              status: "error",
            });
          }
        })
        .catch(() =>
          showMessage({
            title: "ログイン出来ませんでした",
            status: "error",
          })
        )
        .finally(() => setLoading(false));
    },
    [history, showMessage]
  );

  return { login, loading };
};

```
