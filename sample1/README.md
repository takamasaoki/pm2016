# 名簿の仕様

## 文字コード

リクエスト，レスポンスともにデータは全てUTF-8とする。

## API
| No | メソッド | URL              | 概要                 |
|:---|:---------|:-----------------|:---------------------|
| 1  | GET      | /member/all.php  | 全メンバを取得する   |
| 2  | GET      | /member/id.php   | idを指定する         |
| 3  | GET      | /member/name.php | 名前の一部で絞り込む |

（一つのURLで実装するのがふつうだがわかりやすくするために，三つに分けている。GETはHTTPメソッドの一つだが，今はあまり気にしなくてもよい。）

### 各APIの仕様

#### <a name="no1">No.1 GET /member/all.php

##### 概要

登録されているメンバを取得する。

##### パラメータ

なし

### リクエスト例

http://localhost/member/all.php

#### 結果例

```
1 岩瀬
2 岩橋
3 大木
4 小山
```

#### <a name="no2">No.2 GET /member/name.php

##### 概要

idを指定してメンバを取得する。

##### パラメータ

| パラメータ名 | 必須 | 値                              |
|:-------------|:-----|:--------------------------------|
| foo          | 必須 | 指定されたidのメンバを取得する。|

### リクエスト例

http://localhost/member/id.php?foo=2

#### 結果例

```
2 岩橋
```

#### 課題

リクエスト例をもう一つ作っておく。

#### <a name="no3">No.3 GET /member/name.php

##### 概要

名前の一部がマッチするメンバを取得する。

##### パラメータ

| パラメータ名 | 必須 | 値                                                           |
|:-------------|:-----|:-------------------------------------------------------------|
| bar          | 必須 | 名前での絞込み。指定された値に前方一致するメンバを取得する。 |

### リクエスト例

http://localhost/member/name.php?bar=%E5%A4%A7

`%E5%A4%A7`は「大」をUTF-8でパーセントエンコーディングしたもの。`http://localhost/member/name.php?bar=大`だと環境によっては正しく伝わらない（このページはUTF-8だから大丈夫なのだが）。パーセントエンコーディングしたいときは，Googleで検索してその結果のURLをエディタにコピーするのが簡単。

#### 結果例

```
3 大木
```

#### 課題

リクエスト例をもう一つ作っておく。

## 実装

1. データベースの作成
1. テーブルの作成
1. id.phpの作成，テスト
1. name.phpの作成，テスト

### データベースとテーブルの作成

http://localhost/phpmyadmin/ で「SQL」をクリックして`sample1.sql`を実行する。（GUIで作業することもできるが，このようにSQLのコードを書いた方がいろいろ便利。）

データを入力する。

```
insert into members (id, name) values (1, '岩瀬');
insert into members (id, name) values (2, '岩橋');
insert into members (id, name) values (3, '大木');
insert into members (id, name) values (4, '小山');
```

##### 課題

* GUIでデータを確認し，そのときに実行されたSQL文を確認する。
* GUIでデータを追加し，そのときに実行されたSQL文を確認する。



### 課題（余裕のある人向け）

一つのURL`search.php`ですべてを兼ねるようにする。

| パラメータ名 | 必須 | 値                                                           |
|:-------------|:-----|:-------------------------------------------------------------|
| id           |      | 指定されたidのメンバを取得する。                             |
| name         |      | 名前での絞込み。指定された値に前方一致するメンバを取得する。 |

### リクエスト例

* http://localhost/member/search.php （すべて表示）
* http://localhost/member/search.php?id=1
* http://localhost/member/search.php?name=%E5%A4%A7
* http://localhost/member/search.php?id=1&name=%E5%A4%A7（結果無し）
* http://localhost/member/search.php?id=3&name=%E5%A4%A7

### 課題（余裕のある人向け）

HTTPステータスコードを調べ，結果が無いときに，それを表すコードを返すようにする。
