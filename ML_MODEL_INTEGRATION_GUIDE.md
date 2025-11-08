# ML Model JSON Integration Guide - UPDATED

## Overview
This document describes the **COMPLETE** JSON format required by the PRISM frontend to display ML model forecast data with:
- ✅ **Forecast vs Actual vs Last Year** comparison charts
- ✅ **Per-material forecasts** with dropdown selection
- ✅ **Material-specific details** (category, supplier, lead time)
- ✅ **Confidence intervals** for predictions

---

## ⚠️ CRITICAL CHANGES FROM PREVIOUS VERSION

The previous JSON structure was **INSUFFICIENT** because it only contained:
- Single aggregated forecast (no per-material breakdown)
- No actual values for comparison
- No last year values for trend analysis
- No material metadata (category, supplier, etc.)

**NEW REQUIREMENTS:**
1. **Per-Material Forecasts**: Each material must have its own forecast array
2. **Actual Values**: Include actual consumption data for dates that have passed
3. **Last Year Values**: Include historical data from the same period last year
4. **Material Metadata**: Category, supplier, lead time, unit of measurement
5. **Aggregated Forecast**: Total across all materials for overview charts

---

## JSON Structure

### Root Level Fields

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `metadata` | Object | Yes | Project and supplier information | See below |
| `horizon_days` | Integer | Yes | Number of days in the forecast period | `120` |
| `forecast_start` | String (ISO Date) | Yes | Start date of forecast period | `"2024-02-19"` |
| `forecast_end` | String (ISO Date) | Yes | End date of forecast period | `"2024-06-17"` |
| `total_predicted_demand` | Float | Yes | Sum of all predicted demand values across all materials | `6801.873366807411` |
| `materials` | Array | **YES (NEW)** | Array of material-specific forecasts | See below |
| `aggregated_forecast` | Array | **YES (NEW)** | Aggregated forecast across all materials | See below |

---

### Metadata Object

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `project_id` | String | Yes | Unique project identifier | `"PROJ_0050"` |
| `region` | String | Yes | Geographic region | `"West Bengal"` |
| `supplier` | String | Yes | Primary supplier company name | `"Six-Fours-les-Plages Materials Ltd."` |
| `generated_at` | String (ISO DateTime) | Optional | Timestamp when forecast was generated | `"2024-02-19T10:30:00Z"` |
| `model_version` | String | Optional | ML model version identifier | `"v1.0"` |

---

### Materials Array (NEW - REQUIRED)

Each item in the `materials` array represents a specific material/product with its own forecast:

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `material_id` | String | Yes | Unique material identifier | `"MAT_001"` |
| `material_name` | String | Yes | Display name of the material | `"Steel Rods (TMT)"` |
| `category` | String | Yes | Material category | `"Structural"`, `"Electrical"`, `"Construction"` |
| `unit` | String | Yes | Unit of measurement | `"MT"`, `"KM"`, `"Units"` |
| `supplier` | String | Yes | Supplier for this material | `"Tata Steel"` |
| `lead_time_days` | Integer | Yes | Lead time in days | `14` |
| `total_predicted_demand` | Float | Yes | Total predicted demand for this material | `2500.5` |
| `forecast` | Array | Yes | Daily forecast for this material | See "Material Forecast Item" below |

---

### Material Forecast Item

Each item in a material's `forecast` array:

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `date` | String (ISO Date) | Yes | Date of the prediction | `"2024-02-19"` |
| `y_hat` | Float | Yes | Predicted demand value | `59.11` |
| `y_lower` | Float | Yes | Lower confidence bound | `45.2` |
| `y_upper` | Float | Yes | Upper confidence bound | `73.0` |
| `actual` | Float or null | **YES (NEW)** | Actual consumption (null for future dates) | `58.5` or `null` |
| `last_year` | Float or null | **YES (NEW)** | Same date last year (null if not available) | `52.3` or `null` |

---

### Aggregated Forecast Array (NEW - REQUIRED)

