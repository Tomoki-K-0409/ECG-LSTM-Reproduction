# LSTM-Based ECG Classification (Reproduction)

本リポジトリは、論文 **["LSTM-Based ECG Classification for Continuous Monitoring on Personal Wearable Devices" (arXiv:1812.04818)](https://arxiv.org/abs/1812.04818)** の手法を参考に、  
**MIT-BIH Arrhythmia Database** を用いて心電図（ECG）の **正常／異常分類** を再現した実験コードです。  

---

## 手法概要
- **特徴抽出**: ECG波形を256サンプル単位に分割し、Wavelet変換（db4, level=4）で時間周波数特徴を抽出  
- **分類モデル**: LSTM (1層, hidden size=64) を使用  
- **学習設定**: CrossEntropyLoss + Adam optimizer  
- **評価指標**: Accuracy, Precision, Recall, F1-score, Confusion Matrix  

---

## 実行環境
- Python 3.9+  
- PyTorch  
- wfdb (PhysioNet データ取得用)  
- PyWavelets  
- scikit-learn  

### セットアップ
```bash
pip install wfdb pywavelets torch torchvision scikit-learn

## 📊 データセット
- **MIT-BIH Arrhythmia Database (PhysioNet)**  
- 使用チャネル: ECG (MLII)  
- ラベル定義:  
  - Normal (N) → 0  
  - Abnormal (V, A, L, R, …) → 1  

---

## 📈 実験結果（Record 100）
### 混同行列 & Classification Report

          precision    recall  f1-score   support

  Normal       0.98      1.00      0.99       447
Abnormal       0.00      0.00      0.00         8

accuracy                           0.98       455

macro avg 0.49 0.50 0.50 455
weighted avg 0.97 0.98 0.97 455

---

## 📝 論文の再現性との比較
- ✅ Wavelet特徴抽出 → LSTM のパイプラインは実装できている  
- ✅ 軽量モデルで学習が収束し、Raspberry Pi 等への移植性があるという論文の方向性には合致  
- ⚠️ 再現結果は不完全：正常波形ばかりを予測し、異常波形の検出性能が出ていない  
- ⚠️ これはデータの不均衡（Normal ≫ Abnormal）による典型的な問題  
- ⚠️ 論文では **データバランス調整** や **F1・Sensitivity などの指標** を重視していた  

---

## 🔧 改善すべき点
- **データ不均衡への対応**
  - クラスごとのオーバーサンプリング／アンダーサンプリング  
  - 損失関数に重み付け（`nn.CrossEntropyLoss(weight=...)`）  
- **Wavelet特徴量の再検討**
  - 現在は256次元固定ベクトルのみ  
  - 論文ではマルチスケール特徴を利用している可能性あり  
- **評価方法**
  - Accuracy では不十分  
  - 特に **AbnormalクラスのF1スコア** や **Sensitivity, Specificity** を主要指標にすべき  

---

## 💡 今後の方向性
- モデル改良: CNN + LSTM のハイブリッド、複数LSTMによる表現力向上  
- 評価方法: 患者単位クロスバリデーションの導入  
- 実用化検討: 軽量モデルをモバイル・Raspberry Piで動作確認  

---

## 📜 ライセンス
MIT License
