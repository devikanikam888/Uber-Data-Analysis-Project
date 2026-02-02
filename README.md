# 🚗 Uber Ride Analysis: Operational Efficiency & Revenue Recovery

## 📋 Project Overview
Analysis of 20,000+ Uber booking records to identify cancellation drivers, revenue patterns, and operational inefficiencies. Focus on translating data insights into actionable business recommendations.

---

## 🎯 Business Problem
Uber's operations team faced a 62% booking success rate with 38% of bookings failing due to cancellations. The challenge was to:
- Identify **why** bookings were failing (customer vs. driver behavior)
- Quantify revenue loss from cancellations
- Recommend specific operational interventions to recover lost revenue

---

## 📊 Dataset
- **Records:** 20,000+ bookings
- **Time Period:** July 2024
- **Dimensions:** 19 columns including:
  - Booking details (ID, Date, Time, Status)
  - Customer & Driver data (IDs, Ratings)
  - Vehicle information (Type, Location)
  - Financial data (Booking Value, Payment Method)
  - Operational metrics (VTAT, CTAT, Ride Distance)
  - Cancellation data (Reasons, Incomplete Rides)

---

## 🛠️ Tech Stack
- **SQL (MySQL)** - Data querying, aggregation, and view creation
- **Excel** - Data cleaning, transformation, pivot analysis
- **Power BI** - Dashboard creation, DAX measures, interactive visualizations

---

## 🔄 Process & Methodology

### 1️⃣ Data Cleaning & Preparation (Excel)
- Imported 20,000 rows across 19 columns
- Standardized 50+ Bangalore location names for consistency
- Cleaned date/time formats to enable time-series analysis
- Validated booking value ranges and flagged outliers
- Created calculated fields:
  - Booking value categories (<₹500, ₹500-₹1000, >₹1000)
  - Cancellation rate per segment
  - Revenue per ride metrics

### 2️⃣ SQL Analysis
Built **10 reusable SQL views** to answer specific business questions:

**View 1: Successful Bookings**
```sql
CREATE VIEW Successful_Bookings AS
SELECT * FROM bookings 
WHERE Booking_Status = 'Success';
```

**View 2: Average Ride Distance by Vehicle Type**
```sql
CREATE VIEW ride_distance_for_each_vehicle AS
SELECT Vehicle_Type, AVG(Ride_Distance) as avg_distance 
FROM bookings 
GROUP BY Vehicle_Type;
```

**View 3: Cancelled Rides by Customers**
```sql
CREATE VIEW cancelled_rides_by_customers AS
SELECT COUNT(*) FROM bookings 
WHERE Booking_Status = 'Cancelled by Customer';
```

**View 4: Top 5 Customers**
```sql
CREATE VIEW Top_5_Customers AS
SELECT Customer_ID, COUNT(Booking_ID) as total_rides 
FROM bookings 
GROUP BY Customer_ID 
ORDER BY total_rides DESC 
LIMIT 5;
```

**View 5: Driver Cancellations (Personal/Car Issues)**
```sql
CREATE VIEW Rides_cancelled_by_Drivers_P_C_Issues AS
SELECT COUNT(*) FROM bookings 
WHERE cancelled_Rides_by_Driver = 'Personal & Car related issue';
```

**View 6: Max/Min Driver Ratings (Prime Sedan)**
```sql
CREATE VIEW Max_Min_Driver_Rating AS
SELECT MAX(Driver_Ratings) as max_rating, MIN(Driver_Ratings) as min_rating 
FROM bookings 
WHERE Vehicle_Type = 'Prime Sedan';
```

**View 7: UPI Payment Rides**
```sql
CREATE VIEW UPI_Payment AS
SELECT * FROM bookings 
WHERE Payment_Method = 'UPI';
```

**View 8: Average Customer Rating by Vehicle**
```sql
CREATE VIEW AVG_Cust_Rating AS
SELECT Vehicle_Type, AVG(Customer_Rating) as avg_customer_rating 
FROM bookings 
GROUP BY Vehicle_Type;
```

**View 9: Total Successful Ride Value**
```sql
CREATE VIEW total_successful_ride_value AS
SELECT SUM(Booking_Value) as total_successful_ride_value 
FROM bookings 
WHERE Booking_Status = 'Success';
```

**View 10: Incomplete Rides & Reasons**
```sql
CREATE VIEW Incomplete_Rides_Reason AS
SELECT Booking_ID, Incomplete_Rides_Reason 
FROM bookings 
WHERE Incomplete_Rides = 'Yes';
```

### 3️⃣ Power BI Dashboards
Created **5 interactive dashboards** to visualize insights:

**Dashboard 1: Overall Performance**
- Total bookings, booking value, average customer rating (KPI cards)
- Booking status breakdown (pie chart): 62% success, 18% driver cancellations, 10% customer cancellations
- Ride volume over time (line chart): daily fluctuations 650-750 bookings

