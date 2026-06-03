---
name: quotation-generator
description: |
  Generate both a customer-facing quotation and an internal pricing-review workbook from RFQ + supplier quotation using Dewin workflow defaults. Trigger phrase: 生成报价单
---

# Generate Quotation — Full Workflow SOP

Use this skill when the user says **“生成报价单”**.

This skill is a fully standardized Dewin quotation workflow. The goal is not only to generate files, but to **reproduce the same high-quality quotation package consistently** without asking the user to repeat formatting or process preferences every time.

---

## 1. Default Deliverables

Unless the user explicitly says otherwise, always generate **both** files:

1. **Customer quotation**
2. **Internal pricing-review workbook（报价核算）**

Do **not** ask whether the internal version is needed. It is required by default.

---

## 2. Language Rules

### Customer quotation
- Must be **English only**.
- Customer-facing wording should be professional, commercial, and natural.
- Do not expose internal calculation logic, supplier cost, markup, or internal risk commentary.

### Internal pricing-review workbook（报价核算）
- Must be **Chinese by default**.
- Titles, section headers, column names, notes, risk comments, recommendations, and summary text should all be in Chinese.
- Technical identifiers may remain in original format where necessary, including:
  - part numbers
  - material grades
  - standards
  - RFQ abbreviations
  - process abbreviations

---

## 3. File Naming Rules

Use current local date unless user explicitly gives another date.

### Format
- `YYYYMMDD_Quotation_<PartDescriptionShort>_Dewin_<OpportunityNo>.xlsx`
- `YYYYMMDD_报价核算_<PartDescriptionShort>_Dewin_<OpportunityNo>.xlsx`

### Rules for `<PartDescriptionShort>`
- Derive from the RFQ part name(s), translated to English for the customer file.
- Include the quantity of line items as `x<N>` when there are multiple parts (e.g., `x2`, `x3`).
- Keep it concise but descriptive (e.g., `Zinc Die Casting Parts x2`, `Magnesium Shell x1`).
- Use Title Case with spaces (not underscores inside the description).

### Examples
- `20260527_Quotation_Zinc Die Casting Parts x2_Dewin_O5818.xlsx`
- `20260527_报价核算_Zinc Die Casting Parts x2_Dewin_O5818.xlsx`

### Anti-mixup rule
- Internal file name must **not** contain the word `Quotation`.
- This is to reduce accidental send-out to customers.

---

## 4. Input Requirements

Read all relevant source files available for the opportunity, including when present:
- RFQ workbook
- supplier quotation workbook
- RFQ requirement document
- drawings / spec files
- notes extracted from attachments

Typical required user inputs:
- opportunity number
- RFQ files
- supplier quotation file
- exchange rate, if user provides one
- markup / profit rule, if user provides one

If markup or exchange rate is not explicitly provided, infer only when safe or ask only if truly critical.

---

## 5. Mandatory Extraction Checklist

Before generating files, extract and structure the following:

### Line-item quotation data
- Dewin Number / opportunity line code
- part number
- part description
- quantity
- supplier unit price
- supplier material / finish
- RFQ material / finish
- main process
- lead time
- payment term
- trade term

### Mandatory customer clarification data
Extract **all** customer confirmation points and gaps from:
- RFQ sheet
- RFQ requirement document
- drawing notes
- supplier quotation gaps

### Additional cost / refund items
Always extract and include, even when blank:
- tooling cost
- sample cost
- tooling refund condition
- sample refund / sample offset / sample price deduction condition

If any such item is mentioned anywhere but value is incomplete, it must still be present in the output as a blank or pending field.

---

## 6. Dewin Number Rule

- Preserve RFQ / supplier Dewin numbering when already clearly available.
- If missing, generate sequential numbering within the provided opportunity.
- Keep numbering stable and aligned to the opportunity structure.

---

## 7. Pricing Rules

### Base rule
- Customer quotation currency: **USD**
- Exchange rate interpretation example:
  - user says `6.7` → use **1 USD = 6.7 RMB**

### Markup rule
If user says markup like `+12%`:
- `sell_price_rmb = supplier_price_rmb × 1.12`
- `unit_price_usd = sell_price_rmb / exchange_rate`

---

## 8. Customer Quotation Rounding Rule

