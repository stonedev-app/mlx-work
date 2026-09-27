# セットアップ

## 前提

- Apple Silicon搭載Mac(M1以降)
- [uv](https://docs.astral.sh/uv/)(Pythonのバージョン管理・仮想環境・依存関係をまとめて扱うツール)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv --version
```

Python本体(このリポジトリでは3.12系、`.python-version`で固定)はuvが必要に応じて自動で取得するので、別途インストールは不要。

## 環境の構築

プロジェクト直下(`mlx-work/`)で以下を実行する。`pyproject.toml`と`uv.lock`に従って`.venv`が作られ、固定バージョンの依存関係がインストールされる。

```bash
uv sync
```

## コマンドの実行

コマンドは`uv run`を付けて実行する。`.venv`の環境で実行されるので、activateは不要。

```bash
uv run python --version
```

## パッケージの追加・更新

`pip install`は使わない(`pyproject.toml`と`uv.lock`に記録されず、環境を再現できなくなる)。

```bash
uv add <パッケージ名>         # 追加(pyproject.toml と uv.lock が更新される)
uv remove <パッケージ名>      # 削除
uv lock --upgrade            # 全依存を最新に更新
```

このリポジトリの直接の依存は`mlx-lm`のみ。`mlx`や`hf`コマンド(`huggingface_hub`)はその依存として入る。

## 動作確認: コマンドラインでチャットしてみる

`mlx-lm` には `mlx_lm.chat` というCLIコマンドが含まれており、ターミナル上でREPL形式のマルチターンチャットができる。

```bash
uv run mlx_lm.chat --model <モデル名>
```

`--model` を省略した場合のデフォルトは `mlx-community/Llama-3.2-3B-Instruct-4bit`。初回実行時はHugging Face Hubからモデルがダウンロードされる。
