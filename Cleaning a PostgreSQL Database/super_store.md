In this project, i will work with data from hypothetical Super Store. I used a dataset from `DataCamp`. This project aims to identify top categories based on the highest profit margins and to detect missing values. 

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