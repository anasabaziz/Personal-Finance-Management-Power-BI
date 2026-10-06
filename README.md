# Personal Finance Management Dashboard

A Power BI project for combining bank account and credit card transactions, reviewing income and expenses, and tracking allocations to Wants, Needs, and Investments. The supplied transaction workbooks contain dummy sample transactions.

**After cloning or downloading this repository, change `TransactionDataFolder` to the data folder on your own PC and customise `TransactionListing` for the bank/account queries you use.** The saved parameter contains the author's local path and will not automatically follow your repository location.

## Report and model

- **Financial Health:** income, expenses, net cash flow, savings rate, and monthly cash flow comparisons.
- **Spending Detail:** transaction and category analysis, with Wants, Needs, and Investments allocation measures.
- **TransactionCategory_dim:** category mapping imported from Excel.
- **Owner_dim:** source-to-owner mapping; the sample uses Husband and Wife.
- **DateCalendar:** calendar derived from transaction dates.
- **Refresh Info:** refresh information.

`TransactionListing` relates to the calendar by date, categories by `SubCategory`, and owners by `Source`. Amount measures use Malaysian ringgit (RM) formatting.

## Repository structure

```text
Personal-Finance-Management-Power-BI/
├── DataSource/
│   ├── Affin Credit Card.xlsx
│   ├── Affin Debit Card.xlsx
│   ├── HSBC Credit Card.xlsx
│   ├── HSBC Debit Card.xlsx
│   ├── Maybank Debit Card.xlsx
│   ├── CIMB Debit Card.xlsx
│   ├── CIMB Petronas Credit Card.xlsx
│   ├── CIMB Rebate Credit Card.xlsx
│   └── Transaction Category.xlsx
├── PBIP_Files/
│   ├── Personal_Finance_Dashboard.pbip
│   ├── Personal_Finance_Dashboard.Report/
│   │   ├── definition.pbir
│   │   ├── definition/       # PBIR pages, visuals, and report settings
│   │   └── StaticResources/  # Report themes
│   └── Personal_Finance_Dashboard.SemanticModel/
│       ├── definition.pbism
│       ├── definition/       # TMDL tables, queries, and relationships
│       ├── DAXQueries/       # Saved DAX query scripts
│       └── diagramLayout.json
├── README.md
└── LICENSE
```

Keep the `.pbip` file and its Report and SemanticModel folders together so their relative references continue to work.

## Requirements

Use a current **Power BI Desktop for Windows** version that supports PBIP, TMDL, and PBIR. In **File > Options and settings > Options > Preview features**, enable **Power BI Project (.pbip) save option** if required by your version, then restart Desktop. See Microsoft's [Power BI projects documentation](https://learn.microsoft.com/en-us/power-bi/developer/projects/projects-overview) for current setup details.

This project imports prepared Excel tables. It does not parse bank statement PDFs directly; convert or enter statements into the format below before refreshing.

## Set up your copy

### 1. Open the project

Clone or download the repository and open [`PBIP_Files/Personal_Finance_Dashboard.pbip`](./PBIP_Files/Personal_Finance_Dashboard.pbip) in Power BI Desktop. Select **Home > Transform data** to open Power Query Editor. A file-not-found error before updating the parameter can result from the saved local path.

### 2. Update `TransactionDataFolder`

1. In Power Query Editor, select **Home > Manage Parameters > Edit Parameters**.
2. Select **TransactionDataFolder** and keep its type as **Text**.
3. Change **Current Value** to the full folder path containing your transaction workbooks and `Transaction Category.xlsx`. For example:

   ```text
   C:\Users\YourName\Documents\Personal-Finance-Management-Power-BI\DataSource
   ```

4. Select **OK**, then check a bank query and `TransactionCategory_dim` to confirm their sources resolve.

Enter the **folder path**, without quotation marks or a trailing backslash. Do not enter an individual workbook path or the `PBIP_Files` path. A separate data folder is also supported if it contains the required files. Update the parameter whenever you move the data folder.

All eight bank queries and the category query use this parameter. Workbook filenames are fixed in each query, so renaming a file also requires changing that query's Source step.

### 3. Prepare your Excel data

Each bank workbook must contain an **Excel table named `Table1`** with these columns. A worksheet name alone is insufficient.

| Column | Required contents |
| --- | --- |
| `Source` | Consistent bank/account label matching `Owner_dim[Source]` |
| `Date` | Valid transaction date |
| `Description` | Transaction description as text |
| `Amount` | Numeric signed amount; the sample uses negative expenses and positive income |
| `SubCategory` | Text matching the category workbook's subcategory mapping |

Replace dummy rows with your own transactions. Extend the Excel table to include all new rows. Multiple monthly statements for the same account can be consolidated into its `Table1`; avoid importing the same transaction twice.

`Transaction Category.xlsx` also uses `Table1`, with `Financial Type`, `Category`, `SubCategory`, and `Allocation Type` columns. Keep one unique mapping per `SubCategory` and add mappings for new subcategories. Measures expect `Expense` and `Income` financial types and `Wants`, `Needs`, and `Investments` allocation labels. The sample also maps credit card payments and cash transfers to `Asset`, with `Payment` and `Transfer` allocation labels.

### 4. Customise `TransactionListing` for your bank statements/accounts

The project currently appends eight queries:

| Query name | Workbook in the data folder |
| --- | --- |
| `Affin_Credit_Card` | `Affin Credit Card.xlsx` |
| `Affin_Debit_Card` | `Affin Debit Card.xlsx` |
| `HSBC_Credit_Card` | `HSBC Credit Card.xlsx` |
| `HSBC_Debit_Card` | `HSBC Debit Card.xlsx` |
| `Maybank_Debit_Card` | `Maybank Debit Card.xlsx` |
| `CIMB_Debit_Card` | `CIMB Debit Card.xlsx` |
| `CIMB_Petronas_Credit_Card` | `CIMB Petronas Credit Card.xlsx` |
| `CIMB_Rebate_Credit_card` | `CIMB Rebate Credit Card.xlsx` |

