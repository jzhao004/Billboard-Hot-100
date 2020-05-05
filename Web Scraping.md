#### Install Required Libraries

```python
from bs4 import BeautifulSoup 
import requests
import csv
import re
```

#### Web Scrape Billboard Hot 100 webpage
##### Access HTML content of Billboard Hot 100 webpage and parse HTML content 

```python
main_url = 'https://www.billboard.com/charts/hot-100'
main_r = requests.get(main_url)
main_soup = BeautifulSoup(main_r.content, 'html.parser')
```

##### Extract urls of all subpages containing song lyrics from HTML content

```python
links = main_soup.find_all('div', {'class':'chart-list-item__lyrics '})
```

##### Create csv file for data

```python
f = csv.writer(open('billboard-hot100-lyrics.csv', 'w'))
f.writerow(['title', 'artist', 'lyrics', 'url'])
```

##### Access and parse HTML content for each subpage, extract song title, artist(s), and lyrics from HTML content, and save data to csv file

```python 
for link in links:
    url = str(link.find('a').get('href'))
    r = requests.get(url)
    soup = BeautifulSoup(r.content, 'html.parser')
    song = str(soup.find('div', {'class':'lyrics'}))
    
    # Extract artist and song title 
    pat = re.compile(r'"(.*?)"')
    song_title = song[song.find("data-lyric-title"):song.find(">")]
    song_artist = song[song.find("data-lyric-artist"):song.find(">")]

    title = pat.search(song_title)
    if title is not None:
        title = title.group(0)
        
    artist = pat.search(song_artist)
    if artist is not None: 
        artist = artist.group(0)

    # Extract lyrics 
    song_lyrics =  song[:song.find("<em>")]
    lyrics = re.sub("<.*?>"," ", song_lyrics)
    lyrics = re.sub("{.*?}"," ", lyrics)
    
    # Save data to csv file  
    if title is not None and artist is not None:     
        f.writerow([title, artist, lyrics, url])
```
