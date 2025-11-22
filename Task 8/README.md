# 📊 Superstore Sales Analytics: A Power BI Deep Dive  

## 🎯 Executive Summary  
What started as a messy Superstore dataset has become a powerful analytical dashboard that reveals the hidden patterns driving **$2,261,537 in revenue**. Through three carefully crafted visualizations, we've uncovered critical insights:
- **Consumers dominate our revenue stream**
- **Shipping preferences reveal untapped pricing opportunities**
- **Product portfolio analysis now uses robust median calculations** 

---

## 📌 Page 1: Executive KPIs  
**Overview of key performance metrics**

| Metric              | Value                                               |
|---------------------|-----------------------------------------------------|
| Total Revenue       | $2,261,537 (canonical), ~$2,000,000 (displayed)     |
| Total Orders        | 4,922                                               |
| Total Customers     | 793                                                 |
| Total Units Sold    | 9,800                                               |
| Average Order Value | $459.48                                             |

> 💡 *Note:* Displayed revenue is rounded for executive clarity; full-precision totals are maintained for validation and strategic analysis.

---

## 📌 Page 2: Customer Segment Deep Dive  
**Understanding who our customers are and how they behave across segments and shipping preferences**

### 📊 Segment Revenue Breakdown

| Segment       | Revenue     | Percentage | Units Sold | AOV      | Customers |
|---------------|-------------|------------|------------|----------|-----------|
| Consumer      | $1,148,061  | 50.76%     | 5,101      | $452.53  | 409       |
| Corporate     | $688,494    | 30.44%     | 2,953      | $461.77  | 236       |
| Home Office   | $424,982    | 18.79%     | 1,746      | $475.37  | 148       |
| **Total**     | $2,261,537  | 100%       | 9,800      | $459.48  | 793       |

---

### 📊 Ship Mode Preference by Segment

| Ship Mode      | Total Revenue | Consumer     | Corporate    | Home Office  |
|----------------|---------------|--------------|--------------|--------------|
| Standard Class | $1,340,831    | $702,378     | $401,747     | $236,706     |
| Second Class   | $449,914      | $230,126     | $139,045     | $80,743      |
| First Class    | $345,572      | $158,105     | $102,580     | $84,887      |
| Same Day       | $125,218      | $57,452      | $45,121      | $22,645      |

> 🎯 **Insight:** Corporate customers show a strong preference for premium shipping, especially Same Day, with high AOV. This opens up strategic opportunities for:
> - Premium shipping packages  
> - Carrier rate optimization  
> - Segment-specific express promotions

---

### 🧠 Strategic Takeaways  
- **Consumer segment** drives volume — ideal for mass-market campaigns  
- **Home Office** has highest AOV — perfect for premium bundles  
- **Corporate** shows premium shipping behavior — leverage for upselling and express delivery strategies  

---

## 📌 Page 3: Product Portfolio Scatter  
**Analyzing product pricing and performance across categories from 2015 to 2018**

### 📈 Tooltip-Driven Metrics

| Year | Category        | Revenue     | Units Sold | Median Price |
|------|------------------|-------------|------------|---------------|
| 2015 | Furniture        | $156,478    | 414        | $182.86       |
| 2015 | Office Supplies  | $149,513    | 1,192      | $25.54        |
| 2015 | Technology       | $173,866    | 347        | $199.98       |
| 2016 | Furniture        | $164,054    | 440        | $191.67       |
| 2016 | Office Supplies  | $133,124    | 1,210      | $27.22        |
| 2016 | Technology       | $162,258    | 405        | $199.96       |
| 2017 | Furniture        | $195,813    | 547        | $187.06       |
| 2017 | Office Supplies  | $182,418    | 1,537      | $29.72        |
| 2017 | Technology       | $221,962    | 450        | $166.44       |
| 2018 | Furniture        | $212,314    | 677        | $171.29       |
| 2018 | Office Supplies  | $240,368    | 1,970      | $27.16        |
| 2018 | Technology       | $269,371    | 611        | $151.19       |

---

### 🧠 Analytical Highlights  
- **Office Supplies** dominate in unit volume but maintain low pricing — ideal for volume-based strategies  
- **Technology** shows premium pricing early on, but median prices declined — signaling market saturation  
- **Furniture** maintains stable pricing with gradual unit growth — a candidate for margin-focused optimization  

---

### 🛠️ Technical Enhancements  
- Corrected DAX logic to use **Median Price per Unit ($54.49)** instead of skewed average ($230.77)  
- Tooltip integration for full context: Year, Category, Revenue, Units, Median Price  
- Time-aware scatter plot with dynamic filtering  
- Optimized relationships and filters for cross-category analysis  

---

### 📌 Recommendations  
- Implement **median-based pricing analytics** across all categories  
- Explore **bundling strategies** for high-margin Furniture and Technology items  
- Monitor **Office Supplies** for inventory efficiency and promotional targeting  
- Add **time-series visualizations** to track pricing and volume trends  
