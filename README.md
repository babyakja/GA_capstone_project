# What is Kanye Thinking?

---

__An exploration into Kanye's lyrics and state of mind using Machine Learning__


Kanye West is one of the most controversial public figures over the last decade and recently has been scrutinized for his views on politics and Trump. This has led many to view his actions as something to joke about or just plainly being dismissed as crazy. However, on his latest album __ye__, he openly states on the cover _“ I hate being Bi-Polar, its awesome”_ and writes about bipolar disorder being his superpower on the track _Yikes_.

__Resources Used:__
- Data Collection and Cleaning
  - API's
  - web-scraping
  - text cleaning
- Unsupervised Learning
  - NLP
  - Topic Modeling
  - Word2Vec
  - t-SNE

__Python Packages Used:__
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

__Using Machine Learning, what can we learn about how Kanye's behavior and mental state has changed over time and can this be used to build models that can help predict bipolar episodes?__


## Data

Note: WORDS USED IN LYRICS WERE NOT FILTERED OR CENSORED, SOME EXPLICIT CONTENT AHEAD

_Obtain the Data_

__1. Find source for lyrics and create function to access API for each song__
  - My preference was to use a reliable API for lyrics to be able to collect all of Kanye's song. I started by searching for possible APIs and settled on using Orion Apieseed lyric API. This API allowed to search by song and artist and returned the lyrics for each song. To use this, I just needed to generate a list of each of Kanye's song.

`url = "https://orion.apiseeds.com/api/music/lyric/" + artist + "/" + song + "?apikey=" + orion_keys['api_key']`

__2. Create song list of Kanye's body of work__
  - To generate a full list of Kanye's discography, I wanted to use the most consistent and full reference of work available from him. Spotify was the obvious choice and fortunately there was Spotify wrapper available that allowed accessing using a Python library relatively easy. To access each song, I first had to look up each of Kanye's album using the album id used by Spotify and then extract from the returned dictionary the song name of each entry.

```python
from spotipy import Spotify
from spotipy.oauth2 import SpotifyClientCredentials

# Kanye's Spoitfy id
Kanye_spotify_id = '5K4W6rqBFWDnAN6FQUkS6x'
album_dict = sp.artist_albums(Kanye_spotify_id,country='US')
```
__3. Collect lyrics__
  - Once a full list of of Kanye's song was made, I could pass the list into a function to collect the lyrics from Orion Apiseed. After starting extracting lyrics, it became quite clear there was an issue since I was getting quite a bit of 404's from individual requests. What was occurring was a mismatch in song title with what was available in the Orion API. This was either due to __A)__ slight variation in the song title between Orion and Spotify or __B)__ Orion not having the song in their API.

  _3a. Fill in missing songs_

  -  There was a total of 29 songs that were not able to collect lyrics for using the Orion API. In order to fill in the gaps, I decide not to use an API and instead rely on scraping the Genius lyric site using Beautiful Soup. After inspecting their site and understanding I built a function to scrape the site for the missing songs. In order to use the function, some song titles had to be reformatted and condensed to fit into a useable url for the Genius site. Using the function, all remain lyrics were able to be collected.

_Explore the Data_

> __14__ Albums | __128__ Unique Songs | __62,648__ Total Words Used

In order to feed lyrics into the various models I used, significant time was spent on cleaning and making the data that was collected useable.

