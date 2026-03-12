# Zigzag 計算流程

## 流程圖

```mermaid
flowchart TD
    A[輸入 DataFrame + period] --> B[初始化 direction=0<br/>changed=False]
    
    B --> C{遍歷價格數據<br/>idx from 1 to len df }
    
    C --> D[計算最高價 MAX<br/>ta.MAX df.high :idx]
    C --> E[計算最低價 MIN<br/>ta.MIN df.low :idx ]
    
    D --> F[new_high = df.high idx >= highest_high]
    E --> G[new_low = df.low idx  <= lowest_low]
    
    F --> H{new_high AND<br/>NOT new_low}
    G --> I{NOT new_high AND<br/>new_low}
    
    H --> J{direction != 1?}
    I --> K{direction != -1?}
    
    J -->|True| L[direction = 1<br/>changed = True]
    J -->|False| M[changed = False]
    
    K --> N[direction = -1<br/>changed = True]
    K --> O[changed = False]
    
    L --> P{changed OR<br/>len==0?}
    N --> P
    M --> Q
    O --> Q
    
    P -->|direction=1| R[記錄高點  'H', price, idx]
    P -->|direction=-1| S[記錄低點 'L', price, idx]
    
    Q --> T{new_high OR<br/>new_low?}
    T -->|方向相同| U{價格創新高/低?}
    
    U -->|H且新>舊| R
    U -->|L且新<舊| S
    U -->|否| V[跳過]
    
    R --> W[返回 zigzag_pattern]
    S --> W
    V --> W
    W --> C
    
    C -->|遍歷結束| X[返回 zigzag_pattern<br/> 'H', price, idx, 'L', price, idx, ...]
```

## 說明

- **Zigzag** 識別價格走勢中的轉折點（波峰/波谷）
- 使用 `ta.MAX` 和 `ta.MIN` 計算指定週期內的最高/最低價
- 輸出格式：`[['H', price, idx], ['L', price, idx], ...]`
  - `H` = High (高點)
  - `L` = Low (低點)
  - `price` = 價格
  - `idx` = 資料索引

## 參數

| 參數 | 說明 |
|------|------|
| `df` | K 線數據 DataFrame (需包含 high, low 欄位) |
| `period` | 計算週期，用於判斷高低點 |
