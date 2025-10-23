# Super Store Dataset
In this project, i worked with data from hypothetical Super Store. I used a dataset from `DataCamp`. This project aims to identify top categories based on the highest profit margins and to detect missing values. 

## Data Dictionary:

### `orders`:
| Column | Definition | Data type | Comments |
|--------|------------|-----------|----------|
| `row_id`| Unique Record ID | `INTEGER` |
| `order_id` | Identifier for each order in table | `TEXT` | Connects to `order_id` in `returned_orders` table |
| `order_date` | Date when order was placed | `TEXT` |
| `market` | Market order_id belongs to | `TEXT` |
| `region` | Region Customer belongs to | `TEXT` | Connects to `region` in `people` table |
| `product_id` | Identifier of Product bought | `TEXT` | Connects to `product_id` in `products` table |
| `sales` | Total Sales Amount for the Line Item | `DOUBLE PRECISION` |
| `quantity` | Total Quantity for the Line Item | `DOUBLE PRECISION` |
| `discount` | Discount applied for the Line Item | `DOUBLE PRECISION` |
| `profit` | Total Profit earned on the Line Item | `DOUBLE PRECISION` |

### `returned_orders`:
| Column | Definition | Data type |
|--------|------------|-----------|
| `returned`| Yes values for Order / Line Item Returned | `TEXT` |
| `order_id` | Identifier for each order in table | `TEXT` |
| `market` | Market order_id belongs to | `TEXT` |

### `people`:
| Column | Definition | Data type |
|--------|------------|-----------|
| `person`| Name of Salesperson credited with Order | `TEXT` |
| `region` | Region Salesperson in operating in | `TEXT` |

### `products`:
| Column | Definition | Data type |
|--------|------------|-----------|
| `product_id`| Unique Identifier for the Product | `TEXT` |
| `category` | Category Product belongs to | `TEXT` |
| `sub_category` | Sub Category Product belongs to | `TEXT` |
| `product_name` | Detailed Name of the Product | `TEXT` |


<br>

#

This query finds top 5 best selling product in each category based on total sales

**How it work:**

First, I joined two tables `orders` and `products` using `product_id`. Then, i grupped data by `category` and `product_name`. For each product, I calculated total sales and total profit. After that, I used `RANK()` function to rank products within each category from the highest and lowest sales. And finally, I filtered results to show only the 5 top products in each category.


```
-- top_five_products_each_category
SELECT * FROM (
	SELECT p.category, p.product_name, 
		ROUND(SUM(CAST(o.sales AS NUMERIC)),2) AS product_total_sales,
		ROUND(SUM(CAST(o.profit AS NUMERIC)),2) AS product_total_profit, 
		RANK() OVER(PARTITION BY p.category ORDER BY SUM(o.sales) DESC) AS product_rank 
	FROM orders o 
		INNER JOIN products p
		ON o.product_id = p.product_id
	GROUP BY p.category, p.product_name) as data
	WHERE product_rank < 6
	
```
### Results:

| index | category        | product_name                                             | product_total_sales | product_total_profit | product_rank |
| ----- | --------------- | -------------------------------------------------------- | ------------------- | -------------------- | ------------ |
| 0     | Furniture       | Hon Executive Leather Armchair, Adjustable               | 58193.48            | 5997.25              | 1            |
| 1     | Furniture       | Office Star Executive Leather Armchair, Adjustable       | 51449.80            | 4925.80              | 2            |
| 2     | Furniture       | Harbour Creations Executive Leather Armchair, Adjustable | 50121.52            | 10427.33             | 3            |
| 3     | Furniture       | SAFCO Executive Leather Armchair, Black                  | 41923.53            | 7154.28              | 4            |
| 4     | Furniture       | Novimex Executive Leather Armchair, Adjustable           | 40585.13            | 5562.35              | 5            |
| 5     | Office Supplies | Eldon File Cart, Single Width                            | 39873.23            | 5571.26              | 1            |
| 6     | Office Supplies | Hoover Stove, White                                      | 32842.60            | -2180.63             | 2            |
| 7     | Office Supplies | Hoover Stove, Red                                        | 32644.13            | 11651.68             | 3            |
| 8     | Office Supplies | Rogers File Cart, Single Width                           | 29558.82            | 2368.82              | 4            |
| 9     | Office Supplies | Smead Lockers, Industrial                                | 28991.66            | 3630.44              | 5            |
| 10    | Technology      | Apple Smart Phone, Full Size                             | 86935.78            | 5921.58              | 1            |
| 11 | Technology  | Cisco Smart Phone, Full Size                             | 76441.53            | 17238.52             | 2            |
| 12    | Technology      | Motorola Smart Phone, Full Size                          | 73156.30            | 17027.11             | 3            |
| 13    | Technology      | Nokia Smart Phone, Full Size                             | 71904.56            | 9938.20              | 4            |
| 14    | Technology      | Canon imageCLASS 2200 Advanced Copier                    | 61599.82            | 25199.93             | 5            |
