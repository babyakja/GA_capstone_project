# What is Kanye Thinking?

---

__An exploration into Kanye's lyrics and state of mind using Machine Learning__


Kanye West is highly polarizing figure due to his recent behavior and publicly questionable conduct back to the MTV awards in 200X. Recently, on his latest album _ye_, he declares on the cover “I hate being Bi-Polar, its awesome” and sings about it being his superpower on the song _Yikes_. It may be easy to scoff at or dismiss such a condition as being crazy but the reality of bipolar disorder affectsing (x number) of people is an actual problem needing to be addressed. 


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
  - To generate a full list of Kanye's discography, I wanted to use the most consistent and full reference of work available from him. Spotify was the obvious choice and fortunely there was Spotify wrapper available that allowed accessing using a Python library relatively easy. To access each song, I first had to look up each of Kanye's album using the album id used by Spotify and then extract from the returned dictionary the song name of each entry. Some simple `FOR` loops and function building made this part relatively easy.

```
from spotipy import Spotify
from spotipy.oauth2 import SpotifyClientCredentials

# Kanye's Spoitfy id
Kanye_spotify_id = '5K4W6rqBFWDnAN6FQUkS6x'
album_dict = sp.artist_albums(Kanye_spotify_id,country='US')
```
1. Collect lyrics
  - Once a full list of of Kanye's song was made, I could pass the list into a function I built to collect the lyrics from Orion Apiseed. After starting extracting, it became quite clear there was an issue since I was getting quite a bit of 404's. What was occuring was a mismatch 

  1. Fill in missing songs
  
1. Prep text into corpus
  - stop words (preloaded and custom)
  - stemming
  
1. Vectorize words for use in model
  - _Count Vectorizer_ Count Frequency
  - _TFIDF Vectorizer_ Term Frequency times inverse document frequency (TFIDF)

## Models

- Topic Modeling using Latent Dirichlet Allocation (LDA) and non-negative matrix factorization (NMF)
  - evaluation convergence
- Word 2 Vec for word grouping
## Analysis

- Change

## Futurework

There is need to continue the stagnant conversation around mental health and what tools we use to connect those that struggle with issues with the proper resources. What we can discover can help the millions of not only those living with bipolar disorder but the ones who care about them


## Takeaways


Data:
> __Song text data can be inconsistent and difficult to align__
