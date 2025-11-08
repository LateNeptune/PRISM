# 🚨 CRITICAL: Updated JSON Requirements for PRISM Frontend

## ⚠️ Your Current JSON is INSUFFICIENT

### What's Missing:

1. ❌ **No per-material forecasts** - You only have one aggregated forecast
2. ❌ **No actual values** - Cannot show "Forecast vs Actual" comparison
3. ❌ **No last year values** - Cannot show year-over-year trends
4. ❌ **No material metadata** - No category, supplier, lead time info
5. ❌ **No confidence intervals** - All y_lower and y_upper are 0.0

### What the Frontend Needs:

1. ✅ **Multiple materials** - Each with its own forecast (Steel, Cement, Copper, etc.)
2. ✅ **Actual consumption data** - For dates that have passed
3. ✅ **Historical data** - Same period from last year
4. ✅ **Material details** - Category, supplier, lead time, unit
5. ✅ **Confidence bounds** - Real y_lower and y_upper values
6. ✅ **Aggregated totals** - Sum across all materials

---

## 📋 Required JSON Structure

```json
{
  "metadata": { ... },
  "horizon_days": 120,
  "forecast_start": "2024-02-19",
  "forecast_end": "2024-06-17",
  "total_predicted_demand": 6801.87,
  
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
          "actual": 58.5,        ← REQUIRED
          "last_year": 52.3      ← REQUIRED
        },
        {
          "date": "2024-02-20",
          "y_hat": 56.67,
          "y_lower": 42.8,
          "y_upper": 70.5,
          "actual": null,        ← null for future dates
          "last_year": 51.2
        }
      ]
    },
    {
      "material_id": "MAT_002",
      "material_name": "Cement (Grade 53)",
      ...
    }
  ],
  
  "aggregated_forecast": [
    {
      "date": "2024-02-19",
      "y_hat": 214.81,         ← Sum of all materials
      "y_lower": 168.2,
      "y_upper": 261.4,
      "actual": 211.5,         ← REQUIRED
      "last_year": 194.9       ← REQUIRED
    }
  ]
}
```

---

## 🎯 Key Changes from Your Current JSON

| Field | Current | Required | Why |
|-------|---------|----------|-----|
| `materials` | ❌ Missing | ✅ Array of 3-10 materials | Dropdown selection, per-material charts |
| `actual` | ❌ Missing | ✅ Float or null | "Forecast vs Actual" comparison |
| `last_year` | ❌ Missing | ✅ Float or null | Year-over-year trend analysis |
| `y_lower` | ❌ All 0.0 | ✅ Real values | Confidence band visualization |
| `y_upper` | ❌ All 0.0 | ✅ Real values | Confidence band visualization |
| `aggregated_forecast` | ❌ Missing | ✅ Array | Total across all materials |

---

## 📊 How Frontend Will Use This Data

### Home Page
- **Chart**: "Forecast vs Actual" using `aggregated_forecast`
  - Green line: `actual` values
  - Teal line: `y_hat` (forecast)
  - Shows both past performance and future predictions

### Forecast Page
- **Dropdown**: Select material from `materials` array
- **Chart**: Shows 3 lines for selected material:
  - **Forecast** (white): `y_hat`
  - **Actual** (green): `actual` values
  - **Last Year** (gray): `last_year` values
- **Confidence Band**: Shaded area between `y_lower` and `y_upper`
- **Material Info**: Category, supplier, lead time displayed

---

## 🔧 How to Generate This JSON

### Step 1: Prepare Data for Each Material

For each material (Steel, Cement, Copper, etc.):
1. Run your ML model to get predictions for 120 days
2. Calculate confidence intervals (y_lower, y_upper)
3. Fetch actual consumption data for past dates
4. Fetch historical data from same period last year
5. Get material metadata (category, supplier, lead time)

### Step 2: Structure the Data

```python
materials_data = [
    {
        "material_id": "MAT_001",
        "material_name": "Steel Rods (TMT)",
        "category": "Structural",
        "unit": "MT",
        "supplier": "Tata Steel",
        "lead_time_days": 14,
        "predictions": [59.11, 56.67, ...],      # 120 values
        "confidence_lower": [45.2, 42.8, ...],   # 120 values
        "confidence_upper": [73.0, 70.5, ...],   # 120 values
        "actual_values": [58.5, None, ...],      # Past = value, Future = None
        "last_year_values": [52.3, 51.2, ...]    # Historical data
    },
    # Add 2-9 more materials...
]
```

### Step 3: Use the Python Function

See `ML_MODEL_INTEGRATION_GUIDE.md` for complete Python code to generate the JSON.

---

## ✅ Validation Checklist

Before sending JSON to frontend:

- [ ] `materials` array has 3-10 items
- [ ] Each material has unique `material_id`
- [ ] Each material's `forecast` has exactly 120 items
- [ ] Each forecast item has `actual` and `last_year` fields
- [ ] `actual` is `null` for future dates, has value for past dates
- [ ] `y_lower` and `y_upper` are real values (not 0.0)
- [ ] `aggregated_forecast` has exactly 120 items
- [ ] Aggregated values = sum of all materials for each day
- [ ] All dates are consecutive with no gaps
- [ ] Dates are in `YYYY-MM-DD` format

---

## 📁 Files to Reference

1. **`REQUIRED_JSON_STRUCTURE.json`** - Complete example with 3 materials
2. **`ML_MODEL_INTEGRATION_GUIDE.md`** - Full documentation with Python code
3. **`ML_MODEL_JSON_TEMPLATE.json`** - Basic template (OLD - needs update)

---

## 🚀 Next Steps

1. **Update your ML pipeline** to generate data for multiple materials
2. **Add actual consumption data** from your database
3. **Add historical data** from last year
4. **Calculate confidence intervals** if not already done
5. **Use the Python function** in `ML_MODEL_INTEGRATION_GUIDE.md`
6. **Validate** the output JSON against the checklist
7. **Replace** `forecast_120d.json` with the new format
8. **Test** in the frontend

---

## 💡 Example Materials to Include

Recommended 5-8 materials for a realistic dashboard:

1. **Steel Rods (TMT)** - Structural, MT, Tata Steel
2. **Cement (Grade 53)** - Construction, MT, UltraTech
3. **Copper Wire** - Electrical, KM, Hindalco
4. **Transformers** - Equipment, Units, ABB India
5. **Circuit Breakers** - Equipment, Units, Siemens
6. **Aluminum Conductors** - Electrical, KM, Vedanta
7. **Concrete Poles** - Structural, Units, L&T
8. **Insulators** - Electrical, Units, NGK

Each should have:
- Different demand patterns
- Different suppliers
- Different lead times (7-45 days)
- Different categories

---

## ❓ Questions?

If you need help:
1. Check `ML_MODEL_INTEGRATION_GUIDE.md` for detailed documentation
2. See `REQUIRED_JSON_STRUCTURE.json` for a complete example
3. Use the Python code provided to generate the JSON
4. Validate against the checklist above

