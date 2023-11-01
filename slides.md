---
theme: default
class: text-center
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
title: b4fall-seminar
---


### 敵対的再帰反射パッチ:
### 暗闇で有効化する敵対的攻撃

<hr>

### 鶴岡 豪(早稲田大学)


#### 野本一輝(早大)，小林 竜之輔(早大)
#### 田中優奈(早大)，森 達哉(早大/NICT/理研AIP)


---

# 目次

<hr>

1. 論文概要
2. 自動運転の仕組みと交通標識認識
3. 交通標識認識に対する攻撃と現状
4. 現状を踏まえた提案手法の概要
5. 評価
6. 今後の方針
7. まとめ




---

# 論文概要

<hr>

### 敵対的再帰反射パッチ: 暗闇で有効化する敵対的攻撃

再帰反射パッチを用いた交通標識認識に対する検知回避攻撃

新規性: 夜のみで有効・昼間におけるステルス性

ディジタル実験・物理実験で以下を検証

<div class="grid grid-cols-2 gap-4">
<div>

RQ1. 攻撃は実現可能であるか

RQ2. 角度や距離に対して頑健か
  
→攻撃実現性・頑健性が確認

→<span style="font-size:25px">反射板は交通標識認識に対して脆弱</span>

</div>
<div>

<img src = /public/attack.png width = 350>
</div>
</div>


---
clicks: 1
---

# 自動運転の仕組み

<style>
p {
  font-size: 1.5em;
  margin-bottom: 0.5em;
  margin-top: 0.5em!important;
  padding-left: 0.5em;
}

il {
  font-size: 1.5em;
  margin-bottom: 0.5em;
  margin-top: 0.5em!important;
  padding-left: 0.5em;
}

</style>

<hr>

<p v-if="$slidev.nav.clicks === 0" v-motion>

- Sensing…センサで周囲の把握
- Localization…位置推定(地図とセンサの情報)
- Perception…周囲の状況認識(Sensingに基づく状況把握) 
- Planning…運転ルート等を決定する 
- Control…決定に基づいた制御


</p>

<p v-if="$slidev.nav.clicks === 1" v-motion>

- Sensing…センサで周囲の把握
- Localization…位置推定(地図とセンサの情報)
- <span style = "color:red">Perception…周囲の状況認識(Sensingに基づく状況把握)←</span>
- Planning…運転ルート等を決定する 
- Control…決定に基づいた制御

</p>

<center>
<img src = /public/automov_arch.png width = 400>
 </center>


---
clicks: 2
---

# Perceptionについて

<hr>

Perception : 障害物等の周囲の状況認識→安全に最も直結する部分

<div class="grid grid-cols-2 gap-4">
<div>

例: カメラ画像からの物体検出

- 障害物検知
- 交通標識認識

<div v-click="1"><span style = "color:red">→現在運転支援などで利用</span>
</div>

<div v-click="2">今回は交通標識認識に注目</div>

</div>

<div>

<img src = /public/detect_result_2.png width = 400>

</div>
</div>

---

# 交通標識認識の仕組み

<hr>

交通標識認識モデル

→物体検知モデル(YOLO等)を交通標識データセットで学習

<div class="grid grid-cols-2 gap-4">
<div>
YOLO : 以下を同時に予測

- 物体の位置
- 物体のクラス
- 信頼度: 物体が存在する尤度

信頼度は閾値がある

→下回ると存在しないと扱う

</div>

<div>

<img src = /public/detect_result.png width = 350>

</div>
</div>

---

# 交通標識モデルに対する攻撃とその現状

<hr>

交通標識等の機械学習モデルに対する攻撃: Adversarial Example 攻撃

