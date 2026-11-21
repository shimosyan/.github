# .github

複数のリポジトリで共通利用する GitHub Actions ワークフローや設定を集約したリポジトリです。

## Workflows

各リポジトリから `workflow_call` で呼び出して利用する共通ワークフロー一覧です。

| ワークフロー | 概要 | 主な用途 |
| :--- | :--- | :--- |
| `wc-update-actions-lock.yaml` | `actions.lock` の自動更新・コミット | PR 作成・更新時 |
| `wc-update-aqua-checksums.yaml` | `aqua-checksums.json` の自動更新・コミット | `aqua.yaml` 変更時 |
| `wc-document-pull-request.yaml` | Markdown / テキストの Lint チェック | PR 作成・更新時 |
| `wc-auto-rebase-renovate-prs.yaml` | Renovate PR への `rebase` ラベル自動付与 | `main` / `master` ブランチ更新時 |
| `wc-labeler.yaml` | パス・変更行数に応じた PR ラベル自動付与 | PR 作成・更新時 |
| `wc-schedule-create-drift-issues.yaml` | Terraform Drift 検知用 Issue の作成 | 定期実行（日次）・手動実行 |
| `wc-schedule-detect-drifts.yaml` | Terraform Drift の検知・Issue 更新 | 定期実行（毎時）・手動実行 |
| `wc-sync-drift-issue-description.yaml` | Drift Issue 本文の同期 | Issue コメント作成時 |

### Usage

呼び出し側のリポジトリでは、以下のようにコミット SHA またはバージョンタグを指定して参照します。

```yaml
jobs:
  update-actions-lock:
    uses: shimosyan/.github/.github/workflows/wc-update-actions-lock.yaml@<commit-sha>
    secrets: inherit
    permissions:
      id-token: write
      contents: read
      pull-requests: write
```

### Secrets

多くのワークフローでは、コミットや PR・Issue 操作用のトークン生成に以下の Secrets を使用します（`wc-sync-drift-issue-description.yaml` は不要）。`wc-schedule-detect-drifts.yaml` でリポジトリ固有のクラウド・API 認証情報も引き継ぐため、`secrets: inherit` での継承を推奨します。

- `CLIENT_ID`: GitHub App の Client ID
- `PRIVATE_KEY`: GitHub App の Private Key

> **Note:** `wc-auto-rebase-renovate-prs.yaml` を利用するリポジトリでは、あらかじめ `rebase` ラベルを作成しておく必要があります。

## Renovate Preset

リポジトリルートの `default.json` で、各リポジトリ共通の Renovate プリセットを提供しています。

### 主な設定内容

- **ベースプリセット**: `config:recommended` / `helpers:pinGitHubActionDigests` / `aquaproj/aqua-renovate-config` / `suzuki-shunsuke/renovate-config:yaml-language-server`
- **自動マージ**: `minor`・`patch`・`pin`・`digest` の更新および GitHub Actions のダイジェスト固定更新を自動マージ（`platformAutomerge: true`）
- **安定化待機期間**: リリース後 3 日間は PR を作成しない（`minimumReleaseAge: "3 days"`）
- **PR 作成数制限・その他**: 1 時間あたり最大 2 件 / 同時オープン最大 10 件、タイムゾーン `Asia/Tokyo`、Dependency Dashboard 有効化

### 継承方法

各リポジトリの `renovate.json5` で以下のように継承します。

```json5
{
  '$schema': 'https://docs.renovatebot.com/renovate-schema.json',
  extends: [
    'github>shimosyan/.github',
  ],
}
```

リポジトリ固有のルールや `customManagers` が必要な場合は、`extends` の後ろに追記して上書きします。

## License

Copyright (c) 2026 shimosyan  
Released under the MIT license  
[https://github.com/shimosyan/.github/blob/main/LICENSE](https://github.com/shimosyan/.github/blob/main/LICENSE)
