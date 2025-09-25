# Document Workflow Guide

This guide explains how to manage OMATrust documentation across Google Docs and GitHub, ensuring consistent formatting and professional presentation.

## 📋 Overview

Our documentation workflow maintains two versions:
- **Google Docs**: Rich formatting for collaboration and review
- **Markdown**: Clean formatting for GitHub and distribution

## 🔄 Complete Workflow

```
Google Docs → Export .rtf → Convert to Markdown → GitHub → PDF Release
     ↑              ↓              ↓           ↓         ↓
Collaboration   Local File    Clean .md    Version    Distribution
   & Review                   Control      Control
```

## 📝 Step-by-Step Process

### Step 1: Export from Google Docs
1. Open the document in Google Docs
2. **File → Download → Rich Text Format (.rtf)**
3. Save to your local project folder: `/omatrust-docs/temp/`
4. Name consistently: `whitepaper-draft-YYYY-MM-DD.rtf`

### Step 2: Convert to Markdown

#### Option A: Manual Copy-Paste (Recommended - Cross-Platform)
1. Open the `.rtf` file in any text editor (TextEdit, Notepad, VS Code, etc.)
2. Select all content and copy
3. Paste into AI assistant (Claude, ChatGPT, etc.) with conversion prompt below
4. Apply the style guide mapping
5. Save result as `.md` file

