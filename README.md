
# Soft Drink Market Insight — Alimosho, Lagos  
**Power BI • Data Analysis • Geo-Analytics**

## 📸 Dashboard Preview

<p align="center">
  <img src="./Dashboard Report/page1_overview.png" width="32%" />
  <img src="./Dashboard Report/page2_stock_packaging.png" width="32%" />
  <img src="./Dashboard Report/page3_geo_analysis.png" width="32%" />
</p>


**Author:** Oluwaseyi Ajayi  
**Project type:** Market analysis / dashboard for soft drink distribution, availability and packaging in Alimosho LGA, Lagos.

---

## 🚀 Project Aim
Use field-survey data to understand soft drink **availability**, **brand dominance**, **packaging preference**, **outlet performance**, **stock reliability**, and **geographical coverage** across Alimosho. Produce an actionable Power BI dashboard that reveals market realities and suggests distribution & merchandising actions.

---

## 📦 Dataset (source & shape)
- **Source:** Field survey (provided CSV / Excel).  
- **Final cleaned shape (after PQ):** **~1,000 rows × 12 columns** in long/tidy format (one row per outlet × product presence).  
- **Key columns used:**
  - `S/N` (outlet id)
  - `Outlet_type` (Shop, Kiosk, Supermarket, Open market stall, Hawking, Container, etc.)
  - `Latitude`, `Longitude` (decimal)
  - `Product_type`
  - `ProductType-Value` (presence = 1)
  - `Product_Display`, `ProductDisplay_Value`
  - `Package_Type`, `PackageType_Value`
  - `Product with Higher shelf/refrigerator presence`
  - `Stock condition` (Well Stocked / Partially Stocked / Almost Empty)

> **Important:** Latitude & Longitude were set to **Decimal Number** and **Data Category = Latitude / Longitude**, and **Summarization = Don’t summarize** — otherwise map visuals will be incorrect.

---

## 🧹 Data cleaning & transformation (Power Query steps)
All transformation steps performed in **Power Query** — copyable steps:

1. **Promote first row to headers** (if needed).  
2. **Trim** text fields; remove stray characters & double spaces.  
3. **Unpivot** product, display and packaging wide 0/1 columns into three long blocks:
   - `product_type` + `product_type_value`
   - `product_display` + `product_display_value`
   - `package_type` + `package_type_value`
4. **Merge “Product Type - Others”** into `product_type` (replace generic "Others" by the actual other-value where present).  
5. **Filter** to keep only presence rows (value = 1) for product/display/package — we analyze availability, not absence.  
6. **Normalize** all value columns to integers (0/1).  
7. **Remove duplicates** (keep one row per `S/N` + `product_type`).  
8. **Validate coordinates** and set out-of-bounds lat/lon to null (Lagos bounds approx: lat 6.2–6.9, lon 2.8–3.7).  
9. **Finalize** and export `master_fact_final.csv` (one tidy fact table) for Power BI import.

---

## 🔗 Data model (Power BI)
- Single **fact table** (tidy): each row = one outlet × one product presence  
- No extra star schema needed for this project size; categorical fields serve as slicers/dimensions.  
- Key columns: `S/N`, `Outlet_type`, `Latitude`, `Longitude`, `Product_type`, `Package_Type`, `Product_Display`, `Stock condition`.

---

## 📊 Dashboard (Power BI) — structure & visuals
**3 pages** (clean, consistent UI):

### Page 1 — **Market Overview**
- KPI cards: Total Outlets (191), Unique Products (21), Most Common Outlet (Shop — 936 records), Most Available Product (American Cola — 188)
- Top 6 Most Available Drinks (bar chart) — American Cola (188), Fanta (157), Pepsi (145), Coca-Cola (137), 7Up (70), La Casera (67)
- Packaging Distribution (donut) — PET dominates (~845 records), Can (~101), Glass (~54)
- Product Availability by Outlet Type (stacked bars)

### Page 2 — **Stock & Packaging Insights**
- Packaging Type Distribution by Brand (stacked column)
- Packaging Preference by Outlet Type (stacked/clustered bar)
- Product Display Type by Product (stacked bars)
- Stock Condition Distribution (pie): Well Stocked = **476 (47.6%)**, Partially Stocked = **390 (39%)**, Almost Empty = **134 (13.4%)**
- Stock Condition by Outlet Type (100% stacked)
- Stock Condition for Top 5 Products (stacked bars)

