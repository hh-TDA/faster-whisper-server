# faster-whisper-server

NVIDIA GPU を使って日本語音声認識サーバー（faster-whisper）をセルフホストし、Dify などの OpenAI 互換 API クライアントから利用するための Docker Compose 構成です。

## サービス構成

| サービス | イメージ | デフォルトポート | モデル |
|---|---|---|---|
| faster-whisper-v1 | `fedirz/faster-whisper-server:latest-cuda` | 8484 | kotoba-whisper-v1.0 |
| faster-whisper-v2 | `ghcr.io/speaches-ai/speaches:latest-cuda` | 80 | kotoba-whisper-v2.0-faster |

## 前提条件

- Docker / Docker Compose v2
- NVIDIA GPU
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)

## セットアップ

```bash
cp .env.example .env
# 必要に応じて .env を編集

docker compose up -d
```

## 環境変数

[.env.example](.env.example) をコピーして `.env` を作成してください。

| 変数 | デフォルト値 | 説明 |
|---|---|---|
| `V1_PORT` | `8484` | v1 サービスのホストポート |
| `V1_MODEL` | `kotoba-tech/kotoba-whisper-v1.0` | v1 で使用するモデル |
| `V2_PORT` | `80` | v2 サービスのホストポート |
| `V2_MODEL` | `kotoba-tech/kotoba-whisper-v2.0-faster` | v2 で使用するモデル |

初回起動時はモデルのダウンロードが発生します（数 GB）。ログで進捗を確認してください：

```bash
docker compose logs -f
```

## Dify からの利用方法

Dify の **「モデルプロバイダー」→「OpenAI 互換」** に以下を設定します。

**v2（推奨）**
- API エンドポイント: `http://<ホストIP>/v1`
- モデル名: `kotoba-tech/kotoba-whisper-v2.0-faster`

**v1**
- API エンドポイント: `http://<ホストIP>:8484/v1`
- モデル名: `kotoba-tech/kotoba-whisper-v1.0`

API キーは任意の文字列で構いません（認証なし）。

## ライセンス

| モデル | ライセンス | 商用利用 | ベースモデル |
|---|---|---|---|
| kotoba-tech/kotoba-whisper-v1.0 | Apache 2.0 | 可 | OpenAI Whisper large-v3 (MIT) |
| kotoba-tech/kotoba-whisper-v2.0-faster | MIT | 可 | kotoba-whisper-v2.0 (Apache 2.0) → Whisper large-v3 (MIT) |

いずれも商用利用可能なオープンライセンスです。再配布する場合は各ライセンス表記が必要ですが、セルフホストして内部利用する分には制約はありません。

## その他のコマンド

```bash
# 停止
docker compose down

# ログ確認
docker compose logs -f faster-whisper-v2

# 再起動
docker compose restart faster-whisper-v2
```
