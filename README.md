# Python-lab

import requests
from bs4 import BeautifulSoup

url = "https://www.imdb.com/chart/top"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3"
}

# Request the IMDb Top 250 page
response = requests.get(url, headers=headers)

# Check if the request was successful
if response.status_code == 200:
    print("Page fetched successfully!")
    soup = BeautifulSoup(response.content, "html.parser")

    # Use the correct selector to get the movie titles
    movies = soup.select("div.ipc-title a.ipc-title-link-wrapper h3.ipc-title__text")

    # Debug: Check how many movies are retrieved
    print(f"Found {len(movies)} movies.")

    # Iterate through the first 10 movies
    for movie in movies[:10]:
        # Construct the movie detail URL
        link = "https://www.imdb.com" + movie.parent.get("href")
        print(f"Fetching details for: {link}")  # Debugging line

        movie_response = requests.get(link, headers=headers)

        if movie_response.ok:
            movie_soup = BeautifulSoup(movie_response.content, "html.parser")

            # Extract movie details with updated selectors
            try:
                # Movie Title (updated selector)
                movie_name = movie_soup.select_one("span.hero__primary-text").get_text(strip=True)
            except AttributeError:
                movie_name = "N/A"
                
            try:
                # Movie Year (updated approach)
                movie_year = movie_soup.select_one("a[href*='releaseinfo']").get_text(strip=True)
            except AttributeError:
                movie_year = "N/A"
                
            try:
                # Movie Summary (updated selector)
                movie_summary = movie_soup.select_one("span.sc-42125d72-0.gKbnVu").get_text(strip=True)
            except AttributeError:
                movie_summary = "N/A"

            # Print movie details
            print(f"Movie Name: {movie_name}")
            print(f"Movie Year: {movie_year}")
            print(f"Movie Summary: {movie_summary}")
            print("--------------------")
else:
    print(f"Failed to fetch the IMDb Top 250 page. Status code: {response.status_code}")
