---
title: "TypeScriptにてコンポーネント自体の型やpropsの型や、オプショナルチェイングについて学ぶ"
last_modified_at: 2025-04-22T00:00:00+0900
tags:
  - TypeScript
---

## 学んだこと

### props に型を定義する

- 今回は、外部のデータにある completed を確認し、データ取得ボタンを押したときに[未]か[完]をつけて表示したい。

#### App.tsx

- completed を Todo コンポーネントの引数に設定する

```
import { useState } from 'react';
import './App.css';
import axios from 'axios';
import { Todo } from './Todo';

type TodoType = {
  userId: number;
  title: string;
  completed: boolean;
};

export default function App() {
  const [todos, setTodos] = useState<Array<TodoType>>([]);
  const onClickFetchData = () => {
    axios
      .get<Array<TodoType>>('https://jsonplaceholder.typicode.com/todos')
      .then((res) => {
        setTodos(res.data);
      });
  };
  return (
    <div className="App">
      <button onClick={onClickFetchData}>データ取得</button>
      {todos.map((todo) => (
        <Todo
          title={todo.title}
          userId={todo.userId}
          completed={todo.completed}
        />
      ))}
    </div>
  );
}

```

#### Todo.tsx

- completed が false の場合は{完}、そうでない場合は{未}と表示する。
- props に型を宣言し、completed の初期値を false に設定する。
  - そうすることで、データ自体の初期値が undefined だとしても false になる。

```
type TodoType = { userId: number; title: string; completed?: boolean };

export const Todo = (props: TodoType) => {
  const { title, userId, completed = false } = props;
  const completedMark = completed ? '{完}' : '{未}';
  return <p>{`${completedMark}${title}(ユーザ:${userId})`}</p>;
};

```

### 型定義を効率的に管理する

- TodoType を別ファイルに定義し、他のファイルでも使えるようにする。

#### todo.ts

- export することで、他ファイルでも TodoType を使えるようにする。

```
export type TodoType = {
  userId: number;
  id: number;
  title: string;
  completed: boolean;
};

```

#### App.tsx

```
import { useState } from 'react';
import './App.css';
import axios from 'axios';
import { Todo } from './Todo';
import { TodoType } from './types/todo';

export default function App() {
  const [todos, setTodos] = useState<Array<TodoType>>([]);
  const onClickFetchData = () => {
    axios
      .get<Array<TodoType>>('https://jsonplaceholder.typicode.com/todos')
      .then((res) => {
        setTodos(res.data);
      });
  };
  return (
    <div className="App">
      <button onClick={onClickFetchData}>データ取得</button>
      {todos.map((todo) => (
        <Todo
          key={todo.id}
          title={todo.title}
          userId={todo.userId}
          completed={todo.completed}
        />
      ))}
    </div>
  );
}

```

#### Todo.tsx

- Omit を使うことで、TodoType で使わない変数を削除した状態で定義を使うことができる。
  - この場合、TodoType の id は使わない為削除している。
- Omit の他に、Pick もある
  - Pick は使いたい変数を定義する。
  - `Pick<TodoType, 'title'|'userId'|'completed'>`

```
import { TodoType } from './types/todo';

export const Todo = (props: Omit<TodoType, 'id'>) => {
  const { title, userId, completed = false } = props;
  const completedMark = completed ? '{完}' : '{未}';
  return <p>{`${completedMark}${title}(ユーザ:${userId})`}</p>;
};

```

### コンポーネント自体の型定義

- 関数定義の前に FC と型名をつけることで、コンポーネント自体に型を付けることができる。

```
import { FC } from 'react';

type Props = {
  color: string;
  fontSize: string;
};

export const Text: FC<Props> = (props) => {
  const { color, fontSize } = props;
  return <p style={{ color, fontSize }}>テキストです</p>;
};

```

### オプショナルチェイングで NULL 安全なコードを書く

- 新たにユーザ情報を用意する事になり、以下の実装を行った。

#### user.ts

- User の型宣言用のファイル
  - hobbies は省略 OK の変数

```
export type User = {
  name: string;
  hobbies?: Array<string>;
};

```

#### UserProfile.tsx

- hobbies は省略 OK の変数のため、変数自体に値が入ってくるかどうか分からない。
- そのため、オプショナルチェイングを施し、NULL にならないように工夫する。

```
import { User } from './types/user';
import { FC } from 'react';

type Props = {
  user: User;
};

export const UserProfile: FC<Props> = (props) => {
  const { user } = props;
  return (
    <dl>
      <dt>名前</dt>
      <dd>{user.name}</dd>
      <dt>趣味</dt>
      <dd>{user.hobbies?.join('/')}</dd>
    </dl>
  );
};

```