__1. Prep text into corpus__
  - __Regex:__ Some lyric data also contain reference to song structure (i.e. name of collaborating artist) and needed to be removed since it wasn't essential to the analysis. This was completed by filtering words through regex using `r"\[[^\]]*\]"` as the sorting method.
  - __Stop Words:__ Stop word were removed covering basic words and I experimented using the starting list from `NLTK` and `sklearn` english words since they had different totals to start with. Additional words were added manually based on what was seen such as song fillers ('uuuuhhh, 'ooooohhs').
  - __Lemmatization:__ Finally the words were passed through a lemmatization function to reduce plural words to their singular word.

  ```Python
  def clean_text(song):
    clean_text_step = [song.splitlines()]

    for song in clean_text_step:
        clean_song = []
        for line in song:
            if  line != '':
                line = re.sub(r'[^\w\s]','',line) # Removes Punctuation
                line_2 = [lemmatizer.lemmatize(word) for word in line.lower().split() if word not in stop] # Stem words (remove plural)
                clean_song.append(line_2)
            else:
                pass
    return clean_song
    ```

__2. Vectorize words for use in model__
  - In order to separate words in preparation for topic modeling or classification, the cleaned lyrics corpus was prepared in both a count vectorizer and a TFIDIF vectorizer. Both were used to pass words into the LDA and NMF models and analyzed for effectiveness. Below contains a snapshot of the parameters used for each.
  
  - __Count Vectorizer:__ Total Count Frequency
  
  ```python
  cvec = CountVectorizer(min_df=5,
                       max_df=0.95,
                       stop_words=stop,
                       ngram_range=(1,1),
                      )
count = cvec.fit_transform(corpus_lem);
```
  - __TFIDF Vectorizer:__ Term Frequency times inverse document frequency (TFIDF)
  
  ```python
  tvec = TfidfVectorizer(min_df=5,
                       max_df=0.85,
                       stop_words=stop,
                       ngram_range=(1,3),
                      )
tfidf = tvec.fit_transform(corpus_lem);
```

__3. Split lines (for Word2Vec)__

Additionally, lyric data needed to be prepared as a list of list to be feed into the Word2Vec model to create word similarities.

```python
text_corpus_w2v = []
for song in df_lyrics.sort_values('release_date').lyrics_word2vec:
    for line in song:
        text_corpus_w2v.append(line)
```

## Models

### Topic Modeling

_Model the Data using Unsupervised Learning_

To conduct topic modeling, two different models were tested from the sklearn feature extraction library:
  - Latent Dirichlet Allocation (LDA)
  - non-negative matrix factorization (NMF)

Each model type was feed either the count vectorizer results or the TFIDF vectorizer results. At first, the  decision on how many number of topics to use was difficult to isolate on where to draw the line. To help this, I used the coherence score in `gensim` to map how well the topics were being clustered in relevant groups.

- Coherence Score

I chose to use a metric called __Coherence Score__ to help in this process. The coherence score can be calculated across different topic numbers and compare to see which provide the right fidelity to the level you are looking for. My intention was to allocate songs into as many topics as possible that were descriptive but so much that isolated one song per topic. On the flip side, selecting too few topics lumps almost all songs into general groups where uniqueness cannot be easily defined.

![Coherence](https://raw.githubusercontent.com/babyakja/babyakja.github.io/master/assets/img/posts/Coherence%Score.png)

_Evaluate the Data_

Topics that were able to be isolated:
- Topic 1: Public Persona
- Topic 2: Speak Ye Truth
- Topic 3: Broke /Women/ Money
- Topic 4: Life
- Topic 5: Make Right/ Legacy

_Highest Probability Song by Topic_

```
0.9954962574881103
['All of the Lights']
['My Beautiful Dark Twisted Fantasy']
Public Persona

0.9988632784766937
['Last Call']
['The College Dropout']
Speak Ye Truth

0.9966221495074559
['The New Workout Plan']
['The College Dropout']
Broke / Women / Money

0.9955740231167477
['Drive Slow']
['Late Registration']
Life

0.9966283076899758
['Touch the Sky']
['Late Registration']
Make Right/ Legacy
```

![Kanye Discography](https://raw.githubusercontent.com/babyakja/babyakja.github.io/master/assets/img/posts/Kanye-Discography2.png)

### Word2Vec

Using `Word2Vec`, each word is given a directional vector and words most similar to it will be aligned along the same direction. This is done on a multidimensional plane and difficult to interpret but to help us visualize the model outputs I put them into a t-SNE plot to reorient onto a 2-D space.

_t-SNE Plot_

![Word Vectors](https://raw.githubusercontent.com/babyakja/babyakja.github.io/master/assets/img/posts/t-sne.png)

This gives us the ability to see word similarity for common words that Kanye uses in his lyrics.

After the model was compiled, I wanted to isolate particular words that appeared frequently and understand what other similar words he associated with it. I started with _Love_:

_Most Similar to_ __Love__

```
[('fadin', 0.5092264413833618),
 ('mistake', 0.49861404299736023),
 ('wake', 0.46998417377471924),
 ('ho', 0.4642098546028137),
 ('myself', 0.42317628860473633),
 ('blame', 0.3949402868747711),
 ('fuckin', 0.381166934967041),
 ('walk', 0.38080090284347534),
 ('hate', 0.38067206740379333),
 ('save', 0.37218549847602844)]
 ```
A mix of positive and negative terms here is not surprising considering how complicated love is...

Next up was the word _Myself_ since it appeared next to love and was fairly frequent:

_Most Similar to:_ __Myself__

```
[('bed', 0.5850050449371338),
 ('killing', 0.5635855793952942),
 ('killed', 0.5398204326629639),
 ('dawg', 0.5226340293884277),
 ('loving', 0.5117295384407043),
 ('wake', 0.5074575543403625),
 ('hood', 0.5013218522071838),
 ('favorite', 0.4675023555755615),
 ('door', 0.4619908332824707),
 ('catch', 0.44258543848991394)]
 ```

## __Sequential Topic Model__

Back to the core of our question regarding behavior changes over time, incorporating changes in topics over time a period seemed the most appropriate for detecting changes. To process the model, I grouped songs by year of release to create a chronological evaluation group. Then, I tuned parameters in order to create the proper amount of sensitivity in vocabulary changes across topics that could be detected. This was done by adjusting a parameter called _chain variance_.

Visualization was key to understanding the output of the sequential topic model, so using Plotly provided an interactive platform within my Jupyter Notebook to conduct my analysis.

In exploring the model outputs, I picked up on the same word 'myself' being used and contextually what other words were included in that topic over time. The two charts below show the probability of certain words changing overtime along certain themes. Certain words were less used while others became more likely to appear. If words signaling troubling behavior or thoughts can be flagged, then treatment plans can be built around the needs of the patient at that time.

![Topic 2006](https://raw.githubusercontent.com/babyakja/babyakja.github.io/master/assets/img/posts/overtime2006.png)

![Topic 2016](https://raw.githubusercontent.com/babyakja/babyakja.github.io/master/assets/img/posts/overtime2016.png)

## Futurework

There is need to continue the stagnant conversation around mental health and what tools we use to connect those that struggle with issues with the proper resources. What we can discover can help the millions of people not only living with bipolar disorder but also their friends, families, and coworkers manage a often misunderstood disorder.

- Train / test using larger dataset
- build model for emotional detection (Advance LSTM ?)


## Conclusion

_Answer the Problem_

Topics were able to be assigned to each song and showed a change around the release of _My Beautiful Dark Twisted Fantasy_ in general topics per song. This does not indicate there was any changes that can be associated with behavior at this time. Training a vocabulary associated with bipolar disorder can improve these results if what can be associated could be more relevant on a higher frequency dataset such as tweets or other social media posts.

_Takeaways_

Data:

> __Model quality is tied directly to the quality of preprocessing data__

> __Balancing manual text cleaning with scalable operations is extremely helpful__

Model:

> __Topic Modeling can assist in finding overarching groups, flexible based on need (static vs dynamic)__

> __Word2Vec creates reliable word association grouping and can be useful on larger corpus in NLP__

> __t-SNE can be a great visual tool but difficult to fine tune for classification purposes__
