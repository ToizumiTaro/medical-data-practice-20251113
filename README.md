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
