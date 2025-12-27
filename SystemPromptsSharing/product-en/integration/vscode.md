# Integration Example: VS Code + GitHub Copilot

> **Note**: This is a simplified English version. For the complete Chinese version, see [product-zh-TW/integration/vscode.md](../../product-zh-TW/integration/vscode.md).

## Prerequisites

- VS Code v1.85+
- GitHub Copilot Extension
- GitHub Copilot subscription

---

## Installation Steps

### Step 1: Create Instructions File

**Option A: Project-level (Recommended)**
```bash
# Windows PowerShell
New-Item -ItemType Directory -Force -Path .github
Copy-Item prompts/query-only.md .github/copilot-instructions.md
```

**Option B: Global**
```bash
# Windows
Copy-Item prompts/query-only.md $HOME\.copilot-instructions.md
```

### Step 2: Choose Mode

**Query-Only (Safest, Default):**
```bash
.\install-query-only.bat
```

**Query+Action (Recommended):**
```bash
.\install-query-action.bat
```

**Full Solo (Advanced):**
```bash
.\install-full-solo.bat
```

### Step 3: Restart VS Code

Close and reopen VS Code to load new settings.

### Step 4: Verify

Ask in Copilot Chat: "What mode are you in?"

**Expected Response:**
```
I'm in Query Mode (default). For execution, use authorization words.
```

---

## Usage Examples

### Query Mode
```
You: "Analyze this architecture"
AI: [Analysis + suggestions, no execution]
```

### Action Mode  
```
You: "Execute: create README.md"
AI: [Risk assessment → Confirmation → Execute]
```

### Solo Mode
```
You: "Enter Solo mode, refactor entire project"
AI: [Plan → Checkpoints → Progress reports]
```

---

## Troubleshooting

**Instructions not loading?**
- Restart VS Code
- Check file path: `.github/copilot-instructions.md`
- Verify file is not empty

**AI not following instructions?**
- Re-copy prompt file
- Clear VS Code cache
- Check GitHub Copilot subscription status

---

## References

- [README.md](../README.md)
- [Installation Guide](../README_INSTALL.md)
- [System Prompts](../prompts/)

---

**Last Updated**: 2025-12-17  
**Framework Version**: Query/Action/Solo v1.1