Customer-facing quotation uses integer pricing by default.

### Rule
- Unit Price and Amount must have **no decimals**.
- Always **round upward** when decimal exists.

### Formula
- `customer_unit_price_usd = ceil(unit_price_usd)`
- `customer_amount_usd = customer_unit_price_usd × qty`

### Examples
- 1.01 → 2
- 5.98 → 6
- 14.00 → 14

---

## 9. Internal Pricing-Review Calculation Rule

Internal pricing-review workbook must **not** force integer rounding for the pricing review columns.

### The following 3 columns must preserve formula logic whenever practical:
1. converted USD price before rounding
2. customer unit price calculation column
3. customer amount calculation column

### Default behavior
- Keep original raw calculation values
- Do not auto-round these 3 columns to integers
- Prefer keeping formula structure in workbook when practical
- Internal workbook is for review, not customer presentation

---

## 10. Customer Quotation Layout Rules

### Fixed style intent
- clean commercial look
- English only
- one-sheet visibility preferred
- customer-friendly structure

### Header rules
Include at least:
- company heading
- To
- RFQ No.
- PD No.
- Date
- Currency
- Trade Term
- Exchange Rate

### Important header rule
- Remove the title text **`CUSTOMER QUOTATION`** by default
- Keep only the company heading / practical header block unless user explicitly asks to restore it

### Main quotation table columns
- Dewin Number
- Part Number
- Part Description
- Qty
- Unit Price (USD)
- Amount (USD)

### Total row rule
- In Unit Price column, place text: `Total Amount (USD)`
- In Amount column, place grand total integer
- Do not sum unit prices numerically in the Unit Price column

---

## 10A. Advanced Layout: Process Cost Breakdown + Tiered Pricing

Use this layout **only** when **either or both** of the following conditions apply:
- The supplier provided a **process cost breakdown** (i.e., unit price is itemised by manufacturing step, e.g. Raw Material, Die Casting, CNC Machining, Surface Treatment, etc.)
- The customer **requested tiered quantity pricing** (e.g., 1K / 10K / 50K unit price bands)

If neither condition applies, fall back to the standard compact layout (Section 10).

### Trigger detection
Before generating the file, check:
1. Does the supplier quotation contain per-process cost columns? → Use process breakdown layout
2. Did the user or RFQ request multiple quantity tiers? → Add tiered price columns
3. Both? → Full advanced layout below

---

### Advanced layout structure

#### Header block (rows 1–8)
| Row | Content |
|-----|---------|
| 1 | Company heading merged across full width — `Dewin Technology — Supplier Quotation` — dark navy fill (`1F4E79`), white font, size 14, height 30 |
| 2 | `To:` / customer company name |
| 3 | `Attn:` / contact person name |
| 4 | `RFQ No.:` / opportunity number |
| 5 | `Date:` / YYYY-MM-DD |
| 6 | `Currency:` / USD |
| 7 | `Trade Term:` / e.g. FOB Shenzhen |
| 8 | Empty row (height 24, used as visual spacer) |

Note: `Attn:` is **mandatory** in this layout (unlike standard layout where it is optional).

#### Process group sub-header (row 9)
- Merge columns G through P (or whatever spans the process cost columns) → label: `MOQ 1K — Unit Price Breakdown (USD)`
- Fill: medium blue (`2E75B6`), white font, centered
- This sub-header sits directly above the column headers row

#### Column header row (row 10) — dark navy fill (`1F4E79`), white font, size 8, centered, height 44
Fixed left columns:
- A: `Dewin Number`
- B: `Part Number`
- C: `Part Description`
- D: `Material`
- E: `Wt (g)`
- F: `Rev`

Process cost columns (medium blue fill `2E75B6`, white font, size 7) — adapt to actual processes quoted by supplier:
- G: `Raw Material + Scrap Loss`
- H: first process (e.g. `Die Casting`)
- I: second process (e.g. `Deburring`)
- J–N: remaining processes as quoted (e.g. `CNC Machining`, `Grinding & Polishing`, surface treatments)
- O: `Inspection & Pkg` (always last process column)

