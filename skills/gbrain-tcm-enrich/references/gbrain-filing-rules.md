# GBrain Filing Rules — 中醫文本適用版

## 核心原則：Primary Subject 決定目錄

**不是「文本屬於哪裡」，是「這個頁面的主角是誰」。**

---

## 目錄決策樹

```
頁面的主角是什麼？

├── 中醫人物（臨床研究者、醫師）
│   └── people/倪海厦
├── 單味中藥
│   └── herbs/附子、herbs/桂枝、herbs/柴胡
├── 中醫方劑（多味藥組合）
│   └── formulas/四逆湯、formulas/桂枝湯
├── 中醫病理（證型、病機）
│   └── conditions/瘀血、conditions/寒疝、conditions/狐惑
├── 臨床概念/入口頁
│   └── concepts/倪師臨床地圖
└── 原始文獻（不該直接儲存）
    ✗ 不要建立純文本頁面（如「金匱要略-01.md」獨立頁）
    → 原始文獻內容透過 chunk pages 進入系統，
       但 enrich 目標是實體頁，不是文本頁本身
```

---

## 禁止的 Pattern

| 錯誤 | 為什麼錯 |
|------|---------|
| 把文本 chunk 當作 page（如 `金匱要略-01c001`） | 不是 primary subject，是来源证据 |
| 同一實體跨越兩個目錄（如 `pathology/寒疝` + `conditions/寒疝`） | 破壞 graph 一致性 |
| 把臨床觀察放在 `sources/` | sources/ 只能用於 bulk data import |
| 用西醫病名建立病理頁（如「肝癌」） | 這是中醫知識庫，不是西醫 |

---

## 中醫實體特殊規則

### Herbs（單味藥）

- 用中文正名：附子、桂枝、柴胡，不寫拼音
- 臨床判斷為主：性味、功效、劑量、倪師用法
- Back-link 目標：`people/倪海厦`

### Formulas（方劑）

- 以《傷寒論》《金匱要略》經方優先
- 包含：組成藥物、劑量、煎煮法、適用證
- Back-link 目標：`people/倪海厦` + 組成藥物的 herbs pages

### Conditions（中醫病理）

- 用中醫病名（瘀血、寒疝、狐惑），不用西醫譯名
- 包含：診斷標準（倪師怎麼判斷）、治療原則、代表方劑
- Back-link 目標：`people/倪海厦` + 相關 formulas pages

---

## Back-Link Iron Law

每建立一個實體 page，必須同時：
1. 新頁 body 內提及 `people/倪海厦`（自然提及即可）
2. 用 `gbrain link` 建立從新頁到 `people/倪海厦` 的 link
3. 在 `people/倪海厦` 的 linked entities 列表中新增該實體

Format（寫入新頁時）：
```
Linked Entities:
- [[people/倪海厦]] — 臨床經驗來源
```

Format（在 people/倪海厦 更新）：
```
## 相關實體
- [[herbs/附子]] — 溫陽要藥，金匱要略多次提及
- [[formulas/四逆湯]] — 回陽救逆代表方
```