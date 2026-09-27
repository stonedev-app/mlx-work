# mlx-work

MLX(Apple Silicon向け機械学習フレームワーク)の検証・学習用の作業ディレクトリ。

## 使い方

依存関係は[uv](https://docs.astral.sh/uv/)で管理している(`pyproject.toml` / `uv.lock`)。コマンドは`uv run`を付けて実行する(activate不要)。

### このリポジトリの環境を再現する

```bash
uv sync
uv run mlx_lm.chat
```

### 別ディレクトリで新規に作る

```bash
uv init <ディレクトリ名>
cd <ディレクトリ名>
uv add mlx-lm
uv run hf skills add   # 任意: Claude Code 用の hf-cli スキル
uv run mlx_lm.chat
```

詳しいセットアップ手順は [docs/02_setup.md](docs/02_setup.md) を参照。
