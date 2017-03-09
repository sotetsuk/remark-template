class: center, middle

### PGQ: Combining Policy Gradient And Q-learning
#### O’Donoghue et al. ICLR 2017

Sotetsu KOYAMADA

---

### Table of Contents

- 論文概要
- 強化学習の問題設定と定義
- Q学習と方策勾配法のReview
- 論文の理論的結果 (1)
- 論文の理論的結果 (2)
- 提案手法の説明: PGQ
- 実験結果
- 関連研究
- 参考文献

---

## 論文概要

---

<img src='img/front.png' width='600px'>

---

### 紹介する理由

##### 1. 強化学習における二つの別のアプローチの統合を試みている
- 理論的に興味深い
- 実用面からも長所を組み合わせることが期待できる

##### 2. Atariベンチマークでの安定した実験結果

---

### ICLR2017レビュアーのコメント

https://openreview.net/forum?id=B1kJ6H9ex

>  It has the potential to be an impactful paper, with the most positive comment noting that it **"will likely influence a broad swath of RL"**.

---

### コントリビューション

- 理論的結果 (1)
- 理論的結果 (2)
- PGQの提案と評価

---

## 強化学習の問題設定と定義

---

### 一般的な強化学習の問題設定 (1)

#### マルコフ決定過程 (MDP)

.green[**環境**] は現在の **状態** と **行動** にだけ依存して未知の **状態遷移確率** `\(P\)` から次の **状態** と **即時報酬** を返す

`$$(X_{t+1}, R_{t+1}) \sim P( \cdot \,|\, X_t, A_t)$$`

.green[**エージェント**] は現在の **状態** と **報酬** を観測し、現在の **方策** `\(\pi\)` に基づいて次の **行動** を返す

`$$A_{t} \sim \pi(\cdot \,|\, X_t)$$`

サンプル系列 `\(\left(X_0, A_0, R_1, X_1, A_1, \ldots \right)\)` が生成される

---

### 一般的な強化学習の問題設定 (2)

#### コントロール

.green[**目的関数**] `\(J\)` を最適化するように方策を学習する（例: 割引報酬和の期待値）

`$$J(\pi) = \mathbf{E} \left[\sum_{t=1}^{\infty} \gamma^{t} R_{t+1} \right]$$`

---

### ある方策に基づく価値関数

**Def.** 行動価値関数 （期待値は状態遷移確率と方策 `\(\pi\)`）

`$$Q^{\pi}(x, a) := \mathbf{E} \left[\sum_{t=0}^{\infty} \gamma^{t} R_{t+1} \,|\, X_0 = x, A_0 = a \right]$$`


**Def.** 方策 `\(\pi\)` に基づくベルマン作用素

`$$T^{\pi}Q(x, a) = r(x, a) + \gamma \sum_{x^{\prime}, a^{\prime}} \pi(a^{\prime}|x^{\prime}) P(x^{\prime}|x, a) Q(x^{\prime}, a^{\prime})$$`

（ただし、`\(r(x, a) = \mathbf{E} \left[R_{t+1} | X_t=x, A_t=a\right] \)` ）

この行動価値関数 `\(Q^{\pi}\)` は、ベルマン作用素の不動点 (i.e., `\(T^{\pi}Q=Q\)` ) として求まる（める）

---

### 最適行動価値

理想的な行動を取ったとすると、最適化したい指標が期待的にどうなるか

**Def.** 最適行動価値関数

`$$Q^{\ast}(x, a) := \max_{\pi} Q^{\pi} (x, a) $$`


**Def.** ベルマン最適作用素

`$$T^{\ast}Q(x, a) := r(x, a) + \gamma \sum_{x^{\prime}} P(x^{\prime}|x, a) \max_{a^{\prime}} Q(x^{\prime}, a^{\prime})$$`

最適行動価値関数 `\(Q^{\ast}\)` は、ベルマン最適作用素の不動点 (i.e., `\(T^{\ast}Q=Q\)` ) として求まる（める）

---

### 強化学習（コントロール）の二系統

#### 価値ベース

