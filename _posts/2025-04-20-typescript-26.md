---
title: "TypeScriptの概要、型の種類、引数と戻り値の型指定について学ぶ"
last_modified_at: 2025-04-20T00:00:00+0900
tags:
  - TypeScript
---

## 学んだこと

### TypeScript とは

- Microsoft が開発したオープンソースの言語
- JavaScript に「型」という概念
  - より安全にバグが少なく開発できる
- 開発者間で意識を合わせやすい
- エディタでコード補完も効くので開発効率が上がる
- コンポーネント指向の React と非常に相性が良い
- 新規開発で使わない理由はない。

### 基本的な型

```
// boolean型
// bool型
let bool: boolean = true;

//number型
// int型？と同じ
let num: number = 0;

// string型
// stringの型
let str: string = 'A';

// Array型
// 配列
let arr1: Array<number> = [0, 1, 2];
let arr2: number[] = [0, 1, 2];

//　tuple型
// 使わない
let tuple: [number, string] = [0, 'A'];

// any型
// 何でも入る型(C++とかにあるauto型？)
// あまり使わないようにする
let any1: any = false;

// void型
// 関数の戻り値設定で使う
// Cなどと一緒、省略可能
const funcA = (): void => {
  const test = 'TEST';
};

// null型
// nullが入る型
let null1: null = null;

// undefined型
// undefinedが入る型
let undifined1: undefined = undefined;

// object型
// 上記の使い方はほとんどないが、下記の使い方は頻繁にある
let obj1: object = {};
let obj2: { id: number; name: string } = { id: 0, name: 'AAAA' };

```

### 引数の型指定

```
export const Practice1 = () => {
  // この場合はstringになるため、エラーとなった。
  //const onClickPractice = () => calcTotalFee('100');
  // number型なのでエラーにならない
  const onClickPractice = () => calcTotalFee(100);

  // 引数にnumberを設定した。
  // 引数にnumber以外を設定した場合、エラーとなる
  const calcTotalFee = (num: number) => {
    const total = num * 1.1;
    console.log(total);
  };
  return (
    <div>
      <p>練習問題:引数の型指定</p>
      <button onClick={onClickPractice}>練習問題1を実行</button>
    </div>
  );
};

```

### 戻り値の型指定

- 戻り値を明示的に設定するときとしない時がある。
  - 明らかに戻り値で返す値がわかる場合は明示的にかく必要はない。

```
export const Practice2 = () => {
  const onClickPractice = () => console.log(getTotalFee(100));
  // 戻り値は引数の次に設定する。
  const getTotalFee = (num: number): number => {
    const total = num * 1.1;
    // returnで値を返す
    return total;
  };
  return (
    <div>
      <p>練習問題:戻り値の設定</p>
      <button onClick={onClickPractice}>練習問題2を実行</button>
    </div>
  );
};

```
