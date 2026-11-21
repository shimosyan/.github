# .github

複数のリポジトリで共通利用する GitHub Actions ワークフローや設定を集約したリポジトリです。

## Workflows

各リポジトリから `workflow_call` で呼び出して利用する共通ワークフロー一覧です。

| ワークフロー | 概要 | 主な用途 |
| :--- | :--- | :--- |
| `wc-update-actions-lock.yaml` | `actions.lock` の自動更新・コミット | PR 作成・更新時 |
| `wc-update-aqua-checksums.yaml` | `aqua-checksums.json` の自動更新・コミット | `aqua.yaml` 変更時 |
| `wc-document-pull-request.yaml` | Markdown / テキストの Lint チェック | PR 作成・更新時 |
| `wc-auto-rebase-renovate-prs.yaml` | Renovate PR への `rebase` ラベル自動付与 | `main` ブランチ更新時 |
| `wc-labeler.yaml` | パス・変更行数に応じた PR ラベル自動付与 | PR 作成・更新時 |

## Usage

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

多くのワークフローでは、コミットや PR 操作用のトークン生成に以下の Secrets を使用します（`secrets: inherit` での継承を推奨）。

- `CLIENT_ID`: GitHub App の Client ID
- `PRIVATE_KEY`: GitHub App の Private Key

## License

Copyright (c) 2026 shimosyan  
Released under the MIT license  
<https://github.com/shimosyan/.github/blob/main/LICENSE>