Total forecast across all materials for overview charts:

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `date` | String (ISO Date) | Yes | Date of the prediction | `"2024-02-19"` |
| `y_hat` | Float | Yes | Total predicted demand across all materials | `214.81` |
| `y_lower` | Float | Yes | Total lower confidence bound | `168.2` |
| `y_upper` | Float | Yes | Total upper confidence bound | `261.4` |
| `actual` | Float or null | **YES (NEW)** | Total actual consumption (null for future) | `211.5` or `null` |
| `last_year` | Float or null | **YES (NEW)** | Total from same date last year | `194.9` or `null` |

---

## Complete Example (NEW STRUCTURE)

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

---

## Data Requirements

### Date Format
- **Format**: ISO 8601 date format (`YYYY-MM-DD`)
- **Example**: `"2024-02-19"`
- **Timezone**: Dates are treated as local time (no timezone offset)

### Materials Array
- **Minimum**: At least 1 material (recommended: 3-10 materials for realistic dashboard)
- **Each material's forecast array**: Must have exactly `horizon_days` items
- **Material IDs**: Must be unique across all materials
- **Categories**: Common values: `"Structural"`, `"Electrical"`, `"Construction"`, `"Equipment"`
- **Units**: Common values: `"MT"` (metric tons), `"KM"` (kilometers), `"Units"`, `"KG"`, `"Liters"`

### Aggregated Forecast Array
- **Length**: Must match `horizon_days` (e.g., 120 days = 120 array items)
- **Order**: Must be in chronological order (earliest to latest)
- **Continuity**: Dates should be consecutive (no gaps)
- **Values**: Each day's `y_hat` should equal the sum of all materials' `y_hat` for that day

### Actual and Last Year Values
- **Actual**: Set to `null` for future dates, provide actual values for past dates
- **Last Year**: Historical data from same period last year (can be `null` if not available)
- **Purpose**: Enables "Forecast vs Actual vs Last Year" comparison charts

### Numeric Values
- **Precision**: Any decimal precision is acceptable
- **Range**: All values must be non-negative (>= 0)
- **total_predicted_demand**: Should equal the sum of all materials' `total_predicted_demand`

---

## How the Frontend Uses This Data

### Home Page (`/`)
- **KPI Cards**: Displays total predicted demand, average daily demand, forecast horizon, project ID
- **Demand Trends Chart**: Uses `aggregated_forecast` array to show "Forecast vs Actual" comparison
  - Green line: `actual` values (where available)
  - Teal line: `y_hat` (forecast) values
  - Shows both historical performance and future predictions
- **Metadata Display**: Shows project, region, supplier in the UI

### Forecast Page (`/forecast`)
- **Material Selector Dropdown**: Lists all materials from `materials` array
  - User can select a specific material to view its forecast
  - Dropdown shows: `material_name` (e.g., "Steel Rods (TMT)")
- **Forecast Trend Chart**: Displays selected material's forecast with 3 lines:
  - **Forecast** (white/gray): `y_hat` values
  - **Actual** (green): `actual` values (where available)
  - **Last Year** (gray): `last_year` values for comparison
- **Confidence Bands**: Shows `y_lower` and `y_upper` as shaded area around forecast line
- **Material Details**: Displays category, supplier, lead time, unit from material metadata
- **Aggregated View**: Option to view total across all materials using `aggregated_forecast`

---

## Integration Steps

1. **Generate ML Model Output**: Your ML model should output JSON in the format specified above
2. **Save JSON File**: Save the output as `forecast_120d.json` (or any name) in the project root
3. **Update Import Path**: In the frontend code, update the import path if using a different filename:
   ```typescript
   import forecastDataJson from "../../../your_filename.json";
   ```
4. **Verify Data**: Check that:
   - All required fields are present
   - Dates are in ISO format
   - `forecast` array length matches `horizon_days`
   - `total_predicted_demand` equals sum of all `y_hat` values

---

## Validation Checklist

