# 政府発行通貨への移行モデル  
### 借金に依存しない貨幣システムの再現可能な研究

---

## 概要

本プロジェクトは、借入（負債）に依存した通貨発行から、政府が直接通貨を発行する仕組みへの移行を提案するものである。

現代の貨幣システムでは、通貨は主に信用（負債）を通じて創造される。この仕組みは機能している一方で、政府債務の累積、税収依存、そして経済の不安定性といった構造的課題を抱えている。

本リポジトリでは、負債を伴わない通貨発行モデルを提示し、その挙動をシミュレーションによって検証可能な形で示す。

---

## 問題提起

現行の貨幣システムは以下の特徴を持つ：

- 借入（負債）に基づく通貨発行  
- 財政維持のための税収依存構造  
- 政府債務の長期的蓄積  
- デフレ・スタグフレーション下での不安定性  

これらは政策の自由度を制限し、構造的なリスクを生む要因となる。

---

## 提案モデル

本研究では、以下の特徴を持つ政府発行通貨モデルを提案する：

- **負債を伴わない通貨発行**  
- **直接的な通貨供給（UBI的分配）**  
- **実体経済の供給能力によるインフレ制約**  
- **国債依存からの分離**

本モデルでは「財源」を税収ではなく、制御された通貨供給として再定義する。

---

## シミュレーション

本リポジトリには、以下の関係を可視化するシミュレーションが含まれる：

- 通貨供給の増加  
- 実体経済の供給制約  
- インフレの発生条件  

通貨供給が実体供給を上回る場合に、価格水準が上昇する様子を確認できる。

---

## リポジトリ構成

# src/simulation.py

import numpy as np
import matplotlib
matplotlib.use('Agg')  # Docker環境でも動くように（画面表示なし）
import matplotlib.pyplot as plt

# ===== パラメータ =====
periods = 50
ubi = 100                  # 毎期の政府支出（UBI）
production = 80            # 初期供給力
growth_rate = 0.02         # 経済成長率
inflation_sensitivity = 0.05
decay_rate = 0.00          # 半減期通貨（例：0.02で2%減衰）

# ===== 初期値 =====
money_supply = 0
price_level = 1.0

money_history = []
price_history = []
supply_history = []
time = []

# ===== シミュレーション =====
for t in range(periods):
    # 政府による通貨発行
    money_supply += ubi

    # 半減期通貨（任意）
    money_supply *= (1 - decay_rate)

    # 実体経済の成長
    current_supply = production * ((1 + growth_rate) ** t)

    # 需要と供給
    demand = money_supply
    supply = current_supply

    # インフレ計算
    inflation = max(0, (demand - supply) / supply) * inflation_sensitivity
    price_level *= (1 + inflation)

    # 記録
    money_history.append(money_supply)
    price_history.append(price_level)
    supply_history.append(supply)
    time.append(t)

# ===== グラフ作成 =====
plt.figure()

plt.plot(time, money_history, label="Money Supply")
plt.plot(time, supply_history, label="Real Supply")
plt.plot(time, price_history, label="Price Level")

plt.title("Government Money Model Simulation")
plt.xlabel("Time")
plt.ylabel("Value")
plt.legend()
plt.grid()

# ===== 保存 =====
plt.savefig("results/result.png", dpi=300)
