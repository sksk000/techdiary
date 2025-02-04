---
title: "トランザクション処理について理解を深める"
last_modified_at: 2025-02-04T00:00:00+0900
tags:
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8
- Ruby 3.1.2p20

## 問題

- トランザクション処理について自信がなかった

## やったこと

### トランザクション処理とは何か？

- 複数の SQL 文を一つの処理で行う仕組みのことを指す。

### RubyonRails での書き方

- ActiveRecord::Base.transaction で SQL の処理を囲むと、トランザクション処理を行うことができる。

```
# User.create!やOrder.create!は例外処理があった場合
# 自動的にロールバックしトランザクション処理を終了する。
ActiveRecord::Base.transaction do
  user = User.create!(name: 'John Doe', email: 'john@example.com')
  order = Order.create!(user_id: user.id, total: 100)
end
```

### なぜトランザクション処理を行う必要があるか

- 一連のデータ処理の途中で問題が発生した場合、それまで操作をすべてロールバックし、データの不整合を防ぐ事ができる。
- データベースに対するアクセスを削減することができるため、パフォーマンス向上を見込める。

### RubyOnRailsにおけるトランザクション有り無し比較

#### トランザクション処理無し

```
# データベースへのアクセスは2回行われる
# UserとOrderのDB2回
user = User.create!(name: 'John Doe', email: 'john@example.com')
order = Order.create!(user_id: user.id, total: 100)

```

#### トランザクション処理あり

```
# データベースへのアクセスは1回行われる
# UserとOrderのDB1回のみ
ActiveRecord::Base.transaction do
  user = User.create!(name: 'John Doe', email: 'john@example.com')
  order = Order.create!(user_id: user.id, total: 100)
end
```

### 結論

- SQL の実行回数が多く見込まれる場合は、トランザクション処理を挟んでデータベースへの処理を記述するべき。

## 参考 URL

- https://qiita.com/mtoyopet/items/67d1cff3df00aa651cb7
- https://swri.jp/glossary/%E3%83%88%E3%83%A9%E3%83%B3%E3%82%B6%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3
