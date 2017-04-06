# PGQ: Combining policy gradient and Q-learning

<img src='./img/PGQ: Combining policy gradient and Q-learning.png' width=650px />

- 論文リンク: https://arxiv.org/abs/1611.01626
- 出版年: 2017
- ジャーナル・カンファレンス: ICLR
- 著者: Brendan O'Donoghue, Remi Munos, Koray Kavukcuoglu, Volodymyr Mnih
- 所属: DeepMind

---

### 概要
エントロピー正則化付きの方策勾配法とQ学習を組み合わせた新しいアルゴリズムPGQを提案し、DQNやA3Cに対する優位性をAtariドメインで実験的に示した。 Atariの50以上のゲームにおいて、DQNとA3Cと比較したとき3アルゴリズム中PGQが最下位になったのは1つのゲームだけという非凡な性能を示した。

### 目的
方策勾配法は方策オン型で経験再生を使えずサンプル効率が悪いため、Q学習（方策オフ型）と組み合わせてこれを解決したい。

### 結果

#### 1. Atariドメインでの評価
Atariの50以上のゲームにおいて、得られた報酬に基づくスコアによる評価を行い、DQNとA3Cと比較を行った。
50以上のゲームにおける平均スコアだけでなくスコアの中央値でも人間のスコアを上回り、 **.red[PGQとDQNとA3Cの3アルゴリズム中最下位になったのは1つのゲームだけだった。]**

---

### 手法

**.red[重要な貢献] エントロピー正則化付きの方策勾配法の停留点において、方策`\(\pi\)`とこの方策におけるアドバンテージ関数`\(A^{\pi}(s, a)\)`の間に次式の関係性を示した（Eq.4):
`$$\pi(a|s) \propto \exp \left( A^{\pi}(s, a) / \alpha \right)$$`**
これにより、方策勾配法でもQ関数の推定をしているとみなすことが出来るようになった（Eq.6）:
`$$\Delta\theta \propto \mathbf{E} \left[ \left( Q^{\pi}(s, a) - \tilde{Q}^{\pi}(s, a) \right) \nabla_{\theta} \log \pi (s, a) \right]$$`
PGQは、この関係式から逆に行動価値関数を方策`\(\pi\)`と価値関数`\(V^{\pi}\)`から
`$$\tilde{Q}^{\pi}(s, a) = \alpha (\log \pi(s, a) + H^{\pi}(s)) + V^{\pi}(s)$$`
と計算し、`\(\tilde{Q}^{\pi}\)`がベルマン最適方程式に従うよう正則化をかけた方策勾配法の目的関数を最適化する:
`$$\Delta\theta \propto (1-\eta) \mathbf{E} \left[ \left( Q^{\pi} - \tilde{Q}^{\pi} \right) \nabla_{\theta} \log \pi \right] + \eta \mathbf{E} \left[ \left( T^{\ast} \tilde{Q}^{\pi} - \tilde{Q}^{\pi} \right) \nabla_{\theta} \log \pi \right] $$`
第一項がエントロピー正則化付き方策勾配法、第二項がQ学習に対応している。

---

#### 次に読むべき論文
- Nachum et al. (2017) [Bridging the Gap Between Value and Policy Based Reinforcement Learning](https://arxiv.org/abs/1702.08892)

#### 関連リンク
- [openreview](https://openreview.net/forum?id=B1kJ6H9ex)

#### タグ
- :q-learning:
- :policy gradient:
- :atari:
- :neural network:
