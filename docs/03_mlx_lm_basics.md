# mlx-lmの基本操作(モデルの管理とチャット)

## モデル管理はHugging Faceに依存している

`mlx-lm`自体はモデルの配布・キャッシュの仕組みを独自に持っていない。内部で`huggingface_hub`を使い、モデルのダウンロード・保存を行っている(保存先は標準のHFキャッシュディレクトリ)。

そのため、モデルのインストール・一覧表示・削除は`hf`コマンド(`huggingface_hub`のCLI)で行う。`mlx_lm.chat`などに渡す`--model`の値も、Hugging Face Hub上のリポジトリID(例: `mlx-community/Llama-3.2-3B-Instruct-4bit`)がそのまま使える。

## モデルの探し方

MLX用に変換・量子化済みのモデルは、主に`mlx-community`という組織(Apple公式ではなくコミュニティ運営)から配布されている。

**Webで探す(おすすめ)**: [huggingface.co/mlx-community](https://huggingface.co/mlx-community) にアクセスすると一覧が見られる。ダウンロード数順・更新日順のソートやキーワード検索ができる。

**`hf` CLIで探す**:
```bash
uv run hf models list --author mlx-community --search llama --sort downloads --limit 20
```
- `--author mlx-community` — mlx-community組織のモデルに絞る
- `--search <キーワード>` — モデル名で検索
- `--sort downloads` — 人気順に並べる
- `--limit` — 表示件数

**命名のコツ**: 元々PyTorch版で有名なモデル(例: `Qwen/Qwen2.5-7B-Instruct`)があれば、大抵`mlx-community/Qwen2.5-7B-Instruct-4bit`のように「同じ名前+ビット数」で変換版が存在する。この規則性を覚えておくと探しやすい。

## どのモデルを使うか

まずは上記の`mlx-community`から選ぶのが手軽。

最初の一歩としては、`mlx_lm.chat`のデフォルトモデルでもある `mlx-community/Llama-3.2-3B-Instruct-4bit` がおすすめ。約1.8GBで数分でダウンロードでき、動作も軽快。

ただし3B・4bit量子化という軽量構成のため、知識量や事実の正確さは弱く、ハルシネーション(それらしい誤り)を返しやすい。速度よりも精度を重視したい場合は、`mlx-community/Qwen2.5-7B-Instruct-4bit` のようなより大きいモデルを試すとよい。

## インストール(ダウンロード)

```bash
uv run hf download mlx-community/Llama-3.2-3B-Instruct-4bit
```

## インストール済みモデルの一覧表示

```bash
uv run hf cache list
```

ID・サイズなどを省略せず全て表示したい場合:

```bash
uv run hf cache list --no-truncate
```

`ID`列に表示される文字列(例: `model/mlx-community/Llama-3.2-3B-Instruct-4bit`)が、削除コマンドで指定するターゲットになる。

## モデルの削除

```bash
uv run hf cache rm model/mlx-community/Llama-3.2-3B-Instruct-4bit
```

削除後、`uv run hf cache list`を実行すると `No results found.` と表示され、キャッシュが空になったことを確認できる。

## モデルとチャットする

```bash
uv run mlx_lm.chat --model mlx-community/Llama-3.2-3B-Instruct-4bit
```

指定したモデルがキャッシュに無ければ自動でダウンロードされ、あればそのまま使われる。実行するとREPL形式のプロンプトが起動し、対話を続けられる。

## 補足: `hf-cli`スキル

`hf`コマンドはコマンド体系がバージョンごとに変わりやすい(例: `huggingface-cli scan-cache` → `hf cache ls`)。プロジェクト直下で以下を実行しておくと、Claude Codeなどが常に最新の`hf`コマンド体系を参照できるようになる。

```bash
uv run hf skills add --claude
```
