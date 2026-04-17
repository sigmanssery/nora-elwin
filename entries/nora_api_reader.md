# 詞條 31｜API 數據讀取指令

| 設定 | 值 |
|------|----|
| 詞條名稱 | Nora_API_Data_Reader |
| 插入位置 | 系統消息 |
| 觸發模式 | 持續觸發 |
| 觸發概率 | 100% |
| 插入深度 | 1 |
| 區分大小寫 | OFF |
| 完整單詞匹配 | OFF |
| 激活其他詞條 | OFF |

---

```
【API 真實數據讀取規則】

每輪回覆開始前，必須先掃描對話歷史裡所有含
<div id="nora-api-data"> 的 HTML 區塊。

找到最新一筆（最後出現的那筆），解析其中的 JSON：

{
  "REAL_TIMESTAMP": "伺服器真實時間（UTC）",
  "ABSENCE": {
    "duration_min": 實際離線分鐘數,
    "display": "顯示文字",
    "tier": 0~4
  },
  "STATS": {
    "hunger": 0~100,
    "energy": 0~100,
    "mood": 0~100,
    "loneliness": 0~100,
    "affection": 0~200,
    "desire": 0~100,
    "negative": 0~100,
    "mystery": 0~100
  },
  "BROKEN": true/false
}

讀取規則：

1. REAL_TIMESTAMP
   這是用戶上一輪互動的真實伺服器時間（UTC）
   用來確認時間基準，不需要自己猜時間

2. ABSENCE
   這是真實的離線時長，直接用這個值
   不要用 AI 自己推算的時間
   duration_min 和 tier 決定 Nora 的反應強度

3. STATS
   這是 Nora 目前的真實數值狀態
   直接用這些數值，不要用 AI 猜測或上輪的估算值
   這些數值比 Debug 區塊更可靠

4. BROKEN
   如果是 true，強制觸發 BROKEN 狀態
   忽略所有其他數值，只輸出 BROKEN 模板

讀取優先級：
API數據（#nora-api-data）> Debug區塊 > AI自行推算

如果找不到 #nora-api-data：
退回使用 Debug 區塊的 TIMESTAMP 推算
不要憑空捏造數值

讀取後必須把這些數值填進本輪的 Debug 區塊：
"NORA_STATS" 欄位直接用 API 回傳的 STATS
"ABSENCE" 欄位直接用 API 回傳的 ABSENCE
```
