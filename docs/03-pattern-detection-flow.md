# 形態偵測流程

## 流程圖

```mermaid
flowchart TD
    A[輸入 zigzag_pattern] --> B[遍歷 i from 0 to len-5]
    
    B --> C[取出 5 點組合<br/>current_pat = pattern i:i+5]
    C --> D[取得檢測函數列表]
    
    D --> E{遍歷各形態檢測函數}
    
    E --> F[detect_gartley]
    E --> G[detect_bat]
    E --> H[detect_altbat]
    E --> I[detect_butterfly]
    E --> J[detect_crab]
    E --> K[detect_deepcrab]
    E --> L[detect_shark]
    E --> M[detect_5o]
    E --> N[detect_cypher]
    E --> O[detect_abcd]
    
    F --> P{調用檢測函數<br/>func current_pat, predict=False}
    G --> P
    H --> P
    I --> P
    J --> P
    K --> P
    L --> P
    M --> P
    N --> P
    O --> P
    
    P --> Q{返回結果 != None?}
    Q -->|Yes| R[提取 direction + ret_dict]
    Q -->|No| E
    
    R --> S[判斷牛/熊市<br/>direction=1: bullish<br/>direction=-1: bearish]
    S --> T[生成標籤 label]
    T --> U[加入結果列表]
    U --> E
    
    B -->|遍歷結束| V[返回結果列表]
    
    V --> W[filter_duplicats 去重]
    W --> X[結束]
```

## 形態偵測函數

| 函數 | 形態名稱 | 關鍵斐波那契比率 |
|------|----------|------------------|
| `detect_gartley` | Gartley | XAB=0.618, XAD=0.786 |
| `detect_bat` | Bat | XAB=0.618, XAD=0.886 |
| `detect_altbat` | Alt Bat | XAB=0.618, XAD=1.13 |
| `detect_butterfly` | Butterfly | XAB=0.618, XAD=1.27 |
| `detect_crab` | Crab | XAB=0.618, XAD=1.618 |
| `detect_deepcrab` | Deep Crab | XAB=0.886, XAD=1.618 |
| `detect_shark` | Shark | XAB=0.618, XAD=1.13 |
| `detect_5o` | 5-O | XAB=1.618, XAD=1.618 |
| `detect_cypher` | Cypher | XAB=0.618, XAD=0.786 |
| `detect_abcd` | AB=CD | AB=CD (1:1) |

## 單一形態檢測流程

```mermaid
flowchart LR
    A[輸入 5 點組合<br/>X,A,B,C,D] --> B[計算各段比率]
    
    B --> C[XAB = B-A / X-A]
    B --> D[ABC = B-C / A-B]
    B --> E[BCD = C-D / B-C]
    B --> F[XAD = A-D / X-A]
    
    C --> G{使用 is_in 驗證<br/>斐波那契比率範圍}
    D --> G
    E --> G
    F --> G
    
    G --> H{所有比率<br/>都符合?}
    H -->|Yes| I[返回 direction + ret_dict]
    H -->|No| J[返回 None]
```

## 檢測模式

- **predict=False**: 偵測已完成的形態
- **predict=True + predict_mode='reverse'**: 反向預測（從 X,A,B,C 預測 D）
- **predict=True + predict_mode='direct'**: 直接預測（驗證當前 D 點是否有效）

## 輸出格式

```python
[
    [current_pat, current_idx, label, ret_dict],
    ...
]
```

- `current_pat`: 5 點價格列表
- `current_idx`: 對應的資料索引
- `label`: 如 "bullish gartley", "bearish bat"
- `ret_dict`: 包含各段比率的字典
