---
title: "TypeScriptの変数の型指定"
last_modified_at: 2025-04-21T00:00:00+0900
tags:
  - TypeScript
---

## 学んだこと

### 変数の型指定

- 関数の戻り値に型指定していない場合
  - 戻り値を変数に格納することで、戻り値を受け取った変数の型が想定しているものか確認することができる。
  - そのため、安全も考慮できる。

```
export const Practice3 = () => {
  const onClickPractice = () => {
    let total: number = 0;
    total = getTotalFee(100);
    console.log(total);
  };
  const getTotalFee = (num: number)=> {
    const total = num * 1.1;
    return total.toString();
  };
  return (
    <div>
      <p>練習問題:変数の型指定</p>
      <button onClick={onClickPractice}>練習問題4を実行</button>
    </div>
  );
};

```

### tsconfig について

- complierOption にある、strict はある程度厳しいルールを設定している。true のままで OK
- 下記は strict が true になっている場合のエラーの一例。
  - もし、引数に型を指定したくない場合
  - complierOption 内に`noImplicitAny: false`を追加すると strict 側のエラーを解消することができる。

```
export const Practice4 = () => {
  const onClickPractice = () => calcTotalFee(100);
  // strictがtrueの場合numでエラーが出る。
  // 理由としては、引数に型を指定していないため。
  const calcTotalFee = (num) => {
    const total = num * 1.1;
    console.log(total);
  };
  return (
    <div>
      <p>練習問題:設定ファイルの変更</p>
      <button onClick={onClickPractice}>練習問題4を実行</button>
    </div>
  );
};
```

### 型を宣言しないことのデメリット

- 変数や引数にて型を宣言してないため、外部から受け取った値を正常に受け取ることが出来ない。
- そのため、バグが発生してしまう。

### 実際に型を宣言せずにコードを書いてみる

- 今回は jsonplaceholder からデータを取得し、そのデータをブラウザ上に表示する。

#### 型を宣言せずにコードを実行すると・・

- axios を使ってデータを取得しブラウザに表示後、id が undefined と表示された。
  - 理由としては、表示する際に Todo に渡す userId が typo しており userid となっていた。
  - だが表示する値をそもそも型宣言をせずに実装していたため気づくことが出来なかった。

#### App.tsx

```
import { useState } from 'react';
import './App.css';
import axios from 'axios';
import { Todo } from './Todo';

export default function App() {
  const [todos, setTodos] = useState<any>([]);
  const onClickFetchData = () => {
    axios.get('https://jsonplaceholder.typicode.com/todos').then((res) => {
      setTodos(res.data);
    });
  };
  return (
    <div className="App">
      <button onClick={onClickFetchData}>データ取得</button>
      // ここのuseridは本来userIdで渡す必要がある
      {todos.map((todo) => (
        <Todo title={todo.title} userid={todo.userid} />
      ))}
    </div>
  );
}

```

#### Todo.tsx

```
export const Todo = (props) => {
  const { title, userid } = props;
  return <p>{`${title}(ユーザ:${userid})`}</p>;
};

```

### 修正後

- state や表示時にわたす値を型宣言することでコードを見る側もそうだが、コードを書く側もわかりやすくなった。
- 型を決めておくことで、型に関連する関数などの候補を見ることができる為楽になった。

#### App.tsx

```
import { useState } from 'react';
import './App.css';
import axios from 'axios';
import { Todo } from './Todo';

type TodoType = {
  userId: number;
  id: number;
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
        <Todo title={todo.title} userid={todo.userId} />
      ))}
    </div>
  );
}

```
