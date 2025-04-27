---
title: "TypeScriptとReactでChakraUIを使いアプリを作成(ヘッダー作成)"
last_modified_at: 2025-04-28T00:00:00+0900
tags:
  - TypeScript
  - React
---

## 学んだこと

### ヘッダーの作成

- Header.tsx
  - ハンバーガーメニューを用意
    - メニューにある TOP、ユーザ一覧、設定ページすべて飛べるように設定
    - メニューアイコン、メニュー内表示、ヘッダーのレイアウトは肥大化していたためコンポーネント化した。

```
/* eslint-disable react-hooks/exhaustive-deps */
import { Box, Flex, Heading, Link, useDisclosure } from "@chakra-ui/react";
import React, { memo, useCallback, VFC } from "react";
import { MenuIconButton } from "../../atoms/button/MenuIconButton";
import { MenuDrawer } from "../../molecules/MenuDrawer";
import { useHistory } from "react-router-dom";

export const Header: VFC = memo(() => {
  const { isOpen, onOpen, onClose } = useDisclosure();
  const history = useHistory();
  const onClickHome = useCallback(() => history.push("/home"), []);
  const onClickUserManagement = useCallback(
    () => history.push("/home/user_management"),
    []
  );
  const onClickSetting = useCallback(() => history.push("/home/setting"), []);
  return (
    <>
      <Flex
        as="nav"
        bg="teal.500"
        color="gray.50"
        align="center"
        justify="space-between"
        padding={{ base: 3, md: 5 }}
      >
        <Flex
          align="center"
          as="a"
          mr={8}
          _hover={{ cursor: "pointer" }}
          onClick={onClickHome}
        >
          <Heading as="h1" fontSize={{ base: "md", md: "lg" }}>
            ユーザ管理アプリ
          </Heading>
        </Flex>
        <Flex
          align="center"
          fontSize="sm"
          flexGrow={2}
          display={{ base: "none", md: "flex" }}
        >
          <Box pr={4}>
            <Link onClick={onClickUserManagement}>ユーザ一覧</Link>
          </Box>
          <Link onClick={onClickSetting}>設定</Link>
        </Flex>
        <MenuIconButton onOpen={onOpen} />
      </Flex>
      <MenuDrawer
        onClose={onClose}
        isOpen={isOpen}
        onClickHome={onClickHome}
        onClickSetting={onClickSetting}
        onClickUserManagement={onClickUserManagement}
      />
    </>
  );
});

```

- MenuIconButton.tsx

```
import React, { memo, VFC } from "react";
import { HamburgerIcon } from "@chakra-ui/icons";
import { IconButton } from "@chakra-ui/react";

type Props = {
  onOpen: () => void;
};

export const MenuIconButton: VFC<Props> = memo((props) => {
  const { onOpen } = props;
  return (
    <IconButton
      aria-label="メニューボタン"
      icon={<HamburgerIcon />}
      size="sm"
      variant="unstyled"
      display={{ base: "block", md: "none" }}
      onClick={onOpen}
    />
  );
});

```

- MenuDrawer.tsx

```
import {
  Button,
  Drawer,
  DrawerBody,
  DrawerContent,
  DrawerOverlay,
} from "@chakra-ui/react";
import React, { memo, VFC } from "react";

type Props = {
  onClose: () => void;
  isOpen: boolean;
  onClickHome: () => void;
  onClickUserManagement: () => void;
  onClickSetting: () => void;
};

export const MenuDrawer: VFC<Props> = memo((props) => {
  const {
    onClose,
    isOpen,
    onClickHome,
    onClickUserManagement,
    onClickSetting,
  } = props;
  return (
    <Drawer placement="left" size="xs" onClose={onClose} isOpen={isOpen}>
      <DrawerOverlay>
        <DrawerContent>
          <DrawerBody p={0} bg="gray.100">
            <Button w="100%" onClick={onClickHome}>
              TOP
            </Button>
            <Button w="100%" onClick={onClickUserManagement}>
              ユーザ一覧
            </Button>
            <Button w="100%" onClick={onClickSetting}>
              設定
            </Button>
          </DrawerBody>
        </DrawerContent>
      </DrawerOverlay>
    </Drawer>
  );
});

```

- HeaderLayout.tsx

```
import React, { memo, ReactNode, VFC } from "react";
import { Header } from "../organisms/layout/Header";

type Props = {
  children: ReactNode;
};

export const HeaderLayout: VFC<Props> = memo((props) => {
  const { children } = props;
  return (
    <>
      <Header />
      {children}
    </>
  );
});

```

### ヘッダー作成を終えて

- 自分が使ったことがある UI 系のライブラリとして bootstrap がある。bootstrap は class 内にスタイルを書く必要があった。
- chakraUI は直感的に書くことができるしエディタの入力補完が効くのがとても便利だと思った。
- bootstrap は一々スタイル名で検索し、スタイルをあてていかないと行けない。。
- 結果 chakraUI のほうが断然書きやすかった。
