# Agent Learnings

## File Structure

```
amex/
├── statements/              # Canonical monthly Amex statements (source data)
│   ├── 2025/
│   │   ├── 2025-05.csv
│   │   ├── 2025-06.csv
│   │   └── ...
│   └── 2026/
├── trips/                   # Trip expense summaries
│   ├── 2025/
│   │   ├── argentina_oct-nov.csv
│   │   ├── merida_dec.csv
│   │   ├── nyc_dec27-jan1.csv
│   │   └── yucatan_jul.csv
│   └── 2026/
├── work_expenses/           # Work expenses for reimbursement tracking
│   ├── 2025.csv
│   └── 2026.csv
├── AGENTS.md                # This file - agent instructions
└── CLAUDE.md                # Project instructions
```

### Naming Conventions
- **Statements**: `YYYY-MM.csv` (e.g., `2025-01.csv` for January 2025)
- **Trips**: `{destination}_{month(s)}.csv` organized by year folder
- **Work Expenses**: `{year}.csv` (one file per year)

### Column Schema (for derived files)

Both `work_expenses/{year}.csv` and `trips/{year}/*.csv` use this unified schema:

```
Date,Description,Category,Amount_MXN,Amount_USD,Source_File,Status
```

| Column | Description |
|--------|-------------|
| Date | Transaction date (YYYY-MM-DD) |
| Description | Merchant/vendor name |
| Category | Expense category (see classifications below) |
| Amount_MXN | Amount in Mexican Pesos |
| Amount_USD | Amount in US Dollars |
| Source_File | Original monthly CSV filename for traceability |
| Status | REIMBURSED, blank, or other tracking status |

---

## Processing New Monthly CSV Files

When a new monthly CSV is added (e.g., `statements/2026/2026-01.csv`), follow these steps:

### Step 1: Check Watchlist
Search the new CSV for all patterns in the Watchlist section below. Flag any matches for user review.

### Step 2: Identify Work Expenses
Search for known work expense vendors (see classifications below) and add new entries to `work_expenses/{year}.csv` with:
- Appropriate category
- USD conversion (use current exchange rate if not provided)
- Source_File pointing to the new CSV
- Status left blank (user marks as REIMBURSED later)

### Step 3: Check for Trip-Related Expenses
If the user mentions a trip, or if charges match known trip patterns:
- Create a new file in `trips/{year}/` named `{destination}_{month(s)}.csv`
- Include all related charges with categories: Flights, Accommodation, Transportation, Food, Shopping, Entertainment, Services, Refund

### Step 4: Currency Conversion
- If USD amount is missing, calculate using current MXN/USD exchange rate
- Note: Amex CSVs sometimes include USD in format like `"1,150.04 USD"` or as separate column

---

## Expense Classification

### NOT Work Expenses
- **Starlink** - Personal internet service
- **X Corp Paid Features** - Personal social media
- **YouTube Premium** - Personal entertainment
- **Google Nest** - Personal smart home
- **Whitepaper.mx** - Personal

### Work Expenses (AI Services)
- **Anthropic** - AI/LLM service
- **OpenAI / CHATGPT SUBSCR** - AI/LLM service
- **CLAUDE.AI SUBSCRIPTION** - AI/LLM service

### Work Expenses (Cloud/Infrastructure)
- **AWS EMEA** - Cloud infrastructure
- **PAYU-GOOGLE CLOUD** - Cloud services
- **ANKR PBC** - Blockchain infrastructure

### Work Expenses (Education/Research)
- **Bankless** - Web3/blockchain industry research

### Work Expenses (Developer Tools)
- **GITHUB, INC.** - Code repository/collaboration

### Work Expenses (Communication)
- **Discord Nitro** - Work communication (annual subscription)

### Work Expenses (Meals)
- Business meals (requires user confirmation)

### Potentially Work (Needs Confirmation)
- **Google One** - Storage (could be personal or work)

---

## Watchlist - Flag When Found

When processing new monthly CSV files, search for and flag these vendors:

| Vendor | Search Pattern | Reason | First Seen |
|--------|---------------|--------|------------|
| Mariemur | `MARIEMUR` | Monitor for unexpected new charges | Dec 2025 (378.32 + 7,245.88 MXN expected) |
| NYC Trip - Hotel | See hotel patterns below | Check for extra hotel charges (Dec 27 - Jan 1 trip) | Dec 2025 (64,428.62 MXN / 3,449.62 USD booked via Travelocity) |
| NYC Trip - In-trip expenses | See hotel + location patterns | **January CSV will have actual trip expenses** (Uber, meals, etc.) | Trip: Dec 27, 2025 - Jan 1, 2026 |

### Hotel/Accommodation Search Patterns

When searching for hotel-related charges, use these inclusive patterns:

**Booking platforms:**
```
TRAVELOCITY|EXPEDIA|BOOKING\.COM|HOTELS\.COM|AIRBNB|VRBO|KAYAK|HOTWIRE|PRICELINE|AGODA
```

**Major hotel chains:**
```
MARRIOTT|HILTON|HYATT|IHG|WYNDHAM|BEST WESTERN|RADISSON|SHERATON|WESTIN|RITZ|FOUR SEASONS|HOLIDAY INN|HAMPTON|DOUBLETREE|EMBASSY SUITES|COURTYARD|FAIRFIELD|RESIDENCE INN|SPRINGHILL|ALOFT|W HOTEL|ST REGIS|WALDORF|CONRAD
```

**Generic hotel terms:**
```
HOTEL|MOTEL|INN|RESORT|SUITES|LODGING|ACCOMMODATION
```

**Location-specific (for NYC trip):**
```
NEW YORK|NYC|MANHATTAN|BROOKLYN|QUEENS
```

**Combined search for NYC hotel extras:**
```
(HOTEL|MARRIOTT|HILTON|HYATT|INN|RESORT).*(NEW YORK|NYC|MANHATTAN)|(NEW YORK|NYC|MANHATTAN).*(HOTEL|ROOM|STAY)
```

> **Note:** Hotel incidentals (minibar, room service, parking) often appear as separate charges under the hotel's name, not the booking platform.

---

## Trip History

| Trip | Dates | File | Total (MXN) | Total (USD) | Notes |
|------|-------|------|-------------|-------------|-------|
| Yucatan | Jul 2025 | `trips/2025/yucatan_jul.csv` | ~14,944 | ~840 | Short trip; dates TBD |
| Argentina | Oct 26 - Nov 22, 2025 | `trips/2025/argentina_oct-nov.csv` | ~274,000 | ~15,400 | Complete |
| Merida | Dec 14-21, 2025 | `trips/2025/merida_dec.csv` | ~27,645 | ~1,553 | Complete |
| NYC | Dec 27, 2025 - Jan 1, 2026 | `trips/2025/nyc_dec27-jan1.csv` | ~116,100 | ~6,216 | Pre-trip bookings only; in-trip expenses in 2026-01.csv |

---

## Unresolved Items (Need User Clarification)

### August 2025 Vivaaerobus Charges
- Aug 3: 8,277.77 MXN
- Aug 8: 16,677.27 MXN + 1,559.53 MXN
- **Total: 26,514.57 MXN**
- **Missed flights** - booked but not taken, no trip to track

### Burning Man 2025
- Apr 30: BURNINGMAN ticket 33,731.13 MXN (in 2025-05.csv)
- **Did not attend** - tickets only, no trip to track
