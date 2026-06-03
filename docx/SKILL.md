---
name: docx
description: "Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. When needs to work with professional documents (.docx files) for: (1) Creating new documents, (2) Modifying or editing content, (3) Working with tracked changes, (4) Adding comments, or any other document tasks"
license: Proprietary. LICENSE.txt has complete terms
---

# DOCX Creation, Editing, and Analysis

## OpenXML Element Order (CRITICAL — violations corrupt files)

| Parent | Required order |
|--------|---------------|
| `w:p` | `pPr` → runs (`w:r`, `w:ins`, `w:del`…) |
| `w:r` | `rPr` → `w:t` / `w:br` / `w:tab` |
| `w:tbl` | `tblPr` → `tblGrid` → `tr` |
| `w:tr` | `trPr` → `tc` |
| `w:tc` | `tcPr` → `p` (at least one `<w:p/>`) |
| `w:body` | block content → `sectPr` (LAST child) |
| `w:pPr` | `pStyle` → `numPr` → `spacing` → `ind` → `jc` |

---

## Workflow Decision Tree

### Reading/Analyzing Content
→ Text extraction or Raw XML access (below)

### Creating New Document
→ docx-js workflow (below)

### Editing Existing Document
- Own document + simple changes → Basic OOXML editing
- Someone else's document → **Redlining workflow** (recommended default)
- Legal, academic, business, government → **Redlining workflow** (required)

---

## Reading and Analyzing Content

### Text extraction
```bash
pandoc --track-changes=all path-to-file.docx -o output.md
```

### Raw XML access
```bash
python ooxml/scripts/unpack.py <office_file> <output_directory>
```
Key files: `word/document.xml`, `word/comments.xml`, `word/media/`

---

## Creating a New Document

### Step 1: Select an aesthetic recipe (REQUIRED before writing any code)

Choose a recipe from `references/aesthetic_recipes.md`:

| Document Type | Recipe |
|--------------|--------|
| Modern business report | Modern Corporate |
| Chinese government doc | GB/T 9704 |
| Chinese thesis | Academic CJK |
| Academic paper (English) | APA 7th or IEEE |
| Executive brief | Executive Brief |

All font sizes, spacing, and margins come from the recipe. Do not invent values.

### Step 2: Chinese/Japanese/Korean documents — read CJK guide first

**If the document contains CJK text**, read `references/cjk_typography.md` before writing any code. Key rules:

- **`w:eastAsia` is REQUIRED** — `font: "Arial"` alone does nothing for Chinese text
- Use `firstLineChars: 200` for 2-char first-line indent (scales with font size)
- Enable `autoSpaceDE` / `autoSpaceDN` for proper CJK-Latin spacing

```javascript
// WRONG — Chinese text silently uses wrong font
new TextRun({ text: "中文内容", font: "Calibri" })

// CORRECT — set eastAsia at document defaults level
const doc = new Document({
  styles: {
    default: {
      document: {
        run: {
          font: { name: "Calibri", eastAsia: "Microsoft YaHei" },
          size: 24  // 12pt
        }
      }
    }
  }
})
```

### Step 3: Read docx-js.md and write code

**MANDATORY**: Read [`docx-js.md`](docx-js.md) completely before writing any code.

```javascript
const { Document, Packer, Paragraph, TextRun, HeadingLevel } = require('docx');
const fs = require('fs');

const doc = new Document({
  styles: {
    default: {
      document: {
        run: { font: { name: "Calibri", eastAsia: "Microsoft YaHei" }, size: 22 }
      }
    },
    paragraphStyles: [
      { id: "Heading1", name: "Heading 1", basedOn: "Normal",
        run: { font: { name: "Calibri Light" }, size: 40, bold: true, color: "1F3864" },
        paragraph: { spacing: { before: 480, after: 240 }, outlineLevel: 0 }
      }
    ]
  },
  sections: [{ children: [/* content */] }]
});

Packer.toBuffer(doc).then(buf => fs.writeFileSync('output.docx', buf));
```

---

## Editing an Existing Document

**MANDATORY**: Read [`ooxml.md`](ooxml.md) completely before writing any code.

```bash
python ooxml/scripts/unpack.py input.docx unpacked/
# edit XML
python ooxml/scripts/pack.py unpacked/ output.docx
```

---

## Redlining Workflow

### Minimal, Precise Edits Principle (CRITICAL)

**Only mark text that actually changes. Never wrap unchanged text in `<w:del>`/`<w:ins>`.**

```python
# WRONG — entire sentence marked, reviewer cannot see what changed
'<w:del><w:r><w:delText>付款期限为30天。</w:delText></w:r></w:del>'
'<w:ins><w:r><w:t>付款期限为60天。</w:t></w:r></w:ins>'

# CORRECT — only "30" marked, surrounding text preserved with original <w:r>
'<w:r w:rsidR="00AB12CD"><w:t xml:space="preserve">付款期限为</w:t></w:r>'
'<w:del w:id="1" w:author="AI" w:date="2026-01-01T00:00:00Z">'
'  <w:r><w:delText>30</w:delText></w:r>'
'</w:del>'
'<w:ins w:id="2" w:author="AI" w:date="2026-01-01T00:00:00Z">'
'  <w:r><w:t>60</w:t></w:r>'
'</w:ins>'
'<w:r w:rsidR="00AB12CD"><w:t>天。</w:t></w:r>'
```

When preserving unchanged text, copy the original `<w:r>` element with its `w:rsidR` and `rPr` intact.

### Workflow

1. Extract text: `pandoc --track-changes=all file.docx -o current.md`
2. Identify and group changes into batches of 3–10 (by section, type, or proximity)
3. Read `ooxml.md`, unpack: `python ooxml/scripts/unpack.py file.docx unpacked/`
4. For each batch: grep XML to find exact run structure → implement minimal changes
5. Pack: `python ooxml/scripts/pack.py unpacked/ reviewed.docx`
6. Verify: `pandoc --track-changes=all reviewed.docx -o verification.md`

**Always grep `word/document.xml` immediately before writing a script** — line numbers change after every edit.

### Method selection

| Scenario | Method |
|---------|--------|
| Adding changes to regular text | `replace_node()` with `<w:del>`/`<w:ins>` |
| Removing a complete `<w:r>` or `<w:p>` | `suggest_deletion()` |
| Rejecting another author's insertion | `revert_insertion()` — NOT `suggest_deletion()` |
| Rejecting another author's deletion | `revert_deletion()` |

---

## Converting Documents to Images

```bash
soffice --headless --convert-to pdf document.docx
pdftoppm -jpeg -r 150 document.pdf page
```

---

## Reference Files

| File | When to read |
|------|-------------|
| `docx-js.md` | Creating new documents (read completely) |
| `ooxml.md` | Editing existing documents (read completely) |
| `references/cjk_typography.md` | **Any document with Chinese/Japanese/Korean text** |
| `references/aesthetic_recipes.md` | Choosing font/spacing/margin values |

---

## Dependencies

- `npm install -g docx` — creating new documents
- `pandoc` — text extraction
- `pip install defusedxml` — secure XML parsing
- `LibreOffice` — PDF conversion
- `poppler-utils` — PDF to image conversion

---

## Code Style

- Concise code, no unnecessary comments or print statements
- **Always** set `w:eastAsia` for documents containing CJK text
- **Always** pick an aesthetic recipe before writing formatting code
- Handle line breaks with `Paragraph` elements, never `\n` in `TextRun`