- （行動）価値関数を直接推定・最適化する
- **Q学習** [Watkins89] **SARSA** [Rummery&Niranjan94]
- w/ 深層学習: .red[**DQN**] [Mnih+13, 15]

#### 方策ベース

- 方策を直接推定・最適化する
- **方策勾配法** [Sutton+99]
- w/ 深層学習: .red[**A3C**] [Mnih+16]

---

## Q学習, SARSA, 方策勾配法のReview

---

### SARSA, Q学習

SARSAは現在の方策に基づく行動価値関数を、Q学習は最適行動価値関数をパラメトライズしてベルマン作用素の不動点を求めるよう更新する:

`$$\nabla_{\theta}\left( T^{\pi}Q_{\theta} - Q_{\theta} \right) = \mathbf{E} \left[ \left( T^{\pi}Q_{\theta} - Q_{\theta} \right) \nabla_{\theta}Q_{\theta} \right]\\
\nabla_{\theta}\left( T^{\ast}Q_{\theta} - Q_{\theta} \right) = \mathbf{E} \left[ \left( T^{\ast}Q_{\theta} - Q_{\theta} \right) \nabla_{\theta}Q_{\theta} \right]$$`

- サンプリングは現在の `\(Q_{\theta}\)` に基づいた `\(\epsilon\)`-グリーディ方策かボルツマン方策が用いられることが多い
- ターゲットが現在の推定している `\(Q_{\theta}\)` に依存しているのでブートストラップになっている（TD法全般に共通）
- それぞれ適切な条件下で最適行動価値関数へ収束する（SARSAは **方策改善** も必要とする (e.g., GLIEを満たす方策を用いる[XXXX])）

---

### 方策勾配法

方策 `\(\pi\)` をパラメトライズした上で `\(\pi_{\theta}\)` を直接最適化する

`$$\nabla_{\theta} J(\theta) = \mathbf{E} \left[ Q^{\pi}(x, a) \nabla_{\theta} \log \left(\pi(a|x) \right) \right]$$`

- 方策勾配定理 [Sutton+99]
- `\(\pi\)` **からサンプルを生成してサンプル平均を取れば** 不偏推定量が計算できるので、それを使って更新を行なう
- 逆に `\(\pi\)` のような推定している分布に関するサンプリングが必要なような確率的最適化の場面では強化学習以外の文脈でも使われる (e.g., hard attention [Mnih+14, Xu+15] , sequence generatioon [Ranzato+16, Bahdanau+17])

---

### アドバンテージ

---

### 方策勾配法におけるエントロピー正則化

- [HOGE]
- 方策が決定論的にならないようにかける正則化として一般的

##### 目的関数に割引付きエントロピー正則化項を加えたと見なすこともできる

この観点からのより興味深い分析がICMLに投稿されている [Nachum+17]

---

### Q学習と方策勾配法の特徴

- .green[**Q学習**]: 本質的に **方策オフ型学習**（後述）の手法
- .green[**方策勾配法**]: 本質的に **方策オン型学習**（後述）の手法

**なので、方策オフ・オン型学習のPros/Cons（後述）がそのままPros/Consになってしまう**

> ... Q-learning is an inherently off-policy algorithm ... [Szepesvari10]

---

### 方策オンと方策オフ

現在推定している方策（推定方策）と、実際に行動を選択肢、サンプルを生成している方策（行動方策）が等しい場合は **方策オン型** といい、そうでない場合は **方策オフ型** という。

---

### Q学習は何故本質的に方策オフ型？

**Review:** Q学習の更新則

`$$\Delta_{\theta} \propto \nabla_{\theta}\left( T^{\ast}Q_{\theta} - Q_{\theta} \right) = \mathbf{E} \left[ \left( T^{\ast}Q_{\theta} - Q_{\theta} \right) \nabla_{\theta}Q_{\theta} \right]$$`

`$$T^{\ast}Q(x, a) := r(x, a) + \gamma \sum_{x^{\prime}} P(x^{\prime}|x, a) \max_{a^{\prime}} Q(x^{\prime}, a^{\prime})$$`

