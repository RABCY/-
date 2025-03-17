# -这是关于初学者的爬虫代码目标url='https://www.douban.com/doulist/110585733/'
import requests
from bs4 import BeautifulSoup
import time
import random


def fetch_douban_top250():
    base_url = "https://www.douban.com/doulist/110585733/"
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3"
    }

    all_shows = []

    for page in range(0, 250, 25):
        url = f"{base_url}?start={page}&filter="
        print(f"Fetching URL: {url}")

        response = requests.get(url, headers=headers)
        print(f"Response Status Code: {response.status_code}")

        if response.status_code != 200:
            print(f"Failed to fetch page {page}. Skipping...")
            continue

        soup = BeautifulSoup(response.text, 'html.parser')
        items = soup.find_all('div', class_='doulist-item', id=True)
        print(f"Number of Items Found: {len(items)}")

        for item in items:
            title_element = item.find('div', class_='title')
            rating_element = item.find('span', class_='rating_num')
            abstract_element = item.find('div', class_='abstract')

            title = title_element.text.strip() if title_element else "N/A"
            rating = rating_element.text.strip() if rating_element else "N/A"
            abstract = abstract_element.text.strip().split('\n') if abstract_element else []

            director = abstract[0].split('：')[-1].strip() if len(abstract) > 0 else "N/A"
            actors = abstract[1].split('：')[-1].strip() if len(abstract) > 1 else "N/A"
            genre = abstract[2].split('：')[-1].strip() if len(abstract) > 2 else "N/A"
            country = abstract[3].split('：')[-1].strip() if len(abstract) > 3 else "N/A"
            year = abstract[4].split('：')[-1].strip() if len(abstract) > 4 else "N/A"

            show_info = {
                'title': title,
                'rating': rating,
                'director': director,
                'actors': actors,
                'genre': genre,
                'country': country,
                'year': year
            }

            all_shows.append(show_info)

        # 随机延时，防止被封IP
        time.sleep(random.uniform(2, 3))

    return all_shows


# 调用函数并打印结果
shows = fetch_douban_top250()
for show in shows:
    print(show)
