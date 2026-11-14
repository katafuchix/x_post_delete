# X Post Delete

X（旧Twitter）の過去のポストを、ブラウザ上からまとめて削除するJavaScriptです。

[backzso/tweetdelete](https://github.com/backzso/tweetdelete) をベースに、Chromeでアーカイブファイルの選択ダイアログが開かない問題に対応したものです。

> [!WARNING]
> **削除したポストは復元できません。** 実行前にXのアーカイブをバックアップし、削除対象とログイン中のアカウントを確認してください。
> 本スクリプトはXの非公式な内部APIを利用します。X側の仕様変更で動作しなくなる場合があり、利用規約やアカウント制限にも注意が必要です。

## 主な機能

- **アーカイブモード**：Xからダウンロードした `data/tweets.js` に記録されたポストを順次削除
- **タイムラインモード**：プロフィールの現在のタブに表示されるポストやリポストを処理
- **ファイル選択ボタン**：Chromeの「File chooser dialog can only be shown with a user activation.」への対応
- **進捗・結果表示**：アーカイブモードは50件ごとに処理数と成功・失敗件数をConsoleへ表示
- **レート制限への対応**：429レスポンス時に待機し、利用可能なレート制限情報に応じてリクエスト間隔を調整
- **途中停止**：Consoleで `STOP_DELETE = true` を実行して停止を指示

## 動作環境

- Xにログイン済みのブラウザ（Google Chromeで動作確認）
- Chromeの開発者ツール（Console）
- アーカイブモードでは、Xから取得した `tweets.js` ファイル

Python、Node.js、X Developer ConsoleのAPIキーは不要です。

## 使い方

1. Chromeで [X](https://x.com/) にログインし、**自分のプロフィール**を開きます。
2. Macなら `Command + Option + J`、Windowsなら `Ctrl + Shift + J` でConsoleを開きます。
3. [`x_post_delete.js`](./x_post_delete.js) の内容を確認してコピーし、Consoleに貼り付けて実行します。
4. 確認ダイアログでモードを選択します。
   - **OK**：アーカイブモード
   - **キャンセル**：タイムラインモード（削除処理そのもののキャンセルではありません）
5. アーカイブモードの場合は、画面に表示される **「アーカイブファイルを選択」** ボタンをクリックし、`data/tweets.js` を選びます。**選択後、削除処理が始まります。**
6. Consoleで進捗・エラーを確認します。

[X（旧Twitter）の過去ポストを無料で一括削除する方法【API不要・アーカイブ対応】](https://qiita.com/katafuchix/items/f8f268ef3538bdea1267)

### アーカイブモードのログ例

```text
Found 2253 entries in the archive. Starting deletion...
To stop: STOP_DELETE = true
... 50/2253 processed
Deleted: 50 | Unretweeted: 0 | Already gone: 0 | Failed: 0
... 100/2253 processed
Deleted: 100 | Unretweeted: 0 | Already gone: 0 | Failed: 0
```

※上記は実行中に確認したログ例で、全2,253件の削除完了を示すものではありません。

### 途中で停止する

Consoleに次のコードを入力してEnterを押します。

```javascript
STOP_DELETE = true
```

停止が反映されるまでに進行中のリクエストが完了する場合があります。また、**停止以前に削除済みのポストは復元できません**。

## 設定

`x_post_delete.js` の冒頭で調整できます。

| 変数 | 初期値 | 内容 |
| --- | --- | --- |
| `DELETE_DELAY_MS` | `400` | 削除リクエスト間の基本待機時間（ミリ秒） |
| `SMOOTH_PACING` | `true` | レート制限情報に応じて待機間隔を調整 |
| `SCROLL_DELAY_MS` | `1500` | タイムライン末尾で次の読み込みを待つ時間 |
| `MAX_IDLE_SCROLLS` | `12` | 新しい投稿が見つからない場合の停止基準 |

> [!NOTE]
> APIや画面の挙動は変更される可能性があります。429待機があるため、大量削除には時間がかかることがあります。画面上の投稿件数とアーカイブ内の件数は必ずしも一致しません。

## 注意事項

- **自分が管理するアカウントでのみ使用**してください。
- アーカイブの対象ファイルを選ぶと、そのファイルに含まれる投稿が削除対象になります。事前にバックアップを残してください。
- タイムラインモードは表示・読み込みできた範囲を処理するため、古い投稿をすべて処理できるとは限りません。
- スクリプトを実行する前に、ソースコードを読んで内容を確認してください。ログイン中のセッションを使ってXへリクエストします。
- 正式なX APIではなく、Web版Xの内部APIを利用しています。仕様変更や利用制限によって動かなくなる可能性があります。

## 元プロジェクト・クレジット

このスクリプトは [backzso/tweetdelete](https://github.com/backzso/tweetdelete) をベースにしています。元の作者・貢献者に感謝します。

主な変更点は、アーカイブ選択時にクリック可能なボタンを追加し、Chromeのファイル選択制限に対応したことです。

再配布にあたっては、元プロジェクトのライセンスと著作権表示を確認し、必要な `LICENSE` ファイルや表示を保持してください。
