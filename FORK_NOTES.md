# 分支版本說明（Fork Notes）

本 repository 是 [derceg/explorerplusplus](https://github.com/derceg/explorerplusplus) 的分支（fork），
由 [dendrobium0402-dot](https://github.com/dendrobium0402-dot) 維護，僅修正個人使用上遇到的問題，
**未**發送 Pull Request 回原專案。

## 版本

| 項目 | 內容 |
| --- | --- |
| 分支版本號 | **1.5.1**（以原專案 1.5.0 開發版為基礎） |
| 基於原專案 commit | [`adf72c7`](https://github.com/derceg/explorerplusplus/commit/adf72c7)（2026-09-28） |
| 修正 commit | `fc959aa` |
| 授權 | GPL-3.0-only（與原專案相同，見 [LICENSE](LICENSE)） |

## 1.5.1 變更內容

### 修正：預設檔案檢視模式固定為「中圖示」，未套用使用者設定

**問題現象**

在「選項 → Default Settings → Default view」把預設檢視模式改成「詳細資料」或其他模式後，
用以下方式開啟資料夾時，仍然固定顯示為「中圖示（Medium icons）」：

1. 啟動模式設為「開啟自訂資料夾」（Startup folders）時
2. 從命令列開啟資料夾（例如 `explorer++.exe D:\Data`，或將 Explorer++ 設為預設檔案總管時）
3. 從命令列指定要選取的檔案時
4. 啟用「每個工作階段多個視窗」功能後，使用「在新視窗開啟」時

**原因**

上述路徑建立分頁時，使用的是未初始化的 `FolderSettings`。
它的預設值寫死為 `ViewMode::Icons`，沒有讀取使用者設定的 `config.defaultFolderSettings`。

**修正方式**

讓這些路徑建立分頁時，改用 `m_config->defaultFolderSettings`。

| 檔案 | 修改 |
| --- | --- |
| `Explorer++/Explorer++/SessionRestorer.cpp` | 自訂啟動資料夾、命令列資料夾與命令列選取檔案的分頁，改用預設資料夾設定 |
| `Explorer++/Explorer++/MsgHandler.cpp` | 「在新視窗開啟」的分頁，改用預設資料夾設定 |
| `Explorer++/TestExplorer++/SessionRestorerTest.cpp` | 新增 2 個單元測試，驗證自訂啟動資料夾與命令列分頁會套用預設檢視模式 |
| `Explorer++/VersionNumber.props` | 版本號 1.5.0 → 1.5.1 |

「還原上次的分頁」不受影響：每個分頁本來就會保存自己的檢視模式，還原時照原樣顯示。

## 編修過程

1. 從 GitHub clone 原專案 `derceg/explorerplusplus`
2. 搜尋原始碼中檢視模式的預設值與使用處，確認程式已有「預設檢視模式」設定
3. 逐一追查所有建立分頁的路徑，找出未套用設定的 4 條路徑
4. 修改程式碼，並新增單元測試
5. 在本機建立分支 `fix-default-view-mode` 並 commit
6. 推送到本分支 repository，更新版本號與本說明文件

### 建置與測試狀態

- 已用 vcpkg 安裝相依套件。
- 用 Visual Studio 2019 Build Tools（MSVC 14.29、Windows SDK 10.0.19041）嘗試編譯，因工具鏈版本過舊而失敗：
  SDK 的 GDI+ 標頭出現 `min`/`max` 錯誤，C++/WinRT 標頭也造成編譯器內部錯誤。
  原專案建議使用 Visual Studio 2022。
- 因此**本版本尚未編譯，單元測試也尚未執行**。使用前請先用 Visual Studio 2022 自行建置與測試。

## 使用工具

| 工具 | 用途 |
| --- | --- |
| [Claude Code](https://claude.com/claude-code)（Claude 桌面應用程式） | AI 程式助理：分析原始碼、找出問題原因、撰寫修正與測試、撰寫本文件 |
| Git / GitHub | 版本控制、fork 與推送 |
| vcpkg | 安裝 C++ 相依套件 |
| Visual Studio 2019 Build Tools（MSBuild、MSVC） | 嘗試編譯 |

## AI 使用聲明

本次修正在使用者指示與審閱下，由 Anthropic 的 AI 模型 **Claude Opus 5.5** 透過 **Claude Code** 協助完成，
包括程式碼分析、修改、測試撰寫與本說明文件。commit 訊息中以
`Co-Authored-By: Claude Opus 5.5` 標註 AI 的參與。

## 感謝

感謝 Explorer++ 原作者 **David Erceg** 以及所有 [Explorer++ 貢獻者](https://github.com/derceg/explorerplusplus/graphs/contributors)
長期開發與維護這套輕量、快速又完全可攜的 Windows 檔案管理程式，並以 GPL-3.0 開源授權分享。
本分支的修正都建立在他們的成果之上。

原專案網站：<https://explorerplusplus.com>
