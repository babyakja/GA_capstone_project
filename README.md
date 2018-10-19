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

_Obtain the Data_

__1. Find source for lyrics and create function to access API for each song__
  - My preference was to use a reliable API for lyrics to be able to collect all of Kanye's song. I started by seaching for possible APIs and settled on using Orion Apieseed lyric API. This API allowed to search by song and artist and returned the lyrics for each song. To use this, I just needed to generate a list of each of Kanye's song.
  
`url = "https://orion.apiseeds.com/api/music/lyric/" + artist + "/" + song + "?apikey=" + orion_keys['api_key']`

__2. Create song list of Kanye's body of work__
  - To generate a full list of Kanye's discography, I wanted to use the most consistent and full reference of work available from him. Spotify was the obvious choice and fortunely there was Spotify wrapper available that allowed accessing using a Python library relatively easy. To access each song, I first had to look up each of Kanye's album using the album id used by Spotify and then extract from the returned dictionary the song name of each entry. Some simple `FOR` loops and function building made this part relatively easy.

```
from spotipy import Spotify
from spotipy.oauth2 import SpotifyClientCredentials

# Kanye's Spoitfy id
Kanye_spotify_id = '5K4W6rqBFWDnAN6FQUkS6x'
album_dict = sp.artist_albums(Kanye_spotify_id,country='US')
```
__3. Collect lyrics__
  - Once a full list of of Kanye's song was made, I could pass the list into a function I built to collect the lyrics from Orion Apiseed. After starting extracting, it became quite clear there was an issue since I was getting quite a bit of 404's. What was occuring was a mismatch in song title with what was available in the Orion API. This was either due to __A)__ slight variation in the song title between Orion and Spotify or __B)__ Orion not having the song in their API.

  _3a. Fill in missing songs_
  
  -  There was a total of 29 songs that were not able to collect lyrics for using the Orion API. In order to fill in the gaps, I decide not to use an API and instead rely on scraping the Genius lyric site using Beautiful Soup. Understand source. Text cleaning. Extraxt data. 
  
_Explore the Data_

> __128__ Unique Songs | __14__ Albums
  
1. Prep text into corpus
  - stop words (preloaded and custom)
  - stemming
  
2. Vectorize words for use in model
  - _Count Vectorizer_ Count Frequency
  - _TFIDF Vectorizer_ Term Frequency times inverse document frequency (TFIDF)

## Models

_ Model the Data using Unsupervised Learning_

- Topic Modeling using Latent Dirichlet Allocation (LDA) and non-negative matrix factorization (NMF)
  - evaluation convergence
- Word 2 Vec for word grouping
## Analysis

_Evaluate the Data_

- Change

## Futurework

There is need to continue the stagnant conversation around mental health and what tools we use to connect those that struggle with issues with the proper resources. What we can discover can help the millions of not only those living with bipolar disorder but the ones who care about them


## Conclusion

_Answer the Problem_

_Takeaways_

Data:
> __Song text data can be inconsistent and difficult to align__