**Dashboard 2: Vehicle Type Analysis**
- Revenue comparison by vehicle type (table view)
- Average distance traveled per vehicle type
- Total distance covered per vehicle segment

**Dashboard 3: Revenue Analysis**
- Revenue by payment method (bar chart): Cash ₹3.8M (54%), UPI ₹2.8M (40%)
- Daily revenue distribution (histogram)
- Top 5 customers by booking value

**Dashboard 4: Cancellation Deep Dive**
- Customer cancellation reasons (pie chart):
  - Driver not moving: 29% (610 rides)
  - Driver asked to cancel: 26.5%
  - Change of plans: 19.6%
- Driver cancellation reasons (pie chart):
  - Personal & car issues: 35% (1,263 rides)
  - Customer related issues: 29%
  - Customer coughing/sick: 20%

**Dashboard 5: Ratings Overview**
- Driver ratings by vehicle type (table): 3.99-4.01 range
- Customer ratings by vehicle type (table): 3.98-4.02 range
- Minimal variation indicating consistent service quality

**DAX Measures Used:**
- Cancellation Rate: `DIVIDE([Cancelled Rides], [Total Bookings])`
- Rolling 7-day average: `CALCULATE(AVERAGE(Bookings[Count]), DATESINPERIOD(...))`
- Conditional revenue (success only): `CALCULATE(SUM([Booking_Value]), Bookings[Status]="Success")`

---

## 📈 Key Findings

### Cancellation Insights
- **28% of bookings cancelled** (5,735 rides)
  - 18% by drivers
  - 10% by customers
- **Top customer cancellation reason:** "Driver not moving" (29%, 610 rides)
- **Top driver cancellation reason:** Personal/car issues (35%, 1,263 rides)

### Revenue Analysis
- **Total revenue:** ₹7M
- **Payment method split:**
  - Cash: ₹3.8M (54%)
  - UPI: ₹2.8M (40%)
  - Credit/Debit: 6%

### Vehicle Performance
- **All vehicle types generate similar revenue:** ₹1.54M-₹1.62M range
- **Exception:** Auto has significantly lower average distance (10.0km vs. 24-25km for others)

### Service Quality
- **Driver ratings:** 3.99-4.01 (minimal variation)
- **Customer ratings:** 3.98-4.02 (consistently high)

---

## 💡 Business Recommendations

### 1. Driver Behavior Monitoring
**Problem:** 610 rides lost to "driver not moving" cancellations  
**Solution:** Deploy geofencing alerts to detect stationary drivers and auto-reassign rides after 5 minutes  
**Impact:** Recover 610 rides (~₹180K revenue)

### 2. Preventative Maintenance
**Problem:** 1,263 cancellations due to personal/car issues  
**Solution:** Mandate pre-shift vehicle inspections  
**Impact:** Reduce cancellations by 15-20% (~190-250 rides recovered)

### 3. Digital Payment Shift
**Problem:** 54% cash-based revenue (₹3.8M)  
**Solution:** Launch UPI incentives (cashback/discounts)  
**Impact:** Improve transaction tracking, reduce handling costs

### 4. Fleet Reallocation
**Problem:** All vehicle types generate identical revenue (₹1.54M-₹1.62M) despite varying operational costs  
**Solution:** Shift resources to lower-cost vehicles (Auto, Mini, Bike)  
**Impact:** Maintain revenue while reducing operational expenses

### 5. Peak-Time Driver Optimization
**Problem:** Daily volume fluctuations (650-750 bookings) with no visibility into cancellation timing  
**Solution:** Map cancellation patterns to increase driver availability during high-cancellation windows  
**Impact:** Reduce cancellations during peak demand periods

### 6. Proactive Customer Updates
**Problem:** 29% of cancellations driven by perceived driver inactivity  
**Solution:** Send real-time driver location and ETA updates  
**Impact:** Reduce customer anxiety and premature cancellations

**Total Revenue Recovery Opportunity:** ₹2.3M

---

## 📊 Sample Queries

**Get cancellation breakdown:**
```sql
SELECT 
    Booking_Status,
    COUNT(*) as count,
    ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM bookings), 2) as percentage
FROM bookings
GROUP BY Booking_Status;
```

**Find revenue by payment method:**
```sql
SELECT 
    Payment_Method,
    SUM(Booking_Value) as total_revenue,
    COUNT(*) as num_transactions
FROM bookings
WHERE Booking_Status = 'Success'
GROUP BY Payment_Method
ORDER BY total_revenue DESC;
```

---

## 🎓 Skills Demonstrated
- SQL query optimization & view creation
- Data cleaning & validation in Excel
- Business problem framing & root cause analysis
- Dashboard design & DAX calculations in Power BI
- Translating data insights into actionable recommendations

---

## 📧 Contact
**Devika Nikam**  
[LinkedIn](your-linkedin-url) | [Portfolio](your-portfolio-url) | devikanikam888@gmail.com

---

## 📝 License
This project is for portfolio demonstration purposes.

---

**⭐ If you found this analysis useful, please star this repository!**
