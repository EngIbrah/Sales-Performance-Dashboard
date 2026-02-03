# 📦 GitHub Repository Setup Guide

Complete step-by-step instructions to create and upload your Sales Performance Dashboard to GitHub.

---

## 📋 Prerequisites

Before you begin, make sure you have:

- ✅ Git installed on your computer ([Download Git](https://git-scm.com/downloads))
- ✅ GitHub account ([Sign up](https://github.com/join))
- ✅ All project files ready

---

## 📁 Step 1: Organize Your Files

Create this folder structure on your computer:

```
sales-performance-dashboard/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── data/
│   ├── actual-vs-target-dataset.xlsx
│   └── data-dictionary.md
│
├── powerbi/
│   ├── Sales-Performance-Dashboard.pbix
│   └── dax-formulas.md
│
├── docs/
│   └── Sales_Performance_Report.docx
│
└── screenshots/
    ├── dashboard-overview.png
    ├── kpi-cards.png
    ├── monthly-trend.png
    └── salesperson-table.png
```

---

## 📸 Step 2: Prepare Your Screenshots

### Take 4 Key Screenshots from Power BI:

1. **dashboard-overview.png**
   - Show entire dashboard
   - Resolution: 1920x1080 recommended
   - Format: PNG

2. **kpi-cards.png**
   - Close-up of KPI section (left panel)
   - Show all 5 cards clearly

3. **monthly-trend.png**
   - Capture the bar chart with variance indicators
   - Include chart title

4. **salesperson-table.png**
   - Show the performance table with all columns
   - Include team filter panel

### Screenshot Tips:
- Use **Windows:** `Win + Shift + S` → Rectangle snip
- Use **Mac:** `Cmd + Shift + 4`
- Use **Power BI:** Export visual as image
- Save all as PNG format for best quality

---

## 🌐 Step 3: Create GitHub Repository

### Option A: Using GitHub Website (Easier)

1. Go to [GitHub.com](https://github.com)
2. Click **"+"** in top-right → **"New repository"**
3. Fill in:
   - **Repository name:** `sales-performance-dashboard`
   - **Description:** "Interactive Power BI dashboard analyzing YTD sales performance vs targets"
   - **Visibility:** Public (or Private if preferred)
   - **☑️ Add a README file** - UNCHECK this (we have our own)
   - **Add .gitignore:** None (we have our own)
   - **Choose a license:** None (we have our own)
4. Click **"Create repository"**

### Option B: Using Git Command Line (Advanced)

Wait for Step 5 - we'll create it from command line.

---

## 💻 Step 4: Initialize Local Repository

Open terminal/command prompt in your project folder:

```bash
# Navigate to your project folder
cd /path/to/sales-performance-dashboard

# Initialize Git repository
git init

# Add all files to staging
git add .

# Create first commit
git commit -m "Initial commit: Sales Performance Dashboard"
```

---

## ⬆️ Step 5: Push to GitHub

### If you created repository on GitHub (Option A):

```bash
# Link local repo to GitHub (replace YOUR-USERNAME)
git remote add origin https://github.com/YOUR-USERNAME/sales-performance-dashboard.git

# Verify remote is added
git remote -v

# Push to GitHub
git branch -M main
git push -u origin main
```

### If creating new repository (Option B):

```bash
# Create repository using GitHub CLI (if installed)
gh repo create sales-performance-dashboard --public --source=. --remote=origin

# Or push to create new repo
git remote add origin https://github.com/YOUR-USERNAME/sales-performance-dashboard.git
git branch -M main
git push -u origin main
```

### 🔑 Authentication

If prompted for credentials:
- **Username:** Your GitHub username
- **Password:** Use a [Personal Access Token](https://github.com/settings/tokens) (not your actual password)

**To create a token:**
1. Go to: Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate new token
3. Select scopes: `repo`
4. Copy token (you won't see it again!)
5. Use token as password when pushing

---

## ✅ Step 6: Verify Upload

1. Go to `https://github.com/YOUR-USERNAME/sales-performance-dashboard`
2. Check that all files are visible:
   - ✅ README.md displays properly
   - ✅ Screenshots appear in README
   - ✅ Folder structure is correct
   - ✅ All files uploaded

---

## 🎨 Step 7: Customize Repository

### Add Topics (Tags)
1. Go to your repository
2. Click ⚙️ near "About"
3. Add topics:
   - `power-bi`
   - `data-visualization`
   - `business-intelligence`
   - `dax`
   - `sales-analytics`
   - `dashboard`

### Update Repository Description
Add a clear description in the "About" section

### Enable GitHub Pages (Optional)
If you want to host documentation:
1. Settings → Pages
2. Source: Deploy from branch
3. Branch: main, folder: /docs

---

## 🔄 Step 8: Future Updates

When you make changes:

```bash
# Check status
git status

# Add changed files
git add .

# Or add specific files
git add README.md powerbi/Sales-Performance-Dashboard.pbix

# Commit with descriptive message
git commit -m "Update: Added Q2 2024 data and new KPIs"

# Push to GitHub
git push origin main
```

---

## 📝 Common Git Commands

| Command | Purpose |
|---------|---------|
| `git status` | Check which files changed |
| `git add <file>` | Stage specific file |
| `git add .` | Stage all changes |
| `git commit -m "message"` | Save changes with message |
| `git push` | Upload to GitHub |
| `git pull` | Download from GitHub |
| `git log` | View commit history |
| `git branch` | List branches |

---

## ⚠️ Troubleshooting

### Problem: "Permission denied (publickey)"
**Solution:** Set up SSH key or use HTTPS with token

```bash
# Switch to HTTPS
git remote set-url origin https://github.com/YOUR-USERNAME/sales-performance-dashboard.git
```

### Problem: "Repository not found"
**Solution:** Check URL and ensure repository exists

```bash
# Verify remote URL
git remote -v

# Update if wrong
git remote set-url origin <correct-url>
```

### Problem: Screenshots not showing in README
**Solution:** 
- Verify file paths are correct
- Check image files are in `screenshots/` folder
- Use relative paths: `![Description](screenshots/filename.png)`

### Problem: Large file error (.pbix too big)
**Solution:** 
- GitHub has 100MB file limit
- If .pbix > 100MB, use [Git LFS](https://git-lfs.github.com/)
- Or host large files elsewhere and link to them

```bash
# Install Git LFS
git lfs install

# Track .pbix files
git lfs track "*.pbix"

# Add .gitattributes
git add .gitattributes

# Then add and commit as normal
git add powerbi/Sales-Performance-Dashboard.pbix
git commit -m "Add Power BI file with LFS"
git push
```

---

## 📊 Final Checklist

Before going live, verify:

- [ ] README renders correctly with all formatting
- [ ] All screenshots are visible and clear
- [ ] Links work (click each one)
- [ ] Contact information updated (email, LinkedIn)
- [ ] License file includes your name
- [ ] .gitignore prevents temp files
- [ ] Repository description is clear
- [ ] Topics/tags added
- [ ] No sensitive data in files
- [ ] File structure matches documentation
- [ ] Power BI file opens correctly after download
- [ ] Excel dataset contains all sheets

---

## 🎓 Git Best Practices

### Commit Message Format

```
<type>: <short description>

<optional detailed description>

Examples:
- feat: Add salesperson comparison feature
- fix: Correct YTD variance calculation
- docs: Update README with new screenshots
- refactor: Optimize DAX measures
- style: Format code and documentation
```

### What NOT to Commit

- ❌ Temporary files (~$*.xlsx)
- ❌ Large binary files (use Git LFS)
- ❌ Sensitive data (passwords, API keys)
- ❌ Personal information
- ❌ Backup files (.bak)
- ❌ OS-specific files (.DS_Store)

All of these are already in your .gitignore file.

---

## 🔗 Useful Resources

- [GitHub Documentation](https://docs.github.com)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Markdown Guide](https://www.markdownguide.org/)
- [Git LFS Documentation](https://git-lfs.github.com/)

---

## 🎉 You're Done!

Your professional Power BI dashboard is now on GitHub! 🚀

### Next Steps:
1. Share the link on LinkedIn
2. Add to your resume/portfolio
3. Star your own repository
4. Consider creating a GitHub Pages site
5. Keep updating as you add features

---

**Need help?** Open an issue in your repository or check [GitHub Community](https://github.community/)
