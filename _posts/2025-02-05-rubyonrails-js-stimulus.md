---
title: "RubyonRailsにおけるStimulusの使い方"
last_modified_at: 2025-02-05T00:00:00+0900
tags:
  - JavaScript
  - RubyonRails
---

## 開発環境

- Rails 6.1.7.8
- Ruby 3.1.2p20

## 問題

- JavaScript のライブラリ「Stimulus」を RubyonRails で扱いたい

## やったこと

### Stimulus の導入

- Gemfile に`gem "stimulus-rails"`を追加

### Stimulus 専用のファイルを作成

- Stimulus を使用するには、以下のコマンドを実行しコントローラを作成する。

```
// ◯◯◯にはコントローラの名前を入れる
$ rails generate stimulus ◯◯◯
```

### 作成したコントローラに関数を用意

```
import { Controller } from 'stimulus';

export default class extends Controller {
  // Viewに以下のターゲット名を設定すると、設定した要素の情報を取得できる。
  static targets = ['str'];

  // この関数が呼ばれると、"hogehogefugafuga"というアラートが出力される。
  test() {
    const str = this.strTarget.textContent;
    alert(`hogehoge${str}`);
  }
}
```

### View の設定

- 作成したコントローラを使用するには、View で使用するコントローラを定義。
- 以下の様に定義を行う。

```
// data-controller="コントローラの名前"
<div data-controller="hoge">

  // targetsで設定した'str'をdata-targetに設定する。そうすることで、この要素の情報をJS側で取得できる。
  <h4 data-target="str">fugafuga</h4>

  // ボタンをクリックすると、作成したコントローラ内のtest関数を呼び出す。
  // イベント名->コントローラ内の関数
  <button data-action="click->test">実行</button>
</div>
```

## 参考 URL

- https://qiita.com/eichann/items/d175985fc8eaa597819b