**Change the append list to match the sources you actually use.** The number of queries depends on your bank/account workbooks, rather than the number of monthly statements consolidated into each workbook.

1. Select **TransactionListing** in Power Query Editor.
2. In **Applied Steps**, use the gear beside **Source** to edit the append selection, if available. Select only the transaction queries you need; use **Three or more tables** for three or more sources.
3. Alternatively, select **Home > Advanced Editor** and change the query list inside `Table.Combine`. Keep the existing date-sorting step.

For a user with only Affin Credit Card and Maybank Debit Card, the query becomes:

```powerquery
let
    Source = Table.Combine({Affin_Credit_Card, Maybank_Debit_Card}),
    #"Sorted Rows" = Table.Sort(Source, {{"Date", Order.Ascending}})
in
    #"Sorted Rows"
```

For one account, use a single query in the list, for example `Table.Combine({Affin_Credit_Card})`. Append transaction queries only; exclude category, owner, calendar, and refresh tables.

- **Fewer accounts:** remove unwanted queries from the append list first. Then delete unused bank queries if no longer needed, and remove their owner mappings. This avoids references to missing workbooks and prevents sample transactions from appearing in your results.
- **More accounts or a different bank:** duplicate an existing bank query, give it a unique name, and change its Source step to the new workbook filename while retaining `TransactionDataFolder`. Use the same `Table1` schema, keep the staging query's **Enable load** off, and add it to the append list. Add its source label to `Owner_dim`.

### 5. Update owners, apply changes, and refresh

Select **Owner_dim** and edit its Source step or Advanced Editor to replace the sample Husband/Wife mapping with your owners and account labels. Keep one row per unique `Source`, matching the labels in your transaction workbooks. Update these mappings whenever you add or remove accounts.

Select **Close & Apply**, then **Refresh** in Desktop and save the project. Confirm that only your selected accounts appear, category and owner filters work, dates cover your transactions, and totals agree with your prepared source data.

Keep at least one transaction with a valid date. The current calendar query derives its start and end dates from `TransactionListing` and does not handle an empty transaction list.

## Model conventions and report definitions

- Money uses Fixed Decimal Number in Power Query and decimal storage in the model. Monetary measures show RM with two decimals; summary cards can use whole RM. Percentages use one decimal.
- Calendar numbers do not summarize. The date column is the date-table key. Month and weekday labels use their existing numeric sorting fields; technical sorting fields are hidden.
- Measures are organized into Cash Flow, Rates and Allocation, Period Comparisons, Technical, and Data Quality folders. Tables, columns, and measures have descriptions. The existing Positive Amount measure remains available to its Top N filter and is hidden from general authoring.
- Source and signed Amount remain visible for transaction investigation. Signed transaction amounts distinguish inflows from outflows; the overview Spending card uses positive Expense Amount.
- Spending includes savings and investment contributions because the current category mapping classifies them as Expense. Transfers and credit card repayments are excluded from income and expense measures.
- The displayed Surplus rate (%) is the existing Savings Rate calculation: Net Cash Flow divided by Total Income. Surplus is after savings and investment contributions classified as spending; it does not measure total savings contributions. Needs, Wants, and Investments are shares of combined allocated outflows, not percentages of income.
- Information icons explain the summary and allocation calculations. Both pages have visual alternative text and an explicit reading order. Monthly series use different line styles and markers as well as colours.

### Verification of the convention and accessibility update

Source checks covered 4,360 sample transactions and 192 account-month groups. Fixed-decimal conversion preserves every account-month total at cent precision. Category keys are unique, all transaction categories have mappings, and existing DAX expressions and lineage tags are preserved. The pre-existing page-order edit is preserved.

Both pages were reloaded and visually reviewed in Power BI Desktop on 6 October 2026. Allocation percentages, surplus labels, the refresh timestamp, and the restaurant subcategory label fit their containers. The monthly legend uses Income, Spending, and Surplus. Transaction headers use white text on a dark slate background, and all four allocation choices are visible without scrolling. Validation reported no errors and five existing warnings (filter annotations and an unavailable visual schema). Keyboard, screen-reader, and operating-system high-contrast behaviour have not been tested. The sample contains future-dated transactions through December 2026.

The Microsoft report validator reports zero errors and five warnings. Four warnings concern existing `Entity` references inside filter metadata annotations; the actual filter conditions use their `From` aliases correctly, so those annotations are preserved. The fifth warning is an unavailable Microsoft visual-container 2.13.0 schema, which prevents full schema validation of eight existing visuals. Their original schema versions are preserved. Additional checks confirm that all 20 visual bindings resolve, every visual has alternative text, reading orders are unique, and visual bounds do not overlap. Configured text contrast exceeds 4.5:1 and monthly-series colour contrast exceeds 3:1 on the configured light surfaces.

## Troubleshooting

| Issue | What to check |
| --- | --- |
| File or folder not found | Update `TransactionDataFolder` and confirm selected queries' workbook filenames. |
| `Table1` not found | Format the source range as an Excel table and name it `Table1`. |
| Missing column or conversion error | Preserve required column names and use valid dates and numeric amounts. |
| Unexpected sample data in totals | Replace dummy rows and remove unused queries from the append list. |
| Blank category or owner | Match `SubCategory` and `Source` labels to their dimension mappings. |
| Duplicate dimension key | Keep `SubCategory` unique in the category mapping and `Source` unique in the owner mapping. |

## License

This project is licensed under the MIT License. See [LICENSE](./LICENSE).
