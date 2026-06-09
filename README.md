# GitHub Project Publisher Skill

## 中文

`github-project-publisher` 是一個 Codex skill，用於把本機專案整理成可安全上傳 GitHub 的公開或私人 repository。

它的重點不是單純執行 `git push`，而是把「專案整理、風險檢查、README 撰寫、Git 初始化、GitHub CLI 驗證、repo 建立與推送」串成一個可重複執行的流程。

### 解決的問題

很多本機專案在推上 GitHub 前會混有：

- 原始碼與建置產物混在一起。
- `.venv`、`node_modules`、`bin`、`obj`、`dist` 等不該提交的資料夾。
- runtime output、log、cache 或專案成果檔。
- README 只寫給作者自己看，外部使用者看不懂。
- GitHub CLI 未登入、token 失效或 Git ownership 檢查失敗。

這個 skill 會要求 Codex 先整理乾淨副本，再推送到 GitHub。

### 適合誰使用

- 想把多個本機專案整理後推到 GitHub 的開發者。
- 需要避免把原始工作資料夾弄亂的使用者。
- 想在上傳前加入基本安全檢查的人。
- 希望 README 面向外部讀者，而不是只面向自己的專案維護者。

### 主要能力

- 掃描並判斷哪些資料夾適合上傳。
- 將專案複製到 `PushGithub` staging area。
- 建立專案資料夾與版本子資料夾。
- 排除常見不該提交的資料夾與檔案。
- 掃描常見 token、secret、private key 與敏感檔名。
- 撰寫面向非本機使用者的 README。
- 支援中英文雙語 README。
- 初始化 Git repo 並建立初始 commit。
- 檢查 `gh auth status`。
- 建立 GitHub repo 並 push。
- 回報 repo URL、visibility、branch、commit 與本機狀態。

### 安裝

將此 repo 內容放到 Codex skills 目錄下：

```text
<CODEX_HOME>/skills/github-project-publisher/
  SKILL.md
  agents/openai.yaml
```

Windows 常見位置：

```text
%USERPROFILE%\.codex\skills\github-project-publisher\
```

### 使用方式

在 Codex 中可以明確指定：

```text
使用 $github-project-publisher，把這幾個專案整理後推上 GitHub。
```

也可以描述完整需求：

```text
請把這些專案複製到 PushGithub，清理不該上傳的檔案，寫中英文 README，建立公開 GitHub repo 並推送。
```

### GitHub CLI 需求

需要安裝並登入 GitHub CLI：

```powershell
gh --version
gh auth status
```

若 token 失效，重新登入：

```powershell
gh auth login -h github.com
```

### 注意事項

- 此 skill 只做基本上傳前風險檢查，不等於完整資安審計。
- 對公開 repo，仍應人工確認是否包含客戶資料、憑證、商業機密或大型二進位檔。
- 若 Git 顯示 `dubious ownership`，skill 只應對明確專案路徑加入 `safe.directory`，不應使用萬用路徑。

---

## English

`github-project-publisher` is a Codex skill for preparing local project folders for safe GitHub publishing.

It is not just a `git push` helper. It guides Codex through project staging, upload-scope cleanup, basic safety checks, public-facing README writing, Git initialization, GitHub CLI validation, repository creation, push, and final reporting.

### Problem It Solves

Local projects often contain files that should not be uploaded directly:

- source code mixed with build artifacts
- `.venv`, `node_modules`, `bin`, `obj`, `dist`, and similar folders
- runtime outputs, logs, caches, or generated project files
- READMEs written only for the original author
- invalid GitHub CLI tokens or Git ownership issues

This skill asks Codex to prepare a clean staged copy before publishing.

### Who Should Use It

- Developers publishing multiple local projects to GitHub.
- Users who want to keep original working folders untouched.
- Teams that want basic upload-safety checks before public release.
- Maintainers who need public-facing README files rather than local notes.

### Capabilities

- Discover and classify candidate project folders.
- Copy projects into a `PushGithub` staging area.
- Create project folders and optional version subfolders.
- Exclude common non-source folders and generated files.
- Scan for common secret filenames and token patterns.
- Write READMEs for external users.
- Support bilingual Chinese/English README output.
- Initialize Git repositories and create initial commits.
- Validate GitHub CLI authentication.
- Create GitHub repositories and push.
- Report final URLs, visibility, branch, commit, and local repository status.

### Installation

Place this repository under your Codex skills directory:

```text
<CODEX_HOME>/skills/github-project-publisher/
  SKILL.md
  agents/openai.yaml
```

Common Windows path:

```text
%USERPROFILE%\.codex\skills\github-project-publisher\
```

### Usage

Explicit invocation:

```text
Use $github-project-publisher to prepare these local projects and push them to GitHub.
```

Natural-language request:

```text
Copy these projects into PushGithub, clean files that should not be uploaded, write bilingual READMEs, create public GitHub repositories, and push them.
```

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
- For public repositories, manually verify that no client files, credentials, trade secrets, or large binary artifacts are included.
- If Git reports `dubious ownership`, the skill should add `safe.directory` only for the exact staged project path, never a wildcard path.

