# JSON Format Comparison: OLD vs NEW

## Side-by-Side Comparison

### OLD FORMAT (Current - INSUFFICIENT) ❌

```json
{
  "metadata": {
    "project_id": "PROJ_0050",
    "region": "West Bengal",
    "supplier": "Six-Fours-les-Plages Materials Ltd."
  },
  "horizon_days": 120,
  "forecast_start": "2024-02-19",
  "forecast_end": "2024-06-17",
  "total_predicted_demand": 6801.873366807411,
  "forecast": [
    {
      "date": "2024-02-19",
      "y_hat": 59.11,
      "y_lower": 0.0,
      "y_upper": 0.0
    },
    {
      "date": "2024-02-20",
      "y_hat": 56.67,
      "y_lower": 0.0,
      "y_upper": 0.0
    }
  ]
}
```

**Problems:**
- ❌ No per-material breakdown
- ❌ No actual values
- ❌ No last year values
- ❌ No material metadata
- ❌ Confidence bounds are all 0.0
- ❌ Cannot show "Forecast vs Actual vs Last Year"
- ❌ Cannot select individual materials
- ❌ Charts show incomplete data

---

### NEW FORMAT (Required) ✅

```json
{
  "metadata": {
    "project_id": "PROJ_0050",
    "region": "West Bengal",
    "supplier": "Six-Fours-les-Plages Materials Ltd.",
    "generated_at": "2024-02-19T10:30:00Z",
    "model_version": "v1.0"
  },
  "horizon_days": 120,
  "forecast_start": "2024-02-19",
  "forecast_end": "2024-06-17",
  "total_predicted_demand": 6801.873366807411,
  
  "materials": [
    {
      "material_id": "MAT_001",
      "material_name": "Steel Rods (TMT)",
      "category": "Structural",
      "unit": "MT",
      "supplier": "Tata Steel",
      "lead_time_days": 14,
      "total_predicted_demand": 2500.5,
      "forecast": [
        {
          "date": "2024-02-19",
          "y_hat": 59.11,
          "y_lower": 45.2,
          "y_upper": 73.0,
          "actual": 58.5,
          "last_year": 52.3
        },
        {
          "date": "2024-02-20",
          "y_hat": 56.67,
          "y_lower": 42.8,
          "y_upper": 70.5,
          "actual": null,
          "last_year": 51.2
        }
      ]
    },
    {
      "material_id": "MAT_002",
      "material_name": "Cement (Grade 53)",
      "category": "Construction",
      "unit": "MT",
      "supplier": "UltraTech",
      "lead_time_days": 7,
      "total_predicted_demand": 4301.37,
      "forecast": [
        {
          "date": "2024-02-19",
          "y_hat": 120.5,
          "y_lower": 95.0,
          "y_upper": 146.0,
          "actual": 118.2,
          "last_year": 110.5
        },
        {
          "date": "2024-02-20",
          "y_hat": 118.3,
          "y_lower": 92.8,
          "y_upper": 143.8,
          "actual": null,
          "last_year": 109.3
        }
      ]
    }
  ],
  
  "aggregated_forecast": [
    {
      "date": "2024-02-19",
      "y_hat": 214.81,
      "y_lower": 168.2,
      "y_upper": 261.4,
      "actual": 211.5,
      "last_year": 194.9
    },
    {
      "date": "2024-02-20",
      "y_hat": 209.07,
      "y_lower": 162.5,
      "y_upper": 255.6,
      "actual": null,
      "last_year": 192.0
    }
  ]
}
```

**Benefits:**
- ✅ Per-material forecasts with dropdown selection
- ✅ Actual values for comparison
- ✅ Last year values for trends
- ✅ Material metadata (category, supplier, lead time)
- ✅ Real confidence bounds
- ✅ "Forecast vs Actual vs Last Year" charts
- ✅ Material-specific analysis
- ✅ Complete, professional dashboard

---

## Field-by-Field Comparison