#### Option B: Using pandoc (Advanced - Cross-Platform)
1. Install pandoc: [pandoc.org](https://pandoc.org/installing.html)
2. Convert directly: `pandoc document.rtf -o document.md`
3. Apply conversion prompt to clean up formatting

#### Option C: Using Cursor (RTF Direct Read)
1. Open the `.rtf` file directly in Cursor (works on Mac/Windows/Linux)
2. Use the conversion prompt below
3. Apply the style guide mapping
4. Save as `.md` file

**Note**: RTF conversion produces consistent Unicode typography (including em dashes). This ensures clean diffs between RTF-converted documents.

### Step 3: Quality Check
1. Open `.md` file in Cursor or VS Code
2. **Preview markdown**: `Cmd+Shift+V` (Mac) or `Ctrl+Shift+V` (Windows)
3. Verify formatting matches GitHub rendering
4. Check all code blocks, tables, and links
5. Verify heading hierarchy is correct
6. **One-time only**: Handle Unicode consistency if transitioning from non-RTF workflow (see Common Issues section)

### Step 4: Update Repository
1. **Update version number** in document header if this is a new release
2. Replace existing `.md` file in `/omatrust-docs/`
3. Commit changes with descriptive message including version
4. Tag release if this is a major version

### Step 5: Generate PDF (if needed)
1. Export from GitHub or use pandoc
2. Save to `/omatrust-docs/releases/`

## 🎨 Style Guide Mapping

When converting from Google Docs to Markdown, apply these rules:

| Google Docs Formatting | Markdown Conversion | Use Case |
|------------------------|-------------------|----------|
| **Courier New + Gray Background** | `` `inline code` `` | Variable names, functions, short code |
| **Courier New + Blue Background** | `` ```language `` <br> `` code block `` <br> `` ``` `` | Multi-line code, JSON, commands |
| **Bold + Any Color** | `**emphasized text**` | Key terms, important concepts |
| **Italic** | `*italic text*` | Definitions, emphasis |
| **Title** | `# Document Title` | Main document title |
| **Heading 1** | `## Major Section` | Major sections (1., 2., 3.) |
| **Heading 2** | `### Subsection Title` | Subsections (1.1, 2.1, etc.) |
| **Heading 3** | `#### Sub-subsection` | Sub-subsections (1.1.1, 2.1.1, etc.) |
| **Bullet Points** | `- Item` or `* Item` | Lists |
| **Numbered Lists** | `1. Item` | Ordered lists |
| **Tables** | Markdown table syntax | Data presentation |
| **Hyperlinks** | `[text](url)` | External references |

### Special Cases
- **File paths**: `` `path/to/file` `` 
- **URLs**: `` `https://example.com` `` or `[Display Text](URL)`
- **Contract addresses**: `` `0x1234...` ``
- **DID identifiers**: `` `did:web:example.com` ``
- **Schema names**: `` `UserReview@1` ``

## 🤖 Conversion Prompts

### For Cursor/AI Assistant

#### Primary Conversion Prompt
```
Please convert this document content to clean Markdown format following these specific rules:

FORMATTING RULES:
1. Text in Courier New with gray background → `inline code`
2. Text in Courier New with blue/colored background → ```language code blocks```
3. Bold colored text → **emphasized text**
4. Regular bold → **bold** (preserve)
5. Italic → *italic* (preserve)
6. All hyperlinks → [display text](url)

STRUCTURE RULES:
1. Preserve exact heading hierarchy (H1, H2, H3)
2. Maintain numbered sections (1., 2., 2.1, 2.2, etc.)
3. Convert bullet points to markdown lists
4. Preserve table structure using markdown syntax
5. Keep paragraph breaks and spacing

TECHNICAL CONTENT:
1. Contract addresses, DIDs, and technical identifiers should be `inline code`
2. JSON examples should be ```json code blocks```
3. Shell commands should be ```bash code blocks```
4. URLs should be properly linked: [description](url)
5. File paths should be `inline code`

OUTPUT REQUIREMENTS:
- Clean, GitHub-compatible markdown
- No HTML tags unless absolutely necessary
- Consistent spacing and formatting
- All code blocks properly language-tagged

Here is the content to convert:

[PASTE CONTENT HERE]
```

#### Quality Check Prompt
```
Please review this markdown for:
1. Consistent code block formatting
2. Proper heading hierarchy
3. Working links
4. Table formatting
5. Missing backticks around technical terms

Here is the markdown to review:

[PASTE MARKDOWN HERE]
```

## 📁 File Organization

### Directory Structure
```
omatrust-docs/
├── DOCUMENT_WORKFLOW.md       # This file
├── README.md                  # Main entry point
├── specification.md           # Technical specification
├── whitepaper.md             # Project whitepaper
├── repositories.md           # Ecosystem overview
├── releases/                 # PDF versions
│   ├── specification-v1.0.pdf
│   └── whitepaper-v1.0.pdf
└── temp/                     # Working files
    ├── exports/              # .rtf files from Google Docs
    └── drafts/               # Work-in-progress .md files
```

### Naming Conventions
- **Google Docs exports**: `document-name-YYYY-MM-DD.rtf`
- **Draft markdown**: `document-name-draft-YYYY-MM-DD.md`
- **Final markdown**: `document-name.md`
- **PDF releases**: `document-name-v1.0.pdf`

### Version Format
- **Major releases**: `v1.0`, `v2.0` (breaking changes, new major features)
- **Minor releases**: `v1.1`, `v1.2` (new features, significant updates)
- **Patches**: `v1.0.1`, `v1.0.2` (bug fixes, minor corrections)
- **Drafts**: `v1.0-DRAFT`, `v2.0-DRAFT` (work in progress)

### Document Header Format

#### For Whitepapers and General Documents:
```markdown
# Document Title

**Version X.Y** | **Month YYYY** | **OMA3 Consortium**

---
```

#### For Technical Specifications:
Technical specifications use a **Change History table** at the end instead of header versioning:
```markdown
# Change History

|| Version | Date | Comments |
|| :---- | :---- | :---- |
|| 0.1 | 2024-11 | Initial draft - Author Name |
|| 1.0-DRAFT | 2024-12 | Complete draft with updates |
|| 1.0 | 2025-01 | Final release version |
```

This follows standard technical specification practices and provides a complete audit trail.

## ✅ Quality Checklist

Before finalizing any document conversion:

### Content Check
- [ ] All headings properly formatted and numbered
- [ ] All code examples in proper code blocks
- [ ] All technical terms have backticks
- [ ] All links work and are properly formatted
- [ ] Tables render correctly
- [ ] **No emojis used** - maintain professional standards consortium presentation
- [ ] **Version updated**: Header version (whitepapers) OR Change History table (specifications)
- [ ] **Date updated** to reflect current month/year
- [ ] **Unicode consistency**: RTF typography maintained (em dashes, smart quotes) for consistent future diffs

### Format Check  
- [ ] Preview in Cursor matches expected GitHub rendering
- [ ] No HTML tags where markdown should be used
- [ ] Consistent spacing between sections
- [ ] Code blocks have appropriate language tags
- [ ] No formatting artifacts from conversion

### Technical Check
- [ ] All contract addresses properly formatted
- [ ] All DIDs properly formatted
- [ ] All file paths properly formatted
- [ ] All API endpoints properly formatted
- [ ] JSON examples properly formatted and valid

## 🚨 Common Issues & Solutions

### Problem: Massive diffs when transitioning to RTF workflow
**Cause**: RTF conversion consistently uses Unicode em dashes (—) while existing files may use ASCII hyphens (-)

**Solution**: Accept RTF's consistent formatting rather than fighting it.

**For one-time transition** (if existing files have ASCII hyphens):
- **Option 1**: Convert existing files to match RTF output (em dashes)
- **Option 2**: Convert RTF output to match existing files (ASCII hyphens)

**For ongoing workflow**: Choose one approach and stick with it consistently.

**Recommended**: Use em dashes consistently since RTF naturally produces them. This eliminates the need for post-processing and ensures all future RTF conversions will diff cleanly.

**Why it happens**: RTF format standardizes to Unicode typography, including em dashes for better readability

### Problem: Incorrect heading hierarchy
**Cause**: Sub-subsections (3.2.1, 3.2.2, etc.) converted with wrong number of hash marks
**Solution**: Verify heading levels match this hierarchy:
- `##` = Major sections (1, 2, 3, 8, 9)
- `###` = Subsections (3.1, 3.2, 3.3)
- `####` = Sub-subsections (3.2.1, 3.2.2, 3.2.3)

### Problem: Code blocks not rendering
**Solution**: Ensure three backticks (```) on separate lines

### Problem: Tables broken
**Solution**: Check pipe alignment and spacing
```markdown
| Header 1 | Header 2 |
|----------|----------|
| Data 1   | Data 2   |
```

### Problem: Links not working
**Solution**: Use proper markdown link syntax: `[text](url)`

### Problem: Inline code broken
**Solution**: Ensure single backticks surround code: `` `code` ``

### Problem: Inconsistent formatting
**Solution**: Re-run conversion prompt with specific formatting rules

## 👥 Team Responsibilities

### Support Team
- Export documents from Google Docs (any platform)
- Run conversion process using provided cross-platform methods
- Quality check using preview and checklist
- Update GitHub repository
- Generate PDFs for releases folder

**Note**: All conversion methods work on Windows, Mac, and Linux. No platform-specific tools required.

### Technical Team
- Review technical accuracy of converted documents
- Approve final versions before release
- Update this workflow guide as needed

## 📞 Support

If you encounter issues not covered in this guide:
1. Check the [GitHub Issues](https://github.com/oma3dao/omatrust-docs/issues) for similar problems
2. Create a new issue with specific details
3. Tag relevant team members for review

---

*This workflow ensures consistent, professional documentation across all OMATrust materials while enabling efficient team collaboration.*