Price / commercial columns (dark navy fill `1F4E79`, white font):
- P: `Unit Price 1K (USD)` — formula: `=SUM(G:O)` for that row
- Q: `Unit Price 10K (USD)` — formula: `=P-delta` (delta per tier agreed with supplier)
- R: `Unit Price 50K (USD)` — formula: `=Q-delta`
- S: `Tooling Cost (USD)`
- T: `Remarks`

Tiered price column fills:
- 1K column (P): dark navy `1F4E79`
- 10K column (Q): darker navy `1F3864`
- 50K column (R): dark red `4A0000`

If only 1K pricing is available (no tiers), omit Q and R columns.

#### Data rows (row 11 onwards)
- Odd rows: no fill (white)
- Even rows: light blue fill (`D9E1F2`) across all columns A–T
- All cells: thin border, font size 8
- Price result cells (P, Q, R columns): light fill to visually distinguish
  - P (1K): `BDD7EE`
  - Q (10K): `C6EFCE`
  - R (50K): `FFD7CC`
- Tooling column (S): yellow fill `FFF2CC`
- Remarks (T): wrap text, font size 7, left-aligned

Remarks cell content format example:
```
Mold: 1×2 cavity | Life: 50K shots
Sample: 5 pcs free; add quantity at 1K price
Tooling: not refundable
```

#### TOTAL row
- Merge A:F → label `TOTAL`, dark navy fill, white font, right-aligned
- Only S column (Tooling) has a formula: `=SUM(S11:S<last_data_row>)`
- Fill S total cell: amber/orange `E6A817`, white font
- P/Q/R columns in TOTAL row: leave blank (do not sum unit prices)

#### Clarification section (rows starting 2 after TOTAL row)
- Title: merge A:D → `Clarification & Confirmation Items` — light blue fill `BDD7EE`, dark navy font `1F4E79`, size 10, centered, height 20
- Column headers row: No. / Item / Status / Priority — medium blue fill `2E75B6`, white font
- Data rows: columns A–D, wrap text
  - High priority rows: red fill `FFCCCC`
  - Medium priority rows: yellow fill `FFFF99`
  - Low priority rows: no fill
- Sort: High → Medium → Low

---

### When process columns differ across opportunities
- Always adapt the process column labels to match what the supplier actually quoted
- Do not hardcode the O5818 process list — it was specific to Mg die casting with painting
- The `SUM(G:O)` formula range in P column must always span exactly the process columns used

---

## 11. Customer Clarification Section Rules

If clarification / confirmation points are required, keep them on the **same sheet**.

Do **not** move them to Sheet2 unless user explicitly asks.

### Section purpose
- ensure customer sees all important pending points
- avoid missing technical / commercial alignment issues

### Customer-friendly wording rule
Avoid blunt internal wording like:
- Open
- Pending
- Missing

Prefer customer-friendly commercial English such as:
- Under technical review
- Commercial clarification in progress
- Documentation in preparation
- To be confirmed with supplier
- To be submitted after supplier confirmation
- Available
- Partially available

### Priority ordering rule
Sort clarification items by priority:
1. High Priority
2. Medium Priority
3. Low Priority

### Priority-based highlighting rule
The customer file clarification section should use customer-appropriate highlighting:
- High Priority → red-tinted highlight
- Medium Priority → yellow-tinted highlight
- Low Priority → normal / light styling

Use highlighting on clarification rows and/or priority/status cells so key items are visually obvious but still professional.

---

## 12. Internal Pricing-Review Workbook Layout Rules

The internal workbook is a Chinese review tool for Dewin only.

### Required intent
It should help internal review quickly understand:
- supplier cost base
- calculation chain
- raw converted values
- deviations vs RFQ
- pending confirmation items
- risk level
- recommendation

### Recommended internal columns
Include where available:
- 德闻编号
- 图号/品名
- 内部品名
- 数量
- 供应商单价(RMB)
- 加价
- 汇率
- 换算后USD(未取整)
- 客户单价USD(公式结果)
- 客户金额USD(公式结果)
- RFQ要求
- 供应商报价摘要
- 偏差/缺口
- 风险等级
- 内部备注

### Mandatory internal sections
- 关键风险提示
- 客户待确认点跟踪
- 内部建议
- 结论

### Internal anti-send header rule
The internal file should prominently include wording like:
- `仅内部使用`
- `禁止外发客户`

---

## 13. Internal Highlight Logic

