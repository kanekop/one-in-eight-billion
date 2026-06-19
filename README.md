# One in Eight Billion

世帯年収と世帯人数、または純資産を入れると、世界の中で自分がどのくらい上位かを示すWebアプリ。
建前はジョークツール、本意は「金がないと言う先進国の我々が、実は世界でいかに上位にいるか」を体感させること。

- 名前（声に出す方）= **One in Eight Billion**
- マーク（見る方）= **`1∕8B`**

## 動かし方

`index.html` をブラウザで開くだけ（単一ファイル・ビルド不要）。

## 計算方法（v1）

「ファクトで可能な範囲は計算、無理な裾はフェルミ推定」という方針。

### Income

1. 入力した世帯年収を、為替ではなく **PPP（購買力平価）** で international$ に換算（＝そのお金で実際に買える量で比較）。
2. その金額を世帯人数で割り、**1人あたり年収**にそろえる。
3. それを**世界の1人あたり所得分布のモデル**に当てる。
   - 大部分は **対数正規分布**でフィット（未較正の仮アンカー: 世界中央値 ≈ $3,000/年、世界トップ1% ≈ $55,000/年・PPP）。
   - トップ1%より上は調査が富裕層を捉えられないため、**パレート分布（フェルミ推定）** に切り替え。ここが「ファクトが尽きる領域」。

### Wealth

1. 入力した純資産（資産 - 負債）を **名目USD** として扱う。PPPではない。
2. UBS Global Wealth Report 2025 の 2024年末の成人向け wealth band に当てる。
   - $10k未満: 41.3%
   - $10k-$100k: 40.7%
   - $100k-$1M: 約16.4%（$1M超 = 1.6% と整合するよう丸め）
   - $1M超: 1.6%
3. バンド内は補間、$1B超は UBS の top bands（$1B-$50B: 2,860人、$50B-$100B: 15人、$100B超: 16人）をアンカーにして、最上位だけフェルミ裾を使う。

注意: 資産モードの対象は「全人類81億人」ではなく、UBS が分析する成人サンプル。UBS 2025 report は56市場、世界の個人資産の92%以上をカバーすると説明している。

現時点の出典メモ:

- 人口の丸め: [UN DESA World Population Prospects](https://population.un.org/wpp/) を参照する前提で、v1では約81億人に丸めて表示。
- PPP係数: [World Bank WDI `PA.NUS.PPP`](https://data.worldbank.org/indicator/PA.NUS.PPP)（PPP conversion factor, GDP）で置き換える前提。現行値はUI確認用の概算。
- 分布アンカー: [World Bank Poverty and Inequality Platform](https://pip.worldbank.org/) の分位点で置き換える前提。現行の中央値・99パーセンタイルはモデル用の仮置きで、ファクトとして扱わない。
- 資産分布: [UBS Global Wealth Report 2025](https://www.ubs.com/global/en/wealthmanagement/insights/global-wealth-report.html) の 2024年末 global wealth pyramid を使用。資産は名目USDの純資産で、成人サンプル対象。

## v1 の割り切り（今後詰める）

- 世帯人数には対応済み。ただし税引き前のざっくり値で、税引き後・社会保障・世帯内分配は未対応。
- PPP係数は概算（`index.html` の `PPP` 定数）。
- 資産モードはUSD入力のみ。JPY等の名目為替換算は未対応。
- 分布モデルの定数（`MEDIAN` / `SIGMA` / `SPLICE_X` / `TAIL_ALPHA`）は `index.html` 冒頭に集約。実データで較正し直す前提。
- まず「動くものを見る」のが v1 の目的。見てから次の発想へ。

## TODO（v1を見た後に決める）

- [ ] 名目（為替）併記トグル
- [x] 世帯人数への対応
- [x] 資産ランクタブの追加
- [ ] 資産入力のJPY等対応（名目為替としてIncomeのPPPとは分ける）
- [ ] 税引き後への対応
- [ ] 分布データの実値較正（World Bank PIP 等）
- [ ] 共有用の見せ方（OG画像・スクショ・一言コピー）
