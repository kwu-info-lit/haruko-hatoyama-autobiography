# EPUBリーダー（GitHub Pages）

`book.epub` をブラウザで縦書きのまま読むための専用リーダーです。外部ライブラリには依存していません。

## 構成

| ファイル | 役割 |
| --- | --- |
| `index.html` | 画面の骨組み（ツールバー・目次・検索・表示設定） |
| `reader.css` | UIのスタイルと本文の組版（`@layer book` / `@layer reader`） |
| `reader.js` | アプリ本体（操作・設定・進捗・読書位置の保存） |
| `lib/zip.js` | ZIP展開（ブラウザ標準の `DecompressionStream` を使用） |
| `lib/epub.js` | EPUB解析（OPF・目次・スタイルシート・フォント） |
| `lib/pager.js` | CSS段組によるページ分割と読書位置（アンカー）の管理 |
| `lib/search.js` | 本文検索（ルビを除いた本文で照合） |

## 更新方法

EPUBを作り直したら、生成された `book.epub` をこのフォルダに上書きコピーするだけで反映されます。

## ローカルでの確認

```sh
cd docs
python3 -m http.server 8000
# http://localhost:8000/ を開く
```

`file://` では `fetch` が使えないため、必ずHTTPサーバー経由で開いてください。
