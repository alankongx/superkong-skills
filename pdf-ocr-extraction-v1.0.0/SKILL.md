---
name: pdf-ocr-extractor
version: 1.3.0
description: "Extract text from scanned or image-based PDFs using EasyOCR and save results as a Word (.docx) document. No API key required, works fully offline, unlimited usage. Trigger when the user wants to extract or convert text from a scanned PDF. Not suitable for native PDFs with selectable text — direct copy or a text-extraction tool is more efficient in that case."
metadata:
  clawhub:
    emoji: 📄
    requires:
      bins: [python3]
---

# PDF OCR Extractor 📄

Converts scanned or image-based PDFs into editable Word documents using EasyOCR.
**Fully offline. No API key. No system-level dependencies — pure pip install.**

---

## Step 1 — Install Dependencies (first-time setup)

```bash
pip install pymupdf python-docx pillow easyocr numpy
```

> **Note:** The first time the script runs, EasyOCR will automatically download the OCR
> model files (~100–500 MB depending on language). This requires an internet connection on
> the first run only. Subsequent runs are fully offline.

### Verify Installation

```bash
python -c "import easyocr; print('EasyOCR OK')"
```

---

## Step 2 — Confirm Input Details

Ask the user to confirm:
1. **PDF file path** — the scanned PDF to process
2. **Output directory** — where to save the `.docx` file (default: same folder as the PDF)
3. **OCR language** — comma-separated EasyOCR language codes (default: `ch_sim,en`)
   - `ch_sim,en` — Simplified Chinese + English
   - `en` — English only
   - `ja` — Japanese, `ko` — Korean, `fr` — French, etc.
   - Full list: https://www.jaided.ai/easyocr/

---

## Step 3 — Run the Script

```bash
# Basic usage (Simplified Chinese + English, output as .docx)
python3 {baseDir}/scripts/pdf_ocr.py <pdf_path> [output_dir] --lang ch_sim,en

# English only
python3 {baseDir}/scripts/pdf_ocr.py <pdf_path> [output_dir] --lang en

# Output plain text instead of Word
python3 {baseDir}/scripts/pdf_ocr.py <pdf_path> [output_dir] --output-format txt
```

> `{baseDir}` is automatically injected by EasyClaw as the skill's install directory. No manual replacement needed.

The script prints real-time progress for each page. Wait for it to complete.

---

## Step 4 — Post-Processing

Output file: `<original_name>_ocr.docx` in the specified output directory.

**If the output file is large (> 50 MB), compress embedded images:**
```bash
python3 {baseDir}/scripts/compress_docx.py <docx_path> <output_path>
# Optional: [max_image_width, default 600] [jpeg_quality, default 60]
```

---

## Page Handling Strategy

| Page type | Detection | Handling |
|-----------|-----------|----------|
| Normal text page | Default | Crop top 6% (header) + bottom 4% (footer), then OCR |
| Image/figure page | OCR returns no text | Kept as embedded image in Word |
| Colorful cover/chapter page | Colorful pixels > 25% | Kept as embedded image with a grey label |

Crop ratios and the color threshold can be adjusted at the top of `scripts/pdf_ocr.py`.

---

## Known Limitations

- **Mixed text-and-figure pages**: Text inside diagrams or charts will be extracted as body text — review and fix these pages manually
- **First-run model download**: Requires internet on first use per language set; subsequent runs are offline
- **Handwritten text**: EasyOCR accuracy on handwriting is limited — results will need manual review
- **GPU acceleration**: The script defaults to CPU. Set `gpu=True` in `get_reader()` if a CUDA GPU is available for faster processing

---

## When NOT to Use This Skill

- The PDF already has a native text layer (you can select/copy text directly) — use direct copy or a text-extraction tool instead
- The document is password-protected — decrypt it first
- You need cloud-level accuracy for high-volume batch processing — consider a dedicated OCR API service
