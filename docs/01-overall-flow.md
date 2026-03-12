# 整體偵測流程

## 流程圖

```mermaid
flowchart TD
    A[輸入 K 線數據<br/>DataFrame] --> B[HarmonicDetector.get_patterns]
    
    B --> C[get_zigzag 計算 Zigzag 轉折點]
    C --> D[detect_pattern 偵測形態]
    
    D --> E{predict?}
    E -->|True| F[predict_pattern 預測形態]
    E -->|False| G[跳過預測]
    
    F --> H[返回 形態 + 預測結果]
    G --> I[返回 形態]
    
    H --> J{plot?}
    I --> J
    
    J -->|True| K[plot_patterns 繪圖]
    J -->|False| L[結束]
    
    K --> L
```

## API 說明

| 方法 | 說明 |
|------|------|
| `get_patterns(df, window, predict, plot)` | 主入口，輸入 K 線資料輸出形態 |
| `get_zigzag(df, period)` | 計算價格轉折點 |
| `detect_pattern(zigzag_pattern)` | 偵測已完成的形態 |
| `predict_pattern(zigzag_pattern)` | 預測未來形態 |
| `plot_patterns(...)` | 繪製形態圖形 |

## 使用範例

```python
from harmonic_patterns import HarmonicDetector

detector = HarmonicDetector(error_allowed=0.05)
patterns, predict = detector.get_patterns(df, window=5, predict=True, plot=False)
```
