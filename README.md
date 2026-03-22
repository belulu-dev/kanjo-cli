# Kanjo CLI

[Kanjo](https://kanjo.belulu.dev) 家計簿のコマンドラインツール。

## インストール

[Releases](https://github.com/belulu-dev/kanjo-cli/releases) からお使いの OS に合ったバイナリをダウンロードして、パスの通ったディレクトリに配置してください。

```bash
# macOS / Linux
tar xzf kanjo-*.tar.gz
sudo mv kanjo-*/kanjo /usr/local/bin/

# アップグレード
kanjo upgrade
```

## コマンド

| コマンド | 説明 |
|----------|------|
| `kanjo login` | ログイン |
| `kanjo logout` | ログアウト |
| `kanjo whoami` | ログイン中のユーザー情報 |
| `kanjo add` | 取引を登録 |
| `kanjo list` | 取引一覧 |
| `kanjo last` | 直近の取引 |
| `kanjo balance` | 口座残高 |
| `kanjo report` | レポート（月計表・年計表） |
| `kanjo export` | データエクスポート |
| `kanjo import` | データインポート |
| `kanjo upgrade` | 最新バージョンに更新 |
| `kanjo version` | バージョン表示 |

## ライセンス

Proprietary