### Metadata
- [ ] `metadata.project_id` is a valid string
- [ ] `metadata.region` is a valid string
- [ ] `metadata.supplier` is a valid string
- [ ] `horizon_days` is a positive integer
- [ ] `forecast_start` is in `YYYY-MM-DD` format
- [ ] `forecast_end` is in `YYYY-MM-DD` format
- [ ] `forecast_end` is after `forecast_start`
- [ ] `total_predicted_demand` is a non-negative number

### Materials Array
- [ ] `materials` array exists and has at least 1 item
- [ ] Each material has unique `material_id`
- [ ] Each material has `material_name`, `category`, `unit`, `supplier`, `lead_time_days`
- [ ] Each material's `forecast` array has exactly `horizon_days` items
- [ ] Each material's `total_predicted_demand` equals sum of its `y_hat` values
- [ ] All material forecast items have `date`, `y_hat`, `y_lower`, `y_upper`, `actual`, `last_year`
- [ ] Dates in each material's forecast are consecutive and in order
- [ ] First date in each material's forecast matches `forecast_start`
- [ ] Last date in each material's forecast matches `forecast_end`

### Aggregated Forecast
- [ ] `aggregated_forecast` array exists and has exactly `horizon_days` items
- [ ] Each item has `date`, `y_hat`, `y_lower`, `y_upper`, `actual`, `last_year`
- [ ] Dates are consecutive and in order
- [ ] First date matches `forecast_start`
- [ ] Last date matches `forecast_end`
- [ ] Each day's `y_hat` equals sum of all materials' `y_hat` for that day
- [ ] `actual` is `null` for future dates, has value for past dates
- [ ] `last_year` has historical values (or `null` if not available)

---

## Notes

- **Confidence Bounds**: Provide actual `y_lower` and `y_upper` values for confidence bands visualization
- **Actual Values**:
  - For dates in the past: Provide actual consumption values
  - For future dates: Set to `null`
  - This enables "Forecast vs Actual" comparison
- **Last Year Values**:
  - Provide historical data from same period last year
  - Enables year-over-year trend analysis
  - Set to `null` if not available
- **Multiple Materials**:
  - Include 3-10 materials for a realistic dashboard
  - Each material should represent a different category/supplier
  - Common materials: Steel, Cement, Copper Wire, Transformers, Circuit Breakers, etc.
- **Multiple Projects**: To support multiple projects, create separate JSON files and switch between them in the UI
- **Real-time Updates**: For production, set up an API endpoint that serves this JSON format instead of static files

---

## Example Python Code to Generate JSON

