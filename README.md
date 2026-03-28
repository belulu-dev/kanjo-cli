# Kanjo CLI

[Kanjo](https://kanjo.belulu.dev) 家計簿のコマンドラインツール。

## インストール

### Linux / macOS

```bash
curl -fsSL https://raw.githubusercontent.com/belulu-dev/kanjo-cli/main/install.sh | bash
```

`/usr/local/bin` に書き込み権限がない場合は `sudo` で実行されます。インストール先を変更するには `KANJO_INSTALL_DIR` を指定してください。

```bash
KANJO_INSTALL_DIR=$HOME/.local/bin curl -fsSL https://raw.githubusercontent.com/belulu-dev/kanjo-cli/main/install.sh | bash
```

### NixOS

```bash
KANJO_INSTALL_DIR=~/.local/bin curl -fsSL https://raw.githubusercontent.com/belulu-dev/kanjo-cli/main/install.sh | bash
```

`~/.local/bin` が PATH に含まれていることを確認してください。

### Windows (PowerShell)

```powershell
irm https://raw.githubusercontent.com/belulu-dev/kanjo-cli/main/install.ps1 | iex
```

`%LOCALAPPDATA%\kanjo-cli\` にインストールされ、ユーザー PATH に自動追加されます。

### 手動インストール

[Releases](https://github.com/belulu-dev/kanjo-cli/releases) からお使いの OS に合ったバイナリをダウンロードして、パスの通ったディレクトリに配置してください。

```bash
tar xzf kanjo-*.tar.gz
sudo mv kanjo-*/kanjo /usr/local/bin/
```

### アップグレード

```bash
kanjo upgrade
```
## コマンド一覧

| コマンド | 説明 |
|----------|------|
| `kanjo login` | ログイン |
| `kanjo logout` | ログアウト |
| `kanjo whoami` | ログイン中のユーザー情報 |
| `kanjo add expense` | 支出を登録 |
| `kanjo add income` | 収入を登録 |
| `kanjo add transfer` | 振替を登録 |
| `kanjo list` | 取引一覧 |
| `kanjo last` | 直近の取引 |
| `kanjo edit` | 取引を編集 |
| `kanjo delete` | 取引を削除 |
| `kanjo balance` | 口座残高 |
| `kanjo report monthly` | 月計表 |
| `kanjo report yearly` | 年計表 |
| `kanjo report category` | カテゴリ別集計 |
| `kanjo report daily` | 日別集計 |
| `kanjo report ledger` | 元帳 |
| `kanjo export csv` | CSV エクスポート |
| `kanjo export json` | JSON エクスポート |
| `kanjo import csv` | CSV インポート |
| `kanjo import json` | JSON インポート |
| `kanjo upgrade` | 最新バージョンに更新 |
| `kanjo version` | バージョン表示 |

## 共通オプション

### 出力形式

照会系コマンドは `--format` で出力形式を切り替えられます。

```bash
kanjo list --format table   # テーブル形式（デフォルト）
kanjo list --format json    # JSON（スクリプト連携向け）
kanjo list --format csv     # CSV
```

### 名前解決

口座名・費目名は ID ではなく名前で指定します。完全一致 → 部分一致の順で検索します。

```bash
kanjo add expense 1200 食費 -a 三井住友    # 「三井住友」を含む口座にマッチ
```

## 認証

### ログイン

3つの認証方式を提供します。

```bash
kanjo login              # ブラウザ認証（デフォルト）
kanjo login --password   # パスワード方式（対話式入力）
kanjo login --device     # デバイスコード方式（SSH 環境で自動選択）
```

| オプション | 説明 |
|-----------|------|
| `--url` | API の URL（デフォルト: `https://kanjo.belulu.dev`） |
| `--password` | パスワード方式でログイン |
| `--device` | デバイスコード方式でログイン |

SSH 環境ではブラウザが使えないため、自動的にデバイスコード方式に切り替わります。

### その他

```bash
kanjo logout    # セッション破棄
kanjo whoami    # ユーザー名・表示名・接続先を表示
```

## 取引登録

### 支出

```bash
kanjo add expense <金額> <費目> [オプション]
```

| オプション | 短縮 | 説明 |
|-----------|------|------|
| `--account` | `-a` | 口座名 **（必須）** |
| `--date` | `-d` | 日付 YYYY-MM-DD（デフォルト: 今日） |
| `--sub` | | サブカテゴリ |
| `--shop` | | お店 |
| `--memo` | `-m` | メモ |
| `--quantity` | | 数量 |
| `--unit-price` | | 単価 |

```bash
# 基本
kanjo add expense 1200 食費 -a 現金

# フルオプション
kanjo add expense 1200 食費 --sub 外食 -a 三井住友VISA --shop ガスト -m "ランチ" -d 2026-03-01

# 数量×単価
kanjo add expense 0 日用品 -a 現金 --quantity 3 --unit-price 150
```

### 収入

```bash
kanjo add income <金額> <費目> [オプション]
```

| オプション | 短縮 | 説明 |
|-----------|------|------|
| `--account` | `-a` | 口座名 **（必須）** |
| `--date` | `-d` | 日付 YYYY-MM-DD（デフォルト: 今日） |
| `--sub` | | サブカテゴリ |
| `--memo` | `-m` | メモ |

```bash
kanjo add income 250000 給与 -a 三菱UFJ
```

### 振替

```bash
kanjo add transfer <金額> [オプション]
```

| オプション | 短縮 | 説明 |
|-----------|------|------|
| `--from` | | 振替元口座名 **（必須）** |
| `--to` | | 振替先口座名 **（必須）** |
| `--type` | | 振替種別 **（必須）**: `savings`（貯金）/ `credit`（クレカ引落）/ `adjustment`（調整） |
| `--date` | `-d` | 日付 YYYY-MM-DD（デフォルト: 今日） |
| `--memo` | `-m` | メモ |

```bash
kanjo add transfer 50000 --from 三菱UFJ --to SBI証券 --type savings
kanjo add transfer 30000 --from 三菱UFJ --to 三井住友VISA --type credit
```

## 取引照会

### 一覧

```bash
kanjo list [オプション]
```

| オプション | 説明 |
|-----------|------|
| `--month` | 月指定 YYYY-MM（デフォルト: 当月） |
| `--from` | 開始日 YYYY-MM-DD（`--to` と同時指定） |
| `--to` | 終了日 YYYY-MM-DD（`--from` と同時指定） |
| `--category` | 費目フィルタ |
| `--type` | 種別フィルタ: `expense` / `income` / `transfer` |
| `--search` | キーワード検索（メモ・お店名・説明） |
| `--format` | 出力形式: `table` / `json` / `csv` |

```bash
kanjo list                            # 今月の取引
kanjo list --month 2026-02            # 指定月
kanjo list --from 2026-01-01 --to 2026-03-31  # 期間指定
kanjo list --category 食費            # 費目フィルタ
kanjo list --type expense             # 支出のみ
kanjo list --search "ガスト"          # キーワード検索
```

出力例:

```
ID   日付        種別  費目      内訳    金額        口座          メモ
12   2026-03-01  支出  食費      外食    1,200 円    三井住友VISA  ランチ
13   2026-03-02  支出  日用品    —       450 円      現金          —

支出合計: 1,650 円  収入合計: 0 円
```

### 直近の取引

```bash
kanjo last [件数]
```

| 引数 | 説明 |
|------|------|
| `[件数]` | 表示件数（デフォルト: 10、最大: 過去90日分） |

```bash
kanjo last       # 直近 10 件
kanjo last 20    # 直近 20 件
```

### 取引の編集

```bash
kanjo edit <ID> [オプション]
```

変更したいフィールドのみ指定します。未指定のフィールドは変更されません。

| オプション | 短縮 | 説明 |
|-----------|------|------|
| `--amount` | | 金額 |
| `--category` | | 費目 |
| `--sub` | | サブカテゴリ（`--category` と同時指定が必要） |
| `--account` | `-a` | 口座名 |
| `--date` | `-d` | 日付 YYYY-MM-DD |
| `--shop` | | お店（空文字でクリア） |
| `--memo` | `-m` | メモ（空文字でクリア） |

```bash
kanjo edit 12 --amount 1500                    # 金額を変更
kanjo edit 12 --category 交際費 --sub 飲み会   # 費目とサブカテゴリを変更
kanjo edit 12 --shop ""                        # お店をクリア
```

### 取引の削除

```bash
kanjo delete <ID> [オプション]
```

| オプション | 短縮 | 説明 |
|-----------|------|------|
| `--force` | `-f` | 確認プロンプトをスキップ |

ペア取引（クレカ引落）がある場合は自動的に同時削除されます。

```bash
kanjo delete 12        # 確認プロンプトあり
kanjo delete 12 -f     # 確認なしで削除
```

## 口座残高

```bash
kanjo balance [口座名]
```

| オプション | 説明 |
|-----------|------|
| `--all` | 非表示口座も含める |
| `--format` | 出力形式: `table` / `json` / `csv` |

```bash
kanjo balance              # 全口座の残高
kanjo balance 三菱UFJ      # 特定口座
kanjo balance --all        # 非表示口座含む
```

出力例:

```
口座              種別    残高
現金              現金    10,000 円
三菱UFJ           銀行    500,000 円
三井住友VISA      クレカ  -30,000 円

合計: 480,000 円
```

## レポート

### 月計表

```bash
kanjo report monthly [月]
```

| 引数 | 説明 |
|------|------|
| `[月]` | YYYY-MM（デフォルト: 当月） |

```bash
kanjo report monthly              # 今月
kanjo report monthly 2026-02      # 指定月
```

出力例:

```
月計表: 2026-03

【支出】
費目                    金額
食費                    15,000 円
  外食                  8,000 円
  食材                  7,000 円
日用品                  3,000 円
支出合計: 18,000 円

【収入】
費目                    金額
給与                    250,000 円
収入合計: 250,000 円

収支: 232,000 円
```

### 年計表

```bash
kanjo report yearly [年]
```

| オプション | 説明 |
|-----------|------|
| `--type` | 種別: `expense`（デフォルト）/ `income` / `balance` |
| `--format` | 出力形式 |

```bash
kanjo report yearly              # 今年の支出
kanjo report yearly 2025         # 指定年
kanjo report yearly --type income  # 収入の年計表
```

出力例:

```
年計表: 2026年 (支出)

費目      1月        2月        3月        合計
食費      12,000 円  14,000 円  15,000 円  41,000 円
日用品    2,500 円   3,000 円   3,000 円   8,500 円
合計      14,500 円  17,000 円  18,000 円  49,500 円
```

### カテゴリ別集計

```bash
kanjo report category [オプション]
```

| オプション | 説明 |
|-----------|------|
| `--from` | 開始日 YYYY-MM-DD（デフォルト: 当月1日） |
| `--to` | 終了日 YYYY-MM-DD（デフォルト: 当月末日） |
| `--format` | 出力形式 |

```bash
kanjo report category
kanjo report category --from 2026-01-01 --to 2026-03-31
```

### 日別集計

```bash
kanjo report daily [オプション]
```

| オプション | 説明 |
|-----------|------|
| `--month` | 月指定 YYYY-MM（デフォルト: 当月） |
| `--from` | 開始日 YYYY-MM-DD |
| `--to` | 終了日 YYYY-MM-DD |
| `--format` | 出力形式 |

```bash
kanjo report daily
kanjo report daily --month 2026-02
kanjo report daily --from 2026-03-01 --to 2026-03-15
```

出力例:

```
日別集計: 2026-03-01 〜 2026-03-31

日付        支出          収入
03-01       1,200 円      —
03-02       450 円        —
03-25       —             250,000 円

支出合計: 18,000 円  収入合計: 250,000 円  収支: 232,000 円
```

### 元帳

```bash
kanjo report ledger [オプション]
```

`--account` または `--category` のどちらか1つを必ず指定します。

| オプション | 説明 |
|-----------|------|
| `--account` | 口座名（`--category` と排他） |
| `--category` | 費目名（`--account` と排他） |
| `--month` | 月指定 YYYY-MM |
| `--from` | 開始日 YYYY-MM-DD |
| `--to` | 終了日 YYYY-MM-DD |
| `--format` | 出力形式 |

```bash
kanjo report ledger --account 三菱UFJ
kanjo report ledger --category 食費 --month 2026-03
kanjo report ledger --account 現金 --from 2026-01-01 --to 2026-03-31
```

出力例:

```
口座元帳: 三菱UFJ (2026-03-01 〜 2026-03-31)
繰越残高: 300,000 円

日付        摘要              収入          支出          残高
03-25       給与              250,000 円    —             550,000 円
03-27       家賃 振替         —             80,000 円     470,000 円

収入合計: 250,000 円  支出合計: 80,000 円  期末残高: 470,000 円
```

## データ入出力

### エクスポート

```bash
kanjo export csv     # 取引を CSV で出力
kanjo export json    # 全データを JSON で出力
```

標準出力に出力されるので、ファイルに保存する場合はリダイレクトを使います。

```bash
kanjo export csv > transactions.csv
kanjo export json > backup.json
```

### インポート

```bash
kanjo import csv <ファイル>     # CSV から取引をインポート
kanjo import json <ファイル>    # JSON からデータを復元
```

| オプション | 説明 |
|-----------|------|
| `--delete-transactions` | JSON インポート時、既存の取引データを削除してからインポート |

```bash
kanjo import csv ukiuki.csv
kanjo import json backup.json
kanjo import json backup.json --delete-transactions
```

CSV インポートでは口座名・費目名の名前解決を行います。解決できない名前がある場合はエラー一覧を表示して中止します。

## 設定ファイル

認証情報は `~/.config/kanjo/credentials.json` に保存されます。

## ライセンス

Proprietary
