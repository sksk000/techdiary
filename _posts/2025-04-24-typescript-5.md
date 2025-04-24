---
title: "TypeScriptにてカスタムフックの作成について学ぶ"
last_modified_at: 2025-04-24T00:00:00+0900
tags:
  - TypeScript
---

## 学んだこと

### カスタムフックを作成してみた

- 前回作成した処理をカスタムフックにして作成した。

#### コンポーネント一覧

- App.tsx
  - 作成したカスタムフックを呼び出すだけにした。
- useAllUsers.ts
  - App 側に書いていた jsonplaceholder からデータを取得する処理をカスタムフックとして分けた。

#### App.tsx

```
import './App.css';
import { UserCard } from './components/UserCard';
import { useAllUsers } from './hooks/useAllUsers';

export default function App() {
  // カスタムフックの戻り値を取得する。
  const { getUsers, userProfiles, loading, error } = useAllUsers();
  // ボタンを押したら全データ取得する関数でデータ取得する。
  const onClickFetchUser = () => getUsers();
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

#### useAllUsers.ts

```
import { useState } from 'react';
import { UserProfile } from '../types/userProfile';
import axios from 'axios';
import { User } from '../types/api/user';

// 全ユーザ一覧を取得するカスタムフック
export const useAllUsers = () => {
  const [userProfiles, setUserProfiles] = useState<Array<UserProfile>>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(false);
  const getUsers = () => {
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
        setError(true);
      })
      .finally(() => {
        setLoading(false);
      });
  };
  return { getUsers, userProfiles, loading, error };
};

```

### カスタムフックを作ってみた結果

- ロジックを別で分けることができるため、管理が楽
- App.tsx のコード量も減った。
- 一度処理を作ってからカスタムフックに分けるかどうかを吟味する必要がありそう。