```python
import json
from datetime import datetime, timedelta
from typing import List, Dict, Optional

def generate_forecast_json(
    project_id: str,
    region: str,
    primary_supplier: str,
    start_date: str,
    horizon_days: int,
    materials_data: List[Dict]
):
    """
    Generate forecast JSON in the NEW required format.

    Args:
        project_id: Project identifier (e.g., "PROJ_0050")
        region: Geographic region (e.g., "West Bengal")
        primary_supplier: Primary supplier name
        start_date: Start date in YYYY-MM-DD format
        horizon_days: Number of days to forecast
        materials_data: List of material dictionaries, each containing:
            - material_id: str
            - material_name: str
            - category: str
            - unit: str
            - supplier: str
            - lead_time_days: int
            - predictions: List[float] (y_hat values)
            - confidence_lower: List[float] (y_lower values)
            - confidence_upper: List[float] (y_upper values)
            - actual_values: List[Optional[float]] (actual consumption, null for future)
            - last_year_values: List[Optional[float]] (historical data)

    Returns:
        Dictionary in the required JSON format
    """
    start = datetime.strptime(start_date, "%Y-%m-%d")
    end = start + timedelta(days=horizon_days - 1)

    materials = []
    aggregated_forecast = []
    total_demand = 0.0

    # Initialize aggregated forecast
    for i in range(horizon_days):
        date = start + timedelta(days=i)
        aggregated_forecast.append({
            "date": date.strftime("%Y-%m-%d"),
            "y_hat": 0.0,
            "y_lower": 0.0,
            "y_upper": 0.0,
            "actual": None,
            "last_year": None
        })

    # Process each material
    for mat_data in materials_data:
        material_forecast = []
        material_total = 0.0

        for i in range(horizon_days):
            date = start + timedelta(days=i)
            y_hat = float(mat_data['predictions'][i])
            y_lower = float(mat_data['confidence_lower'][i])
            y_upper = float(mat_data['confidence_upper'][i])
            actual = mat_data['actual_values'][i]
            last_year = mat_data['last_year_values'][i]

            material_forecast.append({
                "date": date.strftime("%Y-%m-%d"),
                "y_hat": y_hat,
                "y_lower": y_lower,
                "y_upper": y_upper,
                "actual": float(actual) if actual is not None else None,
                "last_year": float(last_year) if last_year is not None else None
            })

            material_total += y_hat

            # Aggregate across materials
            aggregated_forecast[i]["y_hat"] += y_hat
            aggregated_forecast[i]["y_lower"] += y_lower
            aggregated_forecast[i]["y_upper"] += y_upper

            if actual is not None:
                if aggregated_forecast[i]["actual"] is None:
                    aggregated_forecast[i]["actual"] = 0.0
                aggregated_forecast[i]["actual"] += float(actual)

            if last_year is not None:
                if aggregated_forecast[i]["last_year"] is None:
                    aggregated_forecast[i]["last_year"] = 0.0
                aggregated_forecast[i]["last_year"] += float(last_year)

        materials.append({
            "material_id": mat_data['material_id'],
            "material_name": mat_data['material_name'],
            "category": mat_data['category'],
            "unit": mat_data['unit'],
            "supplier": mat_data['supplier'],
            "lead_time_days": mat_data['lead_time_days'],
            "total_predicted_demand": round(material_total, 2),
            "forecast": material_forecast
        })

        total_demand += material_total

    output = {
        "metadata": {
            "project_id": project_id,
            "region": region,
            "supplier": primary_supplier,
            "generated_at": datetime.utcnow().isoformat() + "Z",
            "model_version": "v1.0"
        },
        "horizon_days": horizon_days,
        "forecast_start": start_date,
        "forecast_end": end.strftime("%Y-%m-%d"),
        "total_predicted_demand": round(total_demand, 2),
        "materials": materials,
        "aggregated_forecast": aggregated_forecast
    }

    return output

# Example usage
materials_data = [
    {
        "material_id": "MAT_001",
        "material_name": "Steel Rods (TMT)",
        "category": "Structural",
        "unit": "MT",
        "supplier": "Tata Steel",
        "lead_time_days": 14,
        "predictions": [59.11, 56.67, 54.23, ...],  # 120 values
        "confidence_lower": [45.2, 42.8, 40.4, ...],  # 120 values
        "confidence_upper": [73.0, 70.5, 68.1, ...],  # 120 values
        "actual_values": [58.5, None, None, ...],  # Past dates have values, future = None
        "last_year_values": [52.3, 51.2, 50.1, ...]  # Historical data
    },
    {
        "material_id": "MAT_002",
        "material_name": "Cement (Grade 53)",
        "category": "Construction",
        "unit": "MT",
        "supplier": "UltraTech",
        "lead_time_days": 7,
        "predictions": [120.5, 118.3, 116.1, ...],
        "confidence_lower": [95.0, 92.8, 90.6, ...],
        "confidence_upper": [146.0, 143.8, 141.6, ...],
        "actual_values": [118.2, None, None, ...],
        "last_year_values": [110.5, 109.3, 108.1, ...]
    },
    # Add more materials...
]

output = generate_forecast_json(
    project_id="PROJ_0050",
    region="West Bengal",
    primary_supplier="Six-Fours-les-Plages Materials Ltd.",
    start_date="2024-02-19",
    horizon_days=120,
    materials_data=materials_data
)

# Save to file
with open("forecast_120d.json", "w") as f:
    json.dump(output, f, indent=2)

print(f"Generated forecast for {len(materials_data)} materials")
print(f"Total predicted demand: {output['total_predicted_demand']}")
```

---

## Support

For questions or issues with the JSON format, please contact the frontend development team.

