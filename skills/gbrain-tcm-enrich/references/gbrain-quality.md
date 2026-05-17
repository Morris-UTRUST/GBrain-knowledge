# GBrain Quality Convention — 中醫文本蒸餾版

## Citation 格式（強制）

每個臨床判斷事實後面必須加 inline citation：

```markdown
附子性味辛、甘、大熱，歸心脾腎經，能溫陽散寒。
[Source: 金匱要略-01.md:1042]
```

### Citation 格式規範

| 來源類型 | 格式 |
|---------|------|
| 倪師視頻同步文稿 | `[Source: 金匱要略-0X.md:行號]` |
| 用戶直接陳述 | `[Source: User, {context}, YYYY-MM-DD]` |
| 多來源綜合 | `[Source: compiled from 金匱要略-01.md:行號, 金匱要略-02.md:行號]` |

### Source Precedence（優先順序）

1. 用戶直接陳述（最高權威）
2. Compiled truth（brain 已蒸餾的結論）
3. Timeline entries（原始事件記錄）
4. 外部來源（中醫文獻、Web search）

---

## Back-Link Iron Law（強制）

**每提到一個有 brain page 的實體，就必須建立雙向 back-link。**

### 操作流程

1. 建立 `herbs/附子` 時：
   - 頁面 body 自然提及：「倪師在金匱要略中提到...」
   - 建立 link：`gbrain link herbs/附子 people/倪海厦`
   - 更新 `people/倪海厦`：在「相關實體」中新增 `[[herbs/附子]]`

2. 每當提及 `people/倪海厦` 的內容被寫入新頁，該新頁也應該 link 回 anchor

### Format（寫入 page 時使用）

```markdown
## 臨床應用

倪師臨床使用附子於四肢厥冷、脈微欲絕之證。
[Source: 金匱要略-01.md:1042]

## Linked Entities

- [[people/倪海厦]] — 臨床經驗來源
- [[formulas/四逆湯]] — 附子配乾姜的經方
```

### 錯誤 Pattern

- ✗ 只建立 link，不更新對方頁面的 back-link
- ✗ citation 寫錯誤格式（如「來源：倪師說」）
- ✗ 把西醫病名當作 citation（無中生有）

---

## Page 品質標準

每個實體 page 必須同時包含：

1. **Compiled Truth（目前我相信的真相）**
   - 臨床判斷、功效主治、用法用量
   - 必須有 inline citations

2. **Timeline（為什麼我相信）**
   - 格式：`- **YYYY-MM-DD** | 事件摘要 [Source: ...]`
   - 只能往後加，不能修改既有條目

3. **Linked Entities（關聯實體）**
   - 列出相關的人、藥、方、病理
   - 每個 link 都是實際存在的 brain page

---

## Notability Gate（中醫適用版）

不是每個提到的藥物都要建立 page。

建立原則：
- **herbs：** 臨床常用（出現頻率高於等於 3 次）且有倪師獨特臨床判斷
- **formulas：** 金匱要略或傷寒論有記載，且有臨床配伍特點
- **conditions：** 中醫病名（不是西醫病名），有診斷與治療原則

不建立：
- 僅提到一次且無特殊臨床意義
- 文獻中作為背景敘述而非治療重點