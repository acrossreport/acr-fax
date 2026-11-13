# ACR FAX

[acr_fax_server](https://github.com/acrossreport/acr-fax-server-src) と
[acr_fax_client](https://github.com/acrossreport/acr-fax-client-src) を、対応する
HylaFAX ビルド済みバイナリと合わせて配布するためのリポジトリです。

このリポジトリ自体にはソースコードは置かず、[Releases](../../releases) に
プラットフォームごとのバイナリ一式をまとめます。

## 含まれるもの(予定)

| コンポーネント | 対応OS/アーキテクチャ |
|---|---|
| acr_fax_server | Windows / macOS / Linux (x64, ARM64) |
| acr_fax_client | Windows / macOS / Linux (x64, ARM64) |
| HylaFAX 7.0.11 (ビルド済み) | Ubuntu 24.04 (x64) |

acr_fax_client は内部で acr_fax_server を呼び出すため、**両方を同じバージョンの組として配布**します
(片方だけ古いバイナリを使うと、機能不整合が起きます)。

HylaFAX 自体はUbuntu/Linux上でのみ動作するサーバーソフトウェアです。Windows/macOS側は
acr_fax_server がネットワーク越しにHylaFAXへ接続するクライアントとして動作します。

## ライセンス

- acr_fax_server / acr_fax_client: Across Report独自ライセンス(別途)
- HylaFAX: Copyright (c) 1990-1996 Sam Leffler, Copyright (c) 1991-1996 Silicon Graphics, Inc.
  同梱の `COPYRIGHT` を参照。MIT/BSD系の緩い許諾ライセンスです。
