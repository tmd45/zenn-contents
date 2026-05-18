---
title: "Redash の Query Result をパラメータ付きで参照する"
emoji: "📊"
type: "tech"
topics: ["redash"]
publication_name: "socialplus"
published: true
---

Redash v10.1 より後（おそらく v25 系から）利用できるようになった、Query Results Data Source (QRDS) のクエリパラメータ指定を含む呼び出しの使い方について、2026/05/18 時点の[公式ユーザーガイドでは、単純な整数値（id）の指定方法の例しか無い](https://redash.io/help/user-guide/querying/query-results-data-source/)ため、複数指定の方法を雑に[^1]確認したメモ記事です。

[^1]: 雑に＝Redash のソースコードを読まずに済ませたという意味で。

```sql title="Query 12345"
-- 参照するクエリ（Query 12345）
-- Query Parameters を出力するだけのクエリ
SELECT
  "{{DATE1}}" AS date1,
  "{{DATE2}}" AS date2,
  "{{DATERANGE.start}}" AS d_start,
  "{{DATERANGE.end}}" AS d_end
;
```

⚠️ 上記例では名前を DATE にしているが、この値は DATE 型に準拠した形式で入力されるとは限らない。パラメータとしてはあくまでも文字列の受け渡しとなる。

以下の呼び出し方の例ではあえて短縮した datetime 形式**のような文字列**を指定している。

```sql title="Query Results Data Source"
-- クエリを呼び出す際の指定方法
SELECT *
FROM param_query_12345_{DATE1=2026-03-15 12:34&DATE2=2026-04-15 12:34&DATERANGE.start=2026-04-14 12:34&DATERANGE.end=2026-05-14 12:34}
;
```
わかったこと:

- 複数パラメータの区切りは `&`
- 空白は空白のまま指定可能（ `%20` 指定も可能。例 `2026-03-15%2012:34`）
- 指定する値をダブルクオート `"` などで囲うとシンタックスエラーになるため不要
- Date Range タイプを指定する場合、単純に `PARAM.start` `PARAM.end` に値を渡す形式で利用できる
- ハイフンが連続する文字列 `--` は SQL のコメント書式になってしまうため指定できない[^2]

[^2]: やり方があればぜひ教えてください。とはいえ使う予定も無いですが……

ほかに、便利な指定方法があったらぜひ教えてください。

## 参考ドキュメント

- [Query Parameters - Redash Knowledge Base](https://redash.io/help/user-guide/querying/query-parameters/)
- [Querying Existing Query Results - Redash Knowledge Base](https://redash.io/help/user-guide/querying/query-results-data-source/)
