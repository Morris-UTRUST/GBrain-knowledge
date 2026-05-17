---
name: gbrain-tcm-enrich
description: |
  使用 gbrain 官方 enrich 流程，從中醫經典文本（如倪海厦《金匱要略》視頻同步文稿）建立知識圖譜。
  適用於：處理中醫文獻、建立倪師臨床經驗知識庫、對中藥/方劑/病理進行實體蒸餾。
  觸發時機：用户要求「處理中醫文本」、「建立倪師知識圖譜」、「 enrich 這份中醫文獻」。
---

# Gbrain TCM Enrich — 中醫文本知識蒸餾工作流

## 核心理念

**不是處理文本，是從文本中發現地圖。**

文本只是證據來源，不是目標產物。真正的目標是：每提到一個中醫實體（單味藥、方劑、病理），就建立一個有 citations、有 back-link、有 timeline 的 brain page。

## 工作流程（4步）

### Step 1：建立核心 anchor

第一個動作：確認 `people/倪海厦` 存在。

```bash
cd ~/gbrain && ~/.bun/bin/bun run src/cli.ts list 2>&1 | grep -v ai.gateway | grep "people/倪海厦"
```

若不存在，用 `put people/倪海厦` 建立。這個 page 是所有其他實體的 back-link 終點。

### Step 2：分析文本結構（不是 enrich，先了解範圍）

在開始 enrich 前，先對文本做結構成像：

1. **確認文本類型**：是「視頻同步文稿」還是「原始文獻」？
2. **確認目錄結構**（適用於《金匱要略》這類有篇目分類的文本）：
   ```bash
   grep -n "篇\|第.*章\|证治\|病脉" /tmp/gbrain-backup/倪海厦/*.md | head -30
   ```
3. **確認實體密度**：哪些章節提到單味藥？哪些提到方劑？哪些提到病理？

這個步驟用 `grep` 即可，不需要建立任何 brain page。

### Step 3：系統性 Enrich（官方 7 步法自訂版）

每次 enrich 一個實體，順序如下：

**優先順序（Tier 1 先）：**
1. `people/倪海厦`（核心，必須存在）
2. 單味藥（herbs/）：臨床最常用的 30 味
3. 方劑（formulas/）：金匱要略中出現的經方
4. 中醫病理（conditions/）：瘀血、寒疝、狐惑、痙病等

**Enrich 流程（每次一個實體）：**

```
1. Identify：從文本中找出該實體的臨床判斷句
2. Check brain：gbrain list 確認是否已存在
3. Extract signal：
   - 臨床判斷（倪師怎麼說）
   - 藥物组合（對什麼症用什麼藥）
   - 劑量與煎煮法
4. Write page：使用 gbrain put 寫入
5. Create bidirectional link：
   - 新頁 → people/倪海厦（back-link）
   - people/倪海厦 → 新頁（update the person page）
6. Add timeline entry：標注「倪師在金匱要略第X篇談到此實體」
```

**Citation 格式（必須）：**
```
[Source: 金匱要略-0X.md:行號]
```
行號從 `grep -n` 的輸出中取得。

### Step 4：Maintain Health（每完成 10 個實體做一次）

```bash
cd ~/gbrain && ~/.bun/bin/bun run src/cli.ts extract links --source db
cd ~/gbrain && ~/.bun/bin/bun run src/cli.ts health
```

確認：
- orphan pages ≤ 5
- dead links = 0
- link coverage ≥ 95%

---

## 實體命名規則

| 類型 | 目錄 | 範例 |
|------|------|------|
| 中醫人物 | people/ | people/倪海厦 |
| 單味藥 | herbs/ | herbs/附子、herbs/桂枝 |
| 方劑 | formulas/ | formulas/四逆湯、formulas/桂枝湯 |
| 中醫病理 | conditions/ | conditions/瘀血、conditions/寒疝 |
| 概念/入口頁 | concepts/ | concepts/倪師臨床地圖 |

**禁止：**
- 不要用西醫病名作為病理 page 名（如「肝癌」、「肺癌」）
- 同一實體不可同時存在於兩個目錄（如 `pathology/` 和 `conditions/` 不能都有「寒疝」）

---

## 紀錄規則（Filing + Quality Convention）

1. **Primary subject 決定目錄**：一篇關於附子的臨床筆記 → `herbs/附子`，不是 `sources/` 或 `notes/`
2. **Inline citation 必須有**：每個臨床判斷事實後面加 `[Source: 金匱要略-0X.md:行號]`
3. **Back-link 雙向**：每次建立實體 page → link 回 `people/倪海厦`；同步 update `people/倪海厦` 的 linked entities 列表
4. **Timeline 只能追加**：新資訊加在 timeline 底部，不修改既有的 compiled truth

---

## 參考文獻（使用前先讀）

- [gbrain-filing-rules.md](references/gbrain-filing-rules.md) — 目錄分類規則，必須遵守
- [gbrain-quality.md](references/gbrain-quality.md) — Citation 格式與 back-link Iron Law
- [gbrain-brain-first.md](references/gbrain-brain-first.md) — 查詢順序：search → query → get → 外部 API

---

## 工作終止條件

滿足以下任一條件即停止，不需要建立所有可能的實體：

1. **覆蓋充分**：主要章節（痙病、狐惑、瘀血、寒疝、陰陽毒、百合）都已建立實體
2. **新發現減少**：連續 3 個實體都只來自同一篇，無新篇目
3. **Brain 健康**：orphan ≤ 5，dead links = 0，agent 即可宣告完成

---

## 常見錯誤（不要这样做）

| 錯誤做法 | 正確做法 |
|---------|---------|
| 直接對文本 grep 建立 page | 先 `gbrain list` 確認不存在再建立 |
| 所有 herbs 一次性建立 | 每次一個，完成並 link 後再下一個 |
| 混用 conditions/ 和 pathology/ | 統一用 conditions/ |
| 不加 citation | 每個臨床判斷必須有 `[Source: ...]` |
| 不連回 people/倪海厦 | 每個新實體 page 必須 link 到 anchor |