# Colab.ComfyUI

Google Colab上でComfyUIを使用し、最新のビデオ生成AIモデル（Wanシリーズ）を簡単に実行するためのリポジトリです。

## 📝 概要

このリポジトリは、Google Colabの無料GPU（T4など）や有料GPU（A100、V100など）を活用して、高品質なAIビデオ生成を行うための環境を提供します。ComfyUIのワークフローファイルと、セットアップを自動化するJupyter Notebookが含まれています。

### 主な特徴

- **完全自動セットアップ**: 1クリックでComfyUI + 必要な全カスタムノードをインストール
- **Google Drive統合**: モデルファイルをDriveに保存し、再利用可能
- **最新ワークフロー**: GitHubから自動的に最新版のワークフローを取得
- **メモリ最適化**: 低VRAMでも動作するワークフロー対応
- **多様なモデル対応**: 14B大規模モデルから5B軽量モデルまで

## 🎯 対応機能

### ビデオ生成タイプ

1. **Image-to-Video (I2V)**: 画像から動画を生成
2. **Text-to-Image-to-Video (TI2V)**: テキストから画像、そして動画を生成
3. **First/Last Frame Video (FLF2V)**: 最初と最後のフレームを指定して中間動画を生成
4. **Video-to-Audio**: 動画フレームから音声を生成

### 高速化・最適化機能

- **TeaCache**: 1.5-2倍の高速化（わずかな品質劣化）
- **低VRAM対応**: 15GB程度のVRAMでも動作
- **LoRA対応**: スタイルのカスタマイズが可能
- **量子化モデル**: GGUF形式でさらにメモリ節約

## 📂 リポジトリ構成

```
Colab.ComfyUI/
├── comfyui.ipynb          # メインのセットアップ＆起動ノートブック
├── workflows/             # ComfyUIワークフローファイル（JSON）
│   ├── i2v-14b.json              # 画像→動画（14Bモデル）
│   ├── i2v-14b-nsfw-lora.json    # NSFW対応I2V（LoRA付き）
│   ├── ti2v-5b-official.json     # テキスト→画像→動画（5B）
│   ├── ti2v-5b-lora.json         # TI2V（LoRA対応版）
│   ├── low-vram-ti2v.json        # 低VRAM版TI2V
│   ├── flf2v-14b-teacache.json   # 最初/最後フレーム動画（TeaCache高速化）
│   ├── flf2v-14b-with-lora.json  # FLF2V（LoRA対応）
│   ├── flf2v-chain-multi-images.json # 複数画像を繋ぐ連続動画
│   ├── rapid-aio-i2v.json        # 高速I2V（軽量版）
│   └── mmaudio-video-to-audio.json # 動画→音声生成
└── success-records/       # 動作確認済みワークフロー記録
```

## 🚀 使い方

### 1. Google Colabで開く

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/hatoya0703/Colab.ComfyUI/blob/main/comfyui.ipynb)

上のボタンをクリックするか、`comfyui.ipynb` を直接開いてください。

### 2. GPU設定

- **メニューバー**: `ランタイム` → `ランタイムのタイプを変更`
- **ハードウェアアクセラレータ**: `T4 GPU` または `A100 GPU` を選択

### 3. セルを順番に実行

各セルを上から順に実行してください：

#### **セル1: GPU確認**
現在のGPUとVRAMを確認し、推奨モデルを表示します。

#### **セル2: Google Driveマウント**
- Google Driveをマウント（認証が必要）
- `/content/drive/MyDrive/ComfyUI/` にモデル保存用フォルダを自動作成
- GitHubから最新のワークフローを自動ダウンロード

#### **セル3: ComfyUI + カスタムノードインストール**
以下を自動インストール：
- ComfyUI本体
- ComfyUI-Manager（ノード管理GUI）
- WanVideoWrapper（TeaCache、MagCache内蔵）
- GGUF対応
- VideoHelperSuite
- KJNodes、rgthree-comfy（低VRAM用）
- MMAudio（音声生成）
- その他ユーティリティノード

#### **セル4: モデルをシンボリックリンクで接続**
Google DriveのモデルフォルダとComfyUIをリンクします。

#### **セル5: ComfyUI起動**
cloudflaredトンネルを使って外部アクセス可能なURLを生成し、ComfyUIを起動します。

### 4. ComfyUIにアクセス

セル5の実行後、以下のようなURLが表示されます：
```
🚀 ComfyUI URL: https://xxxxx-xxxx-xxxx.trycloudflare.com
```

このURLをクリックしてComfyUIにアクセスしてください。

### 5. ワークフローを読み込む

ComfyUI上で：
1. 右上の **「Load」** ボタンをクリック
2. `workflows/` フォルダから目的のワークフローを選択
3. ワークフローが読み込まれたら、必要に応じてパラメータを調整
4. **「Queue Prompt」** で生成開始

## 📊 ワークフロー詳細

### 🎬 Image-to-Video (I2V)

| ワークフロー | モデルサイズ | 解像度 | フレーム数 | 特徴 |
|------------|------------|--------|----------|------|
| `i2v-14b.json` | 14B | 1024x1024 | 81 | 標準I2V、高品質 |
| `i2v-14b-nsfw-lora.json` | 14B | 1024x1024 | 81 | NSFW LoRA対応 |
| `rapid-aio-i2v.json` | AIO | 640x640 | 81 | 高速・軽量版 |

