# [fit] _Data Science - Immersive_
# [fit] Capstone Project @ GA
###__James Babyak__
###__ATX DSI-5__

---

![](assets/kanye-ye.jpg)

# [fit] What is Kanye Thinking?
An exploration into the state of Kanye using Machine Learning

---

![](assets/kanye-ye.jpg)

## __Objective__
How has Kanye changed as an artist and person and what can we find using machine learning about how this is reflected in his music?

---


## __Skills__

### _Applied Tools:_
- Unsupervised Learning
- Topic Modeling
- NLP
- Neural Networks

---

## __Resources__

### _Libraries Used:_
- JSON
- Pandas
- Scikit-Learn
- gensim

---

## __Data__

1. Find source for lyrics and create function to access API for each song
1. Create song list of Kanye's body of work
  - Fill in missing songs

---

```Python
# Make function to scrape Genius lyrics site
def scrap_song_url(song, artist):
    song = song.replace(" ","-")
    print(song)
    url = 'https://genius.com/'+artist+'-'+song+'-lyrics'
    res = requests.get(url)
    print(url)
    if res.status_code != 200:
        artist_2 = 'kids-see-ghosts'
        url2 = 'https://genius.com/'+artist_2+'-'+song+'-lyrics'
        res = requests.get(url2)
        if res.status_code != 200:
            print('Status error: ', res.status_code)
            lyrics = ""
            pass
        else:
            html = BeautifulSoup(res.text, 'html.parser')
            lyrics = html.find('div', class_='lyrics').get_text()
    else:
        html = BeautifulSoup(res.text, 'html.parser')
        lyrics = html.find('div', class_='lyrics').get_text()

    return lyrics
```

---

## __More Data__

### _Clean Data_
  - Unecessary tags or comments from source
  - Remove Stop words

---
## __Vectorize!__

### _Vectorizers_
- TF-IDF
- Count

```python
tvec = TfidfVectorizer(min_df=5, # min apperance in observation
                       max_df=0.85, # max apperance in observation
                       stop_words=stop, # stop words to ignore
                       ngram_range=(1,3), # word length
                       token_pattern=r"(?u)\w+\'\w+\b|\b\w+"
                      )
tfidf = tvec.fit_transform(corpus);  
```
---

## __Models__
### _Topic Modeling_
  - Latent Dirschelt Allocation (Bayesian Inference)
  - Non negative matrix factorization

---

## __Still Ahead__    
- Word 2 vec
  - Similar words
- Recursive Neural Network

---

### THANKS!
