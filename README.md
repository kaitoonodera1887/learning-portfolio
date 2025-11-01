# learning-portfolio

## 概要
このリポジトリは学習用ポートフォリオです。Python の学習記録や小さなプロジェクトを格納します。

---

## Day 1 (作業日: 2025/10/29)
### 今日やったこと
- Gitの基本的なコマンドの習得と演習

### コマンドメモ
- ローカルリポジトリの作成：git init
- ステージングエリアに変更を登録：git add [File name]
- コミットを作成：git commit -m "コメント"
- Git管理下のファイルの削除：git rm [File name]
- Git管理下のディレクトリの削除：git rm -r [pass ]
- ローカルリポジトリの状態の確認：git status
- 各エリアの差分を確認：git diff
- コミットの履歴の確認：git log
- ワークツリーの変更の取り消し：git checkout
- ステージングエリアに追加した変更をワークツリーへ戻す：git reset

### 基本的な流れ（現在の理解）
1. ディレクトリにREADME.mdを作成(VScode)
2. git initでローカルリポジトリを作成
3. git add README.mdでステージングエリアに追加
4. git commit -m "コメント" でコミット/
   git commitでも良いがVScodeでコメントを書く

#### メモ
状態の確認や削除等は、その都度行う。

### 学び
Gitの基本的な特徴やコマンドについて学んだ。今の所基本的な流れしか学んでいないが、自分で作成したプログラムも管理していきたい。


### 次回のTODO
- todo-cliのコミットを自分が何をしているかを考えながら行う。




## Day 2 (作業日: 2025/10/31)
## Practice_Sleep

### 今日やったこと
- time, eeg, emgのデータが入ったcsvファイルを読み込んだ。
- timeデータからサンプリング周波数を推定
- 得られたデータを4秒で1epochとして、データを配列に格納

### 作成したファイル

- step1.py : sampleデータの作成
- step2.py : tkinterを用いた読み込み + サンプリング周波数の推定
- step3.py : step2で作成したファイルを4秒で1epochとして分離する。

### 学んだメソッドなど
- Series.values : Return Series as ndarray or ndarray-like depending on the dtype. 
- numpy.issubdtype(arg1, arg2) : 最初の引数が型階層内でそれより低い/等しい型コードである場合、 True を返します。 
- Series.astype(type) : typeの変換 
- np.diff(List) : 隣同士の差分を計算



## Day3 (作業日:2025/11/1)
## Practice

### 今日やったこと
- 

### 作成したファイル
- step1.py : csvファイルの読み込み、EEG,EMG列の基本統計量の計算
- step2.py : 4秒を1エポックとして区切り、エポックごとに基本統計量の計算し、feature_dfとしてDataFrameでまとめる。
- step3.py : それぞれのエポックごとにWelch法で各周波数帯のPSDを求める。絶対powerと相対powerを計算し、基本統計量とともにfeature_dfにまとめ、features.csvとして出力。
- step4.py : labels.csvを読み込んで、feature_dfに各エポックの判定結果をラベルとして格納。
- step5.py : 作成したfeatures.csvとlabels.csvを読み込んで、ラベルをLabelEncoderで数値に変換する。その後学習用データとテストデータに分離。
- step6.py : ランダンフォレストで学習と評価を行った。特徴量の重要度を視覚的にも確認。モデルの保存。
- step7.py : ハイパーパラメータ調整。n_estimatorsとmax_depthを変更して、その理論的な分析を行った。

### 学び
以下が評価の結果<br>
分類レポート: 

| | precision | recall | f1-score | support |
|-| --------- | ------ | -------- | ------- |
NR | 0.96 | 0.88|0.92 |26 |
WAKE |0.98| 0.99| 0.99| 145|
accuracy | | |0.98 | 171 
macro avg |0.97| 0.94| 0.95| 171 
weighted avg |0.98| 0.98 |0.98 |171 

NRのRecallがWAKEに比べて低いので、見逃しが多いことがわかる。

混同行列: 

$\begin{bmatrix}23~3\\1~144\end{bmatrix}$

Macro F1 score: 0.9531506849315068 

上位特徴量: <br>
idx | Feature | Importance<br>
--  | --      | --
5 | f5 | 0.265066 
6 | f6 | 0.204886 
2 | f2 | 0.130588 
7 | f7 | 0.073842 
3 | f3 | 0.071742 
0 | f0 | 0.066803 
9 | f9 | 0.055246 
14| f14| 0.046561 
11| f11|0.027361 
13| f13|0.016311

上位の特徴量は、emg_rms、delta_abs、eeg_rmsであった。このことから、emg_rmsは覚醒時のきん筋活動量を強く反映し、delta_absは睡眠の深度、eeg_rmsはEEGのパワーを反映している。

ハイパーパラメータ調整で、n_estimatorとmax_depthを変更した。
Best Parametors {'max_depth': 5, 'n_estimators': 100} <br>
Best Macro F1 0.97<br>
この時は、max_depthが浅かったことからノイズを無視した抽象的なデータが分類に効果的だったことが分かった。

また、他のパラメータも試すと<br>
Best Parametors {'max_depth': 3, 'n_estimators': 300}<br>
Best Macro F1 0.98<br>
この時は、より浅くなったが、その分規模が大きくなり、規模の大きさで深さを補っていることが読み取れた。



### 学んだメソッドなど
- signal.welch(epoch, fs=fs, nperseg=nperseg) : Estimate power spectral density using Welch’s method.
- np.trapz(y, x) : Integrate along the given axis using the composite trapezoidal rule.
- numpy.logical_and(x1, x2) : Compute the truth value of x1 AND x2 element-wise.
- np.any(idx) 
- pd.merge(df1, df2, on='epoch_num')：epoch_numで特徴量とラベルを結合。
- LabelEncoder().fit_transform(y)：文字列ラベル → 連続整数（機械学習で使いやすく）。
- train_test_split(X, y, test_size=0.2, stratify=y, random_state=42)：データを分割、stratifyでクラス比を保つ。
- RandomForestClassifier(...).fit(X_train, y_train)：モデル学習。class_weight='balanced' はクラス不均衡対策。
- classification_report(y_test, y_pred)：precision/recall/F1が出る。
- confusion_matrix(y_test, y_pred)：どのクラスをどれだけ間違えたかを見る。
- clf.feature_importances_：モデル内蔵の特徴量重要度（解釈に注意）
- pd.to_numeric(..., errors='coerce') : 数値に変換,変換できない文字は NaN になる
- 文字列.strip() : 文字列型のメソッドで特定の文字列や空白文字を削除
- df.notna() : NaN -> False, not NaN -> True
- pd.merge(df1, df2, on= , how= ) : df1にdf2をonをkeyとしてhowの方法で結合