### Page 3 — **Geographical Insights**
- **Bubble Map**: each outlet as a bubble (legend = brand; size = product count or intensity)
- **Hotspot Map**: combined intensity heatmap (Market Intensity Score = function of outlet presence + product variety + product count)
- Filters/slicers: Product_type, Package_Type, Outlet_type — for dynamic geo-insight

---

## 🔎 Key findings (data-driven, business-focused)
1. **Market leader:** *American Cola* shows the greatest outlet presence (188 appearances).  
2. **Channel:** *Shops* are the primary distribution engine (largest recorded channel).  
3. **Packaging:** *PET bottles dominate* distribution (~845 PET records), making PET the logistical default.  
4. **Stock health:** Only **47.6%** of observed product instances were *Well Stocked*; **39%** partially stocked and **13.4%** almost empty → **supply consistency problem**.  
5. **Display effect:** Products appearing in refrigerators/coolers correlate with stronger stock reliability (fridge presence → higher in-stock rates).  
6. **Geography:** Clear **hotspots** exist around main commercial corridors; **coldspots** indicate underserved opportunities for expansion.

---

## 🛠 DAX examples 
```dax
Total Outlets = DISTINCTCOUNT('Fact'[S/N])

Unique Products = DISTINCTCOUNT('Fact'[Product_type])

OutletsStockingBrand = 
CALCULATE(
  DISTINCTCOUNT('Fact'[S/N]),
  'Fact'[Product_type] = SELECTEDVALUE('Fact'[Product_type])
)

WellStocked_RefrigeratedPct =
DIVIDE(
  CALCULATE(COUNTROWS('Fact'),
    'Fact'[ProductDisplay_Value]=1,
    CONTAINSSTRING(LOWER('Fact'[Product_Display]), "refriger"),
    'Fact'[Stock condition] = "Well Stocked"),
  CALCULATE(COUNTROWS('Fact'),
    'Fact'[ProductDisplay_Value]=1,
    CONTAINSSTRING(LOWER('Fact'[Product_Display]), "refriger"))
)
# ✅ **Business Recommendations**

### **1. Improve Re-supply Cadence (2–3 Days)**
To reduce the 39% partial-stock rate, increase distribution frequency.

### **2. Fridge Activation Programs**
Provide branded fridges to hotspot outlets to boost:
- Visibility  
- Conversion  
- Stock reliability  

### **3. Channel-Based Packaging Strategy**
- PET → shops, kiosks, hawkers  
- Can/Glass → supermarkets, bars, restaurants  

### **4. Expand into Coldspot Regions**
Launch targeted recruitment & incentive programs for underserved neighborhoods.

### **5. Introduce Operational KPIs**
Track:
- Out-of-stock rate  
- Replenishment intervals  
- SKU count per outlet  
- Display presence  

---

# 📁 **Deliverables (in Repository)**

- `README.md` — full documentation  
- `master_fact_final.csv` — cleaned dataset *(optional)*  
- `SoftDrink_Market_Alimosho.pbix` — Power BI dashboard *(optional)*  
- `screenshots/` folder:
  - Page1.png  
  - Page2.png  
  - Page3.png  
- `methodology.md` — Power Query steps *(optional)*  

---

# 🔁 **How to Reproduce**

1. Open **Power BI Desktop**  
2. **Get Data → CSV/Excel → master_fact_final.csv**  
3. Set:
   - Latitude → *Latitude*  
   - Longitude → *Longitude*  
   - Summarization → *Don’t summarize*  
4. Add the provided DAX measures  
5. Rebuild visuals for Pages 1–3  
6. Publish to Power BI Service (optional)  
7. Export screenshots for presentation or portfolio  

---

# ⚠️ **Limitations**

- Data captures **availability**, not sales volume  
- Some KPIs represent **product–outlet combinations**, not unique outlets  
- GPS coordinates depend on device accuracy  

---

# 📬 **Contact & Credits**

**Author:** Oluwaseyi Ajayi  
📧 Email: *oluwaseyi1414@gmail.com*  
🔗 LinkedIn: *https://www.linkedin.com/in/ajayi-oluwaseyi-865a35248*  
🌐 Portfolio: *your portfolio URL*

---

**License:** MIT
