# MLXとは

## 公式サイト

- [GitHub - ml-explore/mlx](https://github.com/ml-explore/mlx) — 本体リポジトリ
- [GitHub - ml-explore/mlx-lm](https://github.com/ml-explore/mlx-lm) — LLM推論・ファインチューニング用パッケージ
- [MLX Documentation](https://ml-explore.github.io/mlx/build/html/index.html) — 公式ドキュメント(英語)
- [Apple Open Source: MLX](https://opensource.apple.com/projects/mlx/) — Apple公式のOSSプロジェクトページ

## 概要

MLXは、Appleの機械学習チームが開発・公開している、Apple Silicon（M1/M2/M3/M4チップなど）向けの機械学習フレームワーク。2023年末にオープンソースとして公開された。

Apple Silicon以外（Windows/Linuxなど）では動作しない。Metal対応のAppleハードウェア専用。

## なぜMLXが作られたか

PyTorchやTensorFlowといった既存フレームワークは汎用的な設計であり、Apple Siliconが持つ「CPUとGPUがメモリを共有する」というハードウェア特性を十分に活かせなかった。MLXはこの特性に最適化する形で設計されている。

## Unified Memory（統合メモリ）

Apple SiliconのMacは、メインメモリをそのままGPU用のメモリ（VRAM相当）としても扱える。一般的なPCのようにGPU専用のVRAM容量がボトルネックにならないため、大きめのモデルでもメモリに載せやすい。

MLXはこのUnified Memoryの帯域を最大限活用するように設計されており、モデルによってはllama.cppより生成速度が10〜20%向上するケースも報告されている。

## PyTorch / NumPyとの違い

- 配列操作のAPIはNumPyに準拠している
- `mlx.nn`（ニューラルネット層）や`mlx.optimizers`（最適化アルゴリズム）はPyTorchの設計に準拠している

→ NumPy/PyTorchの経験があれば学習コストは低い。ただしApple専用フレームワークのため、エコシステムの成熟度（対応モデル数、サードパーティのツール等）ではPyTorchに劣る。

## 遅延評価（Lazy Evaluation）

MLXの配列は演算を書いた時点では計算されず、値が実際に必要になったタイミングで初めて実体化される。

これにより、計算グラフ全体を見てから無駄な計算をまとめて最適化・融合できるため、メモリ効率と実行速度の両方にメリットがある。PyTorchのEager実行（書いた時点で即計算）とはこの点が異なる。

## パラメータと量子化とは

モデルの実体は「大量の数値(重み)の配列」でしかない。「31Bモデル」といった場合の"31B"は、パラメータ(重み)の個数が310億個(31 × 10億)あるという意味。

元のモデルは通常、1つの数値を16bit(2バイト)の浮動小数点数(`BF16`など)で保存している。なので、モデルのファイルサイズはおおよそ以下の掛け算で決まる。

```
パラメータ数 × 1個あたりのビット数 ÷ 8 = ファイルサイズ(バイト)
```

例: 31Bモデルを16bit(フル精度)のまま保存すると、310億 × 16bit ÷ 8 ≒ 62GB になる。

**量子化**とは、この1個あたりのビット数を(16bit→4bitのように)減らして、ファイルサイズと必要メモリを削る作業のこと。ビット数を落とすほど応答品質は下がるが、経験則として16bit→4bitまではほぼ品質が落ちず、4bitを下回ると急激に劣化しやすいことが分かっている。そのため4bit量子化が「サイズと品質のバランスが良い落とし所」として広く使われている。

同じ31Bモデルでも:
- 16bit(フル精度): 約62GB
- 8bit: 約31GB
- 4bit: 約15.5GB

`mlx-community`で配布されているモデル名に付いている`-4bit`や`-8bit`は、この量子化のビット数を表している。

## 動作要件の目安

- Apple Silicon搭載Mac（M1以降）が必須
- 例: 8Bパラメータのモデルを4bit量子化で動かす場合、最低でも5〜6GB程度のメモリを消費
- 快適に動かすには16GB以上のメモリが推奨ライン

## このリポジトリでの構成

`pyproject.toml`(uvで管理)の依存に `mlx-lm`(とその依存の `mlx`)が含まれており、LLMの推論・ファインチューニングを中心に扱う想定。

## 参考にした記事

- [MLX基礎解説 - Appleの次世代MLフレームワーク (Qiita)](https://qiita.com/GeneLab_999/items/a7bcae0c1b3d44f1336b)
- [MLX 使い方 入門 (Negi AI Lab)](https://ai.negi-lab.com/posts/mlx-apple-silicon-local-llm-tutorial/)
