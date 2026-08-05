# meta-plugin-creator

[English](./README.md) | **繁體中文**

一個用來建立 Claude Code plugin（外掛）的 Claude Code plugin。

## 這是什麼

`meta-plugin-creator` 把一套 plugin 撰寫標準（authoring standards）內建在自身，並引導你產出符合這些標準的 plugin。它引導產出的每個 plugin——以及它自己——都遵循以下五項標準：

- **漸進式揭露（progressive disclosure）**——一個精簡的入口檔案（`SKILL.md`、command），細節延後到按需載入的參考檔案中。
- **狀態外部化（state externalization）**——跨步驟的狀態存放在宣告過的檔案中，而不是只存在於對話上下文（conversation context）裡。
- **委派給子代理（delegation to sub-agents）**——繁重或雜訊多的工作委派給 sub-agent，而不是在主要上下文中執行。
- **相容 git worktree（git-worktree compatibility）**——plugin 邏輯絕不假設 `.git` 是一個目錄，也不會寫死主要 checkout 的路徑。
- **支援較低階模型（down-tier model support）**——指令明確到足以在 Opus 這個模型層級也能正確執行，不依賴未言明的慣例。

## 安裝

這個 repository 本身就是一個單一 plugin 的 marketplace（`.claude-plugin/marketplace.json`）。先從 GitHub 加入這個 marketplace，再安裝 plugin。

在 Claude Code session 中：

```
/plugin marketplace add wiasliaw/meta-plugin-creator
/plugin install meta-plugin-creator@meta-plugin-creator
```

或使用 `claude` CLI：

```
claude plugin marketplace add wiasliaw/meta-plugin-creator
claude plugin install meta-plugin-creator@meta-plugin-creator
```

## 使用方式

這個 plugin 提供一個 skill：`plugin-dev`。它沒有提供任何 slash command——而是由模型自動觸發（model-invoked）：當你要求建立新的 plugin、對既有 plugin 新增 skill／command／agent，或是把一組零散的 skill 整合成可發佈的 plugin 時，Claude Code 會自動觸發它。

`plugin-dev` 會自動選擇以下兩種模式之一執行：

### 初始化模式（Init Mode）

當目標 repo 尚未存在 plan 檔案時使用。會執行固定的四題訪談、寫出 plan 檔案、搭建 plugin 骨架（`.claude-plugin/plugin.json`、`.claude-plugin/marketplace.json`，以及所需的 `skills/`、`commands/`、`agents/` 目錄），為每個規劃中的元件各派送一個 sub-agent 進行撰寫，最後執行最終驗證。

### 擴充模式（Extend Mode）

當 plan 檔案已經存在時使用。將要新增的元件加入 plan 的元件清單（component inventory），派送 sub-agent 撰寫該元件，最後執行最終驗證。

跨步驟的工作狀態預設存放在目標 repo 中的 `.meta-plugin-creator/plan.md`；你也可以指示 skill 使用其他位置。

## Repository 結構

簡要概覽——完整的目錄樹請參見 `CLAUDE.md`：

- `.claude-plugin/` — plugin manifest 與 marketplace listing。
- `skills/plugin-dev/` — `plugin-dev` skill 及其參考檔案。
- `.changeset/` — 供 release 工具使用的 changeset 項目。
- `.harness/` — 長期專案記憶（架構、限制條件、決策紀錄、已驗證功能）。

## 開發

- 工具鏈：Node.js 24.x + pnpm 10.x，僅用於 release 工具——plugin 本身是純 markdown，沒有任何 runtime 相依套件。
- 驗證：`claude plugin validate --strict .`
- 發佈：由 changesets 驅動版本管理。推送到 `main` 後，GitHub Action 會開啟一個 release PR；合併該 PR 會建立 tag 並發佈 GitHub Release。發佈的載體就是這個 git repository 本身——不會發佈到 npm。

## 授權條款

MIT——詳見 [LICENSE](./LICENSE)。
