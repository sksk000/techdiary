---
title: "RuboCopの導入方法について"
last_modified_at: 2025-02-03T00:00:00+0900
tags:
  - Ruby
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8
- Ruby 3.1.2p20

## 問題

- リファクタリングを行いたかった。

## やったこと

### Gemfile に RuboCop 追加

- Gemfile の development、test のグループ内に RuboCop を追加

```
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '3.1.2'

...
group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]

  # ↓に追加
  gem 'rubocop', require: false

  # Rails向けの拡張で、Railsアプリケーション向けのコーディング規約をチェックする
  gem 'rubocop-rails', require: false

  # パフォーマンスをチェックする
  gem 'rubocop-performance', require: false
end
...
```

### RuboCop インストール

- Gemfile 記述後、bundle install を実行しインストールを行う。

```
$ bundle install
```

### RubuCop 実行

- インストール完了後、RuboCop 実行する。

```
$ rubocop
```

- 実行すると、以下の様なログが出力される。

```
...
# 注目するのは大文字のC

# 大文字のCは違反レベルで、レベルはC,R,F,E,Wとある。
# 修正の重要度としては、要修正(F,E,W) 修正の余地あり(C,R)となる。
# 違反レベルの後に続くエラー内容を確認し、修正を行う。
app/models/user.rb:37:7: C: [Correctable] Style/RedundantSelf: Redundant self detected.
      self.posts
      ^^^^

# エラーの数
# 102ファイルをチェックし、50件の違反を確認。そのうち34件は自動修正可能という意味
102 files inspected, 50 offenses detected, 34 offenses autocorrectable
```

- エラーの内容にはよるが、自動修正も可能。
- rubocop のオプションで-a を渡すと、自動修正してもらえる。
- 自動修正後は必ず git diff 等で差分をチェックし、問題ないか確認を行うことを忘れずに

```
$ rubocop -a
```

## 参考 URL

- https://zenn.dev/isawa/articles/ad0da7acdc276a
