# 📚 JSON Requirements Documentation - Quick Navigation

## 🚨 START HERE

Your current JSON format is **INSUFFICIENT** for the frontend requirements.

**The Problem:**
- ❌ Charts show incomplete data (no Actual, no Last Year comparison)
- ❌ Cannot select individual materials (no dropdown)
- ❌ No material-specific details (category, supplier, lead time)
- ❌ Confidence intervals are all 0.0

**The Solution:**
Update your ML model output to include per-material forecasts with actual and historical data.

---

## 📁 Documentation Files

### 1. **WHAT_YOU_NEED_TO_DO.md** ⭐ START HERE
**Purpose:** Step-by-step action plan
**Read this if:** You want to know exactly what to do next
**Contains:**
- Action items checklist
- Data sources you need
- Example code snippets
- Validation checklist

### 2. **JSON_REQUIREMENTS_SUMMARY.md** ⭐ QUICK REFERENCE
**Purpose:** High-level overview of requirements
**Read this if:** You want a quick summary
**Contains:**
- What's missing from current JSON
- Required structure overview
- Key changes table
- Validation checklist

### 3. **OLD_VS_NEW_JSON_COMPARISON.md**
**Purpose:** Side-by-side comparison
**Read this if:** You want to see exact differences
**Contains:**
- OLD format (current)
- NEW format (required)
- Field-by-field comparison
- What each addition enables

### 4. **ML_MODEL_INTEGRATION_GUIDE.md** ⭐ TECHNICAL REFERENCE
**Purpose:** Complete technical documentation
**Read this if:** You're implementing the solution
**Contains:**
- Full field specifications
- Data requirements
- Python code to generate JSON
- Validation rules

### 5. **REQUIRED_JSON_STRUCTURE.json**
**Purpose:** Complete working example
**Read this if:** You want to see the exact format
**Contains:**
- Full JSON with 3 materials
- All required fields
- Proper structure

### 6. **ML_MODEL_JSON_TEMPLATE.json**
**Purpose:** Basic template (OLD - needs update)
**Status:** ⚠️ Outdated - use REQUIRED_JSON_STRUCTURE.json instead

---

## 🎯 Quick Start Guide

### For Busy People (5 minutes)
1. Open `REQUIRED_JSON_STRUCTURE.json` - see the format
2. Read `JSON_REQUIREMENTS_SUMMARY.md` - understand what's needed
3. Check `WHAT_YOU_NEED_TO_DO.md` - know what to do

### For Implementers (30 minutes)
1. Read `WHAT_YOU_NEED_TO_DO.md` - action plan
2. Read `ML_MODEL_INTEGRATION_GUIDE.md` - technical details
3. Copy Python code from guide
4. Modify for your data sources
5. Generate and validate JSON

### For Reviewers (15 minutes)
1. Read `OLD_VS_NEW_JSON_COMPARISON.md` - see changes
2. Review `REQUIRED_JSON_STRUCTURE.json` - verify format
3. Check validation checklist in `JSON_REQUIREMENTS_SUMMARY.md`

---

## 🔑 Key Concepts

### Materials Array
**What:** List of individual materials/products (Steel, Cement, Copper, etc.)
**Why:** Enables dropdown selection and per-material analysis
**Example:**
```json
"materials": [
  {
    "material_id": "MAT_001",
    "material_name": "Steel Rods (TMT)",
    "forecast": [...]
  }
]
```

### Actual Values
**What:** Real consumption data for dates that have passed
**Why:** Enables "Forecast vs Actual" comparison charts
**Example:**
```json
{
  "date": "2024-02-19",
  "y_hat": 59.11,
  "actual": 58.5  ← Real consumption
}
```

### Last Year Values
**What:** Historical data from same period last year
**Why:** Enables year-over-year trend analysis
**Example:**
```json
{
  "date": "2024-02-19",
  "y_hat": 59.11,
  "last_year": 52.3  ← Same date in 2023
}
```

### Aggregated Forecast
**What:** Total across all materials
**Why:** Overview charts on home page
**Example:**
```json
"aggregated_forecast": [
  {
    "date": "2024-02-19",
    "y_hat": 214.81,  ← Sum of all materials
    "actual": 211.5,
    "last_year": 194.9
  }
]
```

---

## 📊 What Frontend Will Display

### Home Page
```
┌─────────────────────────────────────┐
│ Demand Trends                       │
│ Forecast vs Actual Performance      │
│                                     │
│  [Chart with 2 lines]               │
│  - Teal: Forecast (y_hat)          │
│  - Green: Actual (actual)          │
└─────────────────────────────────────┘
```

### Forecast Page
```
┌─────────────────────────────────────┐
│ Demand Forecast Trend (ML Model)    │
│ [Dropdown: Steel Rods (TMT) ▼]     │
│                                     │
│  [Chart with 3 lines]               │
│  - White: Forecast (y_hat)         │
│  - Green: Actual (actual)          │
│  - Gray: Last Year (last_year)     │
│  - Shaded: Confidence band         │
│                                     │
│ Category: Structural                │
│ Supplier: Tata Steel                │
│ Lead Time: 14 days                  │
└─────────────────────────────────────┘
```

