# CosyVoice2 OpenAI Compatible TTS Server

![Development Status](https://img.shields.io/badge/status-development-yellow)
![Testing Status](https://img.shields.io/badge/testing-verification%20needed-orange)

## ⚠️ 開発状況について

> **現在の状況 (2025年6月26日更新)**  
> このプロジェクトは現在開発中です。READMEに記載されている機能の多くは設計段階であり、実際の動作確認が不足しています。
> 
> **動作確認状況**: 
> - [ ] 基本的なTTS機能
> - [ ] OpenAI API互換性の検証
> - [ ] Docker環境での動作確認
> - [ ] CosyVoice2モデルの統合テスト
> - [ ] 音声クローニング機能
> - [ ] Dify統合テスト
> - [ ] パフォーマンス検証
> 
> **ご注意**: このREADMEは「実装予定」の内容を多く含んでいます。実際に動作するかどうかの検証が不足している状態です。
> 
> **貢献・フィードバック大歓迎**: 
> - 動作テストのお手伝い
> - バグ報告や改善提案
> - 実装のサポート
> - ドキュメントの修正
> 
> 実際に試していただいた結果（成功・失敗問わず）をIssueで報告していただけると大変助かります！

---

Dify用のCosyVoice2を使用したOpenAI API互換のTTSサーバーです。

## 🚀 機能（予定・実装中）

- **OpenAI TTS API完全互換** (`/v1/audio/speech`) *- 実装予定*
- **CosyVoice2-0.5Bモデル使用**（最高のストリーミング性能） *- 統合作業中*
- **多言語対応**（中国語、英語、日本語、韓国語、方言） *- 検証予定*
- **Zero-shot音声クローニング** *- 実装予定*
- **ストリーミング対応**（低遅延150ms） *- 未検証*
- **Docker対応**（CPU/GPU両対応） *- 設定ファイルのみ作成済み*
- **Dify直接統合可能** *- 未テスト*

## 🚧 開発ロードマップ

### Phase 1: 基礎実装 🟡
- [ ] CosyVoice2の基本統合
- [ ] シンプルなTTS APIエンドポイント
- [ ] 基本的な音声生成機能

### Phase 2: API互換性 ⚪
- [ ] OpenAI TTS API仕様への準拠
- [ ] エラーハンドリングの実装
- [ ] 複数音声フォーマット対応

### Phase 3: 高度な機能 ⚪
- [ ] ストリーミング対応
- [ ] 音声クローニング機能
- [ ] パフォーマンス最適化

### Phase 4: 本格運用 ⚪
- [ ] Docker環境での安定動作
- [ ] Dify統合の動作確認
- [ ] 本番環境対応

## 📦 クイックスタート（未検証）

> **注意**: 以下の手順は理論上の手順です。実際には動作しない可能性があります。

### 🐳 Docker使用（推奨・未検証）

#### GPU版（推奨）
```bash
git clone https://github.com/ShunsukeTamura06/cosyvoice2-openai-tts-server.git
cd cosyvoice2-openai-tts-server

# GPU版で起動
docker-compose --profile gpu up -d

# ログ確認
docker-compose logs -f cosyvoice-tts-gpu
```

#### CPU版（GPUが利用できない場合）
```bash
# CPU版で起動
docker-compose --profile cpu up -d

# ログ確認
docker-compose logs -f cosyvoice-tts-cpu
```

#### 🔧 手動セットアップ

1. **環境構築**

```bash
# Conda環境作成
conda create -n cosyvoice python=3.10
conda activate cosyvoice

# リポジトリクローン
git clone https://github.com/ShunsukeTamura06/cosyvoice2-openai-tts-server.git
cd cosyvoice2-openai-tts-server

# セットアップスクリプト実行
bash setup.sh
```

2. **サーバー起動**

```bash
# 自動起動スクリプト
./start_server.sh

# または手動起動
python app.py
```

3. **動作確認**

```bash
# テストスクリプト実行
python test_server.py
```

## 🔧 API使用例（理論上）

### OpenAI SDK使用

```python
from openai import OpenAI

client = OpenAI(
    api_key="dummy-key",  # 認証なし
    base_url="http://localhost:8000/v1"
)

response = client.audio.speech.create(
    model="cosyvoice2-0.5b",
    voice="alloy",  # または "echo", "fable", "onyx", "nova", "shimmer"
    input="こんにちは、CosyVoice2です。",
    response_format="mp3",
    speed=1.0
)

with open("output.mp3", "wb") as f:
    f.write(response.content)
```

### cURL使用

```bash
curl -X POST "http://localhost:8000/v1/audio/speech" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "cosyvoice2-0.5b",
    "input": "Hello from CosyVoice2!",
    "voice": "alloy",
    "response_format": "mp3",
    "speed": 1.0
  }' \
  --output speech.mp3
```

## 🎭 音声クローニング（実装予定）

音声サンプルを使用したクローニング:

```python
# 音声サンプルをアップロード
with open("sample_voice.wav", "rb") as f:
    files = {"voice_sample": f}
    data = {"speaker_name": "my_voice"}
    response = requests.post(
        "http://localhost:8000/v1/voice/clone",
        files=files,
        data=data
    )

# クローンした音声で合成
response = client.audio.speech.create(
    model="cosyvoice2-0.5b",
    voice="my_voice",  # 登録したスピーカー名
    input="クローンされた音声です。",
    response_format="mp3"
)
```

## 🔗 Dify統合（未テスト）

1. Difyの「モデルプロバイダー」→「OpenAI-API-compatible」を選択
2. 以下の設定を入力:
   - **API Base URL**: `http://localhost:8000/v1`
   - **API Key**: `dummy-key` (任意の値)
   - **Model Type**: `TTS`
   - **Model Name**: `cosyvoice2-0.5b`

## 🐳 Docker使用方法

### プロファイル使用

```bash
# GPU版（最高性能）
docker-compose --profile gpu up -d

# CPU版（GPUなし環境）
docker-compose --profile cpu up -d

# 開発版（デバッグ用）
docker-compose --profile dev up -d
```

### 個別ビルド

```bash
# GPU版
docker build -f Dockerfile -t cosyvoice-tts:gpu .
docker run -d --gpus all -p 8000:8000 cosyvoice-tts:gpu

# CPU版
docker build -f Dockerfile.cpu -t cosyvoice-tts:cpu .
docker run -d -p 8000:8000 cosyvoice-tts:cpu
```

## 📊 対応音声形式（予定）

- **入力**: テキスト（任意の言語）
- **出力**: MP3, WAV, FLAC, AAC
- **音声**: 内蔵音声 + カスタム音声クローニング

## ⚡ パフォーマンス（理論値）

| 項目 | GPU版 | CPU版 |
|------|-------|-------|
| 初回応答遅延 | 150ms | 2-5秒 |
| リアルタイム係数 | <1.0 | 3-10 |
| 推奨メモリ | 4GB | 8GB |
| ストリーミング | ✅ | ❌ |

## ⚙️ 環境変数

`.env` ファイルで設定可能:

```bash
# サーバー設定
HOST=0.0.0.0
PORT=8000
DEBUG=false

# CosyVoice設定
MODEL_PATH=./pretrained_models/CosyVoice2-0.5B
DEVICE=auto  # auto, cpu, cuda
FP16=true
STREAMING=true

# 詳細設定
MAX_TEXT_LENGTH=1000
CACHE_SIZE=100
CONCURRENT_REQUESTS=4
```

## 🔧 トラブルシューティング

### Dockerエラー

#### "nvidia/cuda image not found"
```bash
# CPU版を使用
docker-compose --profile cpu up -d
```

#### メモリ不足
```bash
# 軽量設定で起動
export MAX_TEXT_LENGTH=200
export CONCURRENT_REQUESTS=1
docker-compose --profile cpu up -d
```

### 手動セットアップエラー

#### GPU メモリ不足
```bash
export CUDA_VISIBLE_DEVICES=""  # CPU強制使用
export FP16=false
```

#### モデルダウンロード失敗
```bash
# 手動ダウンロード
python -c "
from modelscope import snapshot_download
snapshot_download('iic/CosyVoice2-0.5B', local_dir='./pretrained_models/CosyVoice2-0.5B')
"
```

#### 中国語音声合成問題
```bash
# TTSFRD リソース インストール
cd pretrained_models/CosyVoice-ttsfrd/
unzip resource.zip -d .
pip install ttsfrd_dependency-0.1-py3-none-any.whl
pip install ttsfrd-0.4.2-cp310-cp310-linux_x86_64.whl
```

## 🤝 開発への参加

このプロジェクトは現在開発初期段階のため、以下のような貢献をお待ちしています：

### 🧪 動作テスト
- 実際に環境を構築してテスト実行
- 成功・失敗の報告（どちらも大変貴重です）
- 環境依存の問題の報告

### 💡 改善提案
- 実装方針に関するアドバイス
- より良いアーキテクチャの提案
- ドキュメントの改善

### 🛠️ 実装サポート
- 足りない機能の実装
- バグ修正
- テストコードの作成

### 📝 簡単な貢献方法
1. このリポジトリをクローン
2. セットアップを試行
3. 結果をIssueで報告（動いた/動かなかった、エラーメッセージなど）

## 📚 詳細ドキュメント

- [使用ガイド & デモ](USAGE.md) *- 作成予定*
- [Docker各種選択肢](#-docker使用方法)
- [API仕様](http://localhost:8000/docs) *- サーバー起動後*

## 📄 ライセンス

MIT License

## 📞 サポート

問題や質問がある場合は、GitHubのIssuesまでお願いします。

**Note**: このプロジェクトは学習・実験を目的として作成されており、多くの機能が未実装・未検証の状態です。ご理解とご協力をお願いします。