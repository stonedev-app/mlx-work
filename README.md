# mlx-work

MLX(Apple Silicon向け機械学習フレームワーク)の検証・学習用の作業ディレクトリ。

## 使い方

依存関係は[uv](https://docs.astral.sh/uv/)で管理している(`pyproject.toml` / `uv.lock`)。初回は以下で`.venv`を作成・同期する。

```bash
uv sync
```

コマンドは`uv run`を付けて実行する(activate不要)。

```bash
uv run mlx_lm.chat
```

詳しいセットアップ手順は [docs/02_setup.md](docs/02_setup.md) を参照。
