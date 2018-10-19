# What is Kanye Thinking?

---

__An exploration into Kanye's lyrics changing over time using Machine Learning__

Kanye West is highly polarizing figure due to his atypical behavior and rece. 
On his most recent album declar bipolar
easy to scoff or dismiss as crzy
bipolar affects x number of people in parts ofthier


__Resources Used:__
- Data Collection and Cleaning
  - API's
  - webscraping
  - text cleaning
- Unsupervised Learning
  - NLP
  - Topic Modeling
  - Word2Vec
  - t-SNE

__Packages Used:__
- JSON
- Pandas
- BeautifulSoup
- sklearn
- gensim
- NLTK
- Spotipy

---

## Objective

_Problem Statement_

How has Kanye's changed as an artist and person and what can we learn using machine learning about detecting bipolar tendencies and behavior changes?

## Data

1. Find source for lyrics and create function to access API for each song
  - My preference was to use a reliable API for lyrics to be able to collect all of Kanye's song. I started by seaching for possible APIs and settled on using Orion Apieseed lyric API. This API allowed to search by song and artist and returned the lyrics for each song. To use this, I just need a list of each of Kanye's song   

`url = "https://orion.apiseeds.com/api/music/lyric/" + artist + "/" + song + "?apikey=" + orion_keys['api_key']`

1. Create song list of Kanye's body of work
  - To generate a full list of Kanye's discography, I wanted to use the most consistent and full reference of work available from him. Spotify was the obvious choice and fortunely there was Spotify wrapper available that allowed accessing using a Python library relatively easy. THis 

```
from spotipy import Spotify
from spotipy.oauth2 import SpotifyClientCredentials

# Kanye's Spoitfy id
Kanye_spotify_id = '5K4W6rqBFWDnAN6FQUkS6x'
album_dict = sp.artist_albums(Kanye_spotify_id,country='US')
```
1. Collect lyrics
  - Started and had access, quite a bit of 404's

  1. Fill in missing songs
  
1. Prep text into corpus
  - stop words (preloaded and custom)
  - stemming
  
1. Vectorize words for use in model
  - _Count Vectorizer_ Count Frequency
  - _TFIDF Vectorizer_ Term Frequency times inverse document frequency (TFIDF)

## Models

- Topic Modeling using Latent Dirichlet Allocation (LDA) and non-negative matrix factorization (NMF)
  - evaluation convergenc
- Word 2 Vec for word grouping
## Analysis

- Change

## Futurework

## Takeaways
