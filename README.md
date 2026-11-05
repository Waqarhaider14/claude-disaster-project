# Global Disasters Dashboard

An interactive Power BI report that explores global natural-disaster events, with a focus on event counts, severity, duration, affected population, and year-over-year (YTD / PY) comparisons.

The project is stored as a **Power BI Project (PBIP)**, so the report and its semantic model are kept as plain-text files (JSON and TMDL) that work with Git.

---

## Features

- **Headline KPI:** total disaster records card, with YTD / MTD switching through a calculation group.
- **Severity and duration measures:** average severity and average duration, each with year-over-year (YoY) comparisons, available in the model for use in visuals.
- **Time analysis:** a line chart of disaster counts by date, with a granularity selector and YTD / MTD / PYTD / PMTD period switching through a calculation group.
- **Event-type breakdown:** a combo chart of disaster counts by event type (flood, earthquake, drought, wildfire, tropical cyclone, volcano, and others).
- **Geographic view:** a map of disaster counts by country.
- **Interactive filters (slicers):** date range, year, timeframe, country, event type, and date granularity. Cross-filtering is wired between visuals on the page.
- **Row-level security:** a `Country_role` role restricts users to the countries they are assigned to, matched on their sign-in (`USERPRINCIPALNAME()`).

## Data Model

The semantic model is a star schema built around one fact table.

| Table | Type | Purpose |
|---|---|---|
| `dw fact_disaster_event` | Fact | One row per disaster record: severity, duration, population affected, and keys to every dimension |
| `dw dim_date` | Dimension | Calendar dimension (`full_date`, `date_id`) used for start and end dates |
| `dw dim_location` | Dimension | Location, country name, ISO3 code |
| `dw dim_event_type` | Dimension | Disaster type, plus a slicer-friendly label |
| `dw dim_alert` / `dw dim_source` | Dimension | Alert level and data source |
| `dw dim_flood`, `dw dim_earthquake`, `dw dim_drought`, `dw dim_wild_fire`, `dw dim_tropical_cyclone`, `dw dim_volcano` | Dimension | Type-specific attributes, linked to the fact table by `disaster_event_id` |
| `Calculation group` | Calculation group | YTD and PM time-intelligence items |
| `Disaster Date Granularity`, `timeframe`, `Disaster Count Analysis` | Helper | Slicer and label helpers for the time views |
| `Table` | Security | Maps users (`email`) to countries for row-level security |

Data is imported from a PostgreSQL data warehouse.

### Key measures

- `DisasterRecord`, `DisasterCount` / `DistinctCount`: event counts
- `Average Severity`, `Average Duration (days)`, `MaxDuration`
- `Events YTD`, `Events PY YTD`, `Events YoY %`
- `Events Rolling 12M`, `Events Rolling 12M YoY %`
- `Affected Population YTD`, `Affected Population YoY %`
- `Average Severity YTD`, `Average Severity YoY %`
- `% of Disaster count`, `RankCountry`
- Time-intelligence measures: `_YTD`, `_MTD`, `_PYTD`, `_PMTD`, and the matching `*_label` and `Difference_*` measures

## Repository Structure

```
Global Disasters/
├── Disaster Dashboard.pbip            # Open this file in Power BI Desktop
├── .gitignore
├── Disaster Dashboard.Report/         # Report layer (pages, visuals, theme, images)
│   ├── definition.pbir                # Points to the semantic model
│   ├── definition/
│   │   ├── report.json                # Report settings and theme reference
│   │   ├── version.json
│   │   └── pages/                     # One folder per page, with its visuals
│   └── StaticResources/               # Theme (CY26SU02) and image assets
└── Disaster Dashboard.SemanticModel/  # Data layer (tables, measures, relationships, roles)
    ├── definition/
    │   ├── model.tmdl
    │   ├── relationships.tmdl
    │   ├── tables/                    # One .tmdl file per table, with its measures
    │   ├── roles/                     # Row-level security roles
    │   └── cultures/en-US.tmdl
    └── DAXQueries/                    # Saved DAX query scratch file
```

## Requirements

- **Power BI Desktop** (a recent version, with the PBIP / TMDL save format enabled in the preview features settings)
- **PostgreSQL** running locally, with a database named `dw_disaster_events` that contains the warehouse tables listed above. The database is **not** included in this repository; you need to create or restore it yourself.

## Getting Started

1. Clone the repository:
   ```bash
   git clone https://github.com/Waqarhaider14/Global-Disasters.git
   ```
2. Restore the `dw_disaster_events` PostgreSQL database on `localhost`.
3. Open `Disaster Dashboard.pbip` in Power BI Desktop.
4. If prompted, sign in to the PostgreSQL source with your credentials and refresh the data.
5. Use the slicers on the report page to explore the data.

To test row-level security, use *Modeling → View as* in Power BI Desktop and select the `Country_role` role.

## Notes on Editing

- Changes made in Power BI Desktop are saved as text files, so Git diffs show the actual changes to visuals, measures, and relationships.
- `.pbi/localSettings.json` and `.pbi/cache.abf` are ignored by `.gitignore` and should not be committed.

## Data Source

The dashboard's title is **"Global Disaster Overview 2000 - 2025"**, so the data covers the years 2000 to 2025. Add the original dataset source and license here (for example, a link to the public dataset you used).

## Author

**Waqar Haider Saddique**: created as part of Power BI learning practice.
