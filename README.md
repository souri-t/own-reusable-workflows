# own-workflows

GitHub Actions の Reusable Workflow を管理するリポジトリです。

---

## ワークフロー一覧

### `summarize.yml` — コミット要約の自動生成

プッシュ時に Git の差分・コミットログを LLM (OpenRouter) に送り、日本語の要約 Markdown を自動生成します。

#### 動作の流れ

1. 呼び出し元リポジトリへのプッシュを検知
2. 直前のコミットとの差分・ログを取得
3. OpenRouter 経由で LLM が日本語要約を生成
4. `.github/summaries/YYYY-MM-DD-{sha7}.md` として保存・コミット

---

## 使い方

### 1. シークレットの設定

呼び出し元リポジトリの **Settings > Secrets and variables > Actions > Secrets** に追加してください。

| シークレット名 | 説明 |
|---|---|
| `OPENROUTER_API_KEY` | [OpenRouter](https://openrouter.ai/) の API キー |

### 2. ワークフローファイルの作成

呼び出し元リポジトリに `.github/workflows/summarize.yml` を作成します。

```yaml
name: コミット要約の自動生成

on:
  push:
    branches:
      - main
      - master

jobs:
  summarize:
    uses: {YOUR_GITHUB_USERNAME}/own-workflows/.github/workflows/summarize.yml@main
    secrets:
      OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
```

`{YOUR_GITHUB_USERNAME}` を実際の GitHub ユーザー名に置き換えてください。

> **注意**: このリポジトリ (`own-workflows`) は **Public** である必要があります。

---

## オプション

`with:` でデフォルト値を上書きできます。

```yaml
jobs:
  summarize:
    uses: {YOUR_GITHUB_USERNAME}/own-workflows/.github/workflows/summarize.yml@main
    secrets:
      OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
    with:
      model: "anthropic/claude-3-haiku"
      base_url: "https://openrouter.ai/api/v1"
      summary_dir: ".github/summaries"
```

| パラメータ | デフォルト値 | 説明 |
|---|---|---|
| `model` | `openai/gpt-4o-mini` | 使用する LLM モデル名 ([OpenRouter モデル一覧](https://openrouter.ai/models)) |
| `base_url` | `https://openrouter.ai/api/v1` | OpenAI 互換エンドポイント URL |
| `summary_dir` | `.github/summaries` | 要約ファイルの保存先（リポジトリルートからの相対パス） |

---

## 生成される要約ファイルの例

**ファイル名**: `.github/summaries/2026-02-21-a1b2c3d.md`

```markdown
# コミット要約: 2026-02-21 (`a1b2c3d`)

**コミット SHA**: `a1b2c3d...`
**生成日時**: 2026-02-21 12:00:00 UTC

---

### 変更の概要
...

### 主な変更点
- ...

### 変更の目的・背景
...
```

---

## サンプルファイル

[examples/summarize-caller.yml](examples/summarize-caller.yml) に呼び出し側のサンプルがあります。
