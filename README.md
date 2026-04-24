# comdec-ai-trial-scenarios

コムデック 生成AI for kintone の体験会セットアップ機能で使用するシナリオ定義リポジトリです。

体験会セットアップでは、ここで定義されたシナリオを GitHub Pages 経由で取得し、kintone 環境に必要なアプリ・サンプルデータ・AI設定を自動生成します。

## 公開URL

GitHub Pages で `docs/` ディレクトリを公開しています。

- ベースURL: `https://comdec-dev.github.io/comdec-ai-trial-scenarios/`
- シナリオ一覧: `https://comdec-dev.github.io/comdec-ai-trial-scenarios/index.json`

## ディレクトリ構成

```
docs/
├── index.json                    # シナリオ一覧（カタログ）
└── scenarios/
    └── <scenario-id>/
        └── scenario.json         # シナリオ定義
```

## ファイル仕様

### `docs/index.json`

シナリオの一覧と各シナリオJSONへのパスを管理します。

```json
{
  "version": "1.0",
  "updatedAt": "YYYY-MM-DD",
  "scenarios": [
    {
      "id": "faq",
      "name": "社内FAQ",
      "description": "FAQマスタを参照してAIが質問に回答します",
      "appCount": 2,
      "path": "scenarios/faq/scenario.json"
    }
  ]
}
```

| フィールド | 説明 |
|---|---|
| `id` | シナリオの一意識別子（英数字・ハイフン） |
| `name` | UI表示用のシナリオ名 |
| `description` | UI表示用の説明文 |
| `appCount` | 作成されるアプリ数（UI表示用） |
| `path` | `docs/` からの相対パス |

### `docs/scenarios/<id>/scenario.json`

シナリオの詳細定義。以下の3要素から構成されます。

```json
{
  "id": "faq",
  "name": "社内FAQ",
  "description": "...",
  "apps": [...],            // 作成するアプリの定義（フィールド・サンプルデータ）
  "settingTemplate": {...}  // AI設定のテンプレート
}
```

#### `apps[]`（アプリ定義）

```json
{
  "role": "reference|output",   // 参照アプリ or 出力アプリ
  "key": "faqMaster",           // settingTemplate内で参照するキー
  "name": "アプリ名",
  "fields": {                    // kintone REST API のフィールド定義形式
    "fieldCode": { "type": "...", "code": "...", "label": "..." }
  },
  "sampleData": [                // 投入するサンプルレコード
    { "fieldCode": "値" }
  ]
}
```

- `role`: `reference`（参照アプリ）または `output`（出力アプリ）
- `key`: 同シナリオ内で一意なキー。`settingTemplate` 内のプレースホルダで参照
- `fields`: kintone REST API `/k/v1/preview/app/form/fields.json` の `properties` と同形式
- `sampleData`: 各オブジェクトのキーがフィールドコード、値がフィールド値

#### `settingTemplate`（AI設定テンプレート）

AI設定の `settingJson` と同じ形式です。プレースホルダで動的な値を埋め込めます。

| プレースホルダ | 置換内容 |
|---|---|
| `{{appIds.<key>}}` | 作成されたアプリのID |
| `{{appUrls.<key>}}` | アプリの実行URL |
| `{{apiKey}}` | OpenAI APIキー |
| `{{licenseKey}}` | ライセンスキー |
| `{{eventName}}` | イベント名 |

例:
```json
{
  "outputAppId": "{{appIds.faqInquiry}}",
  "outputAppUrl": "{{appUrls.faqInquiry}}",
  "apiKey": "{{apiKey}}"
}
```

## シナリオの追加手順

1. `docs/scenarios/<新シナリオID>/scenario.json` を作成
2. `docs/index.json` の `scenarios` 配列に新シナリオを追加
3. PR作成 → レビュー → main へマージ
4. GitHub Pages に自動反映（数分後）

## 注意事項

- このリポジトリは Public です。**機密情報（APIキー等）を含めないでください**
- 既存シナリオの `id` は変更しないでください（既存環境への影響を防ぐため）
- JSON ファイルは UTF-8 / LF で保存してください
