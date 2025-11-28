# SQL-Powered E-Commerce Sales Insights: Trends, Correlations & Performance Metrics.

This project analyzes e-commerce time series sales data using SQL to uncover relationships between product attributes, customer feedback, and sales performance for data-driven business optimization.

The E-Commerce Sales Dataset sourced from Kaggle is a comprehensive collection of transactional information that reflects the dynamics of an online retail marketplace over a 12-month period for 1000+ products.

The project starts with Data overview and profiling. This is followed by finding duplicates and handling null values. It was followed by outlier detection. 

The feature grouping such as review score by category or total units per category is done. This analysis gives us the summary of the dataset. We also look at top 5 most expensive products which will serve as the basis for further analysis.

As the dataset has time series data, it is imperative to study seasonality trends. the data was stable with slight fluctuations. Next, we find top products which generate the most revenue. These are the winners of the catalogue. From our study we found books to be the best products.

Next, we see if higher rated products and categories sell more? The results found that the correlation between review score and sales is not as strong as it expected, which showed there might be some hidden good products who cannot find customers. Same correlation is performed with price and sales, where we see negative correlation ship between them.

As the Bi-variate analysis continues, we attempt to find the  'hidden gems' - products with higher ratings but lower sales, this will help the business to promote them adequately making them easier to find customers and ultimately increase the business. 

Finally, we find 1 winner product per category in the catalogue, The reason we do this analysis is to compare winners per category with their total sales. Like in Electronics the total sale of the winner is 9151, which is much higher than Home & Kitchen with 8337. this comparative analysis helps us locate the under performers and make informed policy decision regarding them.



