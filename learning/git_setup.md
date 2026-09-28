# Complete Guide: Duplicating a GitHub Repository & Managing Upstream Sync

This guide provides a step-by-step workflow for creating a standalone, independent copy of a public GitHub repository under your personal GitHub account and maintaining sync capability with the original project.

---

## Part 1: Initial Setup & Duplication

### Step 1: Create a Destination Repository on GitHub
1. Go to [github.com/new](https://github.com/new).
2. Set the **Repository name** (e.g., `differentiable_modeling`).
3. Select **Public** or **Private** based on your preference.
4. **DO NOT** initialize the repository with a `README`, `.gitignore`, or `License`. It **must** remain completely empty.
5. Click **Create repository**.

---

### Step 2: Bare Clone & Mirror Push

Run the following commands in your terminal:

```bash
# 1. Navigate to your working directory
cd ~/GITDIR

# 2. Clone the original repository as a bare repository
# (A bare clone downloads only raw Git data without checking out working source files)
git clone --bare https://github.com/BennettHydroLab/differentiable_modeling_workshop.git

# 3. Navigate into the newly created bare folder
cd differentiable_modeling_workshop.git

# 4. Mirror-push all branches, tags, and commits to your new personal repository
git push --mirror https://github.com/Ben-Choat/differentiable_modeling.git
# Note: If using SSH keys, use: git push --mirror git@github.com:Ben-Choat/differentiable_modeling.git

# 5. Leave the bare directory and delete the temporary folder
cd ..
rm -rf differentiable_modeling_workshop.git
```

---

### Step 3: Clone Your Personal Copy Local & Add Upstream Remote

```bash
# 1. Clone your clean personal repository
git clone https://github.com/Ben-Choat/differentiable_modeling.git

# 2. Navigate into your repository folder
cd differentiable_modeling

# 3. Connect the original repository as an 'upstream' remote
git remote add upstream https://github.com/BennettHydroLab/differentiable_modeling_workshop.git

# 4. Verify your remote configurations
git remote -v
# Output should display:
# origin   https://github.com/Ben-Choat/differentiable_modeling.git (fetch & push)
# upstream https://github.com/BennettHydroLab/differentiable_modeling_workshop.git (fetch & push)
```

---

## Part 2: Branching Strategy for Custom Development

To prevent conflicts between your personal changes and incoming updates from the original repository, use the **Clean `main` + Feature Branch** model.

### Strategy Overview
* **`main` Branch:** Kept completely pristine. Its sole purpose is to reflect and mirror changes from `upstream/main`.
* **`dev` / Working Branches:** Where you write custom code, add new files, and modify existing notebooks or scripts.

---

### Setting Up Your Working Branch

Run this once after the initial clone:

```bash
# Create and switch to a dedicated development branch
git checkout -b dev

# Push your development branch to your remote GitHub repository
git push -u origin dev
```

> **Daily Workflow:** Always ensure you are on `dev` before editing files, committing changes, or pushing to GitHub:
> ```bash
> git checkout dev
> # ... make changes ...
> git add .
> git commit -m "Add custom analysis scripts"
> git push origin dev
> ```

---

## Part 3: Incorporating Upstream Updates

When the original repository (`upstream`) updates and you want to pull those changes into your project, perform the following 3-phase sync process:

### Phase A: Update Your Local `main`
```bash
# 1. Switch to your pristine main branch
git checkout main

# 2. Pull the latest upstream changes into main
git pull upstream main

# 3. Sync your personal remote GitHub main branch
git push origin main
```
*Because you never edit files on `main` directly, this step will always execute clean, automatic fast-forward merges without merge conflicts.*

---

### Phase B: Merge Updates Into Your Active Branch
```bash
# 1. Switch back to your working development branch
git checkout dev

# 2. Merge the updated main branch into dev
git merge main
```

---

### Phase C: Resolve Conflicts (If Applicable) & Push
If the original author modified a file that you also edited in `dev`:
1. Open the affected files in your editor.
2. Resolve any flagged conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`).
3. Save the files, stage them, complete the commit, and push:

```bash
git add .
git commit -m "Merge upstream updates from main into dev"
git push origin dev
```

---

## Summary Command Cheatsheet

| Task | Command Sequence |
| :--- | :--- |
| **Check Remotes** | `git remote -v` |
| **Daily Work** | `git checkout dev` <br> `git add .` <br> `git commit -m "msg"` <br> `git push origin dev` |
| **Sync Upstream** | `git checkout main` <br> `git pull upstream main` <br> `git push origin main` <br> `git checkout dev` <br> `git merge main` |
