---
title: "TypeScriptにてカスタムフックについて学ぶ"
last_modified_at: 2025-04-23T00:00:00+0900
tags:
  - TypeScript
---

## 学んだこと

### ライブラリの型について

- TypeScript にて、`react-router-dom`を使いたい場合
  - `@types/react-router-dom`をインストールすることで型定義されている`react-router-dom`を使うことができる。
- TypeScript 用の型定義されているライブラリとされていないライブラリが存在する。
- そのため、TypeScript を使う場合型定義されている TypeScript 用のライブラリを使う。
- ライブラリによっては、型定義がされているライブラリもある。
  - 見分け方としては、ライブラリに`index.d.ts`が入っていればライブラリ内で型定義が行われている。
  - もう一つは`package.json`内に`typings`があり、`index.d.ts`が定義されているかで確認する。

### カスタムフックについて

- ただの関数
- hooks の各機能を使用できる
- コンポーネントからロジックを分離できる
  - 使いまわし、テスト容易、見通しが良くなる。
- 自由に作れる
  - 命名規則として use ～という名前にする。

### カスタムフックを使わないでデータを取得してみる

- jsonplaceholder からデータを取得し、ブラウザに表示する処理を作成する。

#### コンポーネント一覧

- App.tsx
  - UserCard.tsx
    - ユーザデータをカード型でまとめたコンポーネント
    - user.ts
      - api 使う時の型を宣言
    - userProfile.ts
      - jsonplaceholder でデータを取得するときに取得するデータを型で宣言

#### App.tsx

- ローディングの処理とエラーの処理を記述
- jsonholder からデータを取得し、UserCard コンポーネントを使って各データをブラウザに表示
- finally は ES2018 以降で使える処理
  - もし使えない場合は`tsconfig`で`compileroption`を確認し、`lib`に ES2023 を設定すれば使える。(ES2018 以降であれば何でも OK)

```
import './App.css';
import { UserCard } from './components/UserCard';
import { User } from './types/api/user';
import { useState } from 'react';
import { UserProfile } from './types/userProfile';
import axios from 'axios';

export default function App() {
  const [userProfiles, setUserProfiles] = useState<Array<UserProfile>>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(false);
  const onClickFetchUser = () => {
    setLoading(true);
    setError(false);

    axios
      .get<Array<User>>('https://jsonplaceholder.typicode.com/users')
      .then((res) => {
        const data = res.data.map((user) => ({
          id: user.id,
          name: `${user.name}(${user.username})`,
          email: user.email,
          address: `${user.address.city}${user.address.suite}${user.address.street}`,
        }));
        setUserProfiles(data);
      })
      .catch(() => {
        // catch: データを取得出来なかった場合に呼ばれる。
        setError(true);
      })
      .finally(() => {
        // finally: なにが起きてもaxiosの実行完了後に呼ばれる。
        setLoading(false);
      });
  };
  return (
    <div className="App">
      <button onClick={onClickFetchUser}>データ取得</button>
      <br />
      {error ? (
        <p style={{ color: 'red' }}>データの取得に失敗しました。</p>
      ) : loading ? (
        <p>Loading...</p>
      ) : (
        <>
          {userProfiles.map((user) => (
            <UserCard key={user.id} user={user} />
          ))}
        </>
      )}
    </div>
  );
}

```

#### UserCard.tsx

```
import { UserProfile } from '../types/userProfile';
import { FC } from 'react';

type Props = {
  user: UserProfile;
};

export const UserCard: FC<Props> = (props) => {
  const { user } = props;
  const style = {
    border: 'solid 1px #ccc',
    borderRadius: '8px',
    padding: '0 16px',
    margin: '8px',
  };
  return (
    <div style={style}>
      <dl>
        <dt>名前</dt>
        <dd>{user.name}</dd>
        <dt>メール</dt>
        <dd>{user.email}</dd>
        <dt>住所</dt>
        <dd>{user.address}</dd>
      </dl>
    </div>
  );
};

```

#### user.ts

- jsonplaceholder のデータを型として宣言している。

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
        lng: string
      }
      phone: string;
      website: string;
      company: {
        name: string;
        catchPhrase: string;
        bs: string
      }
}
```

#### userProfile.ts

- ブラウザ上に表示するデータを型として宣言している。

```
export type UserProfile = {
  id: number;
  name: string;
  email: string;
  address: string;
};

```

### カスタムフックを使わずに書いてみた結果

- シンプルな機能でも処理が長い
  - コンポーネント自体が肥大化している。
  - 見づらい。
- ロジックを使い回す場合、同じ処理を別のコンポーネントにコピペする他ない。
