# 🎯 What You Need to Do - Action Plan

## Current Situation

Your ML model outputs this:
```json
{
  "forecast": [
    {"date": "2024-02-19", "y_hat": 59.11, "y_lower": 0.0, "y_upper": 0.0}
  ]
}
```

**Problem:** This is ONE aggregated forecast with no context.

---

## What Frontend Needs

The frontend needs forecasts for **MULTIPLE MATERIALS** with **ACTUAL DATA** and **HISTORICAL DATA**.

Think of it like this:
- Your current JSON = "Total demand will be 6,802 units"
- Required JSON = "Steel: 2,500 MT, Cement: 4,301 MT, Copper: 1,850 KM, etc."

---

## 📋 Action Items

### 1. Identify Your Materials (Products)

List all materials/products you're forecasting. Examples:
- Steel Rods (TMT)
- Cement (Grade 53)
- Copper Wire
- Transformers
- Circuit Breakers
- Aluminum Conductors
- Concrete Poles
- Insulators

**Action:** Create a list of 3-10 materials with:
- Material ID (e.g., MAT_001)
- Material name
- Category (Structural, Electrical, Construction, Equipment)
- Unit (MT, KM, Units, KG, Liters)
- Supplier name
- Lead time in days

---

### 2. Run ML Model for Each Material

**Current:** You run model once for total demand
**Required:** Run model separately for each material

```python
# Instead of this:
total_predictions = ml_model.predict(total_data)

# Do this:
steel_predictions = ml_model.predict(steel_data)
cement_predictions = ml_model.predict(cement_data)
copper_predictions = ml_model.predict(copper_data)
# ... for each material
```

**Action:** Modify your ML pipeline to:
- Train/predict for each material separately
- Generate 120-day forecast for each
- Calculate confidence intervals for each

---

### 3. Get Actual Consumption Data

**Required:** Actual consumption values for dates that have already passed

Example:
- Today is Feb 25, 2024
- Forecast starts Feb 19, 2024
- You need actual consumption for Feb 19-24 (6 days)
- Feb 25 onwards = null (future dates)

```python
# Query your database
actual_steel = db.query("""
    SELECT date, consumption 
    FROM material_consumption 
    WHERE material_id = 'MAT_001' 
    AND date BETWEEN '2024-02-19' AND '2024-02-24'
""")

# For future dates, set to None
if date > today:
    actual = None
else:
    actual = actual_steel[date]
```

**Action:** 
- Query your database for actual consumption
- For each material
- For dates that have passed
- Set to `null` for future dates

---

### 4. Get Historical Data (Last Year)

**Required:** Consumption from same period last year

Example:
- Forecast period: Feb 19 - Jun 17, 2024
- Last year period: Feb 19 - Jun 17, 2023
- Query consumption from 2023

```python
# Query last year's data
last_year_steel = db.query("""
    SELECT date, consumption 
    FROM material_consumption 
    WHERE material_id = 'MAT_001' 
    AND date BETWEEN '2023-02-19' AND '2023-06-17'
""")
```

**Action:**
- Query database for same period last year
- For each material
- Match dates (Feb 19, 2024 → Feb 19, 2023)

---

### 5. Calculate Confidence Intervals

**Current:** y_lower and y_upper are all 0.0
**Required:** Real confidence bounds

```python
# Calculate 95% confidence interval
from scipy import stats

predictions = model.predict(X)
std_error = model.predict_std(X)  # Standard error

y_lower = predictions - 1.96 * std_error
y_upper = predictions + 1.96 * std_error
```

**Action:**
- Calculate confidence intervals from your model
- Or use historical error rates
- Or use quantile regression

---

### 6. Structure the Data

Organize data for each material:

```python
materials_data = []

for material in materials_list:
    # Get predictions
    predictions = run_ml_model(material)
    
    # Get confidence intervals
    conf_lower, conf_upper = calculate_confidence(material)
    
    # Get actual values
    actual_values = get_actual_consumption(material, start_date, end_date)
    
    # Get last year values
    last_year_values = get_last_year_consumption(material, start_date, end_date)
    
    materials_data.append({
        "material_id": material.id,
        "material_name": material.name,
        "category": material.category,
        "unit": material.unit,
        "supplier": material.supplier,
        "lead_time_days": material.lead_time,
        "predictions": predictions,
        "confidence_lower": conf_lower,
        "confidence_upper": conf_upper,
        "actual_values": actual_values,
        "last_year_values": last_year_values
    })
```

---

### 7. Generate JSON

Use the Python function from `ML_MODEL_INTEGRATION_GUIDE.md`:

```python
from forecast_json_generator import generate_forecast_json

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
```

---

## 🔍 Data Sources You Need

### From ML Model:
- ✅ Predictions (y_hat) for each material
- ✅ Confidence intervals (y_lower, y_upper)

### From Database:
- ✅ Actual consumption (past dates)
- ✅ Historical consumption (last year)
- ✅ Material metadata (category, supplier, lead time)

### From Configuration:
- ✅ Project ID
- ✅ Region
- ✅ Forecast period (start/end dates)

---

## 📊 Example Data Flow

```
1. Materials List
   ↓
2. For each material:
   - Run ML model → predictions
   - Calculate confidence → y_lower, y_upper
   - Query DB → actual values
   - Query DB → last year values
   ↓
3. Aggregate across materials
   ↓
4. Generate JSON
   ↓
5. Save to forecast_120d.json
```

---

## ✅ Validation Before Sending to Frontend

Check:
- [ ] JSON has `materials` array with 3-10 items
- [ ] Each material has 120 forecast items
- [ ] Each forecast item has `actual` and `last_year` fields
- [ ] `actual` is null for future dates, has value for past dates
- [ ] `y_lower` and `y_upper` are not all 0.0
- [ ] `aggregated_forecast` has 120 items
- [ ] Aggregated values = sum of materials for each day
- [ ] All dates are consecutive
- [ ] File size is reasonable (< 1MB)

---

## 🚀 Quick Start

1. **Read:** `REQUIRED_JSON_STRUCTURE.json` - See complete example
2. **Understand:** `OLD_VS_NEW_JSON_COMPARISON.md` - See what changed
3. **Implement:** Use Python code from `ML_MODEL_INTEGRATION_GUIDE.md`
4. **Validate:** Check against `JSON_REQUIREMENTS_SUMMARY.md`
5. **Deploy:** Replace `forecast_120d.json`

---

## 💡 Tips

- Start with 3-5 materials, add more later
- If you don't have last year data, use synthetic data for testing
- If you don't have actual data, use predictions + noise for testing
- Confidence intervals can be ±20% of prediction as a starting point
- Test with small dataset first (7 days instead of 120)

---

## ❓ Common Questions

**Q: Do I need to retrain my model?**
A: No, just run it separately for each material instead of once for total.

**Q: What if I don't have actual consumption data?**
A: For testing, you can use predictions + random noise. For production, query your database.

**Q: What if I don't have last year data?**
A: For testing, you can use predictions * 0.9. For production, query historical database.

**Q: How do I calculate confidence intervals?**
A: Use your model's prediction intervals, or calculate from historical errors, or use ±20% as approximation.

**Q: How many materials should I include?**
A: Minimum 3, recommended 5-8, maximum 10-15.

---

## 📞 Next Steps

1. Review the example JSON in `REQUIRED_JSON_STRUCTURE.json`
2. Identify your materials and their metadata
3. Modify your ML pipeline to predict per-material
4. Query actual and historical data from database
5. Use the Python function to generate JSON
6. Validate the output
7. Test in frontend

