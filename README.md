# Medical Data Practice - 
## Day1

## 概要
Python (pandas, matplotlib) を使って医療データを読み込み、基本的な集計と可視化を行った。

## 内容
- sample.csv（自作データ）
- pandas での読み込み、describe(), groupby() を実行
- ヒストグラムによる value の分布可視化

## 今後の予定
- 医療データを題材にした前処理・分析の練習を継続
- 3日間連続でアウトプットを更新

## Day2：データ読込・加工・可視化の基礎

### 目的
Python（pandas）を用いて、医療系データを“表として扱える状態”にする基礎を習得。

### 実施内容
- `sample_day2.csv` の作成（100件：patient_id, age, value, date, flag）
- pandas を用いたデータ読込  
  - `pd.read_csv()`
- 基本的な操作  
  - `df.head()`, `df.info()`, `df.dtypes`
- 集計処理  
  - `groupby("date")["value"].mean()`
  - `value_counts()`
  - `sort_values()`
- 可視化（2種）
  - 検査値（value）ヒストグラム
  - 日付ごと平均値の推移（line / bar）
- GitHub への commit/push  
  - Day2 のノートブック  
  - sample_day2.csv  
  - README への Day2 ログ追加

### 習得ポイント
- pandas の基本操作  
- 集計ロジック  
- データから“意味のある数字”を取り出す基礎  
- GitHub によるバージョン管理フロー

## Day3：前処理（型変換・欠損処理・特徴量作成）＋簡易分析

### 目的
AI/医療データ分析で必須となる「データ前処理」の基礎を固める。  
特に、文字列（object）のままでは分析できないため「使える型に変換する」ことが中心。

### 実施内容
#### 1. データ型の変換（object → 数値 / 日付）
- `pd.to_numeric()`  
- `pd.to_datetime()`  
- 検査値 `value`、年齢 `age`、フラグ `flag` を適切な型へ変換

#### 2. 欠損値処理
- `.isnull().sum()` による欠損確認  
- `fillna()` による value の補完（平均値）  
- `dropna()` による日付・flag の欠損除外

#### 3. 特徴量エンジニアリング
- 年齢層カテゴリー  
  - `age_group = young/middle/senior`
- 高リスクフラグ  
  - `high_risk = (value > 7.5).astype(int)`

#### 4. 集計（クロス集計・平均）
- `df.groupby("age_group")["value"].mean()`
- `df["high_risk"].value_counts()`
- `df.groupby(["flag", "high_risk"]).size()`

#### 5. 可視化（追加）
- valueのヒストグラム  
- age_group × value のバー or ボックスプロット

### 習得ポイント
- AI/分析の前段階である「前処理」の正しい流れ  
- 型変換 → 欠損処理 → 特徴量作成 → 集計 → 可視化  
- 医療データに必要な「解析可能な構造」への変換
- Notebook の構成方法（処理の順序整理）

## Day4：EDA（探索的データ分析）

### 目的
前処理済みデータを用いて、
「flag（異常/正常）をどの特徴量が説明するのか」を読み解く。
AI モデル構築の前段階として、分布・相関・傾向を明確に把握する。

---

### 実施内容

#### 1. 基本統計量の確認
- `df.describe()` で value / age の分布を把握  
- 平均値、標準偏差、四分位点を確認  
- value が高齢層で高くなる傾向を確認

#### 2. 相関の確認
- `df.corr()` で特徴量同士の相関を確認  
- value と flag の間に差がある可能性を確認  
- high_risk と flag の関係にも注目

#### 3. 箱ひげ図（value の外れ値可視化）
- `df.boxplot(column="value")`  
- 外れ値・分布の偏りを視覚的に確認

#### 4. age_group × value 解析
- `df.boxplot(column="value", by="age_group")`  
- young / middle / senior の分布比較  
- senior が最も中央値が高い  
- middle の IQR（バラつき）が最も広い  
- 年齢が上がるほど value が上昇する傾向を確認

#### 5. flag（診断ラベル）との関係性
- `df.groupby("flag")["value"].mean()`  
  - flag=0 → 約5.48  
  - flag=1 → 約7.56  
  → value は flag を強く説明できる特徴量

#### 6. high_risk × flag のクロス集計
- `df.groupby(["flag", "high_risk"]).size()`  
  - high_risk=1 の 52% が flag=1  
  → high_risk も flag を説明する有効な特徴量

---

### 得られた洞察（重要点）

- **value は flag（異常/正常）を強く説明する**  
  → 平均値に明確な差があるため、分類モデルの主軸になりうる。

- **high_risk は flag と強く関連する**  
  → 閾値による特徴量化が有効に機能している。

