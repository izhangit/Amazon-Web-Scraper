# Amazon Web Scraping

### - By Izhan Abdullah 


```python
from bs4 import BeautifulSoup
import requests
import pandas as pd
import numpy as np
from urllib.parse import urljoin

```


```python
# Function to extract Product Title
def get_title(soup):
    try:
        title = soup.find("span", attrs={'id': 'productTitle'})
        if title:
            title_value = title.text.strip()
            return title_value
        else:
            return ""
    except AttributeError:
        return ""


    return title_string


# Function to extract Product Price
def get_price(soup):
    try:
        price = soup.find("span", attrs = {'class' : 'a-price-whole'}).string.strip()

    except AttributeError:
        try:
            # If there is some deal price
            price = soup.find("span", attrs = {'class' : 'a-price-whole'}).string.strip()
        except:
            price = ""

    return price


# Function to extract Product Rating
def get_rating(soup):
    try:
        rating = soup.find("i", attrs = {'id' : 'acrCustomerReviewText'}).string.strip()

    except AttributeError:
        try:
            rating = soup.find("span",attrs = {'id' : 'acrCustomerReviewText'}).string.strip()
        except:
            rating = ""

    return rating


# Function to extract Number of User Reviews
def get_review_count(soup):
    try:
        review_count = soup.find("i", attrs={'class': 'a-icon a-icon-star a-star-4-5 cm-cr-review-stars-spacing-big'}).string.strip()

    except AttributeError:
        review_count = ""

    return review_count

```


```python
if __name__ == '__main__':
    # user agent
    HEADERS = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36',
        'Accept-Language': 'en-US, en;q=0.5'
    }

    # Webpage URL
    URL = "https://www.amazon.com/s?k=playstation+5&crid=1D0N9P29353LK&sprefix=playstation+5%2Caps%2C375&ref=nb_sb_noss_2"

    # HTTP Request
    webpage = requests.get(URL, headers=HEADERS)

    # Soup Object containing all data
    soup = BeautifulSoup(webpage.content, "html.parser")

    # Fetch links as list of Tag Objects
    Links = soup.find_all("a", attrs={'class': 'a-link-normal s-underline-text s-underline-link-text s-link-style a-text-normal'})

    # Store the Links
    Links_list = []

    # Loop for extracting links from Tag objects
    for link in Links:
        Links_list.append(link.get('href'))

    d = {
        "title": [],
        "price": [],
        "rating": [],
        "reviews": []
    }


    for link in Links_list:
        # Make sure the link is a valid URL before making a request
        full_url = urljoin("https://www.amazon.com", link)
        
        try:
            new_webpage = requests.get(full_url, headers=HEADERS)
            new_soup = BeautifulSoup(new_webpage.content, "html.parser")
            
            # Function calls to display all necessary product information
            d['title'].append(get_title(new_soup))
            d['price'].append(get_price(new_soup))
            d['rating'].append(get_rating(new_soup))
            d['reviews'].append(get_review_count(new_soup))

        except requests.exceptions.RequestException as e:
            print("Error making request for link:", full_url)
            print("Error message:", str(e))

    
    amazon_df = pd.DataFrame.from_dict(d)
    amazon_df['title'].replace('',np.nan, inplace = True)
    amazon_df = amazon_df.dropna(subset=['title'])
    amazon_df.to_csv("amazon_data.csv", header= True, index= False)
```


```python
amazon_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>price</th>
      <th>rating</th>
      <th>reviews</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>PlayStation 5 Console (PS5)</td>
      <td></td>
      <td>7,134 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>PlayStation 5 Console – Marvel’s Spider-Man 2 ...</td>
      <td></td>
      <td>121 ratings</td>
      <td>4.6 out of 5 stars</td>
    </tr>
    <tr>
      <th>2</th>
      <td>PlayStation 5 Console CFI-1102A</td>
      <td></td>
      <td>7,922 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>PlayStation PS5 Console – God of War Ragnarök ...</td>
      <td></td>
      <td>12,312 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>PlayStation 5 Console (Renewed)</td>
      <td></td>
      <td>31 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>5</th>
      <td>$100 PlayStation Store Gift Card [Digital Code]</td>
      <td></td>
      <td>230,001 ratings</td>
      <td>4.7 out of 5 stars</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Just Dance 2023 Edition (Code In Box) for Play...</td>
      <td></td>
      <td>287 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>7</th>
      <td>$50 PlayStation Store Gift Card [Digital Code]</td>
      <td></td>
      <td>230,001 ratings</td>
      <td>4.7 out of 5 stars</td>
    </tr>
    <tr>
      <th>8</th>
      <td>$25 PlayStation Store Gift Card [Digital Code]</td>
      <td></td>
      <td>230,001 ratings</td>
      <td>4.7 out of 5 stars</td>
    </tr>
    <tr>
      <th>9</th>
      <td>$30 PlayStation Plus – Wallet Funds [Digital C...</td>
      <td></td>
      <td>230,001 ratings</td>
      <td>4.7 out of 5 stars</td>
    </tr>
    <tr>
      <th>10</th>
      <td>$70 PlayStation Plus – Wallet Funds [Digital C...</td>
      <td></td>
      <td>230,001 ratings</td>
      <td>4.7 out of 5 stars</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Retro Game Console,Classic Mini Console with B...</td>
      <td></td>
      <td>384 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>12</th>
      <td>PlayStation DualSense Wireless Controller</td>
      <td></td>
      <td>91,894 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>13</th>
      <td>Dead Island 2: HELL-A Edition - PlayStation 5</td>
      <td></td>
      <td>300 ratings</td>
      <td>4.7 out of 5 stars</td>
    </tr>
    <tr>
      <th>14</th>
      <td>EA SPORTS WRC - PlayStation 5</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>15</th>
      <td>Among Us: Ejected Edition - PlayStation 5</td>
      <td></td>
      <td>86 ratings</td>
      <td>4.4 out of 5 stars</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Dust Cover Net for PS5 Console - Dustproof Hea...</td>
      <td></td>
      <td>7 ratings</td>
      <td>4.4 out of 5 stars</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Ratchet &amp; Clank: Rift Apart - PlayStation 5</td>
      <td></td>
      <td>2,680 ratings</td>
      <td></td>
    </tr>
    <tr>
      <th>18</th>
      <td>Assassin's Creed® Mirage Launch Edition, PlayS...</td>
      <td></td>
      <td>9 ratings</td>
      <td>4.4 out of 5 stars</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Lords of the Fallen Deluxe Edition - PlayStati...</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>20</th>
      <td>NASCAR Arcade Rush - PlayStation 5</td>
      <td></td>
      <td>1 rating</td>
      <td></td>
    </tr>
    <tr>
      <th>21</th>
      <td>PDP Victrix Pro BFG Wireless Gaming Controller...</td>
      <td></td>
      <td>851 ratings</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>


