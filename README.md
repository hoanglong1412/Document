# Git Tutorial

## 📑 Table of Contents
1. [Rebase](#1-rebase)
2. [Pull Rebase](#2-pull-rebase)
3. [Rename Commit](#3-rename-commit)
4. [Reflog](#4-reflog)
5. [Export Source](#5-export-source)
6. [Edit Commit](#6-edit-commit)
7. [Checkout Path](#7-checkout-path)
8. [Create Patch](#8-create-patch)
9. [Bundle](#9-bundle)
10. [Git Hook](#10-git-hook)

---

## 1. Rebase
**Tool:** Git Extensions  
**Usage:** Reorder, rename, fixup, or remove commits.

**Steps:**
1. Right-click the commit you want to rebase onto.
2. Select **Rebase current branch on → Select commit interactively…**
3. Adjust commits (pick, drop, fixup, reorder…).
4. Save and close (clear dialog to abort).
<img width="2762" height="1000" alt="image" src="https://github.com/user-attachments/assets/952024cd-f725-4e6e-bc5f-6f4732b1dce8" />

---

## 2. Pull Rebase
**Tool:** Git Extensions  
**Usage:** Pull remote updates and replay your commits on top.

**Steps:**
1. Open **Pull dialog…**
2. Enable **Rebase current branch on top remote branch (linear history)**.
<img width="3180" height="802" alt="image" src="https://github.com/user-attachments/assets/4475ac16-c50e-4965-8801-37bd45cbcef3" />

---

## 3. Rename Commit
**Tool:** Git Extensions  
**Usage:** Change commit message.

**Step:**  
- Select **Reword commit**.
<img width="1612" height="768" alt="image" src="https://github.com/user-attachments/assets/c3fcc3c9-70d7-4336-acae-931e4c011221" />

---

## 4. Reflog
**Tool:** TortoiseGit  
**Usage:** View all Git actions; restore mistakes from rebase/force-push.

**Step:**  
- Select **Show Reflog**.
<img width="1893" height="1229" alt="image" src="https://github.com/user-attachments/assets/461231fc-45a3-42dc-a86f-a39e91468054" />

---

## 5. Export Source
**Tool:** Git Extensions / TortoiseGit  
**Usage:** Export source code from a specific commit.

**Ways:**
- **Git Extensions:** **Archive this commit…** → ZIP export  
- **TortoiseGit:** **Export selection to…**
<img width="3167" height="1145" alt="image" src="https://github.com/user-attachments/assets/ab215721-cc91-445e-9349-8b8130c1f8c2" />
<img width="1856" height="983" alt="image" src="https://github.com/user-attachments/assets/e9b30efc-6c5f-4e86-a3ed-e24ffeff010e" />

---

## 6. Edit Commit
**Tool:** Git Extensions  
**Usage:** Modify code inside a past commit.

**Steps:**
1. Choose **Edit commit**.
2. Update files in working directory.
3. Click **You are in the middle of a rebase**.
4. Commit using **Amend**.
5. Continue rebase + resolve conflicts.
<img width="5501" height="2769" alt="image" src="https://github.com/user-attachments/assets/a1a3eb23-6d76-44cf-830d-68acf00be663" />


---

## 7. Checkout Path
**Tool:** Git Extensions  
**Usage:** Checkout only specific folders/files.

**Steps:**
1. (Optional) Clone with `--no-checkout`.
2. Set folder: `git sparse-checkout set <path>`
3. Checkout branch.

**Note:**  
Reset to normal: `git sparse-checkout disable`
<img width="3072" height="699" alt="image" src="https://github.com/user-attachments/assets/3932a167-82db-4d17-877d-c55c0bba3fca" />

---

## 8. Create Patch (diff file)
**Tool:** Command / Git Extensions / TortoiseGit  
**Usage:** Export `.diff` or `.patch` for code review or sharing.

**Ways:**
- **Command:** `git diff commit1 commit2 > FileChanges.diff`
- **Git Extensions:** **Format patch…**
- **TortoiseGit:** **Create Patch Serial…**
<img width="3042" height="2552" alt="image" src="https://github.com/user-attachments/assets/43a74d32-6f6f-4913-bd5c-2904380e7a60" />
---

## 9. Bundle
**Tool:** Command  
**Usage:** Save repo into a single portable file; clone without internet.

**Steps:**
1. Create bundle: `git bundle create MyRepo.bundle <branch>`
2. Clone bundle: `git clone MyRepo.bundle <path>`
<img width="2270" height="1144" alt="image" src="https://github.com/user-attachments/assets/14e66f95-1a34-47d9-8e71-8aaa666d5168" />

---

## 10. Git Hook
**Tool:** No

**Usage:** Trigger custom scripts when Git performs an action (commit, push, merge,…).

**Steps:**
1. Open the .git/hooks folder.
2. Pick the hook you need (e.g., pre-commit, pre-push, post-merge).
3. Remove the .sample extension if present.
4. Add your script logic (bash, PowerShell, etc.).
5. Save — Git auto-triggers it during that action.

---
