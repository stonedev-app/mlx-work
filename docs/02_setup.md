# セットアップ

## 前提

- Apple Silicon搭載Mac(M1以降)
- Homebrewでインストールした `python3`(このリポジトリでは Python 3.14系)

```bash
python3 --version
which python3
```

## venvの作成

プロジェクト直下(`mlx-work/`)で以下を実行する。

```bash
python3 -m venv .venv
```

## venvの有効化

```bash
source .venv/bin/activate
```

有効化されるとプロンプト先頭に `(.venv)` と表示される。確認コマンド:

```bash
which pip
which python
```

`.venv/bin/pip` のパスが出れば有効化できている。

## mlx-lmのインストール

```bash
pip install mlx-lm
```

### requirements.txt から再現する場合

他のマシン・別のvenvでこのリポジトリの環境を再現したい場合は、固定バージョンで一括インストールできる。

```bash
pip install -r requirements.txt
```

## venvを抜ける

```bash
deactivate
```

## 動作確認: コマンドラインでチャットしてみる

`mlx-lm` には `mlx_lm.chat` というCLIコマンドが含まれており、ターミナル上でREPL形式のマルチターンチャットができる。

```bash
mlx_lm.chat --model <モデル名>
```

`--model` を省略した場合のデフォルトは `mlx-community/Llama-3.2-3B-Instruct-4bit`。初回実行時はHugging Face Hubからモデルがダウンロードされる。