---

## ✅ Validation Checklist

Before sending JSON to frontend:

**Structure:**
- [ ] Has `materials` array with 3-10 items
- [ ] Has `aggregated_forecast` array
- [ ] All arrays have exactly `horizon_days` items

**Materials:**
- [ ] Each has unique `material_id`
- [ ] Each has `material_name`, `category`, `unit`, `supplier`, `lead_time_days`
- [ ] Each has `forecast` array with 120 items

**Forecast Items:**
- [ ] Each has `date`, `y_hat`, `y_lower`, `y_upper`, `actual`, `last_year`
- [ ] `actual` is null for future dates, has value for past dates
- [ ] `last_year` has historical values
- [ ] `y_lower` and `y_upper` are not all 0.0

**Dates:**
- [ ] All in `YYYY-MM-DD` format
- [ ] Consecutive with no gaps
- [ ] First date = `forecast_start`
- [ ] Last date = `forecast_end`

**Values:**
- [ ] All non-negative
- [ ] Aggregated = sum of materials
- [ ] `total_predicted_demand` = sum of all materials' totals

---

## 🚀 Implementation Steps

1. **Identify Materials** (1 hour)
   - List 3-10 materials
   - Gather metadata (category, supplier, lead time)

2. **Update ML Pipeline** (4-8 hours)
   - Run model for each material separately
   - Calculate confidence intervals
   - Query actual consumption data
   - Query historical data

3. **Generate JSON** (1 hour)
   - Use Python code from guide
   - Structure data correctly
   - Save to file

4. **Validate** (30 minutes)
   - Check against checklist
   - Verify file size
   - Test JSON parsing

5. **Deploy** (15 minutes)
   - Replace `forecast_120d.json`
   - Test in frontend
   - Verify charts display correctly

**Total Time:** 1-2 days

---

## 💡 Tips for Success

1. **Start Small:** Begin with 3 materials and 7 days
2. **Use Sample Data:** Test with synthetic data first
3. **Validate Early:** Check JSON structure before full implementation
4. **Test Incrementally:** Add one material at a time
5. **Document Assumptions:** Note any data gaps or approximations

---

## 🆘 Troubleshooting

**Problem:** Don't have actual consumption data
**Solution:** Use predictions + random noise for testing, query database for production

**Problem:** Don't have last year data
**Solution:** Use predictions * 0.9 for testing, query historical database for production

**Problem:** Don't know how to calculate confidence intervals
**Solution:** Use ±20% of prediction as approximation, or use model's built-in intervals

**Problem:** Too many materials to manage
**Solution:** Start with top 5 by volume/value, add more later

**Problem:** JSON file too large
**Solution:** Reduce precision (2 decimal places), compress file, or use API instead

---

## 📞 Support

**Questions about:**
- **Structure:** See `REQUIRED_JSON_STRUCTURE.json`
- **Fields:** See `ML_MODEL_INTEGRATION_GUIDE.md`
- **Implementation:** See `WHAT_YOU_NEED_TO_DO.md`
- **Comparison:** See `OLD_VS_NEW_JSON_COMPARISON.md`

---

## 📈 Expected Results

After implementing the new JSON format:

✅ **Home Page:**
- Chart shows Forecast vs Actual comparison
- Green line for actual consumption
- Teal line for predictions

✅ **Forecast Page:**
- Dropdown to select materials
- Chart shows Forecast vs Actual vs Last Year
- Confidence bands around forecast
- Material details displayed

✅ **Overall:**
- Professional, complete dashboard
- Meaningful comparisons
- Actionable insights

---

## 🎯 Success Criteria

You'll know it's working when:
1. Dropdown shows list of materials
2. Charts display 3 lines (Forecast, Actual, Last Year)
3. Confidence bands appear around forecast
4. Material details show category, supplier, lead time
5. No console errors
6. Data looks realistic and meaningful

---

## 📝 File Summary

| File | Purpose | Priority | Read Time |
|------|---------|----------|-----------|
| WHAT_YOU_NEED_TO_DO.md | Action plan | ⭐⭐⭐ | 10 min |
| JSON_REQUIREMENTS_SUMMARY.md | Quick reference | ⭐⭐⭐ | 5 min |
| REQUIRED_JSON_STRUCTURE.json | Example | ⭐⭐⭐ | 2 min |
| ML_MODEL_INTEGRATION_GUIDE.md | Technical docs | ⭐⭐ | 20 min |
| OLD_VS_NEW_JSON_COMPARISON.md | Comparison | ⭐⭐ | 10 min |
| README_JSON_REQUIREMENTS.md | This file | ⭐ | 5 min |

---

**Last Updated:** 2024-11-08
**Version:** 2.0 (Updated with per-material requirements)

