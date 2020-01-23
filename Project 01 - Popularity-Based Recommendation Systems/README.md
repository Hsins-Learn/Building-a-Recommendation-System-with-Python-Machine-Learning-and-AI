# Project 01 - Popularity-Based Recommendation Systems

**Popularity-Based Recommendation Systems** offer a very primitive form of collaborative filtering, where items are recommended to users based on how popular those items are among other users.

In this section, we are going to build a Popularity-based Recommendation Systems to recommend the places by taking a count of the number of raints that were given to eat. We assumpt that:

> The places that have the most number of ratings or reviews are the most popular.

## Program Flow

### 01. Load and Observe Data

- Use standard module `pathlib` deal with the path problem under different operating system.
- Use `pd.read_csv()` method to read csv files.
- Use `pd.DataFrame.head(self, n)` method show first `n` rows (show 5 rows in default).

```python
# import packages
from pathlib import Path

import numpy as np
import pandas as pd

# declare constants
DATA_FOLDER = './data'

# load and explore data
rating_data = pd.read_csv(Path(f'{DATA_FOLDER}/rating_final.csv'))
cuisine_data = pd.read_csv(Path(f'{DATA_FOLDER}/chefmozcuisine.csv'))
rating_data.head()
cuisine_data.head()
```

### 02. Calculate the Counts of Rating and Find Out the Most Rated Places

```python
# calculate the counts of rating and find out the most rated places
rating_count = pd.DataFrame(rating_data.groupby('placeID')['rating'].count())
rating_count.sort_values('rating', ascending=False).head()

# find out the most rated places and its cuisine
most_rated_places = pd.DataFrame([135085, 132825, 135032, 135052, 132834], index=np.arange(5), columns=['placeID'])

summary = pd.merge(most_rated_places, cuisine_data, on='placeID')
summary
```

## Summary

The concepts of Popularity-Based Recommendation Systems are:

- Recommend the most popular item.
- Depend on resource strictly.
- Doesn't take the data of users into account. (can't be customized for users)
- Easily built offline and can be incrementally. (require little computation)