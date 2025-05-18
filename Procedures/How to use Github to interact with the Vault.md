
==Git can be confusing, if you are added to the vault to make changes, below is a general guideline to get started==

## 🔐 Step 1: Set Up a Personal Access Token (PAT)
### 1. Go to GitHub:

- Navigate to [https://github.com/settings/tokens](https://github.com/settings/tokens)
    
### 2. Click **"Generate new token (classic)"**

- Alternatively, go to **Developer settings > Personal access tokens > Tokens (classic)**
    
### 3. Configure the token:

- **Name**: Something like `Vault Access Token`
- **Expiration**: 30/60/90 days or no expiration
- **Scopes**:
    - ✅ `repo` (for full access to private repositories)
    - Optionally: `workflow` if you need to run actions
### 4. Click **Generate token**
- ⚠️ **Copy the token now**. You won’t be able to see it again.
    
---

## 🛠️ Step 2: Use the Token for Git Authentication

If Git prompts you for a username/password when pushing:
- **Username**: Your GitHub username
- **Password**: Paste your **PAT** instead of your GitHub password

Or cache it with:
`git config --global credential.helper store`
Then next time you push and enter your credentials, Git will save them.

---

## 🧱 Step 3: General Git Workflow for Vault Edits

Assume you're working on a branch (`Lamoreaux/Additions`) and want to make and submit changes.
### 🔄 1. Clone the repo (if not already):
`git clone https://github.com/CedarvilleCyber/RedTeamVault.git cd RedTeamVault`
### 🌿 2. Create or switch to your branch:
`git switch -c Lamoreaux/Additions # OR switch if it exists: git checkout Lamoreaux/Additions`

### ✍️ 3. Make your changes locally

### ✅ 4. Stage and commit:

`git add -A                 # Adds all changes (new, modified, deleted) git commit -m "Your message here"`
### 🚀 5. Push your changes:

`git push origin Lamoreaux/Additions`
### 🔁 6. Open a Pull Request:
- Go to GitHub → Click **"Compare & pull request"**
- Describe your change → Submit

==Below is valuable if you want to not have to use the git command line and make changes right from Obsidian==
## 🔧 How to Create and Customize Hotkeys in Obsidian

### 🧭 Step-by-Step Instructions

1. **Open Obsidian**
2. Click on **Settings** (gear icon in the left sidebar)
3. Navigate to **Hotkeys**
4. Use the search bar to find a specific command (e.g., `Toggle preview`, `Create new note`)
5. Click on the command you want to assign a hotkey to
6. Press the key combination you want to use (e.g., `Ctrl + Alt + N`)
7. Obsidian will assign your custom hotkey