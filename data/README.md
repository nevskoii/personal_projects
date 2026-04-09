# Nexus Datacom Database

SQLite database with power monitoring data for 27 data centers and 113 server halls.

## File

| File | Description |
|------|-------------|
| `nexus_datacom.db` | SQLite database (primary data source) |

## Tables & Columns

### `data_centers`
| Column | Description |
|--------|-------------|
| dc_id | Primary key |
| dc_name | Data center name |
| dc_type | new / medium / moderate / legacy / critical |
| total_halls | Number of halls |
| base_power | Base capacity (kW) |

### `halls`
| Column | Description |
|--------|-------------|
| hall_id | Primary key |
| dc_id | Foreign key → data_centers |
| hall_name | Hall name (Hall-01, Hall-02...) |
| project_power | Projected capacity (kW) |

### `consumption`
| Column | Description |
|--------|-------------|
| id | Primary key |
| hall_id | Foreign key → halls |
| fact_power | Actual consumption (kW) |
| record_date | Date of record |

### `commercial`
| Column | Description |
|--------|-------------|
| id | Primary key |
| hall_id | Foreign key → halls |
| comm_power | Commercial power sold (kW) |
| record_date | Date of record |

## Quick Stats

| Metric | Value |
|--------|-------|
| Data centers | 27 |
| Server halls | 113 |
| Total capacity | 130 MW |
| Avg utilization | 60% |
| Avg sell-through | 108% |

## Load with Python

```python
import sqlite3
import pandas as pd

conn = sqlite3.connect('data/nexus_datacom.db')

# Load consumption data
df = pd.read_sql('''
    SELECT dc.dc_name, h.hall_name, h.project_power, c.fact_power
    FROM halls h
    JOIN data_centers dc ON h.dc_id = dc.dc_id
    JOIN consumption c ON h.hall_id = c.hall_id
''', conn)