**推奨VRAM**: 24GB以上（T4: fp8量子化、A100: fp16）

### 📝 Text-to-Image-to-Video (TI2V)

| ワークフロー | モデルサイズ | 解像度 | フレーム数 | 特徴 |
|------------|------------|--------|----------|------|
| `ti2v-5b-official.json` | 5B | 1280x704 | 121 | 標準TI2V |
| `ti2v-5b-lora.json` | 5B | 1280x704 | 121 | LoRA対応（アニメ等） |
| `low-vram-ti2v.json` | 5B | 1280x704 | 121 | 低VRAM最適化 |

**推奨VRAM**: 15GB以上（T4で動作可能）

### 🎞️ First/Last Frame Video (FLF2V)

| ワークフロー | モデルサイズ | 解像度 | フレーム数 | 特徴 |
|------------|------------|--------|----------|------|
| `flf2v-14b-teacache.json` | 14B | 1024x1024 | 81 | TeaCacheで1.5-2倍高速化 |
| `flf2v-14b-with-lora.json` | 14B | 1024x1024 | 81 | LoRA対応版 |
| `flf2v-chain-multi-images.json` | 14B | 1024x1024 | 81x3 | 複数画像を繋ぐ連続動画 |

**推奨VRAM**: 24GB以上

### 🔊 Video-to-Audio

| ワークフロー | モデル | 特徴 |
|------------|--------|------|
| `mmaudio-video-to-audio.json` | MMAudio Large 44K V2 | 動画フレームから音声を生成 |

**推奨VRAM**: 12GB以上

## 🔧 必要なモデルファイル

ワークフローを実行する前に、必要なモデルをGoogle Driveにダウンロードしておく必要があります。

### 主要モデル

#### Wan 2.2 Diffusion Models (HuggingFace)
- `wan_2.2_i2v_14b_high_noise_bf16.safetensors` (28GB)
- `wan_2.2_i2v_14b_low_noise_bf16.safetensors` (28GB)
- `wan_2.2_ti2v_5b_bf16.safetensors` (10GB)

配置先: `/content/drive/MyDrive/ComfyUI/diffusion_models/`

#### Text Encoders
- `umt5_xxl_fp8_e4m3fn_scaled.safetensors` (5GB)

配置先: `/content/drive/MyDrive/ComfyUI/text_encoders/`

#### VAE
- `wan_2.1_vae_bf16.safetensors` (335MB)

配置先: `/content/drive/MyDrive/ComfyUI/vae/`

#### LoRAファイル（オプション）
配置先: `/content/drive/MyDrive/ComfyUI/loras/`

### MMAudio用モデル（音声生成する場合）
- `mmaudio_large_44k_v2.pth`
- `mmaudio_16khz.pth`
- `mmaudio_16khz_30s.pth`

配置先: `/content/drive/MyDrive/ComfyUI/mmaudio/`

## 💡 GPUとモデルの推奨

| GPU | VRAM | 推奨モデル |
|-----|------|-----------|
| T4 | 15GB | TI2V-5B、GGUF-Q4量子化、Rapid AIO |
| L4 | 24GB | I2V-14B (fp8)、FLF2V-14B (fp8) |
| A100 | 40GB+ | I2V-14B (fp16)、FLF2V-14B (fp16) |

### メモリ不足時の対処法

1. **量子化モデルを使用**: fp16 → fp8 → GGUF-Q8 → GGUF-Q4
2. **小型モデルを使用**: 14Bモデル → 5Bモデル
3. **解像度を下げる**: 1024x1024 → 640x640
4. **フレーム数を減らす**: 121フレーム → 81フレーム
5. **低VRAM用ワークフローを使用**: `low-vram-ti2v.json`

## 🛠️ トラブルシューティング

### モデルが見つからない
- Google Driveにモデルファイルが正しく配置されているか確認
- セル4のシンボリックリンク設定を再実行

### メモリ不足エラー
- より小さいモデルやワークフローを使用
- Colabのランタイムを再起動して再試行

### cloudflared URLが表示されない
- セル5を再実行
- ログを確認し、`https://xxxx.trycloudflare.com` のURLを探す

### ワークフローが読み込めない
- ComfyUI-Managerで不足しているカスタムノードをインストール
- セル3のカスタムノードインストールを再実行

## 📚 参考リンク

- [ComfyUI公式](https://github.com/comfyanonymous/ComfyUI)
- [WanVideoWrapper](https://github.com/kijai/ComfyUI-WanVideoWrapper)
- [Wan 2.1/2.2モデル](https://huggingface.co/Comfy-Org/Wan_2.1_ComfyUI_repackaged)
- [MMAudio](https://github.com/kijai/ComfyUI-MMAudio)

## 📄 ライセンス

このリポジトリはMITライセンスの下で公開されています。

使用するモデルやカスタムノードのライセンスについては、各プロジェクトのドキュメントを確認してください。

## 🤝 コントリビューション

問題報告やプルリクエストは歓迎します。新しいワークフローや最適化の提案もお待ちしています。

---

**注意**: このリポジトリは個人的な用途のために作成されています。商用利用する場合は、使用するモデルやツールのライセンスを必ず確認してください。
