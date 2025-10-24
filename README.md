### EX8 Web Scraping On E-commerce platform using BeautifulSoup
### DATE: 
### AIM: To perform Web Scraping on Snapdeal using (beautifulsoup) Python.
### Description: 
<div align = "justify">
Web scraping is the process of extracting data from various websites and parsing it. In other words, it’s a technique 
to extract unstructured data and store that data either in a local file or in a database. 
There are many ways to collect data that involve a huge amount of hard work and consume a lot of time. Web scraping can save programmers many hours. Beautiful Soup is a Python web scraping library that allows us to parse and scrape HTML and XML pages. 
One can search, navigate, and modify data using a parser. It’s versatile and saves a lot of time.
<p>The basic steps involved in web scraping are:
<p>1) Loading the document (HTML content)
<p>2) Parsing the document
<p>3) Extraction
<p>4) Transformation

### Procedure:

1) Import necessary libraries (requests, BeautifulSoup, re, matplotlib.pyplot).
2) Define convert_price_to_float(price) Function: to Remove non-numeric characters from a price string and convert it to a float.
3) Define get_Snapdeal_products(search_query) Function: to Scrape Amazon for product information based on the search query.
4) Fetch and parse the HTML content then Extract product names and prices from the search results and Sort product information based on converted prices in ascending order.
5) Return sorted product data as a list of dictionaries.
6) Call get_Snapdeal_products(search_query) to get product data based on the user's search query.
7) Check if products are found; if not, display "No products found."
8) Visualize Product Data using a Bar Chart

### Program:
```python
import requests
from bs4 import BeautifulSoup
import re
import matplotlib.pyplot as plt

def convert_price_to_float(price):
    """Convert price string like '₹1,299.00' → 1299.0"""
    price = re.sub(r'[^\d.]', '', price)
    parts = price.split('.')
    if len(parts) > 1:
        price = parts[0] + '.' + ''.join(parts[1:])
    return float(price) if price else 0.0

def get_amazon_products(search_query):
    base_url = 'https://www.amazon.in'
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
                      "AppleWebKit/537.36 (KHTML, like Gecko) "
                      "Chrome/114.0.0.0 Safari/537.36",
        "Accept-Language": "en-IN,en;q=0.9",
        "Referer": "https://www.google.com/",
        "Connection": "keep-alive"
    }

    search_query = search_query.replace(' ', '+')
    url = f'{base_url}/s?k={search_query}'

    session = requests.Session()
    response = session.get(url, headers=headers)
    products_data = []

    if response.status_code == 200:
        soup = BeautifulSoup(response.content, 'html.parser')
        product_elements = soup.select('div.s-main-slot div[data-component-type="s-search-result"]')

        for product in product_elements:
            # Product title
            title_elem = product.select_one('h2 span')
            title = title_elem.text.strip() if title_elem else 'No Title'

            # Current price
            price_container = product.select_one('span.a-price')
            if price_container:
                price_whole = price_container.select_one('span.a-price-whole')
                price_fraction = price_container.select_one('span.a-price-fraction')
                if price_whole and price_fraction:
                    price = f"{price_whole.text.strip()}.{price_fraction.text.strip()}"
                elif price_whole:
                    price = price_whole.text.strip()
                else:
                    price = '0'
            else:
                price = '0'

            # List price / Original price for discount calculation
            list_price_elem = product.select_one('span.a-price.a-text-price span.a-offscreen')
            list_price = convert_price_to_float(list_price_elem.text) if list_price_elem else convert_price_to_float(price)

            current_price = convert_price_to_float(price)
            # Discount percentage
            discount = round(((list_price - current_price) / list_price) * 100, 2) if list_price > current_price else 0.0

            products_data.append({
                'Product': title,
                'Price': current_price,
                'Discount (%)': discount
            })

            print(f'Product: {title}\nPrice: ₹{current_price}\nDiscount: {discount}%\n---')

    else:
        print(f'Failed to fetch Amazon page: {response.status_code}')

    # Sort by price ascending
    return sorted(products_data, key=lambda x: x['Price'])

# Main
search_query = input('Enter product to search on Amazon: ')
products = get_amazon_products(search_query)

# Plotting
if products:
    product_names = [p['Product'][:30] + '...' if len(p['Product']) > 30 else p['Product'] for p in products]
    product_prices = [p['Price'] for p in products]
    product_discounts = [p['Discount (%)'] for p in products]

    plt.figure(figsize=(12, 8))
    bars = plt.barh(range(len(product_prices)), product_prices, color='skyblue')
    plt.xlabel('Price (₹)')
    plt.ylabel('Product')
    plt.title(f'Products, Prices & Discounts on Amazon for "{search_query}"')
    plt.yticks(range(len(product_prices)), [f"{n} ({d}%)" for n, d in zip(product_names, product_discounts)])
    plt.tight_layout()
    plt.show()
else:
    print('No products found.')

```
### Output:

<img width="1283" height="424" alt="image" src="https://github.com/user-attachments/assets/c20f4a23-3d83-4277-aad8-392328e58ee5" />
<img width="1291" height="426" alt="image" src="https://github.com/user-attachments/assets/8e30083e-7d1b-459b-b54b-57b5d22aafae" />
<img width="1289" height="428" alt="image" src="https://github.com/user-attachments/assets/6bb26ff9-f096-4830-8d00-ff1b62335587" />
<img width="1282" height="377" alt="image" src="https://github.com/user-attachments/assets/4c7996b7-6019-45df-823e-86c4b173bf38" />

### Result:

Hence, Successfully performed Web Scraping on Snapdeal using (beautifulsoup) Python.
