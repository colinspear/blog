---
title: 'Custom sklearn transformer'
date: 2020-01-11
tags: ['machine learning', 'shallow']
author: 'Colin'
summary: "For when your daily affirmations just aren't cutting it"
draft: true
---

Scikit-learn comes with a number of useful built-in data transformation functions that allow you to [impute missing values](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.impute), [scale numerical data](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.preprocessing), etc. 
Eventually, however, you will want to manipulate your data in a way that is not supported by the built-in offerings. 
Luckily for us, sklearn is also incredibly [well designed](https://arxiv.org/abs/1309.0238) and makes creating custom  methods and integrating them into your workflow easy as pie.

## How the sausage gets made

- Include a brief intro to sklearn's API design

For this exercise, I'm going to be using data about my Spotify Discover Weekly playlists. 
If you like music, but don't know about [Discover Weekly](https://hackernoon.com/spotifys-discover-weekly-how-machine-learning-finds-your-new-music-19a41ab76efe), you'll probably want to acquaint yourself. 
I've been collecting data on my weekly playlists since fall of 2019 with vague plans to turn it into some sort of project. 
The [spotipy](https://spotipy.readthedocs.io/en/latest/) library makes it easy to access all of the amazing data that Spotify makes available through its API.

```python
import pandas as pd
import pathlib

project_dir = pathlib.Path().cwd().parent
song_features = pd.read_pickle(project_dir / 'data/raw/dw_combined.pkl')

cols = ['song_length_ms', 'tempo', 'tempo_confidence',
        'instrumentalness', 'liveness', 'loudness', 'speechiness', 
        'valence', 'acousticness', 'danceability', 
        'energy', 'popularity']

song_features = song_features[cols]
```

Generally, when we want to make a custom transformer, we should have some reason to believe that a specific combination or transformation of one or more variables will be a good predictor for the target variable. We don't have a super obvious such relationship here, so I'm going to make some hypotheses without any evidence in their favor. Don't do this in the wild. 

My first hypothesis is that faster soongs will be more popular. But I want to adjust for uncertainty in song speed. To do this I can use `tempo_confidence` to weight `tempo`, essentially "slowing down" tracks that have lower degree of tempo certainty.

The second hypothesis is a bit more far-fetched, so I am only going to include it as an optional argument in my transformer. I'm going to say more danceable songs will be more popular, so long as they are not too long (people get tired, right?). So if we divide `song_length_ms` by `danceability`, the resulting feature (`fatigue_factor`) should have an inverse relationship with popularity. We can alway see if this brazen assumption holds after we make the transformation.

```python
import numpy as np
from sklearn.base import BaseEstimator, TransformerMixin

# get the right column indices: safer than hard-coding indices 3, 4, 5, 6
tempo_ix, tempo_conf_ix, dance_ix, length_ix = [
    list(song_features.columns).index(col)
    for col in ('tempo', 'tempo_confidence', 'danceability', 'song_length_ms')]

class CustomFeaturesAdder(BaseEstimator, TransformerMixin):
    def __init__(self, add_fatigue_factor = True): # no *args or **kwargs
        self.add_fatigue_factor = add_fatigue_factor
    def fit(self, X, y=None):
        return self  # nothing else to do
    def transform(self, X, y=None):
        weighted_tempo = X[:, tempo_ix] * X[:, tempo_conf_ix]
        if self.add_fatigue_factor:
            fatigue_factor = X[:, length_ix] / X[:, dance_ix]
            return np.c_[X, weighted_tempo, fatigue_factor]
        else:
            return np.c_[X, weighted_tempo]

features_adder = CustomFeaturesAdder(add_fatigue_factor=True)
music_plus = features_adder.transform(song_features.values)
```
