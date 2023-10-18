# Tonnes_Project
Scraping a list of Nigerian players who play overseas from a website so we can anlayse

from bs4 import BeautifulSoup

import requests

url = 'https://ng.soccerway.com/players/players_abroad/nigeria/'

req = requests.get(url,headers={'User-Agent':'Mozilla/5.0'})

page = req

soup = BeautifulSoup(page.text, 'html')

table = soup.find_all('table', class_="playersabroad table")

player_country = soup.find_all('th')

player_country_header = [country.text.strip() for country in player_country]

import pandas as pd
import numpy as np

#table_data = soup.find_all('td')

cols = ["player-name", "League", "team_name"]

tmp = pd.read_html(requests.get(
    url, headers={"User-Agent": "Mozilla/5.0"}).content)[0]
    # the `#storage_options` argument throws an HTTPError

df = (
    tmp.T.reset_index().T # to slip down the incorrect 'England' header
        .assign(country=lambda x: x.pop(3).str.split(".").str[0].ffill())
        .iloc[1:].loc[tmp.iloc[:, -1].isna()]
        .set_axis(cols + ["country"], axis=1)
)

print(df)


