# Mock Dataset Summary
## Overview

This document provides a summary of the mock dataset generated by a Rust program. The dataset simulates transactional data and includes various attributes related to transactions in different office locations.
## Dataset Schema

The dataset consists of the following fields:

    Year-Month (String): The year and month of the transaction, formatted as YYYY-MM.
    Office Location (String): The location of the office where the transaction occurred. This is one of the five largest cities in the US.
    Product Class (String): The class of the product involved in the transaction. The classes include Commercial, Financial, Consumer, or Support.
    Customer Class (String): The class of the customer based on age, grouped by decade (e.g., 10-19, 20-29, etc.).
    Transaction Count (Integer): The count of transactions.
    Transaction Amount (Float): The total amount of the transaction in USD.

## Data Generation

The data was generated using the following criteria:

    Year Range: 2000 to 2022.
    Month Range: January (1) to December (12).
    Office Locations: New York, Los Angeles, Chicago, Houston, Phoenix.
    Product Classes: Commercial, Financial, Consumer, Support.
    Customer Classes: 10-19, 20-29, 30-39, 40-49, 50-59, 60-69, 70-79, 80+.
    Transaction Count Range: 1 to 100.
    Transaction Amount Range: $100.00 to $10,000.00.

## File Format

The dataset is stored in a parquet file named mock_dataset.parquet, located in the ../data directory.

## Usage

This dataset is intended for mock purposes, such as testing, development of data processing pipelines, and analysis practice. It is not based on real transaction data.

## Implementation  

python
```bash
$ pip install pandas pyarrow numpy
```

```python
import pandas as pd
import numpy as np
import os
from pyarrow import parquet as pq

def generate_parquet():
    office_locations = ["New York", "Los Angeles", "Chicago", "Houston", "Phoenix"]
    product_classes = ["Commercial", "Financial", "Consumer", "Support"]
    customer_classes = ["10-19", "20-29", "30-39", "40-49", "50-59", "60-69", "70-79", "80+"]
    
    data = {
        "year_month": [f"{np.random.randint(2000, 2023)}-{np.random.randint(1, 13):02}" for _ in range(10000)],
        "office_location": [np.random.choice(office_locations) for _ in range(10000)],
        "product_class": [np.random.choice(product_classes) for _ in range(10000)],
        "customer_class": [np.random.choice(customer_classes) for _ in range(10000)],
        "transaction_count": np.random.randint(1, 101, size=10000),
        "transaction_amount": np.random.uniform(100.0, 10001.0, size=10000)
    }

    df = pd.DataFrame(data)

    # Create the 'data' directory if it doesn't exist
    data_dir = os.path.join(os.path.dirname(__file__), 'data')
    os.makedirs(data_dir, exist_ok=True)

    # Set the file path for the Parquet file
    file_path = os.path.join(data_dir, "mock_dataset.parquet")

    # Write the DataFrame to a Parquet file
    df.to_parquet(file_path)

    return True

if __name__ == "__main__":
    generate_parquet()
```
