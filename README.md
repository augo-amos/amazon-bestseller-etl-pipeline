# Amazon Bestseller Software & Digital Products ETL Pipeline

A comprehensive ETL (Extract, Transform, Load) pipeline that processes Amazon's bestseller software and digital products data from multiple countries. This project extracts data from a REST API, performs data transformation and feature engineering, and loads the processed data into a PostgreSQL database.

## Project Overview

This project automates the process of collecting, cleaning, and analyzing Amazon's top-selling software products across 10 different countries. The pipeline extracts product information including pricing, ratings, reviews, and metadata, then enriches the data with derived features for better analytical insights.

## Features

- **Multi-country Support**: Processes data from IN, US, CA, AU, DE, FR, IT, ES, JP, MX
- **Currency Conversion**: Automatically converts local prices to USD
- **Advanced Feature Engineering**: Creates 7 derived variables for enhanced analysis
- **Database Integration**: Stores processed data in PostgreSQL
- **Data Quality Handling**: Robust error handling for missing values and edge cases

## Technical Stack

- **Python 3.12.7**
- **Pandas** - Data manipulation and transformation
- **Requests** - API data extraction
- **SQLAlchemy** - Database ORM and connection management
- **PostgreSQL** - Data storage and persistence
- **Psycopg2** - PostgreSQL database adapter

## Project Structure
```
amazon_etl/
├── amazon_task.ipynb # Main ETL pipeline notebook
├── amazon_products.csv # Output CSV file
├── requirements.txt # Python dependencies
└── README.md # Project documentation
```

## ETL Process

### 1. Extract
- Fetches data from REST API endpoint: `https://data-liart.vercel.app/data`
- Retrieves JSON data containing Amazon bestseller product information
- Initial data includes product titles, prices, ratings, country codes, and metadata

### 2. Transform
The transformation phase creates several derived variables:

#### Derived Variables:
- **`price_usd`**: Converts local currency to USD using exchange rates
- **`revenue_estimate`**: Estimates potential revenue (price × number of ratings)
- **`rating_bucket`**: Categorizes products into rating tiers (Low, Medium, High, Excellent)
- **`review_density`**: Measures engagement relative to price (ratings/price)
- **`category`**: Classifies products into software categories (Security, Productivity, Design, etc.)
- **`brand`**: Extracts brand names from product titles
- **`country_region`**: Groups countries into geographical regions

#### Currency Support:
- Indian Rupee (₹) → USD: 0.012
- US Dollar ($) → USD: 1.0
- Euro (€) → USD: 1.08
- British Pound (£) → USD: 1.27

#### Rating Categories:
- **Low**: 1–2 stars
- **Medium**: 2–3.5 stars  
- **High**: 3.5–4.5 stars
- **Excellent**: 4.5–5 stars

### 3. Load
- Stores transformed data in PostgreSQL database
- Creates `amazon_products` table with all derived features
- Also exports data to CSV file for alternative use cases
- Uses secure connection with SSL mode required

## Database Schema

The `amazon_products` table includes:

| Column | Type | Description |
|--------|------|-------------|
| product_title | TEXT | Product name and description |
| product_price | TEXT | Original price with currency symbol |
| product_star_rating | FLOAT | Star rating (1-5) |
| product_num_ratings | FLOAT | Number of customer ratings |
| country | TEXT | 2-letter country code |
| price_usd | FLOAT | Price converted to USD |
| revenue_estimates | FLOAT | Estimated revenue potential |
| rating_bucket | TEXT | Rating category (Low/Medium/High/Excellent) |
| review_density | FLOAT | Engagement metric (ratings/price) |
| category | TEXT | Product category (Security/Productivity/etc.) |
| brand | TEXT | Extracted brand name |
| country_region | TEXT | Geographical region grouping |

## Usage

### Prerequisites
- Python 3.8+
- PostgreSQL database
- Required packages: `pandas`, `requests`, `sqlalchemy`, `psycopg2`

### Installation
1. Clone the repository
2. Install dependencies:
   ```bash
   pip install pandas requests sqlalchemy psycopg2

3. Set up PostgreSQL database connection
4. Update database credentials in the notebook

### Running the Pipeline
Execute the Jupyter notebook amazon_task.ipynb cells sequentially to:

1. Extract data from the API
2. Perform all transformations
3. Load data into PostgreSQL
4. Export to CSV

## Analytical Insights
The transformed data enables various analytical use cases:

- Price comparison across regions and categories
- Revenue potential analysis by product type
- Rating distribution and quality assessment
- Market engagement metrics by geography
- Brand performance analysis
- Category-wise market trends

## Configuration
### Database Connection
Update the connection string in the notebook:
`engine = create_engine('postgresql://username:password@host:port/database?sslmode=require')`

### Currency Rates
Modify the currency_rates dictionary for updated exchange rates:

```python
currency_rates = {
    "₹": 0.012,   # Indian Rupee
    "$": 1.0,     # US Dollar
    "€": 1.08,    # Euro
    "£": 1.27     # British Pound
}
```

## Notes
- The pipeline handles missing values gracefully, using NaN for calculations where data is unavailable
- Products with null prices are excluded from revenue calculations
- Brand extraction uses simple string parsing; complex brand names might require additional processing
- The category classification uses keyword matching and may need expansion for new product types

## Contributing
Feel free to submit issues and enhancement requests! Please ensure that any contributions maintain data quality and include appropriate tests.

## License
This project is intended for educational and analytical purposes. Please ensure compliance with Amazon's terms of service when using this code.

## Contact
For questions or suggestions regarding this ETL pipeline, please open an issue in the repository.
