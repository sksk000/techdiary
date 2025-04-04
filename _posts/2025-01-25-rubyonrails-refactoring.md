---
title: "ファットコントローラをリファクタリングする方法を整理する"
last_modified_at: 2025-01-25T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8
- Ruby 3.1.2p20

## 問題

- ファットコントローラになっているのでリファクタリングを実施したい。

## やったこと

- コントローラとモデルはどのような処理を書くべきなのか理解を深めた。
- ファットコントローラをリファクタリングを行うために必要なことを理解した。
- モデルのインスタンスメソッドとクラスメソッドの違いを理解した。
- ビジネスロジックの理解を深めた。

### コントローラとモデルの違い

- コントローラ
  - リクエストに応じて、どのビューを表示するか決定する。
- モデル
  - DB の操作を行う、必要に応じて計算処理やバリデーションも行う。

### ファットコントローラをリファクタリングを行う為に必要なこと

- DB からのデータ取得や計算処理はせず、対応するモデルへ移動する。
- バリデーション・クエリ処理をモデルへ移動する。

### モデルのインスタンスメソッドとクラスメソッドの違い

- インスタンスメソッド
  - 各モデルのインスタンス毎に呼ばれる関数。
  - C++や C#だとメンバ関数と同様。
  - 各モデルのインスタンス毎に呼び出したい関数を定義する。
- クラスメソッド
  - クラス自体に関連している関数。
  - C++や C#だと static がついている関数を同様。
  - 共通処理を関数化したい場合に定義する。

### ビジネスロジックとは

- システムの固有処理のことを指す。
  - 例えば、ショッピングサイトであれば送料計算や合計金額を計算が該当する。

#### ビジネスロジックはどこに書くべきか

- モデルに書くべき
  - 保守性と可読性が上がる。
  - コントローラの処理が薄くなる。
  - 単体テストがしやすい。

## 参考 URL

- https://qiita.com/os1ma/items/25725edfe3c2af93d735
- https://wa3.i-3-i.info/word13666.html
- https://kc-masui-programmer.hatenablog.com/entry/2019/05/19/160023
