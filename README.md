<p align="center"> <img src="https://github.com/user-attachments/assets/689c0852-ca27-4ee9-a845-5a4020b87c57" />
" width="900" alt="Music Store Analysis Banner" style="margin: 12px 0; border-radius: 18px; background: #fff; box-shadow: 0 4px 16px rgba(0,0,0,0.12); padding: 0;"> </p>

# 🎵 Music Store Analysis

Music Store Analysis is a comprehensive SQL-based data analytics project that explores operational data from an online music store. It performs in-depth analysis of customer behavior, artist performance, revenue trends, and genre preferences to generate actionable insights for business growth and strategic decision-making.

## 🎯 Objective
- Analyze the online music store's operational performance
- Perform customer segmentation and identify spending patterns
- Uncover trends in music preferences across regions and genres
- Evaluate artist effectiveness and growth potential
- Identify high-value customers and revenue opportunities
- Support data-driven business decisions for growth

## 📊 Dataset Description

| Table | Description |
|-------|-------------|
| album | Album information and metadata |
| artist | Artist details and catalog information |
| customer | Customer profiles and contact information |
| employee | Employee records and organizational hierarchy |
| invoice | Transaction and billing records |
| genre | Music genre classifications |
| invoice_line | Line-item details of invoices |
| media_type | Format and type of media available |
| playlist | User-created and curated playlists |
| playlist_track | Tracks included in playlists |
| track | Individual song information including duration and pricing |

## 🛠️ Technologies Used
- MySQL – Database management and querying
- SQL – Data analysis and manipulation
- Relational Database Design – Data organization
- Query Optimization – Performance analysis

## 🧑‍💻 Project Queries

### ✤ Q1: Senior Most Employee
```sql
-- Who is the senior most employee based on job title?
SELECT * FROM employee 
ORDER BY levels DESC 
LIMIT 1;
```

### ✤ Q2: Countries with Most Invoices
```sql
-- Which countries have the most Invoices?
SELECT billing_country, COUNT(*) as invoice_count
FROM invoice
GROUP BY billing_country
ORDER BY invoice_count DESC;
```

### ✤ Q3: Top 3 Invoice Values
```sql
-- What are top 3 values of total invoice?
SELECT total FROM invoice
ORDER BY total DESC
LIMIT 3;
```

### ✤ Q4: Best Customer City
```sql
-- Which city has the best customers? 
-- (City with most money spent)
SELECT billing_city, SUM(total) as total_spent
FROM invoice
GROUP BY billing_city
ORDER BY total_spent DESC
LIMIT 1;
```

### ✤ Q5: Best Customer
```sql
-- Who is the best customer? 
-- (Customer who has spent the most money)
SELECT c.customer_id, c.first_name, c.last_name, SUM(i.total) as total_spent
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY total_spent DESC
LIMIT 1;
```

### ✤ Q6: Rock Music Listeners
```sql
-- Return the email, first name, last name, & Genre of all Rock Music listeners.
SELECT DISTINCT c.email, c.first_name, c.last_name, g.name
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
JOIN invoice_line il ON i.invoice_id = il.invoice_id
JOIN track t ON il.track_id = t.track_id
JOIN genre g ON t.genre_id = g.genre_id
WHERE g.name LIKE 'Rock'
ORDER BY c.email;
```

### ✤ Q7: Top Rock Artists
```sql
-- Invite the artists who have written the most rock music.
SELECT a.artist_id, a.name, COUNT(t.track_id) as rock_songs
FROM artist a
JOIN album al ON a.artist_id = al.artist_id
JOIN track t ON al.album_id = t.album_id
JOIN genre g ON t.genre_id = g.genre_id
WHERE g.name LIKE 'Rock'
GROUP BY a.artist_id, a.name
ORDER BY rock_songs DESC;
```

### ✤ Q8: Tracks Longer Than Average
```sql
-- Return all track names that have a song length longer than the average song length.
SELECT name, milliseconds
FROM track
WHERE milliseconds > (SELECT AVG(milliseconds) FROM track)
ORDER BY milliseconds DESC;
```

### ✤ Q9: Customer Spending by Artist
```sql
-- Find how much amount spent by each customer on artists?
-- Return customer name, artist name and total spent
SELECT c.customer_id, c.first_name, c.last_name, a.name as artist_name, 
       SUM(il.quantity * il.unit_price) as total_spent
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
JOIN invoice_line il ON i.invoice_id = il.invoice_id
JOIN track t ON il.track_id = t.track_id
JOIN album al ON t.album_id = al.album_id
JOIN artist a ON al.artist_id = a.artist_id
GROUP BY c.customer_id, c.first_name, c.last_name, a.artist_id, a.name
ORDER BY c.customer_id, total_spent DESC;
```