The internal workbook must use strong visual risk emphasis.

### Red emphasis
Use red-tinted logic for:
- 高风险
- 重大RFQ偏差
- 材质不符
- 厚度不符且影响符合性
- 关键必答项缺失，可能影响通过

### Yellow emphasis
Use yellow-tinted logic for:
- 中风险
- 信息不完整
- 商务信息待澄清
- 包装/产能/工艺资料待补充

### Normal styling
Use normal / light styling for:
- 低风险
- 已提供信息
- 说明性内容

### Highlight application targets
Apply this logic directly where practical to:
- 风险等级 cells
- 偏差/缺口 cells
- 关键风险提示 rows
- 内部建议 rows
- 待确认事项的严重程度 cells

---

## 14. Commercial Terms Defaults

If user does not specify otherwise, use:
- material / finish from supplier quote or RFQ
- main process from supplier quote if available
- lead time from supplier quote if available
- payment term from supplier quote if available
- trade term from RFQ if available
- remarks for spec-change disclaimer / pending final confirmation where necessary

---

## 15. Gap Handling Rules

### Supplier did not quote an item
- Do not omit it if RFQ requires it
- Include it and leave value blank if necessary

### RFQ requires confirmation but supplier did not answer
- Add to clarification section
- Add to internal tracking section
- mark with appropriate priority / severity

### Multiple supplier options exist
- Customer file should use the safest / selected offer basis unless user instructs otherwise
- Internal file should preserve recommendation note and mention alternative option(s)

---

## 16. Workflow Procedure

Execute in this order:

1. Read RFQ files
2. Read supplier quotation files
3. Read template binding spec
4. Extract all line items
5. **Detect layout mode** — check if supplier provided process cost breakdown OR customer/RFQ requested tiered pricing
   - Yes → use Section 10A advanced layout
   - No → use Section 10 standard layout
6. Extract RFQ requirements and confirmation points
7. Extract tooling / sample / refund items even if blank
8. Detect RFQ vs supplier deviations
9. Apply markup and FX conversion
10. Apply upward integer rounding **only** to customer quotation
11. Preserve raw calculation values / formulas in internal workbook
12. Generate customer file in English using detected layout
13. Generate internal file in Chinese
14. Apply customer priority highlighting
15. Apply internal red/yellow warning logic
16. Save both files to Downloads using fixed naming convention
17. Reply with saved paths and concise summary (mention which layout was used)

---

## 17. Output Quality Checklist

Before finishing, verify:

### Customer file
- English only
- no `CUSTOMER QUOTATION` title unless requested
- same-sheet clarification section when needed
- priority sorting correct
- priority color highlighting applied
- all required confirmation items included
- tooling / sample / refund items included even if blank
- customer pricing rounded upward to integers
- **if advanced layout used**: verify process columns span matches SUM formula in Unit Price column; verify tiered price delta formulas are correct; verify Tooling TOTAL row only sums S column

### Internal file
- Chinese by default
- file name uses `报价核算`
- contains anti-send wording
- 3 pricing columns keep raw calculation logic
- not integer-rounded in those formula review columns
- red/yellow highlighting applied correctly
- RFQ deviations visible
- tooling / sample / refund items included
- missing mandatory RFQ items tracked

---

## 18. Guardrails

- Do not expose supplier raw price or markup math in customer file unless user explicitly asks.
- Do not hide critical pending items in another sheet by default.
- Do not omit tooling/sample/refund fields just because supplier left them blank.
- Internal file should remain clearly distinguishable from customer file.
- Prefer consistency and repeatability over ad hoc formatting changes.

---

## 19. Reusable Default Convention

For future runs of this skill, assume all of the following by default:
- generate both customer quotation and internal pricing-review workbook
- customer file is English
- internal file is Chinese
- customer file name uses `Quotation`
- internal file name uses `报价核算`
- customer file removes `CUSTOMER QUOTATION`
- customer file rounds prices upward to integers
- internal file preserves raw calculation values for the 3 pricing columns
- same-sheet clarification section when needed
- customer clarification items are priority-sorted and color-highlighted
- internal workbook uses red/yellow risk logic
- tooling/sample/refund-related items always included even when blank

This skill should now be treated as the standardized Dewin quotation workflow unless the user explicitly overrides a rule.
