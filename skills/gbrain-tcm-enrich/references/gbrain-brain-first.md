# Brain-First Lookup Convention — 中醫知識蒸餾版

## 查詢順序（強制順序）

每次尋找中醫實體資訊時，必須依序執行：

```
Step 1: gbrain search "實體名"        # 關鍵字搜索，快速
Step 2: gbrain query "相關問題"        # 混合搜索，探索更多
Step 3: gbrain get <slug>             # 直接讀取已確認的 page
Step 4: 外部搜尋                       # 只有前三步都無結果才做
```

---

## GBrain CLI 常用命令

| 用途 | 命令 |
|------|------|
| 列出所有實體 | `~/.bun/bin/bun run src/cli.ts list` |
| 關鍵字搜索 | `~/.bun/bin/bun run src/cli.ts search "附子"` |
| 混合搜索 | `~/.bun/bin/bun run src/cli.ts query "倪師怎麼用附子"` |
| 讀取已知 page | `~/.bun/bin/bun run src/cli.ts get <slug>` |
| 讀取 source chunk | `~/.bun/bin/bun run src/cli.ts get 金匱要略-01c001` |
| 建立/更新 page | `~/.bun/bin/bun run src/cli.ts put <slug> < content.md` |
| 查健康狀態 | `~/.bun/bin/bun run src/cli.ts health` |
| 查孤兒頁 | `~/.bun/bin/bun run src/cli.ts orphans` |
| 搜尋死連結 | `~/.bun/bin/bun run src/cli.ts check-backlinks fix` |

---

## 對中醫文本的特殊應用

### 確認實體是否已存在

```bash
# 先查 herb 是否存在
~/.bun/bin/bun run src/cli.ts list | grep -v ai.gateway | grep "^herbs/附子"

# 若無輸出，代表尚未建立，可以建立
```

### 確認 people/倪海厦 是否存在

```bash
~/.bun/bin/bun run src/cli.ts list | grep -v ai.gateway | grep "^people/倪海厦"
```

若不存在，必須先建立這一個 anchor，再建立其他實體。

### 從 source chunk 獲取臨床內容

```bash
# 找包含附子的 chunk
~/.bun/bin/bun run src/cli.ts query "附子 臨床" | head -20

# 找倪師談特定方劑
~/.bun/bin/bun run src/cli.ts query "四逆湯 倪師" | head -20
```

---

## 當 Spawn 子代理時

若需要子代理協助處理特定實體，在 task prompt 中加入：

```
在開始任何工作前，先閱讀 skills/gbrain-tcm-enrich/references/gbrain-filing-rules.md
```

這確保子代理也知道分類規則，不會重蹈覆轍。