### ✤ Q10: Most Popular Genre by Country
```sql
-- Find the most popular music Genre for each country.
SELECT i.billing_country, g.name, COUNT(il.quantity) as genre_count
FROM invoice i
JOIN invoice_line il ON i.invoice_id = il.invoice_id
JOIN track t ON il.track_id = t.track_id
JOIN genre g ON t.genre_id = g.genre_id
GROUP BY i.billing_country, g.genre_id, g.name
HAVING COUNT(il.quantity) = (
    SELECT MAX(genre_count)
    FROM (
        SELECT COUNT(il.quantity) as genre_count
        FROM invoice i2
        JOIN invoice_line il2 ON i2.invoice_id = il2.invoice_id
        JOIN track t2 ON il2.track_id = t2.track_id
        JOIN genre g2 ON t2.genre_id = g2.genre_id
        WHERE i2.billing_country = i.billing_country
        GROUP BY g2.genre_id, g2.name
    ) as subquery
)
ORDER BY i.billing_country;
```

### ✤ Q11: Customer with Most Spending per Country
```sql
-- Determine the customer that has spent the most on music for each country.
SELECT i.billing_country, c.customer_id, c.first_name, c.last_name, SUM(i.total) as total_spent
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
GROUP BY i.billing_country, c.customer_id, c.first_name, c.last_name
HAVING SUM(i.total) = (
    SELECT MAX(total_spent)
    FROM (
        SELECT SUM(i2.total) as total_spent
        FROM customer c2
        JOIN invoice i2 ON c2.customer_id = i2.customer_id
        WHERE i2.billing_country = i.billing_country
        GROUP BY c2.customer_id
    ) as subquery
)
ORDER BY i.billing_country;
```

## 📈 Key Insights & Analysis Areas

### 📌 Employee Management
- Identify organizational hierarchy and seniority levels
- Track employee performance and responsibilities

### 📌 Geographic Analysis
- Revenue distribution across countries and cities
- Identify high-performing markets for targeted marketing

### 📌 Customer Segmentation
- Classify customers by spending patterns
- Identify VIP and high-value customers
- Understand geographic customer preferences

### 📌 Genre & Artist Performance
- Analyze genre popularity across regions
- Identify top-performing artists by music genre
- Track artist growth and catalog effectiveness

### 📌 Revenue Insights
- Top invoice values and spending patterns
- Customer lifetime value analysis
- Revenue by genre, artist, and region

### 📌 Music Catalog Analysis
- Identify track length preferences
- Analyze playlist composition
- Media type distribution and popularity

## 🔌 Example Use Cases
- Launch targeted promotional campaigns in high-revenue cities
- Develop personalized recommendations based on customer genre preferences
- Negotiate better deals with top-performing artists
- Optimize music catalog based on customer demand
- Identify expansion opportunities in emerging markets
- Create loyalty programs for high-value customers
- Plan music festival events in strategic locations

## 📁 Project Structure

```
Music-Store-Analysis/
├── Queries/
│   ├── Q1_Senior_Employee.sql
│   ├── Q2_Most_Invoices_Country.sql
│   ├── Q3_Top_3_Invoice_Values.sql
│   ├── Q4_Best_City_Customers.sql
│   ├── Q5_Best_Customer.sql
│   ├── Q6_Rock_Listeners.sql
│   ├── Q7_Top_Rock_Artists.sql
│   ├── Q8_Tracks_Above_Average.sql
│   ├── Q9_Customer_Spending_Artist.sql
│   ├── Q10_Popular_Genre_Country.sql
│   └── Q11_Top_Customer_Country.sql
├── Database/
│   ├── music_store_schema.sql
│   ├── music_store_data.sql
│   └── music_store.sql
├── Reports/
│   ├── Analysis_Report.pdf
│   └── Insights.md
├── README.md
└── ...
```

## 📝 Getting Started

### Prerequisites
- MySQL Server (version 5.7 or higher)
- MySQL Client or GUI tool (MySQL Workbench, DBeaver, etc.)

### Setup Instructions
1. Download the database files
2. Create a new database in MySQL
3. Import the provided SQL schema and data files
4. Execute the query files in the Queries folder

## 🧠 Future Improvements
- Build interactive dashboards (Tableau, Power BI)
- Implement machine learning for customer churn prediction
- Create recommendation engine for music suggestions
- Develop real-time sales monitoring system
- Add sentiment analysis on customer reviews
- Implement data warehouse for advanced analytics
- Create predictive models for revenue forecasting

## 👩‍💻 Author

Made with ❤️ by Rakhi Yadav

### Transforming music store data into strategic insights 🎵📊✨  
