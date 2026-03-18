# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 指令

```bash
npm install      # 安裝依賴
npm run dev      # 啟動開發伺服器（自動開啟 http://localhost:3000）
npm run build    # 建置輸出到 ./build/
```

沒有測試框架設定，測試方式為手動在瀏覽器中驗證。

## 架構

這是一個單頁 React 寶藏獵人遊戲，所有遊戲邏輯集中在 [src/App.tsx](src/App.tsx)。

**遊戲狀態**（全在 `App.tsx` 的 React state 中）：
- `boxes[]` — 三個寶箱，每個記錄 `id`、`isOpen`、`hasTreasure`
- `score` — 當前分數（找到寶藏 +100，骷髏 -50）
- `gameEnded` — 找到寶藏或所有箱子都開完時觸發

**動畫**：使用 Framer Motion（`motion/react`）實現 3D 翻轉效果（`rotateY`）與 hover/tap 縮放。

**UI 元件**：[src/components/ui/](src/components/ui/) 是 shadcn/ui 風格的預建元件庫（Radix UI + Tailwind），共 49 個元件，直接使用即可，不需修改。

**音效資源**（已 import 但尚未整合）：
- `src/audios/chest_open.mp3` — 開寶箱音效
- `src/audios/chest_open_with_evil_laugh.mp3` — 開到骷髏的音效

**圖片資源**：
- `src/assets/treasure_closed.png` — 未開箱
- `src/assets/treasure_opened.png` — 寶藏
- `src/assets/treasure_opened_skeleton.png` — 骷髏
- `src/assets/key.png` — 鑰匙（可做 cursor 使用）

## 注意事項

- 樣式使用 Tailwind CSS v4（設定在 `src/styles/globals.css`）
- Vite path alias：`@` → `./src`
- 新增 function 時，在函數頂部加一行繁體中文(zh-TW)的 comment，說明用途及輸入輸出參數
