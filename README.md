# Git turtotial

## 1. Rebase
**Tool:** Git extensions

**Usage:** Rebase (rename/delete/arrange/.... commit)

**Step:**
- Step 1: Right click at commit base
- Step 2: Choose `Rebase current branch on` then choose `Select commit interactively…`	
- Step 3: Edit what you want (order-by, fixup, pick, drop,…)	
- Step 4: Save and close dialog	 (Delete all data of dialog and save to abort rebase)
  
  <img width="2762" height="1000" alt="image" src="https://github.com/user-attachments/assets/952024cd-f725-4e6e-bc5f-6f4732b1dce8" />

## 2. Pull Rebase
**Tool:** Git extensions

**Usage:** Pull source and pick your code to top

**Step:**
- Step 1: Choose `Open pull dialog…`
- Step 2: Choose `Rebase current branch on top remote branch, creates linear history (use with caution)`
  
  <img width="3180" height="802" alt="image" src="https://github.com/user-attachments/assets/4475ac16-c50e-4965-8801-37bd45cbcef3" />

## 3. Rename Commit
**Tool:** Git extensions

**Usage:** Rename commit

**Step:** Choose `Reword commit`

<img width="1612" height="768" alt="image" src="https://github.com/user-attachments/assets/c3fcc3c9-70d7-4336-acae-931e4c011221" />

## 4. RefLog
**Tool:** TortoiseGit

**Usage:** Show history git change, can use this to fix "rebase|force push|.." mistake

**Step:** Step: Choose `Show Reflog`

<img width="1893" height="1229" alt="image" src="https://github.com/user-attachments/assets/461231fc-45a3-42dc-a86f-a39e91468054" />

## 5. Export Src
**Tool:** Git extensions && TortoiseGit

**Usage:** Export source from commit to files

**Step:** 
- Way 1: Use git extensions - Choose `Archive this commit…` (default: zip all src code)
- Way 2: Use tortoise git - Choose `Export selection to…`
<img width="3167" height="1145" alt="image" src="https://github.com/user-attachments/assets/ab215721-cc91-445e-9349-8b8130c1f8c2" />
<img width="1856" height="983" alt="image" src="https://github.com/user-attachments/assets/e9b30efc-6c5f-4e86-a3ed-e24ffeff010e" />


## 6. Edit Commit
**Tool:** Git extensions

**Usage:** Edit code of Commit

**Step:** 
- Step 1: Choose `Edit commit`
- Step 2: Edit sth you want in working directory
- Step 3: Click on `You are in the middle of a rebase`
- Step 4: Commit with amend to combine the working directory with the edit commit
- Step 5: Continue Rebase + Resolve conflict

<img width="5501" height="2769" alt="image" src="https://github.com/user-attachments/assets/a1a3eb23-6d76-44cf-830d-68acf00be663" />


