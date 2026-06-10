# GitHub Project Publisher Skill

## 中文

`github-project-publisher` 是一個 Codex skill，用來協助把本機專案整理成適合上傳到 GitHub 的版本。

它不是單純的 `git push` 指令包，而是一套發布前流程：複製專案到 `PushGithub` 暫存區、整理不該上傳的檔案、檢查常見敏感資訊、撰寫面向外部使用者的 README、初始化 Git、建立 GitHub repository、推送並回報結果。

### 適用情境

- 你有多個本機專案，想分批整理後上傳 GitHub。
- 你想保留原始工作資料夾，不希望發布流程改動原本檔案。
- 你需要在上傳前排除 `.venv`、`node_modules`、`bin`、`obj`、`dist`、log、cache、暫存檔、輸出檔等內容。
- 你需要把原本只給自己看的說明文件，改寫成網路上第一次看到專案的人也能理解的 README。
- 你希望 Codex 在推送前先確認 GitHub CLI 登入狀態與 Git repository 狀態。

### 主要功能

- 掃描並分類候選專案資料夾。
- 將專案複製到 `PushGithub` staging area，不改動原始工作檔。
- 建立專案資料夾與版本子資料夾，方便日後維護。
- 排除常見依賴、編譯輸出、暫存與大型產物。
- 檢查常見 secret 檔名與 token pattern。
- 撰寫中文或中英文雙語 README。
- 初始化 Git repository，建立 commit。
- 使用 GitHub CLI 建立 repository 並 push。
- 回報 repository URL、公開狀態、branch、commit 與本機路徑。

### 安裝位置

將此資料夾放到 Codex skills 目錄：

```text
<CODEX_HOME>/skills/github-project-publisher/
  SKILL.md
  agents/openai.yaml
```

Windows 常見位置：

```text
%USERPROFILE%\.codex\skills\github-project-publisher\
```

安裝或更新後，請完整關閉並重新開啟 Codex Desktop，讓 skill index 重新載入。

### 正確呼叫方式

建議使用 `$github-project-publisher` 明確呼叫此 skill：

```text
使用 $github-project-publisher，將這些本機專案整理到 PushGithub，檢查可上傳範圍，撰寫中英文 README，並推送到 GitHub。
```

也可以用英文：

```text
Use $github-project-publisher to prepare these local projects for GitHub, write bilingual READMEs, and push them safely.
```

### `$` 與 `@` 的差異

`$github-project-publisher` 是正式且穩定的 skill 呼叫方式。

`@github` 不是 skill 專用呼叫方式。它會觸發 Codex 的混合搜尋，可能同時列出 GitHub 外掛、檔案、資料夾與其他候選項。因為 `github-project-publisher` 的名稱包含 `github`，它容易被內建 GitHub 外掛或 `PushGithub` 裡的檔案結果蓋過。

如果你要使用此 skill，請優先輸入：

```text
$github-project-publisher
```

如果只是想在選單中搜尋，可以嘗試：

```text
@publisher
@project-publisher
@GitHub Project Publisher
```

但正式工作流程仍建議使用 `$github-project-publisher`。

### GitHub CLI 需求

此 skill 會透過 GitHub CLI 建立 repository 與 push。請先確認：

```powershell
gh --version
gh auth status
```

如果尚未登入或 token 失效：

```powershell
gh auth login -h github.com
```

### 注意事項

- 此 skill 會做基本上傳前檢查，但不能取代完整安全稽核。
- 公開 repository 前，仍應人工確認沒有客戶資料、私鑰、token、內部商業資料或大型二進位檔。
- 如果 Git 顯示 `dubious ownership`，只應針對明確的 staged project path 加入 `safe.directory`，不要使用萬用路徑。
- 預設流程應複製專案到 `PushGithub`，不要直接修改原始工作資料夾。

---

## English

`github-project-publisher` is a Codex skill for preparing local project folders for GitHub publishing.

It is not just a `git push` helper. It guides Codex through a publishing workflow: copying projects into a `PushGithub` staging area, cleaning upload scope, checking common sensitive files, writing public-facing READMEs, initializing Git, creating GitHub repositories, pushing, and reporting final links.

### When To Use It

- You have multiple local projects that need to be prepared for GitHub.
- You want to keep original working folders untouched.
- You need to exclude dependency folders, build outputs, logs, caches, temporary files, and generated artifacts.
- You need README files that make sense to people who have never seen the project before.
- You want Codex to verify GitHub CLI authentication and Git repository status before publishing.

### Capabilities

- Discover and classify candidate project folders.
- Copy projects into a `PushGithub` staging area.
- Create project folders and version subfolders for maintainability.
- Exclude common dependency, build, runtime, and cache artifacts.
- Check common secret filenames and token patterns.
- Write Chinese or bilingual Chinese/English READMEs.
- Initialize Git repositories and create commits.
- Create GitHub repositories with GitHub CLI and push.
- Report repository URL, visibility, branch, commit, and local path.

### Installation

Place this folder under your Codex skills directory:

```text
<CODEX_HOME>/skills/github-project-publisher/
  SKILL.md
  agents/openai.yaml
```

Common Windows path:

```text
%USERPROFILE%\.codex\skills\github-project-publisher\
```

After installing or updating the skill, fully quit and reopen Codex Desktop so the skill index reloads.

### Recommended Invocation

Use `$github-project-publisher` to explicitly invoke this skill:

```text
Use $github-project-publisher to prepare these local projects for GitHub, write bilingual READMEs, and push them safely.
```

### Difference Between `$` And `@`

`$github-project-publisher` is the stable skill invocation syntax.

`@github` is not skill-specific. It triggers mixed search across plugins, files, folders, and other candidates. Because this skill name contains `github`, it may be overshadowed by the built-in GitHub plugin or file results under `PushGithub`.

For reliable use, type:

```text
$github-project-publisher
```

For menu search only, these may work better than `@github`:

```text
@publisher
@project-publisher
@GitHub Project Publisher
```

For actual publishing work, prefer `$github-project-publisher`.

### GitHub CLI Requirement

GitHub CLI must be installed and authenticated:

```powershell
gh --version
gh auth status
```

If authentication is invalid:

```powershell
gh auth login -h github.com
```

### Notes

- This skill performs basic pre-upload screening, not a full security audit.
- Before publishing a public repository, manually verify that no client files, credentials, private keys, internal business data, or large binary artifacts are included.
- If Git reports `dubious ownership`, add `safe.directory` only for the exact staged project path, never a wildcard path.
- The default workflow should copy projects into `PushGithub` instead of modifying original working folders directly.