- **更新則の期待値のサンプル平均が推定方策に依存していないため、
.red[関係無い分布からサンプリングをしてもサンプル平均が不偏推定量になる]**
- 逆に、方策オンのサンプルだけから学習するのが難しい（たとえ現在の `\(Q^{\ast}\)` の `\(\epsilon\)`-グリーディ方策やボルツマン方策からサンプリングしても、探索的行動が取られれば `\(\pi(a|x) = \text{argmax}_{a} Q^{\ast} (x, a) \)` には沿えない）

---

### 方策勾配法は何故本質的に方策オン型？

**Review:** 方策勾配法の更新則

`$$\Delta_{\theta} \propto \nabla_{\theta} J(\theta) = \mathbf{E} \left[ Q^{\pi}(x, a) \nabla_{\theta} \log \left(\pi(a|x) \right) \right]$$`

**この期待値は推定方策に依存しているためサンプル平均が、.red[方策オンでサンプリングされていないと不偏推定量になってくれない]**

---

## 方策オフ型のPros/Cons
- **Pros:** 一度サンプルを生成すれば何度も活用できる・複数のモデルを学習できる
- **Pros:** **経験再生** [HOGEHOGE] を使えるのでデータ効率的
- **Cons:** .red[**サンプル平均がサンプリングする方策に依存する場合（特に複数ステップ法を使う場合に）バイアスが生じる**]（加えてそもそも依存しない手法が多くない...）

##### このConsを解決するアプローチ

- Importance sampling (IS) [XXXX]
- Retrace(λ) [XXXX]
- PCL [XXXX]
- その他手法はxxxにまとまってる

---

### 方策オン型のPros/Cons

- **Pros:** サンプル平均が行動方策に依存する場合でもバイアスがない
- **Pros:** 複数ステップ法が使えるのでバイアス・バリアンスのトレードオフがとれる
- **Cons:** .red[**経験再生が使えずサンプルが使い捨てになりデータ効率が良くない**]

##### このConsを解決しようとするアプローチ

- ACER [XXXX]
- .red[**PGQ**] [XXXX]

---
## この論文の理論的な貢献

- エントロピー正則化付きの方策勾配法は実は `\(Q^{\pi}\)` をこっそり推定していた
- Actor-criticと価値ベースは更新則が実は（特別な場合に）一緒だった

---

### 理論的貢献(1)

### エントロピー正則化付きの方策勾配法は実は `\(Q^{\pi}\)` を推定していた

---

### 理論的貢献(2)

### Actor-criticと価値ベースは更新則が実は（特別な場合に）一緒だった

---

### アルゴリズムの基本的なアイディア

- 現在推定している `\(\pi(a|x)\)`と`\(V(x)\)` から `\(Q(x, a)\)` を求めて **(!)** この `\(Q(x, a)\)` にQ学習の更新則を適用する
-

---

### アルゴリズム

---

## 参考文献（年代順）

---

- [Watkins89] Learning from delayed rewards. PhD thesis 1989
- [Sutton+99] Policy Gradient Methods for Reinforcement Learning with Function Approximation. NIPS 1999
- [Rummery&Niranjan94] On-line Q-learning using connectionist systems. 1994
- [Szepesvari10] Algorithms for Reinforcement Learning. 2010
- [Mnih+13] Playing atari with deep reinforcement learning. NIPS-WS 2013

---

- [Mnih+14] Recurrent Models of Visual Attention. NIPS 2014
- [Mnih+15] Human-level control through deep reinforcement learning. Nature 2015
- [Xu+15] Show, attend and tell: Neural image caption generation with visual attention ICML 2015
- [Mnih+16] Asynchronous Methods for Deep Reinforcement Learning ICML 2016
- [Ranzato+16] Sequence level training with recurrent neural networks ICLR 2016

---

- [Bahdanau+17] An Actor-Critic Algorithm for Sequence Prediction. ICLR 2017
- [Munos+16] Safe and efficient off-policy reinforcement learning. NIPS 2016
- [Wang+17] Sample Efficient Actor-Critic with Experience Replay. ICLR 2017
- [O’Donoghue+17] PGQ: Combining Policy Gradient and Q-learning. ICLR 2017
- [Nachum+17] Bridging the Gap Between Value and Policy Based Reinforcement Learning. submitted to ICML 2017