| Field | OLD | NEW | Purpose |
|-------|-----|-----|---------|
| `metadata.project_id` | ✅ | ✅ | Project identifier |
| `metadata.region` | ✅ | ✅ | Geographic region |
| `metadata.supplier` | ✅ | ✅ | Primary supplier |
| `metadata.generated_at` | ❌ | ✅ | Timestamp |
| `metadata.model_version` | ❌ | ✅ | Model version |
| `horizon_days` | ✅ | ✅ | Forecast period |
| `forecast_start` | ✅ | ✅ | Start date |
| `forecast_end` | ✅ | ✅ | End date |
| `total_predicted_demand` | ✅ | ✅ | Total demand |
| `forecast` | ✅ Single array | ❌ Removed | Replaced by materials + aggregated |
| `materials` | ❌ | ✅ Array | Per-material forecasts |
| `materials[].material_id` | ❌ | ✅ | Unique ID |
| `materials[].material_name` | ❌ | ✅ | Display name |
| `materials[].category` | ❌ | ✅ | Material category |
| `materials[].unit` | ❌ | ✅ | Unit of measurement |
| `materials[].supplier` | ❌ | ✅ | Material supplier |
| `materials[].lead_time_days` | ❌ | ✅ | Lead time |
| `materials[].forecast[].actual` | ❌ | ✅ | Actual consumption |
| `materials[].forecast[].last_year` | ❌ | ✅ | Historical data |
| `aggregated_forecast` | ❌ | ✅ Array | Total across materials |
| `aggregated_forecast[].actual` | ❌ | ✅ | Total actual |
| `aggregated_forecast[].last_year` | ❌ | ✅ | Total last year |

---

## What Each Addition Enables

### `materials` Array
**Enables:**
- Dropdown to select specific material
- Per-material forecast charts
- Material-specific details display
- Category-based filtering
- Supplier-based analysis

**Example UI:**
```
[Dropdown: Select Material ▼]
  - Steel Rods (TMT)
  - Cement (Grade 53)
  - Copper Wire
  - Transformers
```

### `actual` Field
**Enables:**
- "Forecast vs Actual" comparison
- Forecast accuracy calculation
- Performance tracking
- Green line on charts showing real consumption

**Example Chart:**
```
Forecast (white line): Predicted values
Actual (green line): Real consumption
```

### `last_year` Field
**Enables:**
- Year-over-year trend analysis
- Growth rate calculation
- Seasonal pattern identification
- Gray line on charts showing historical data

**Example Chart:**
```
Forecast (white): This year prediction
Actual (green): This year actual
Last Year (gray): Same period last year
```

### `y_lower` and `y_upper` (Real Values)
**Enables:**
- Confidence band visualization
- Uncertainty quantification
- Risk assessment
- Shaded area around forecast line

**Example Chart:**
```
Forecast line with shaded confidence band
Upper bound: y_upper
Lower bound: y_lower
```

### `aggregated_forecast` Array
**Enables:**
- Total demand overview
- Cross-material analysis
- Portfolio-level forecasting
- Home page summary chart

---

## Migration Path

### Step 1: Understand Current Limitations
Your current JSON only shows:
- One aggregated forecast
- No comparison data
- No material breakdown
- Incomplete charts

### Step 2: Gather Required Data
For each material, collect:
1. ML model predictions (y_hat)
2. Confidence intervals (y_lower, y_upper)
3. Actual consumption from database
4. Historical data from last year
5. Material metadata (category, supplier, etc.)

### Step 3: Update ML Pipeline
Modify your pipeline to:
1. Run predictions for each material separately
2. Calculate confidence intervals
3. Query actual consumption data
4. Query historical data
5. Aggregate across materials

### Step 4: Generate New JSON
Use the Python function in `ML_MODEL_INTEGRATION_GUIDE.md`

### Step 5: Validate
Check against validation checklist in `JSON_REQUIREMENTS_SUMMARY.md`

### Step 6: Deploy
Replace `forecast_120d.json` with new format

---

## Quick Reference

**Files to Check:**
1. `REQUIRED_JSON_STRUCTURE.json` - Complete example
2. `ML_MODEL_INTEGRATION_GUIDE.md` - Full documentation + Python code
3. `JSON_REQUIREMENTS_SUMMARY.md` - Quick summary
4. `OLD_VS_NEW_JSON_COMPARISON.md` - This file

**Key Takeaway:**
Your current JSON is a **single aggregated forecast**.
The new JSON needs **multiple materials + actual data + historical data**.