- **age_group によって value の分布が異なる**  
  → 年齢も flag に寄与する可能性があるため、特徴量として使う価値がある。

- **EDA の結果、flag（目的変数）を  
  value / high_risk / age で予測できる構造が明確に存在する。**

---

### Day4 で構築したもの
- `day4_eda.ipynb`（Notebook）
- value / age / flag / high_risk / age_group の分布理解
- 機械学習（Day5）に必要な特徴量・データ構造を確定
- GitHub で EDA ログとして公開可能なレポート形式

## Day5：初回モデル構築（RandomForest による二値分類）

### 目的
Day1〜4 で前処理・EDA まで行ったデータを用いて、
`flag`（0=正常, 1=異常）を予測する機械学習モデルを一通り構築し、
「前処理 → 特徴量設計 → 学習 → 評価」の流れを自分で回せる状態にする。

### 実施内容

1. 前処理の再現
   - `sample_day2.csv` の読み込み
   - 型変換：`age`, `value`, `flag`, `patient_id` を数値型へ変換
   - `date` を `datetime` 型へ変換
   - 欠損行を削除
   - 特徴量作成：
     - `age_group`（young / middle / senior）
     - `high_risk`（`value > 7.5` を 1 とするフラグ）

2. 特徴量・目的変数の定義
   - **目的変数（y）**：`flag`（診断ラベル：0=正常 / 1=異常）
   - **特徴量（X）**：
     - `age`
     - `value`
     - `high_risk`
     - `age_group`（`get_dummies(drop_first=True)` でダミー変数化）

3. 学習・評価フローの実装
   - `train_test_split(test_size=0.2, stratify=y, random_state=42)` による train/test 分割
   - `RandomForestClassifier(n_estimators=100, random_state=42)` で学習
   - `accuracy_score` と `roc_auc_score` による性能評価

4. 評価結果（今回のデータ）
   - **Accuracy: 0.950**
   - **AUC: 1.000**
   - → この簡易データセットでは、`value` / `age` / `high_risk` / `age_group` から
     `flag` をほぼ完全に判別できる構造があることを確認。

### 学んだポイント
- 目的変数（`flag`）と特徴量（`age`, `value`, `high_risk`, `age_group`）を明確に分けて設計する重要性
- 前処理・特徴量設計を Notebook 内で完結させてからモデルに渡す流れ
- accuracy と AUC の両方でモデルを評価する基本パターン
- 小規模・単純なデータでは高いスコアが出やすく、過信せず「構造が単純だから」と理解しておくこと

## Day6：モデル解釈と評価・閾値調整

### 目的
Day5 で構築した RandomForest モデルに対して、
「どの特徴量がどれだけ効いているか」「どこで誤判定しているか」を分析し、
医療データにおけるモデル評価・改善の基本パターンを身につける。

---

### 実施内容

#### 1. 特徴量重要度（Feature Importance）の可視化
- `model.feature_importances_` と `X.columns` から DataFrame を作成
- 横棒グラフ（barh）で可視化
- value / high_risk / age / age_group_* のどれが支配的かを確認
- 結果として、value 周辺の特徴量が支配的であることを確認

#### 2. 混同行列（Confusion Matrix）の取得
- `confusion_matrix(y_test, y_pred)` で 2×2 行列を確認
- 例：`[[10, 0], [1, 9]]`
  - TN=10, FP=0, FN=1, TP=9
- 異常患者の見逃し（FN）が 1 件存在することを認識

#### 3. Precision / Recall / F1 の計算
- `precision_score`, `recall_score`, `f1_score` を計算
  - Precision: 1.0
  - Recall: 0.9
  - F1: 約0.947
- 医療文脈では特に **Recall（異常患者の見逃しをどれだけ減らせるか）** が重要であることを再確認

#### 4. ROC 曲線と AUC
- `roc_curve(y_test, y_prob)` で FPR, TPR, 閾値を取得
- ROC 曲線をプロットし、AUC が 1.0 に近いことを確認
- 異常と正常のスコア分布がほぼ完全に分離している“おもちゃデータ”であると理解

#### 5. 閾値調整（Threshold Tuning）
- デフォルトの閾値 0.5 の代わりに、
  `threshold = 0.3` として再分類：
  ```python
  y_pred_adj = (y_prob > 0.3).astype(int)
### 次のステップ（Day5）
- X（特徴量）と y（目的変数）の準備  
- train/test 分割  
- ランダムフォレスト or ロジスティック回帰による分類モデル作成  
- 精度（accuracy, ROC-AUC）の評価  
- GitHub への成果の追加
