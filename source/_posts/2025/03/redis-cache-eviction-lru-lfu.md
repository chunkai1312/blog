---
title: Redis 記憶體不夠用怎麼辦？瞭解 LRU、LFU 與快取淘汰策略
date: 2025-03-18 16:19:35
tags: [redis, cache-eviction, lru, lfu, backend-engineering]
categories: 技術分享
index_img: /2025/03/18/redis-cache-eviction-lru-lfu/cover.png
---

> **「記憶體有限，該留下誰、捨棄誰？」**  
> 快取系統的本質，就是一場資料的生存競爭。

在系統開發中，我們經常使用 Redis 作為快取層，以加速資料存取、減少資料庫壓力。但你是否曾思考過：**當 Redis 記憶體爆滿時，該怎麼辦？新的資料進不來，又該移除哪些？**

這時就必須仰賴 Redis 的淘汰策略（Eviction Policy）。本文將從 Redis 快取運作的核心出發，深入介紹兩大常見策略 **LRU（Least Recently Used）** 和 **LFU（Least Frequently Used）**，並彙整 Redis 所支援的所有淘汰策略，幫助你依據系統需求做出最佳選擇。

![](/2025/03/18/redis-cache-eviction-lru-lfu/cover.png)

<!-- more -->

## 為什麼 Redis 需要淘汰策略？

Redis 將所有資料存放在記憶體中，速度雖然極快，但記憶體空間卻是有限的。當資料量不斷累積，超過透過 `maxmemory` 所設定的記憶體上限後，新的資料就無法再寫入。

這時就需要 Redis 啟動淘汰策略，自動釋放記憶體空間。透過設定 `maxmemory-policy`，Redis 能根據不同的邏輯判斷哪些資料應該保留、哪些資料可以移除。

## LRU：最近最少使用，優先淘汰

**LRU（Least Recently Used）** 是最常見的快取淘汰策略之一，其核心邏輯簡單直觀：如果某筆資料很久沒有被使用，代表它的價值可能較低，當空間不足時可以優先移除。

### 適用情境

- 資料具有**時間性熱度**，例如使用者最近查詢、即時熱搜、熱門新聞等。
- 快取內容短期內可能重複存取。

### Redis 的實作方式

Redis 採用**近似 LRU（Approximate LRU）** 演算法，以提升效能：

- 每個 key 都記錄最近一次被存取的時間。
- 記憶體達上限時，Redis 隨機抽樣幾個 key（預設 5 個），從中選出最久未被使用的淘汰。

這種方法不需要維護完整的 LRU 鏈結結構，效能與命中率之間達到不錯的平衡。

## LFU：使用次數最少，優先淘汰

**LFU（Least Frequently Used）** 著重於資料的「存取次數」而非「最近存取時間」。即使某些資料近期未被使用，只要曾經經常被查詢，就會被保留下來。

### 適用情境

- 資料具有**長期熱門性**，例如 API 回應快取、熱門商品、用戶設定等。
- 希望快取能根據資料的整體價值決定是否保留。

### Redis 的實作方式

Redis 自 4.0 起支援 LFU，並設計了一套結合「計數」與「衰減」的高效機制：

- 每個 key 維護一個頻率計數器 `freq`。
- 每次存取時，不是直接 +1，而是透過機率增量方式調整計數，避免極端資料壟斷空間。
- Redis 定期讓計數自然衰減，讓過時的熱門資料逐漸退出舞台。

這讓 LFU 策略既能保留高頻資料，又能避免快取老化。

## Redis 支援的所有淘汰策略總覽

除了 LRU 與 LFU，Redis 還提供了多種彈性策略，針對不同場景進行優化選擇：

| 策略名稱           | 說明 |
|--------------------|------|
| `noeviction`       | 預設策略。不進行淘汰，記憶體滿時回傳錯誤。 |
| `allkeys-lru`      | 所有 key 均使用 LRU 策略進行淘汰。 |
| `volatile-lru`     | 僅針對設有 TTL 的 key 使用 LRU。 |
| `allkeys-lfu`      | 所有 key 均使用 LFU 策略。 |
| `volatile-lfu`     | 僅淘汰設有 TTL 的 key，依據使用頻率。 |
| `allkeys-random`   | 隨機淘汰任意 key，不考慮使用狀況。 |
| `volatile-random`  | 隨機淘汰設有 TTL 的 key。 |
| `volatile-ttl`     | 淘汰 TTL 剩餘時間最短的 key。 |

### 設定範例：

```bash
# 設定最大記憶體為 512MB
CONFIG SET maxmemory 512mb

# 設定淘汰策略為 LFU
CONFIG SET maxmemory-policy allkeys-lfu
```

## LRU、LFU 或其他策略該怎麼選？

| 策略               | 淘汰依據               | 適合場景                                 |
|--------------------|------------------------|------------------------------------------|
| `allkeys-lru`      | 最近最少被使用         | 熱門內容快速輪替、即時查詢               |
| `allkeys-lfu`      | 使用次數最少           | 穩定熱門內容、避免快取污染               |
| `volatile-lru`     | 設有 TTL 的 key + LRU  | 混合快取與常駐資料                       |
| `volatile-ttl`     | TTL 最短的 key         | 控制資料生命週期、可預期過期行為         |
| `allkeys-random`   | 隨機                   | 資料價值不明確，或進行測試用途           |
| `noeviction`       | 不淘汰                 | Redis 作為資料庫使用，需嚴格監控記憶體   |

## 快取效果觀察方式：使用 `INFO stats`

Redis 提供即時統計資訊，方便開發者了解快取命中與淘汰情況：

```bash
INFO stats
```

範例輸出：

```
keyspace_hits:45678
keyspace_misses:1234
evicted_keys:89
```

說明：

- `keyspace_hits`：快取命中的次數  
- `keyspace_misses`：未命中的次數  
- `evicted_keys`：因記憶體限制被淘汰的 key 數量  

也可以搭配 `MEMORY STATS` 或 `MONITOR` 進一步觀察系統表現與空間使用效率。

## 快取策略選用建議

1. **資料為短期熱點** → 建議使用 `allkeys-lru`
2. **資料具有長期使用價值** → 適合選擇 `allkeys-lfu`
3. **快取與常駐資料並存，部分有 TTL** → 建議採用 `volatile-lru` 或 `volatile-lfu`
4. **需要精確控制資料壽命** → 可考慮使用 `volatile-ttl`
5. **Redis 作為主要資料庫（非快取）** → 使用 `noeviction`，並務必搭配記憶體監控

## 結語：選對策略，才能發揮快取真正價值

Redis 快取策略的重點不在於「放多少資料進來」，而是「放誰進來、讓誰出去」。  

LRU 反映短期熱度、LFU 保留高價值資料，其它策略則提供更細膩的選擇彈性。唯有深刻理解每一種淘汰策略的運作邏輯，才能打造出真正高效、穩定的快取系統。

記憶體有限，策略得當，快取才能發揮最大效益。
