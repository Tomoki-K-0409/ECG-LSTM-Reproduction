# ECG-LSTM-Reproduction
本リポジトリは、論文   **"LSTM-Based ECG Classification for Continuous Monitoring on Personal Wearable Devices" (arXiv:1812.04818)**    の手法を参考に、MIT-BIH Arrhythmia Database を用いて 心電図（ECG）の正常／異常分類を再現したものです。 

- Wavelet変換による特徴抽出
- LSTMモデルによる分類
- 混同行列・精度評価を実施
- 再現実験の結果と今後の改善点を整理

## ⚙️ 環境
- Python 3.9+
- PyTorch
- wfdb（PhysioNetデータ取得用）
- PyWavelets
- scikit-learn

セットアップ:
```bash
pip install wfdb pywavelets torch torchvision scikit-learn

**データセット**

MIT-BIH Arrhythmia Database (PhysioNet)

使用チャネル: ECG (MLII)

ラベル:

Normal (N) → ラベル 0

Abnormal (V, A, L, R, …) → ラベル 1

🔍 手法

ECG波形を256サンプル単位に分割

Wavelet変換（db4, level=4）で特徴抽出

LSTMモデル（1層, hidden size=64）に入力

CrossEntropyLoss + Adam optimizer で学習

Accuracy, Precision, Recall, F1-score, Confusion Matrixで評価

📈 実験結果（Record 100のみ）
Confusion Matrix

Classification Report
              precision    recall  f1-score   support

      Normal       0.98      1.00      0.99       447
    Abnormal       0.00      0.00      0.00         8

    accuracy                           0.98       455
   macro avg       0.49      0.50      0.50       455
weighted avg       0.97      0.98      0.97       455

考察

表面的な精度 (98%) は高いが、実際には Abnormal クラスを全く検出できていない

強いクラス不均衡（Normal >> Abnormal）が原因で、モデルが常に Normal を出力してしまう状態

論文のように「高感度で異常を検出するモデル」とは差がある

 改善ポイント

クラス不均衡への対策

重み付きCrossEntropyLossの導入

Abnormalクラスのオーバーサンプリング

モデル改良

CNN + LSTMのハイブリッド化

複数LSTMの結合による表現力向上

評価指標の追加

Sensitivity, Specificityを報告

患者単位のクロスバリデーション実施
