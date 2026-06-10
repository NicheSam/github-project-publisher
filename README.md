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
- 你需要更新已經上傳過的專案，而不是重新建立一個 GitHub repository。

### 主要功能

- 掃描並分類候選專案資料夾。
- 將專案複製到 `PushGithub` staging area，不改動原始工作檔。
- 建立專案資料夾與版本子資料夾，方便日後維護。
- 排除常見依賴、編譯輸出、暫存與大型產物。
- 檢查常見 secret 檔名與 token pattern。
- 撰寫中文或中英文雙語 README。
- 初始化 Git repository，建立 commit。
- 使用 GitHub CLI 建立 repository 並 push。
- 自動判斷專案是首次發布還是既有 repository 更新。
- 視情況建立 Git tag 或 GitHub Release。
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

### 首次發布與版本更新

此 skill 會先判斷每個專案目前的狀態，再決定使用哪一個流程。

它會檢查：

- `PushGithub` 中是否已經有對應的 staged project。
- staged project 是否已經是 Git repository。
- 是否已經設定 GitHub remote。
- GitHub 上是否已存在對應 repository。
- 本機 working tree 是否乾淨。
- 是否已有既有 tag 或 release。

依照檢查結果，流程會分成：

- `first-publish`：本機與 GitHub 都沒有既有 repository，建立乾淨副本、初始化 Git、建立 GitHub repo 並 push。
- `local-staged-only`：`PushGithub` 已有 staged project，但尚未連到 GitHub，檢查後設定 remote 並 push。
- `github-existing-local-missing`：GitHub repo 已存在，但本機 staged repo 不存在，優先 clone 到 `PushGithub`，避免重建 repo。
- `update-existing`：本機 staged repo 已連到正確 GitHub repo，複製更新內容、保留 `.git`、檢查差異、commit 並 push。
- `ambiguous`：repo 名稱、remote、版本資料夾或 working tree 狀態不清楚時，停止並要求確認。

版本更新時，預設使用 Git commit 管理歷史。只有在有明確版本意義時才建立 tag 或 GitHub Release：

- `patch`：README、文件、metadata、小修正。
- `minor`：新增功能、支援新流程、新專案類型。
- `major`：破壞性資料夾結構、API 或發布流程變更。

一般 GitHub repository 不建議每次更新都在 repo 裡新增一層版本資料夾。版本資料夾可以留在 `PushGithub` 的本機整理區；公開 repo 的版本歷史優先使用 commit、tag 與 release。

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
- You need to update a project that has already been published instead of creating a duplicate GitHub repository.

### Capabilities

- Discover and classify candidate project folders.
- Copy projects into a `PushGithub` staging area.
- Create project folders and version subfolders for maintainability.
- Exclude common dependency, build, runtime, and cache artifacts.
- Check common secret filenames and token patterns.
- Write Chinese or bilingual Chinese/English READMEs.
- Initialize Git repositories and create commits.
- Create GitHub repositories with GitHub CLI and push.
- Detect whether a project is a first publish or an update to an existing repository.
- Create Git tags or GitHub Releases when appropriate.
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

### First Publish And Version Updates

This skill checks each project's current state before choosing a workflow.

It checks whether:

- a matching staged project already exists under `PushGithub`
- the staged project is already a Git repository
- a GitHub remote is already configured
- a matching GitHub repository already exists
- the local working tree is clean
- existing tags or releases are present

Based on that state, it chooses one route:

- `first-publish`: no local staged repository and no matching GitHub repository exist; create a clean copy, initialize Git, create the GitHub repo, and push.
- `local-staged-only`: a staged project exists but is not connected to GitHub; inspect it, configure the remote, and push.
- `github-existing-local-missing`: the GitHub repository exists but the local staged repository is missing; clone it into `PushGithub` instead of recreating it.
- `update-existing`: the staged repository already points to the intended GitHub repository; copy updated files, preserve `.git`, review diffs, commit, and push.
- `ambiguous`: repo name, remote, version folder, or working tree state is unclear; stop and ask for confirmation.

For version updates, normal Git commits are the default history mechanism. Tags or GitHub Releases should be created only when the update has clear version meaning:

- `patch`: README, docs, metadata, small fixes, or cleanup.
- `minor`: new features, new workflows, or support for a new project type.
- `major`: breaking folder structure, API, or publishing workflow changes.

For normal GitHub repositories, avoid adding a new version-numbered source folder for every update. Local version folders can remain useful inside `PushGithub`; public repository history should usually be represented with commits, tags, and releases.

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
