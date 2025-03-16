```python
import requests
import json
import pandas as pd
from bs4 import BeautifulSoup
```


```python
URL = "https://www.imdb.com/chart/top/"
```


```python
# Headers to mimic a real browser
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36"
}
```


```python
# Fetch the page content
response = requests.get(URL, headers=HEADERS)
```


```python
if response.status_code == 200:
    soup = BeautifulSoup(response.text, "html.parser")

    # Extract JSON-LD data from IMDb
    json_data = None
    for script in soup.find_all("script", type="application/ld+json"):
        if '"@type":"ItemList"' in script.text:  # Look for ItemList JSON
            json_data = json.loads(script.string)
            break  # Stop after finding the correct JSON

    if json_data:
        movies = json_data["itemListElement"][:10]  # List of movies

        movie_list = []
        for movie in movies:
            item = movie["item"]  # Get the movie details
            
            # Extract relevant data
            title = item.get("name", "N/A")
            url = item.get("url", "N/A")
            rating = item.get("aggregateRating", {}).get("ratingValue", "N/A")
            year = item.get("datePublished", "N/A")  # Some movies may not have this
            genre = item.get("genre", "N/A")
            duration = item.get("duration", "N/A")
            
            # Store movie details in a list
            movie_list.append({
                "Title": title,
                "Year": year,
                "Rating": rating,
                "Genre": genre,
                "Duration": duration,
                "URL": url
            })

        #rint(movie_list)
        # Convert list to DataFrame
        df = pd.DataFrame(movie_list)

        # Save to CSV
        df.to_csv("imdb_top_movies.csv", index=False)

        print("Scraping successful! IMDb Top movies saved to 'imdb_top_movies.csv'.")
    else:
        print("JSON data not found on IMDb page. Check the page structure.")
else:
    print(f" Failed to fetch IMDb data. Status code: {response.status_code}")
```

    Scraping successful! IMDb Top movies saved to 'imdb_top_movies.csv'.
    


```python

```
