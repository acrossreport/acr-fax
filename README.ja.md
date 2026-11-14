# ACR FAX

[English](README.md)

ACR FAXは、帳票エンジンであるACRの機能をHylaFAXと組み合わせることで、外部システム
（発注業務、作業報告書など）と連動したFAX送信を可能にします。また、HylaFAX本来の
画面キャプチャ・Excel/WordなどのOfficeファイル・PDFなどの送信も同時に行えるシステムです。

## 特長

- **実績あるHylaFAXをそのまま活用。** 自前でFAXプロトコルを実装するのではなく、
  長年使われてきたHylaFAXをFAXサーバーの土台にしている。HylaFAX本体には一切手を加えておらず、
  ソースを改変せずビルドしたバイナリをそのまま使用します(互換性・将来のアップデート取り込みやすさを優先)。
- **検証済みのモデム:** 現状はUSB接続のFAXモデム(Class 1 / 1.0 / 2)で動作検証済み。
  具体的には[プラネックス](https://www.planex.co.jp/product/accessories_modem.html)、
  AGPtek External USB2.0 FAX MODEM(ファックスモデム、アナログ、デュアルポート)で検証済み。
  PCI/PCIe接続のマルチポートシリアルボードでの検証は今後追加していく予定です。
- **専用の環境設定ツールがあり、モデムの自動認識に対応。** 導入時・モデム交換時に、
  接続されているモデムを自動スキャンして必要な設定を行う専用ツールを用意しています。
  (詳細は後述「モデムの設定」)。
- **サーバー接続は専用クライアントがプロトコルレベルで直接行う。** 外部コマンドをラップするのではなく
  軽量・高速に動作します。
- **クライアントはAvalonia UIで実装。** 同一コードベースでWindows/macOS/Linuxのx64/ARM64すべてを
  カバーするマルチベンダー対応を実現いたします。
- **モデムのダイヤル設定やユーザー管理も、クライアントの画面からリモートで変更可能。**
  サーバー機の前まで行かなくても設定を修正できます。
- **クライアントアプリは自己完結型。** ランタイムの個別インストールが不要で、
  配布された実行ファイルをそのまま起動します。

## 全体像

```mermaid
flowchart TB
    ACR["ACRエンジン<br/>(帳票・画面を自動でFAX送信)"]
    C1["クライアント #1<br/>(Windows / macOS / Linux)"]
    C2["クライアント #2<br/>(Windows / macOS / Linux)"]
    C3["クライアント #N<br/>(Windows / macOS / Linux)"]
    HF["HylaFAX<br/>(Ubuntu サーバー、1台)"]
    PSTN[("電話回線 / 相手先FAX機")]

    ACR --> C1
    ACR -- "自動でFAX送信ジョブ投入" --> C2
    ACR --> C3
    C1 --> HF
    C2 -- "送信ジョブ / 受信FAX・送信履歴を共有" --> HF
    C3 --> HF
    HF <--> PSTN
```

- **サーバーは1台**（Ubuntu上でHylaFAXを動かす）。FAXモデム/回線はここに接続する。
- **クライアントは複数台**がこのサーバーに接続できる。各クライアントから送信操作ができ、
  受信したFAXや送信履歴は、どのクライアントからでも同じように確認できる（特定の1台だけが
  持っているわけではない）。
- クライアントは手動操作のほか、**ACRエンジンによる自動送信**にも対応する
  （帳票データからPDFを自動生成し、人手を介さずFAX送信するルート）。

## 動作環境

- **サーバー: Ubuntu Desktop 24.04 LTSを推奨。** FAXモデム/回線に対応したシリアル・USBポートが
  必要なため、物理マシンまたはそれに準ずる環境での運用を基本とする。
- **Windows機しかない場合、WSL2上のUbuntu 24.04で動作することは確認済み。** その場合、Windows側の
  物理メモリは最低16GB以上を推奨する。単体のUbuntu(ベアメタル)での動作検証はこれから行う。
- **ARM64実機での検証を実施済み。** Windows ARM機(Snapdragon X)上のWSL2 Ubuntu 24.04(aarch64)に
  HylaFAXをインストールし、USBモデムの検出・登録・実際の電話回線経由での送受信まで成功を確認した。
  同じ機体上でクライアント(Windows ARM64ネイティブビルド)からの送受信、およびモデム設定ツール
  (WSL2上で実行)によるモデム検出・AT通信テストの動作も確認済み。ベアメタルのLinux ARM64
  (WSLを介さない環境)での検証は今後の課題。
- クライアントはWindows / macOS / Linux(x64, ARM64)に対応。Windows ARM64は実機検証済み(上記)、
  macOS / Linux ARM64はビルドのみで実機検証はこれから。

## モデムの設定

モデムの検出・udevルールの自動生成・AT通信テスト・HylaFAXサービスの再起動までを
GUIから一括で行う、Ubuntu専用の環境設定ツールを別途配布している。導入時だけでなく、
運用開始後にモデムを交換・増設した際も、このツールで接続中の全回線を自動再スキャンできる。

検証済みのモデムは[プラネックス](https://www.planex.co.jp/product/accessories_modem.html)、
AGPtek External USB2.0 FAX MODEM(ファックスモデム、アナログ、デュアルポート)。

> **現在の状況:** [v0.1.0](../../releases/tag/acr-fax-v0.1.0)をテスト用に先行公開済み。
> クライアント・サーバーを揃ったバージョンの組として配布している。

## 配布バイナリ

[Releases](../../releases/tag/acr-fax-v0.1.0)(v0.1.0)で配布しているのは、クライアント6種
(OS/CPUアーキテクチャごと)とサーバー2種(Linux x86_64/aarch64)の計8ファイル。
いずれもランタイム同梱の自己完結型で、解凍してそのまま実行できる。

| ファイル名 | 役割 | OS | CPUアーキテクチャ | 形式 |
|---|---|---|---|---|
| acr-fax-client-windows-x64.zip | クライアント | Windows | x64 | zip |
| acr-fax-client-windows-arm64.zip | クライアント | Windows | ARM64 | zip |
| acr-fax-client-macos-x64.tar.gz | クライアント | macOS | x64(Intel) | tar.gz |
| acr-fax-client-macos-arm64.tar.gz | クライアント | macOS | ARM64(Apple Silicon) | tar.gz |
| acr-fax-client-linux-x64.tar.gz | クライアント | Linux | x64 | tar.gz |
| acr-fax-client-linux-arm64.tar.gz | クライアント | Linux | ARM64 | tar.gz |
| acr-fax-server-linux-x86_64.tar.gz | サーバー | Linux | x64 | tar.gz |
| acr-fax-server-linux-aarch64.tar.gz | サーバー | Linux | ARM64 | tar.gz |

サーバー側(`acr_fax_server`)はHylaFAXが稼働するUbuntu機上にそのまま置いて動かす前提のため、
Linuxのみの配布。

## ライセンス

- 本体ソフトウェア一式: Across Report独自ライセンス(別途)
- HylaFAX: Copyright (c) 1990-1996 Sam Leffler, Copyright (c) 1991-1996 Silicon Graphics, Inc.
  (MIT/BSD系の緩い許諾ライセンス。無改造で使用。条件は[Releases](../../releases/tag/acr-fax-v0.1.0)に
  同梱の`HylaFAX-COPYRIGHT.txt`を参照)