夜における攻撃やパッチによる攻撃の既存研究
- [Phantom Attack](https://www.nassiben.com/phantoms)　→プロジェクターで投影,検知誤りを狙う
- [SLAP](https://arxiv.org/abs/2007.04137)　→プロジェクターで投影,検知回避を狙う
- AEパッチの研究(例が多い)→昼の状況下にパッチ貼り検知回避等
<!-- ２つに分ける -->

<div class="grid grid-cols-3 gap-4">
  <div>
  <img src = https://static.wixstatic.com/media/a53494_617a29c7cca44db99ceea14ec6572431~mv2.png width =300>
  </div>
  <div>
<img src = https://ar5iv.labs.arxiv.org/html/2007.04137/assets/figures/intro2_a.png width = 300>
  </div>
  <div>
  <img src = https://149695847.v2.pressablecdn.com/wp-content/uploads/2020/08/Adversarial-attacks.png width = 300>
  </div>

</div>

---

# 交通標識モデルに対する攻撃とその現状

<hr>

### 現状の攻撃の問題点

夜での攻撃 : プロジェクターが必要→ステルス性▲・予算・技術的ハードル

Adversarial Example パッチ: 昼間での攻撃に特化，ステルス性▲

<div v-click =1>
本論文:

- 昼間/ヘッドライトなしの夜間の**ステルス性**(cf.夜，AEパッチ)
- 安価・簡単(cf.夜)


</div>

<div v-click=2>

→<span style="color:red">暗闇における脅威性の高い攻撃</span>

</div>

<!--
2消す
-->

---

# 本攻撃の概要

<hr>

ARPA攻撃　再帰反射パッチを使った夜に有効な敵対サンプル攻撃
- 再帰反射パッチがヘッドライト光を反射することで摂動を実現

→YOLO v3-tinyのSTOPサインクラスの検知回避

再帰反射パッチ : 光源の方向に反射する特殊素材

<div class="grid grid-cols-2 gap-4">

<div>

<center>

<img src = public/attack.png width = 250>

</center>

</div>

<div>

<img src = https://swingby-nino.com/wp-content/uploads/2020/08/20200810_c.png width = 400>

</div>

</div>

---

# 攻撃の手順

<hr>

**Step1.** パッチを貼る位置の決定

- 画像処理による最適化
  - 反射色は白と仮定
  - 信頼度を落とすような貼り付け位置を探索


**Step2.**  実際に貼り付け


**Step3.**  自動車のヘッドライト光が反射
→摂動となり検知回避

- 脅威モデル

  - 交差点における標識無視
  - 速度制限無視による脱輪等の事故

---

# 最適化の概要

<hr> 

最適化の目的 : YOLOの検知回避
<br>
→信頼度が一定以下で物体が存在しないと扱う
<br>
→信頼度が下がるようにパッチを貼り付ければOK

<div class="grid grid-cols-2 gap-4">
<div>
Step1. STOPサインの領域を特定

Step2. グリッドで区切る

Step3. <span style = "font-size : 26px">信頼度が下がるグリッドの探索</span>

- 白で塗り信頼度評価
- ビームサーチを用いる
- 最大貼り付け枚数は5枚
</div>

<div>

<img src = /public/gridimage.png width = 300>

</div>

</div>

---

# 評価内容

<hr>

ディジタル実験，物理実験でそれぞれ画像20枚に対する攻撃成功率を評価

<div class="grid grid-cols-2 gap-4">
<div>

**評価事項**

1. 攻撃の実現性
2. 角度や距離に対するロバスト性

**評価条件**

攻撃の実現性

:5m 正面での攻撃成功率

ロバスト性

<span style="font-size:18pt">3m,7m 正面，5m ±30度での攻撃成功率</span>
<!-- 5m正面で適用した貼り方のまま

- 距離 : 3,7m正面
- 角度 : 5m±30度 -->

</div>

<div>
<center>
<img src =/public/setting-from-above.png width = 230 >
</center>
</div>

</div>

---

# ディジタル実験

<hr>

評価手順
- 5m 正面での画像を1枚用意，貼り付け位置を最適化
  - 反射色を白と仮定して画像処理を利用

- 最適化した貼る位置を他の写真にも適用→攻撃成功率を測定

<!-- 評価方法
- 攻撃の実現性 : 5m 正面の20枚の画像で評価
- ロバスト性 : 距離や角度を変え各々20枚で評価 -->

<center>
<img src = /public/stopsign-simulation-result.png width = 250>
</center>

<center>
シミュレーション上でパッチを貼った場合の状況
</center>

---

# ディジタル実験の結果

<hr>

**評価結果**

攻撃の実現性 : 80％の攻撃成功率
<br>
ロバスト性 : 一部を除き80〜100％の攻撃成功率

#### →攻撃の成立・一定のロバスト性が確認

<center>
<img src = /public/result_simulation.png width = 700>
</center> 

---

# 物理実験

<hr>

**評価手順**

- 5m 正面での画像を1枚用意，貼り付け位置を最適化
- 実際に交通標識に貼る
- ヘッドライト光が当たる状況で撮影→撮影画像に対しYOLOで評価

<div class = "grid grid-cols-2 gap-4">
<div>

<img src = /public/STOPSIGN_Patch.jpeg width = 300>
</div>

<div>

<img src = /public/attack.png width = 250>
</div>
</div>

---

# 物理実験

<hr>


**実験設定**
<br>
ヘッドライト：実際のヘッドライトを利用
<br>
カメラ：ミラーレスカメラを利用
<br>
カメラとヘッドライトの位置:同じ位置

<center>
<img src = /public/setting.png width = 400>
</center>

---

# 物理実験の結果

<hr>

攻撃実現性 : 5m 正面で 90％の攻撃成功率
<br>
ロバスト性 : 35〜90％の攻撃成功率を達成

→ 1. 攻撃は実際の環境で成功する
     2. 攻撃には一定のロバスト性がある

<img src = /public/result_physical.png>

<!--
数字治す
-->

---

# 評価結果まとめ・考察

<hr>

攻撃実現性
- ディジタル・物理実験で80％以上の攻撃成功率

距離・角度に対するロバスト性

- 一部を除き35〜90％の攻撃成功率


→攻撃実現性・一定のロバスト性が確認できた

- 攻撃成功率のゆらぎ:学習データセット等が原因


---

# 今後の方針

<hr>

- 最適化方法の工夫
  - Optunaなどでの連続最適化の活用
  - 最適化時に環境変化についても考慮
- 攻撃ベクトルの増加
  - クラス誤り，誤検知

- 交通標識認識専用モデルに対する評価(今回は一般の物体検出モデル)
- 実車を用いた実験等で現実性の検証
- 既存の攻撃手法に対する評価，防御手法の提案


---

# まとめ

<hr>

- 再帰反射パッチを使った夜に有効な敵対サンプル攻撃
- YOLO v3-tinyのSTOPサインクラスの検知回避で評価
- ディジタル実験・物理実験で攻撃の成立・一定のロバスト性を確認

→交通標識は反射パッチ等を使った攻撃に対して脆弱

- 今後の方針
  - 最適化の工夫 
  - 攻撃ベクトルの増加
  - 防御手法の提案等

