![R.png](student_files/R.png)


# DSC Phase One Project


Student name: Moses Kigo

Student pace: part time

Scheduled project review date/time: November 2023

Instructor name: Everlyn Asiko

Blog post URL: moseswanja.blogspot.com/2023/11/student-pre-line-height-125-td.html

## Project Overview

For this project, we will use exploratory data analysis to generate insights for a business stakeholder.

## Business Problem
Microsoft sees all the big companies creating original video content and they want to get in on the fun. They have decided to create a new movie studio, but they do not know anything about creating movies. We will explore what types of films are currently doing the best at the box office. The findings will be translated into actionable insights that the head of Microsoft's new movie studio can use to help decide what type of films to create.

## Background
Since the early 1900s, when specialized cinema theaters first appeared, motion pictures have been an integral part of contemporary culture. It is anticipated that box office receipts worldwide would rise rapidly in the upcoming years.
Making a movie is a difficult process with many steps and obstacles. The cost of production is high up front, and there is a significant wait before any returns appear. Consequently, making a movie is a dangerous endeavor because it is difficult to predict whether a film will be financially successful or not. This is particularly problematic for high-end films because there's a lot of money on the line.
There are five significant phases in the making of a movie:

• Development
This phase involves deciding on a movie concept, obtaining the required rights, writing the screenplay, and securing funding.

• Pre-production
Prior to filming, preparations are undertaken by recruiting the actors and crew, finding suitable locations for the shoot, and building the sets and props that will be needed.

• Production
the actual filming of the picture, during which extra sound and raw footage are recorded.

• Post-production
The finished output is created by editing recorded video and audio, adding graphic effects, and adding music.

• Distribution
The film is then promoted, released, and shown in theaters or on other media.

With Microsoft hoping to become a new player in the market, a few major studios are fighting for the interest of the public.

The increase of movie sequels, remakes, and adaptations in today's film industry, which are safer investments because they already have an established following, may be explained by the risk involved in making blockbusters. Aside from creating sequels, there are other alternative financial choices at the various stages of production.

Which genre of screenplay should have its rights purchased?

What is the competition, their investment and ROI?

What time is the best to release the film?

In what way ought the film to be released?

This project aims to equip Microsoft's new movie studio with the knowledge and insights needed to make informed decisions in the competitive and dynamic film industry, utilizing data from various sources and ensuring the creation of successful and engaging films that resonate with audiences.

![Hollywood.jpg](student_files/Hollywood.jpg)

## The Data
I have Used the three movie datasets below, The datasets contain movie ratings, genres, gross earnings, production years and number votes. In the folder zippedData are movie datasets from:

bom.movie_gross

title.basics

title.basics

title.ratings

tn.movie_budgets

rt.movie_info

rt.reviews

tmdb.movies

genres


```python
#import required libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
import seaborn as sns
import sqlite3
%matplotlib inline
import requests
from fuzzywuzzy import fuzz
from fuzzywuzzy import process
from IPython.display import Image
import warnings
warnings.filterwarnings('ignore')
warnings.simplefilter('ignore')
```


```python
#load the datasets
movie_gross = pd.read_csv('bom.movie_gross.csv')
title_basics = pd.read_csv('title.basics.csv', engine='python')
basic_title = pd.read_csv('title.basics.tsv', sep='\t',low_memory=False)
imdb_ratings = pd.read_csv('title.ratings.csv')
movie_budgets = pd.read_csv('tn.movie_budgets.csv')
rt_movie = pd.read_csv('rt.movie_info.tsv', sep='\t')
movie_info = pd.read_csv('rt.movie_info.tsv', sep='\t')
rt_review = pd.read_csv('rt.reviews.tsv', sep='\t', encoding='latin-1')
tmdb_movies = pd.read_csv('tmdb.movies.csv', index_col='Unnamed: 0',)
genres_df = pd.read_csv('genres.csv')
```


```python
movie_gross['foreign_gross'] = pd.to_numeric(movie_gross['foreign_gross'].str.replace(",", ""))
```


```python
#preview movie_gross dataset

'''
The movie_gross file contain information about movie gross earnings. Here are the columns and a brief description of each:

1. **title**: The title of the movie.
2. **studio**: The studio that produced the movie.
3. **domestic_gross**: The gross domestic earnings of the movie in USD.
4. **foreign_gross**: The gross foreign earnings of the movie. It seems like this might be a string, we may need to convert it to a numeric type for analysis.
5. **year**: The year the movie was released.
'''
# Show the first few rows of the DataFrame

movie_gross.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Toy Story 3</td>
      <td>BV</td>
      <td>415000000.0</td>
      <td>652000000.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alice in Wonderland (2010)</td>
      <td>BV</td>
      <td>334200000.0</td>
      <td>691300000.0</td>
      <td>2010</td>
    </tr>
  </tbody>
</table>
</div>




```python
#preview title_basics dataset
title_basics.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0063540</td>
      <td>Sunghursh</td>
      <td>Sunghursh</td>
      <td>2013</td>
      <td>175.0</td>
      <td>Action,Crime,Drama</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0066787</td>
      <td>One Day Before the Rainy Season</td>
      <td>Ashad Ka Ek Din</td>
      <td>2019</td>
      <td>114.0</td>
      <td>Biography,Drama</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0069049</td>
      <td>The Other Side of the Wind</td>
      <td>The Other Side of the Wind</td>
      <td>2018</td>
      <td>122.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0069204</td>
      <td>Sabse Bada Sukh</td>
      <td>Sabse Bada Sukh</td>
      <td>2018</td>
      <td>NaN</td>
      <td>Comedy,Drama</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0100275</td>
      <td>The Wandering Soap Opera</td>
      <td>La Telenovela Errante</td>
      <td>2017</td>
      <td>80.0</td>
      <td>Comedy,Drama,Fantasy</td>
    </tr>
  </tbody>
</table>
</div>




```python
#preview imdb_ratings dataset
imdb_ratings.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt10356526</td>
      <td>8.3</td>
      <td>31</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt10384606</td>
      <td>8.9</td>
      <td>559</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt1042974</td>
      <td>6.4</td>
      <td>20</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt1043726</td>
      <td>4.2</td>
      <td>50352</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt1060240</td>
      <td>6.5</td>
      <td>21</td>
    </tr>
  </tbody>
</table>
</div>




```python
#preview movie_budgets dataset
   
'''
The movie_budgets file contain information about movie budgets and earnings.

Here are the columns and a brief description of each:

1. **id**: An identifier for the movie.
2. **release_date**: The date the movie was released.
3. **movie**: The title of the movie.
4. **production_budget**: The budget for producing the movie.
5. **domestic_gross**: The gross domestic earnings of the movie in USD.
6. **worldwide_gross**: The gross worldwide earnings of the movie.

'''

movie_budgets['title'] = movie_budgets['movie']
movie_budgets.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>$425,000,000</td>
      <td>$760,507,625</td>
      <td>$2,776,345,279</td>
      <td>Avatar</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>$410,600,000</td>
      <td>$241,063,875</td>
      <td>$1,045,663,875</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>$350,000,000</td>
      <td>$42,762,350</td>
      <td>$149,762,350</td>
      <td>Dark Phoenix</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>$330,600,000</td>
      <td>$459,005,868</td>
      <td>$1,403,013,963</td>
      <td>Avengers: Age of Ultron</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>$317,000,000</td>
      <td>$620,181,382</td>
      <td>$1,316,721,747</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
    </tr>
  </tbody>
</table>
</div>




```python
movie_budgets["production_budget"] = movie_budgets["production_budget"]\
        .str.replace("$", "").str.replace(",", "").astype(int)
```


```python
movie_budgets['domestic_gross'] = movie_budgets['domestic_gross']\
        .str.replace("$", "").str.replace(",", "").astype(int)
```


```python
movie_budgets['worldwide_gross'] = pd.to_numeric(movie_budgets['worldwide_gross']\
        .str.replace("$", "").str.replace(",", ""))
```


```python
movie_budgets.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>425000000</td>
      <td>760507625</td>
      <td>2776345279</td>
      <td>Avatar</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>410600000</td>
      <td>241063875</td>
      <td>1045663875</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>42762350</td>
      <td>149762350</td>
      <td>Dark Phoenix</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>459005868</td>
      <td>1403013963</td>
      <td>Avengers: Age of Ultron</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Dec 15, 2017</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
      <td>317000000</td>
      <td>620181382</td>
      <td>1316721747</td>
      <td>Star Wars Ep. VIII: The Last Jedi</td>
    </tr>
  </tbody>
</table>
</div>




```python
for column in ["box_office"]:
    rt_movie[column] = rt_movie[column].replace('$', '',regex=True).replace(',', '',regex=True).astype(float)
rt_movie.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>synopsis</th>
      <th>rating</th>
      <th>genre</th>
      <th>director</th>
      <th>writer</th>
      <th>theater_date</th>
      <th>dvd_date</th>
      <th>currency</th>
      <th>box_office</th>
      <th>runtime</th>
      <th>studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>This gritty, fast-paced, and innovative police...</td>
      <td>R</td>
      <td>Action and Adventure|Classics|Drama</td>
      <td>William Friedkin</td>
      <td>Ernest Tidyman</td>
      <td>Oct 9, 1971</td>
      <td>Sep 25, 2001</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>104 minutes</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>New York City, not-too-distant-future: Eric Pa...</td>
      <td>R</td>
      <td>Drama|Science Fiction and Fantasy</td>
      <td>David Cronenberg</td>
      <td>David Cronenberg|Don DeLillo</td>
      <td>Aug 17, 2012</td>
      <td>Jan 1, 2013</td>
      <td>$</td>
      <td>600000.0</td>
      <td>108 minutes</td>
      <td>Entertainment One</td>
    </tr>
  </tbody>
</table>
</div>




```python
rt_review.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>review</th>
      <th>rating</th>
      <th>fresh</th>
      <th>critic</th>
      <th>top_critic</th>
      <th>publisher</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>A distinctly gallows take on contemporary fina...</td>
      <td>3/5</td>
      <td>fresh</td>
      <td>PJ Nabarro</td>
      <td>0</td>
      <td>Patrick Nabarro</td>
      <td>November 10, 2018</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>It's an allegory in search of a meaning that n...</td>
      <td>NaN</td>
      <td>rotten</td>
      <td>Annalee Newitz</td>
      <td>0</td>
      <td>io9.com</td>
      <td>May 23, 2018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>... life lived in a bubble in financial dealin...</td>
      <td>NaN</td>
      <td>fresh</td>
      <td>Sean Axmaker</td>
      <td>0</td>
      <td>Stream on Demand</td>
      <td>January 4, 2018</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Continuing along a line introduced in last yea...</td>
      <td>NaN</td>
      <td>fresh</td>
      <td>Daniel Kasman</td>
      <td>0</td>
      <td>MUBI</td>
      <td>November 16, 2017</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3</td>
      <td>... a perverse twist on neorealism...</td>
      <td>NaN</td>
      <td>fresh</td>
      <td>NaN</td>
      <td>0</td>
      <td>Cinema Scope</td>
      <td>October 12, 2017</td>
    </tr>
  </tbody>
</table>
</div>




```python
'''
tmdb_movies Data Frame has;

1. **genre_ids** (Indexed by "Unnamed: 0"): A list of genre IDs associated with the movie.
2. **id**: An identifier for the movie.
3. **original_language**: The original language of the movie.
4. **original_title**: The original title of the movie.
5. **popularity**: A measure of the movie's popularity.
6. **release_date**: The date the movie was released.
7. **title**: The title of the movie.
8. **vote_average**: The average vote or rating for the movie.
9. **vote_count**: The number of votes the movie received.


'''

tmdb_movies.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>genre_ids</th>
      <th>id</th>
      <th>original_language</th>
      <th>original_title</th>
      <th>popularity</th>
      <th>release_date</th>
      <th>title</th>
      <th>vote_average</th>
      <th>vote_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[12, 14, 10751]</td>
      <td>12444</td>
      <td>en</td>
      <td>Harry Potter and the Deathly Hallows: Part 1</td>
      <td>33.533</td>
      <td>2010-11-19</td>
      <td>Harry Potter and the Deathly Hallows: Part 1</td>
      <td>7.7</td>
      <td>10788</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[14, 12, 16, 10751]</td>
      <td>10191</td>
      <td>en</td>
      <td>How to Train Your Dragon</td>
      <td>28.734</td>
      <td>2010-03-26</td>
      <td>How to Train Your Dragon</td>
      <td>7.7</td>
      <td>7610</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[12, 28, 878]</td>
      <td>10138</td>
      <td>en</td>
      <td>Iron Man 2</td>
      <td>28.515</td>
      <td>2010-05-07</td>
      <td>Iron Man 2</td>
      <td>6.8</td>
      <td>12368</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[16, 35, 10751]</td>
      <td>862</td>
      <td>en</td>
      <td>Toy Story</td>
      <td>28.005</td>
      <td>1995-11-22</td>
      <td>Toy Story</td>
      <td>7.9</td>
      <td>10174</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[28, 878, 12]</td>
      <td>27205</td>
      <td>en</td>
      <td>Inception</td>
      <td>27.920</td>
      <td>2010-07-16</td>
      <td>Inception</td>
      <td>8.3</td>
      <td>22186</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create a dictionary mapping genre IDs to names
genre_dict = genres_df.set_index('id')['name'].to_dict()

# Define a function to map IDs to names
def map_ids_to_names(ids):
    return [genre_dict.get(id, id) for id in ids]
```

# Data Pre-Analysis and Cleanup


```python
# Check for missing values in all dataframes
df_list=  [movie_gross,title_basics,basic_title,imdb_ratings,movie_budgets,rt_movie,movie_info,rt_review,tmdb_movies,genres_df]
df_list_names = ['movie_gross','title_basics','basic_title','imdb_ratings','movie_budgets','rt_movie','movie_info','rt_review','tmdb_movies','genres_df']
for i, n in zip(df_list, df_list_names):
    print(f'{n}:')
    print(i.isna().sum(), '\n ****************\n')
```

    movie_gross:
    title                0
    studio               5
    domestic_gross      28
    foreign_gross     1350
    year                 0
    dtype: int64 
     ****************
    
    title_basics:
    tconst                 0
    primary_title          1
    original_title        22
    start_year             0
    runtime_minutes    31739
    genres              5408
    dtype: int64 
     ****************
    
    basic_title:
    tconst             0
    titleType          0
    primaryTitle      17
    originalTitle     17
    isAdult            0
    startYear          0
    endYear            0
    runtimeMinutes     0
    genres            18
    dtype: int64 
     ****************
    
    imdb_ratings:
    tconst           0
    averagerating    0
    numvotes         0
    dtype: int64 
     ****************
    
    movie_budgets:
    id                   0
    release_date         0
    movie                0
    production_budget    0
    domestic_gross       0
    worldwide_gross      0
    title                0
    dtype: int64 
     ****************
    
    rt_movie:
    id                 0
    synopsis          62
    rating             3
    genre              8
    director         199
    writer           449
    theater_date     359
    dvd_date         359
    currency        1220
    box_office      1220
    runtime           30
    studio          1066
    dtype: int64 
     ****************
    
    movie_info:
    id                 0
    synopsis          62
    rating             3
    genre              8
    director         199
    writer           449
    theater_date     359
    dvd_date         359
    currency        1220
    box_office      1220
    runtime           30
    studio          1066
    dtype: int64 
     ****************
    
    rt_review:
    id                0
    review         5563
    rating        13517
    fresh             0
    critic         2722
    top_critic        0
    publisher       309
    date              0
    dtype: int64 
     ****************
    
    tmdb_movies:
    genre_ids            0
    id                   0
    original_language    0
    original_title       0
    popularity           0
    release_date         0
    title                0
    vote_average         0
    vote_count           0
    dtype: int64 
     ****************
    
    genres_df:
    id      0
    name    0
    dtype: int64 
     ****************
    
    

# Summary of missing values

'''
movie_gross:
· studio: 5 missing values
· domestic_gross: 28 missing values
· foreign_gross: 1350 missing values
title_basics:
· original_title: 21 missing values
· runtime_minutes: 31739 missing values
· genres: 5408 missing values
basic_title:
· genres: 7 missing values
imdb_ratings:
· No missing values
movie_budgets:
· No missing values
rt_movie:
· synopsis: 62 missing values
· rating: 3 missing values
· genre: 8 missing values
· director: 199 missing values
· writer: 449 missing values
· theater_date: 359 missing values
· dvd_date: 359 missing values
· currency: 1220 missing values
· box_office: 1220 missing values
· runtime: 30 missing values
· studio: 1066 missing values
movie_info:
· synopsis: 62 missing values
· rating: 3 missing values
· genre: 8 missing values
· director: 199 missing values
· writer: 449 missing values
· theater_date: 359 missing values
· dvd_date: 359 missing values
· currency: 1220 missing values
· box_office: 1220 missing values
· runtime: 30 missing values
· studio: 1066 missing values
rt_review:
· review: 5563 missing values
· rating: 13517 missing values
· critic: 2722 missing values
· publisher: 309 missing values
tmdb_movies:
· No missing values
genres_df:
· No missing values
'''


```python
#summary of movie_gross dataframe
print(f'movie_gross has {movie_gross.shape[0]} rows and {movie_gross.shape[1]} columns')

movie_gross.info()
```

    movie_gross has 3387 rows and 5 columns
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3387 entries, 0 to 3386
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   title           3387 non-null   object 
     1   studio          3382 non-null   object 
     2   domestic_gross  3359 non-null   float64
     3   foreign_gross   2037 non-null   float64
     4   year            3387 non-null   int64  
    dtypes: float64(2), int64(1), object(2)
    memory usage: 132.4+ KB
    

Assuming that the films associated with null values had no domestic gross income or foreign gross income within that year in order to manage the missing values in the domestic_gross and foreign_gross columns of movie_gross. And 0.00, will to be used to fill up the missing values of the two columns.
'Unknown' can be used to replace the five records in the studio column that have missing values.


```python
#replace the missing values of foreign_gross and domestic_gross with 0.
movie_gross['domestic_gross'] = movie_gross.domestic_gross.fillna(0.0)
movie_gross['foreign_gross'] = movie_gross.foreign_gross.fillna(0.0)
#movie_gross['foreign_gross'] = movie_gross['foreign_gross'].str.replace(",","",regex=True).apply(float)
#replace missing values of studio with 'Unknown'
movie_gross['studio'] = movie_gross.studio.fillna('Unknown')
```


```python
# Drop rows with missing values in 'box_office' in rt_movie
rt_movie = rt_movie.dropna(subset=['box_office'])

# Drop rows with missing values in 'review' or 'rating' in rt_review
rt_review = rt_review.dropna(subset=['review', 'rating'])
```


```python
rt_movie.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>synopsis</th>
      <th>rating</th>
      <th>genre</th>
      <th>director</th>
      <th>writer</th>
      <th>theater_date</th>
      <th>dvd_date</th>
      <th>currency</th>
      <th>box_office</th>
      <th>runtime</th>
      <th>studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>New York City, not-too-distant-future: Eric Pa...</td>
      <td>R</td>
      <td>Drama|Science Fiction and Fantasy</td>
      <td>David Cronenberg</td>
      <td>David Cronenberg|Don DeLillo</td>
      <td>Aug 17, 2012</td>
      <td>Jan 1, 2013</td>
      <td>$</td>
      <td>600000.0</td>
      <td>108 minutes</td>
      <td>Entertainment One</td>
    </tr>
    <tr>
      <th>6</th>
      <td>10</td>
      <td>Some cast and crew from NBC's highly acclaimed...</td>
      <td>PG-13</td>
      <td>Comedy</td>
      <td>Jake Kasdan</td>
      <td>Mike White</td>
      <td>Jan 11, 2002</td>
      <td>Jun 18, 2002</td>
      <td>$</td>
      <td>41032915.0</td>
      <td>82 minutes</td>
      <td>Paramount Pictures</td>
    </tr>
    <tr>
      <th>7</th>
      <td>13</td>
      <td>Stewart Kane, an Irishman living in the Austra...</td>
      <td>R</td>
      <td>Drama</td>
      <td>Ray Lawrence</td>
      <td>Raymond Carver|Beatrix Christian</td>
      <td>Apr 27, 2006</td>
      <td>Oct 2, 2007</td>
      <td>$</td>
      <td>224114.0</td>
      <td>123 minutes</td>
      <td>Sony Pictures Classics</td>
    </tr>
    <tr>
      <th>8</th>
      <td>14</td>
      <td>"Love Ranch" is a bittersweet love story that ...</td>
      <td>R</td>
      <td>Drama</td>
      <td>Taylor Hackford</td>
      <td>Mark Jacobson</td>
      <td>Jun 30, 2010</td>
      <td>Nov 9, 2010</td>
      <td>$</td>
      <td>134904.0</td>
      <td>117 minutes</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>15</th>
      <td>22</td>
      <td>Two-time Academy Award Winner Kevin Spacey giv...</td>
      <td>R</td>
      <td>Comedy|Drama|Mystery and Suspense</td>
      <td>George Hickenlooper</td>
      <td>Norman Snider</td>
      <td>Dec 17, 2010</td>
      <td>Apr 5, 2011</td>
      <td>$</td>
      <td>1039869.0</td>
      <td>108 minutes</td>
      <td>ATO Pictures</td>
    </tr>
  </tbody>
</table>
</div>



We need to convert the `foreign_gross` column in `movie_gross`, the `box_office` and `runtime` columns in `rt_movie`, and the `release_date` columns in `rt_movie`, `tmdb_movies`, and `movie_budgets` to the appropriate data types. Let's proceed with these conversions.


```python
# Convert 'box_office' and 'runtime' in rt_movie to numeric types
rt_movie['runtime'] = rt_movie['runtime'].str.replace(' minutes', '').astype(float)

# Convert 'release_date' in rt_movie, tmdb_movies, and movie_budgets to datetime
# rt_movie['theater_date'] = pd.to_datetime(rt_movie['theater_date'], errors='coerce')
# rt_movie['dvd_date'] = pd.to_datetime(rt_movie['dvd_date'], errors='coerce')
# tmdb_movies['release_date'] = pd.to_datetime(tmdb_movies['release_date'], errors='coerce')
# movie_budgets['release_date'] = pd.to_datetime(movie_budgets['release_date'], errors='coerce')

# Check data types again
data_types_after = {
    "movie_gross": movie_gross.dtypes,
    "rt_movie": rt_movie.dtypes,
    "tmdb_movies": tmdb_movies.dtypes,
    "movie_budgets": movie_budgets.dtypes
}

data_types_after
```




    {'movie_gross': title              object
     studio             object
     domestic_gross    float64
     foreign_gross     float64
     year                int64
     dtype: object,
     'rt_movie': id                int64
     synopsis         object
     rating           object
     genre            object
     director         object
     writer           object
     theater_date     object
     dvd_date         object
     currency         object
     box_office      float64
     runtime         float64
     studio           object
     dtype: object,
     'tmdb_movies': genre_ids             object
     id                     int64
     original_language     object
     original_title        object
     popularity           float64
     release_date          object
     title                 object
     vote_average         float64
     vote_count             int64
     dtype: object,
     'movie_budgets': id                    int64
     release_date         object
     movie                object
     production_budget     int32
     domestic_gross        int32
     worldwide_gross       int64
     title                object
     dtype: object}




```python
#replace missing values in the original_title column with the primary title
title_basics['original_title'] = title_basics['original_title'].fillna(title_basics['primary_title'])

#Checking for the median runtime
title_basics['runtime_minutes'].median()

# replace  missing values of the runtime_minutes with the median value
title_basics['runtime_minutes'] = title_basics['runtime_minutes'].fillna(title_basics['runtime_minutes'].median())

#drop the missing values in the genres column
title_basics = title_basics.dropna(subset=['genres'])
```


```python
#Summary of the title_basics DataFrame

print(f'title_basics has {title_basics.shape[0]} rows and {title_basics.shape[1]} columns')
title_basics.info()
```

    title_basics has 140736 rows and 6 columns
    <class 'pandas.core.frame.DataFrame'>
    Index: 140736 entries, 0 to 146143
    Data columns (total 6 columns):
     #   Column           Non-Null Count   Dtype  
    ---  ------           --------------   -----  
     0   tconst           140736 non-null  object 
     1   primary_title    140735 non-null  object 
     2   original_title   140735 non-null  object 
     3   start_year       140736 non-null  int64  
     4   runtime_minutes  140736 non-null  float64
     5   genres           140736 non-null  object 
    dtypes: float64(1), int64(1), object(4)
    memory usage: 7.5+ MB
    


```python
#Check the number of missing values in the title_basics Data Frame
title_basics.isna().sum()
```




    tconst             0
    primary_title      1
    original_title     1
    start_year         0
    runtime_minutes    0
    genres             0
    dtype: int64




```python
#Check if there are any duplicate rows in the title_basics DF
title_duplicate = len(title_basics)-len(title_basics.drop_duplicates())
print(title_duplicate)
```

    0
    


```python
title_basics.head(15)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0063540</td>
      <td>Sunghursh</td>
      <td>Sunghursh</td>
      <td>2013</td>
      <td>175.0</td>
      <td>Action,Crime,Drama</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0066787</td>
      <td>One Day Before the Rainy Season</td>
      <td>Ashad Ka Ek Din</td>
      <td>2019</td>
      <td>114.0</td>
      <td>Biography,Drama</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0069049</td>
      <td>The Other Side of the Wind</td>
      <td>The Other Side of the Wind</td>
      <td>2018</td>
      <td>122.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0069204</td>
      <td>Sabse Bada Sukh</td>
      <td>Sabse Bada Sukh</td>
      <td>2018</td>
      <td>87.0</td>
      <td>Comedy,Drama</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0100275</td>
      <td>The Wandering Soap Opera</td>
      <td>La Telenovela Errante</td>
      <td>2017</td>
      <td>80.0</td>
      <td>Comedy,Drama,Fantasy</td>
    </tr>
    <tr>
      <th>5</th>
      <td>tt0111414</td>
      <td>A Thin Life</td>
      <td>A Thin Life</td>
      <td>2018</td>
      <td>75.0</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>6</th>
      <td>tt0112502</td>
      <td>Bigfoot</td>
      <td>Bigfoot</td>
      <td>2017</td>
      <td>87.0</td>
      <td>Horror,Thriller</td>
    </tr>
    <tr>
      <th>7</th>
      <td>tt0137204</td>
      <td>Joe Finds Grace</td>
      <td>Joe Finds Grace</td>
      <td>2017</td>
      <td>83.0</td>
      <td>Adventure,Animation,Comedy</td>
    </tr>
    <tr>
      <th>8</th>
      <td>tt0139613</td>
      <td>O Silêncio</td>
      <td>O Silêncio</td>
      <td>2012</td>
      <td>87.0</td>
      <td>Documentary,History</td>
    </tr>
    <tr>
      <th>9</th>
      <td>tt0144449</td>
      <td>Nema aviona za Zagreb</td>
      <td>Nema aviona za Zagreb</td>
      <td>2012</td>
      <td>82.0</td>
      <td>Biography</td>
    </tr>
    <tr>
      <th>10</th>
      <td>tt0146592</td>
      <td>Pál Adrienn</td>
      <td>Pál Adrienn</td>
      <td>2010</td>
      <td>136.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>11</th>
      <td>tt0154039</td>
      <td>So Much for Justice!</td>
      <td>Oda az igazság</td>
      <td>2010</td>
      <td>100.0</td>
      <td>History</td>
    </tr>
    <tr>
      <th>12</th>
      <td>tt0159369</td>
      <td>Cooper and Hemingway: The True Gen</td>
      <td>Cooper and Hemingway: The True Gen</td>
      <td>2013</td>
      <td>180.0</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>13</th>
      <td>tt0162942</td>
      <td>Children of the Green Dragon</td>
      <td>A zöld sárkány gyermekei</td>
      <td>2010</td>
      <td>89.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>14</th>
      <td>tt0170651</td>
      <td>T.G.M. - osvoboditel</td>
      <td>T.G.M. - osvoboditel</td>
      <td>2018</td>
      <td>60.0</td>
      <td>Documentary</td>
    </tr>
  </tbody>
</table>
</div>




```python
#summary of the imdb_ratings dataframe
imdb_ratings.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 73856 entries, 0 to 73855
    Data columns (total 3 columns):
     #   Column         Non-Null Count  Dtype  
    ---  ------         --------------  -----  
     0   tconst         73856 non-null  object 
     1   averagerating  73856 non-null  float64
     2   numvotes       73856 non-null  int64  
    dtypes: float64(1), int64(1), object(1)
    memory usage: 1.7+ MB
    


```python
#summary statistics of imdb_ratings
imdb_ratings.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>73856.000000</td>
      <td>7.385600e+04</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>6.332729</td>
      <td>3.523662e+03</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.474978</td>
      <td>3.029402e+04</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>5.000000e+00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>5.500000</td>
      <td>1.400000e+01</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>6.500000</td>
      <td>4.900000e+01</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>7.400000</td>
      <td>2.820000e+02</td>
    </tr>
    <tr>
      <th>max</th>
      <td>10.000000</td>
      <td>1.841066e+06</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Drop rows with missing values in 'domestic_gross' or 'foreign_gross' in df
movie_gross = movie_gross.dropna(subset=['domestic_gross', 'foreign_gross'])

# Drop rows with missing values in 'box_office' in rt_movie
rt_movie = rt_movie.dropna(subset=['box_office'])

# Drop rows with missing values in 'review' or 'rating' in df3
rt_review = rt_review.dropna(subset=['review', 'rating'])

# Check for missing values again
missing_values_after = {
    "movie_gross": movie_gross.isnull().sum(),
    "rt_movie": rt_movie.isnull().sum(),
    "rt_review": rt_review.isnull().sum()
}

missing_values_after
```




    {'movie_gross': title             0
     studio            0
     domestic_gross    0
     foreign_gross     0
     year              0
     dtype: int64,
     'rt_movie': id               0
     synopsis         0
     rating           0
     genre            0
     director        41
     writer          67
     theater_date     6
     dvd_date         6
     currency         0
     box_office       0
     runtime          2
     studio          35
     dtype: int64,
     'rt_review': id               0
     review           0
     rating           0
     fresh            0
     critic        1190
     top_critic       0
     publisher      227
     date             0
     dtype: int64}




```python
#Replace rt_movies and rt_review missing values with Unknown

rt_movie = rt_movie.fillna(0)
rt_review= rt_review.fillna(0)
```


```python
rt_movie.isnull().sum()
rt_review.isnull().sum()
```




    id            0
    review        0
    rating        0
    fresh         0
    critic        0
    top_critic    0
    publisher     0
    date          0
    dtype: int64



## Check for Duplicates


```python
#display all duplicate records for movie gross 'title' column
movie_gross[movie_gross.duplicated(keep=False, subset='title')].sort_values(by='title')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>317</th>
      <td>Bluebeard</td>
      <td>Strand</td>
      <td>33500.0</td>
      <td>5200.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>3045</th>
      <td>Bluebeard</td>
      <td>WGUSA</td>
      <td>43100.0</td>
      <td>0.0</td>
      <td>2017</td>
    </tr>
  </tbody>
</table>
</div>



The two records above, with the same title 'Bluebeard', are not duplicate records because the other columns in the dataset, other than the title, have different values, they are removed so as not to cause problems down the road.


```python
movie_gross.drop_duplicates(subset=['title'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Toy Story 3</td>
      <td>BV</td>
      <td>415000000.0</td>
      <td>652000000.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alice in Wonderland (2010)</td>
      <td>BV</td>
      <td>334200000.0</td>
      <td>691300000.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Harry Potter and the Deathly Hallows Part 1</td>
      <td>WB</td>
      <td>296000000.0</td>
      <td>664300000.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Inception</td>
      <td>WB</td>
      <td>292600000.0</td>
      <td>535700000.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Shrek Forever After</td>
      <td>P/DW</td>
      <td>238700000.0</td>
      <td>513900000.0</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>3382</th>
      <td>The Quake</td>
      <td>Magn.</td>
      <td>6200.0</td>
      <td>0.0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3383</th>
      <td>Edward II (2018 re-release)</td>
      <td>FM</td>
      <td>4800.0</td>
      <td>0.0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3384</th>
      <td>El Pacto</td>
      <td>Sony</td>
      <td>2500.0</td>
      <td>0.0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3385</th>
      <td>The Swan</td>
      <td>Synergetic</td>
      <td>2400.0</td>
      <td>0.0</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3386</th>
      <td>An Actor Prepares</td>
      <td>Grav.</td>
      <td>1700.0</td>
      <td>0.0</td>
      <td>2018</td>
    </tr>
  </tbody>
</table>
<p>3386 rows × 5 columns</p>
</div>




```python
imdb_ratings[imdb_ratings.duplicated(keep=False, subset=['tconst'])].sort_values(by='tconst')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>



imdb_ratings dataframe has no duplicate records.


```python
#display all duplicate records for title_basics dataframe using 'primaryTitle','startYear' and 'genres' columns
title_basics[title_basics.duplicated(keep=False, subset=['primary_title','start_year', 'genres'])].sort_values(by='primary_title')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>129962</th>
      <td>tt8032828</td>
      <td>100 Milioni di bracciate</td>
      <td>100 Milioni di bracciate</td>
      <td>2017</td>
      <td>87.0</td>
      <td>Biography</td>
    </tr>
    <tr>
      <th>129979</th>
      <td>tt8034014</td>
      <td>100 Milioni di bracciate</td>
      <td>100 Milioni di bracciate</td>
      <td>2017</td>
      <td>87.0</td>
      <td>Biography</td>
    </tr>
    <tr>
      <th>145118</th>
      <td>tt9773302</td>
      <td>3. Elma</td>
      <td>3. Elma</td>
      <td>2014</td>
      <td>65.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>144392</th>
      <td>tt9660588</td>
      <td>3. Elma</td>
      <td>3. Elma</td>
      <td>2014</td>
      <td>65.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>144337</th>
      <td>tt9653930</td>
      <td>3. Elma</td>
      <td>3. Elma</td>
      <td>2014</td>
      <td>65.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>66992</th>
      <td>tt3815128</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>2010</td>
      <td>60.0</td>
      <td>Biography,Documentary</td>
    </tr>
    <tr>
      <th>66989</th>
      <td>tt3815122</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>2010</td>
      <td>60.0</td>
      <td>Biography,Documentary</td>
    </tr>
    <tr>
      <th>66993</th>
      <td>tt3815130</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>2010</td>
      <td>60.0</td>
      <td>Biography,Documentary</td>
    </tr>
    <tr>
      <th>66994</th>
      <td>tt3815132</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>2010</td>
      <td>60.0</td>
      <td>Biography,Documentary</td>
    </tr>
    <tr>
      <th>66995</th>
      <td>tt3815134</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>Ângelo de Sousa - Tudo o Que Sou Capaz</td>
      <td>2010</td>
      <td>60.0</td>
      <td>Biography,Documentary</td>
    </tr>
  </tbody>
</table>
<p>655 rows × 6 columns</p>
</div>




title_basics has duplicate records based on the primaryTitle and the startYear. Some of the duplicate records have slightly different genres while others have missing values in either the runtimeMinutes or genre columns.
The duplicate records will be dropped, while keeping the entry with the least number of missing values.


```python
#assign a different variable name to a copy of the title_basics dataframe
title_df = title_basics.copy()

#assign a new column, 'missing', to the sum of missing values in each row
#sort the dataframe by values of the 'primary_title' and 'missing'
title_df = title_df.assign(missing=title_df.isnull().sum(axis=1)).sort_values(by=['primary_title', 'missing'], ascending=True)

# preview of duplicated records
title_df[title_df.duplicated(keep=False, subset=['primary_title','start_year'])][19:29]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>missing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>59640</th>
      <td>tt3463230</td>
      <td>37</td>
      <td>37</td>
      <td>2018</td>
      <td>90.0</td>
      <td>Drama</td>
      <td>0</td>
    </tr>
    <tr>
      <th>99507</th>
      <td>tt5815216</td>
      <td>37</td>
      <td>37</td>
      <td>2018</td>
      <td>87.0</td>
      <td>Drama,Romance,Thriller</td>
      <td>0</td>
    </tr>
    <tr>
      <th>30089</th>
      <td>tt2148554</td>
      <td>41</td>
      <td>41</td>
      <td>2012</td>
      <td>98.0</td>
      <td>Documentary</td>
      <td>0</td>
    </tr>
    <tr>
      <th>35991</th>
      <td>tt2319739</td>
      <td>41</td>
      <td>41</td>
      <td>2012</td>
      <td>80.0</td>
      <td>Drama,Sci-Fi</td>
      <td>0</td>
    </tr>
    <tr>
      <th>142077</th>
      <td>tt9331072</td>
      <td>44 Days</td>
      <td>44 Days</td>
      <td>2018</td>
      <td>52.0</td>
      <td>Mystery</td>
      <td>0</td>
    </tr>
    <tr>
      <th>143987</th>
      <td>tt9614898</td>
      <td>44 Days</td>
      <td>44 Days</td>
      <td>2018</td>
      <td>76.0</td>
      <td>Drama,War</td>
      <td>0</td>
    </tr>
    <tr>
      <th>51820</th>
      <td>tt3072686</td>
      <td>5 People</td>
      <td>5 People</td>
      <td>2013</td>
      <td>87.0</td>
      <td>Thriller</td>
      <td>0</td>
    </tr>
    <tr>
      <th>55539</th>
      <td>tt3252324</td>
      <td>5 People</td>
      <td>5 People</td>
      <td>2013</td>
      <td>87.0</td>
      <td>Comedy,Drama,Thriller</td>
      <td>0</td>
    </tr>
    <tr>
      <th>46602</th>
      <td>tt2771990</td>
      <td>7 Days</td>
      <td>7 Days</td>
      <td>2015</td>
      <td>87.0</td>
      <td>Sci-Fi</td>
      <td>0</td>
    </tr>
    <tr>
      <th>88065</th>
      <td>tt5112592</td>
      <td>7 Days</td>
      <td>Nanoka</td>
      <td>2015</td>
      <td>110.0</td>
      <td>Drama</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#drop duplicates while keeping the entry with least missing values and drop the column 'missing'
title_df = title_df.drop_duplicates(keep='first', subset=['primary_title','start_year']).drop(columns='missing')
```


```python
##recheck for duplicates
title_df[title_df.duplicated(keep=False, subset=['primary_title','start_year'])].sort_values(by=['primary_title','start_year'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
title_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 138926 entries, 13871 to 98772
    Data columns (total 6 columns):
     #   Column           Non-Null Count   Dtype  
    ---  ------           --------------   -----  
     0   tconst           138926 non-null  object 
     1   primary_title    138925 non-null  object 
     2   original_title   138925 non-null  object 
     3   start_year       138926 non-null  int64  
     4   runtime_minutes  138926 non-null  float64
     5   genres           138926 non-null  object 
    dtypes: float64(1), int64(1), object(4)
    memory usage: 7.4+ MB
    


```python
# Check for duplicates in all dataframes and drop them
duplicates = {
    "movie_gross": movie_gross.duplicated().sum(),
    "rt_movie": rt_movie.duplicated().sum(),
    "rt_review": rt_review.duplicated().sum(),
    "tmdb_movies": tmdb_movies.duplicated().sum(),
    "movie_budgets": movie_budgets.duplicated().sum()
}

# Drop duplicates
movie_gross = movie_gross.drop_duplicates()
rt_movie = rt_movie.drop_duplicates()
rt_review = rt_review.drop_duplicates()
tmdb_movies = tmdb_movies.drop_duplicates()
movie_budgets = movie_budgets.drop_duplicates()

duplicates
```




    {'movie_gross': 0,
     'rt_movie': 0,
     'rt_review': 1,
     'tmdb_movies': 1020,
     'movie_budgets': 0}



## Checking for wrong Data Types


```python
#check the data type of each column
print(f'movie_gross data types: \n {movie_gross.dtypes} \n **********************')
print(f'\n title_df data types: \n {title_df.dtypes} \n **********************')
print(f'\n imdb_ratings data types: \n {imdb_ratings.dtypes} \n **********************')
print(f'\n movie_budgets data types: \n {movie_budgets.dtypes} \n **********************')
```

    movie_gross data types: 
     title              object
    studio             object
    domestic_gross    float64
    foreign_gross     float64
    year                int64
    dtype: object 
     **********************
    
     title_df data types: 
     tconst              object
    primary_title       object
    original_title      object
    start_year           int64
    runtime_minutes    float64
    genres              object
    dtype: object 
     **********************
    
     imdb_ratings data types: 
     tconst            object
    averagerating    float64
    numvotes           int64
    dtype: object 
     **********************
    
     movie_budgets data types: 
     id                    int64
    release_date         object
    movie                object
    production_budget     int32
    domestic_gross        int32
    worldwide_gross       int64
    title                object
    dtype: object 
     **********************
    


```python
movie_gross.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3387 entries, 0 to 3386
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   title           3387 non-null   object 
     1   studio          3387 non-null   object 
     2   domestic_gross  3387 non-null   float64
     3   foreign_gross   3387 non-null   float64
     4   year            3387 non-null   int64  
    dtypes: float64(2), int64(1), object(2)
    memory usage: 132.4+ KB
    

Check for missing values


```python
#check for missing values
df_list=  [movie_gross,title_df,imdb_ratings]
df_list_names = ['movie_gross','title_df','imdb_ratings']
for i, n in zip(df_list, df_list_names):
    print(f'{n}:')
    print(i.isna().sum(), '\n ****************\n')
```

    movie_gross:
    title             0
    studio            0
    domestic_gross    0
    foreign_gross     0
    year              0
    dtype: int64 
     ****************
    
    title_df:
    tconst             0
    primary_title      1
    original_title     1
    start_year         0
    runtime_minutes    0
    genres             0
    dtype: int64 
     ****************
    
    imdb_ratings:
    tconst           0
    averagerating    0
    numvotes         0
    dtype: int64 
     ****************
    
    

## Merging Data Frames

### Import studio from movies_gross to movie_budgets since movies_budgets has more information including production budget and worldwide gross


```python
#Merge the title_df with the imdb_ratings DataFrames an inner join
joined_imdb_df = title_df.merge(imdb_ratings, on='tconst', how='inner')
joined_imdb_df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt1699720</td>
      <td>!Women Art Revolution</td>
      <td>Women Art Revolution</td>
      <td>2010</td>
      <td>83.0</td>
      <td>Documentary</td>
      <td>6.9</td>
      <td>196</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt2346170</td>
      <td>#1 Serial Killer</td>
      <td>#1 Serial Killer</td>
      <td>2013</td>
      <td>87.0</td>
      <td>Horror</td>
      <td>5.6</td>
      <td>40</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt3120962</td>
      <td>#5</td>
      <td>#5</td>
      <td>2013</td>
      <td>68.0</td>
      <td>Biography,Comedy,Fantasy</td>
      <td>6.8</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>




```python
joined_imdb_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 72300 entries, 0 to 72299
    Data columns (total 8 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   tconst           72300 non-null  object 
     1   primary_title    72300 non-null  object 
     2   original_title   72300 non-null  object 
     3   start_year       72300 non-null  int64  
     4   runtime_minutes  72300 non-null  float64
     5   genres           72300 non-null  object 
     6   averagerating    72300 non-null  float64
     7   numvotes         72300 non-null  int64  
    dtypes: float64(2), int64(2), object(4)
    memory usage: 4.4+ MB
    

The joined_imdb_df dataframe has two columns for the movie title: primaryTitle and the originalTitle
It is important to determine the column to use for merging the movie_gross_df and joined_imdb_df dataframes.
From the IMDB Datasets website:

primary_title is the more popular title / the title used by the filmmakers on promotional materials at the point of release.
original_title is the original title, in the original language
In this case, since primary_title is the more popular title, it will be used to merge the two datasets.


```python
#Rename primary_title column to movie
joined_imdb_df.rename(columns = {'primary_title': 'title'}, inplace = True)
#Merge movie_gross and joined_imdb_df on the movie column using an inner join
movies_df = joined_imdb_df.merge(movie_gross, on='title', how='inner')
movies_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tconst</th>
      <th>title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>averagerating</th>
      <th>numvotes</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt2614684</td>
      <td>'71</td>
      <td>'71</td>
      <td>2014</td>
      <td>99.0</td>
      <td>Action,Drama,Thriller</td>
      <td>7.2</td>
      <td>46103</td>
      <td>RAtt.</td>
      <td>1300000.0</td>
      <td>355000.0</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt2353767</td>
      <td>1,000 Times Good Night</td>
      <td>Tusen ganger god natt</td>
      <td>2013</td>
      <td>117.0</td>
      <td>Drama,War</td>
      <td>7.1</td>
      <td>6848</td>
      <td>FM</td>
      <td>53900.0</td>
      <td>0.0</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt1179933</td>
      <td>10 Cloverfield Lane</td>
      <td>10 Cloverfield Lane</td>
      <td>2016</td>
      <td>103.0</td>
      <td>Drama,Horror,Mystery</td>
      <td>7.2</td>
      <td>260383</td>
      <td>Par.</td>
      <td>72100000.0</td>
      <td>38100000.0</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt1715873</td>
      <td>10 Years</td>
      <td>10 Years</td>
      <td>2011</td>
      <td>100.0</td>
      <td>Comedy,Drama,Romance</td>
      <td>6.1</td>
      <td>22484</td>
      <td>Anch.</td>
      <td>203000.0</td>
      <td>0.0</td>
      <td>2012</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt3346824</td>
      <td>1001 Grams</td>
      <td>1001 Gram</td>
      <td>2014</td>
      <td>93.0</td>
      <td>Drama</td>
      <td>6.3</td>
      <td>1301</td>
      <td>KL</td>
      <td>11000.0</td>
      <td>0.0</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Check joined_imdb_df for duplicates in primaryTitle column
movies_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2948 entries, 0 to 2947
    Data columns (total 12 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   tconst           2948 non-null   object 
     1   title            2948 non-null   object 
     2   original_title   2948 non-null   object 
     3   start_year       2948 non-null   int64  
     4   runtime_minutes  2948 non-null   float64
     5   genres           2948 non-null   object 
     6   averagerating    2948 non-null   float64
     7   numvotes         2948 non-null   int64  
     8   studio           2948 non-null   object 
     9   domestic_gross   2948 non-null   float64
     10  foreign_gross    2948 non-null   float64
     11  year             2948 non-null   int64  
    dtypes: float64(4), int64(3), object(5)
    memory usage: 276.5+ KB
    


```python
#shape of merged dataframe
movies_df.shape
```




    (2948, 12)




```python
#check for duplicates in merged dataframe
movies_df.duplicated().value_counts()
```




    False    2948
    Name: count, dtype: int64




```python
##unique value counts in columns with dtype object in the merged dataset
cat_columns = movies_df.select_dtypes(include='object').columns
for column in cat_columns:
    print(f'{column}:')
    print(movies_df[column].value_counts())
    print()
```

    tconst:
    tconst
    tt6599340    2
    tt2442772    2
    tt2614684    1
    tt1464580    1
    tt2250912    1
                ..
    tt5220122    1
    tt1573072    1
    tt4991978    1
    tt1980986    1
    tt2948356    1
    Name: count, Length: 2946, dtype: int64
    
    title:
    title
    One Day                         6
    Gold                            6
    Youth                           5
    We Are Family                   5
    Mama                            4
                                   ..
    I Can Speak                     1
    I Don't Know How She Does It    1
    I Feel Pretty                   1
    I Give It a Year                1
    Zootopia                        1
    Name: count, Length: 2598, dtype: int64
    
    original_title:
    original_title
    The Journey             4
    Mama                    4
    Aurora                  4
    The Gambler             4
    Eden                    4
                           ..
    Ang-ma-reul bo-at-da    1
    I Saw the Light         1
    I Smile Back            1
    I Spit on Your Grave    1
    Zootopia                1
    Name: count, Length: 2704, dtype: int64
    
    genres:
    genres
    Drama                      296
    Comedy,Drama,Romance       131
    Comedy,Drama               131
    Drama,Romance              111
    Documentary                104
                              ... 
    Drama,History,Sport          1
    Action,Animation,Sci-Fi      1
    Adventure,Comedy,Music       1
    Adventure,Drama,Sport        1
    Sport                        1
    Name: count, Length: 320, dtype: int64
    
    studio:
    studio
    Uni.     145
    IFC      142
    Fox      136
    WB       118
    Magn.    114
            ... 
    ITL        1
    AZ         1
    MPI        1
    SHO        1
    ICir       1
    Name: count, Length: 217, dtype: int64
    
    


```python
#make a copy of the dataframe and assign it to a different variable name.
movies = movies_df.copy()
#split the genre categories into a list, around the comma separator
movies['genres'] = movies['genres'].str.split(',')
#transform each element in the genres list to a row
movies = movies.explode('genres')
```


```python
#reset index
movies = movies.reset_index(drop=True)
```


```python
#value counts of the genres column in the new dataset after separating the genres
movies['genres'].value_counts()
```




    genres
    Drama          1718
    Comedy          918
    Action          638
    Romance         464
    Thriller        439
    Adventure       436
    Crime           379
    Biography       282
    Horror          230
    Documentary     216
    Mystery         206
    Fantasy         166
    Animation       151
    Sci-Fi          135
    History         134
    Family          114
    Music            89
    Sport            52
    War              50
    Western          21
    Musical          18
    News              2
    Name: count, dtype: int64




```python
#value counts of the genres column in the dataset before separating the genres
movies_df['genres'].value_counts()
```




    genres
    Drama                      296
    Comedy,Drama,Romance       131
    Comedy,Drama               131
    Drama,Romance              111
    Documentary                104
                              ... 
    Drama,History,Sport          1
    Action,Animation,Sci-Fi      1
    Adventure,Comedy,Music       1
    Adventure,Drama,Sport        1
    Sport                        1
    Name: count, Length: 320, dtype: int64




```python
import ast
# Merge the result with movie_budgets based on the 'id' column
merged_df = pd.merge(tmdb_movies, movie_gross, on='title')
final_merged_df = pd.merge(merged_df, movie_budgets, on='title')

# # Drop the duplicate columns
final_merged_df.drop(columns=['release_date_x', 'domestic_gross_y', 'id_x', 'original_title','id_y', 'movie'], inplace=True)

# Rename the 'movie' column to 'title'
final_merged_df.rename(columns={'domestic_gross_x': 'domestic_gross'}, inplace=True)
final_merged_df.rename(columns={'release_date_y': 'release_date'}, inplace=True)

# Convert the string representation of lists into actual lists of integers
final_merged_df['genre_ids'] = final_merged_df['genre_ids'].apply(ast.literal_eval)

# Create a new column 'genre_names' that maps the genre IDs to their names
final_merged_df['genre_names'] = final_merged_df['genre_ids'].apply(map_ids_to_names)

#Remove any duplicates occuring
final_merged_df.drop_duplicates(subset='title',keep=False, inplace=True)

# Save DataFrame to CSV
final_merged_df.to_csv('final_merged_df.csv', index=False)

final_merged_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>genre_ids</th>
      <th>original_language</th>
      <th>popularity</th>
      <th>title</th>
      <th>vote_average</th>
      <th>vote_count</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
      <th>release_date</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>genre_names</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[14, 12, 16, 10751]</td>
      <td>en</td>
      <td>28.734</td>
      <td>How to Train Your Dragon</td>
      <td>7.7</td>
      <td>7610</td>
      <td>P/DW</td>
      <td>217600000.0</td>
      <td>277300000.0</td>
      <td>2010</td>
      <td>Mar 26, 2010</td>
      <td>165000000</td>
      <td>494870992</td>
      <td>[Fantasy, Adventure, Animation, Family]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[12, 28, 878]</td>
      <td>en</td>
      <td>28.515</td>
      <td>Iron Man 2</td>
      <td>6.8</td>
      <td>12368</td>
      <td>Par.</td>
      <td>312400000.0</td>
      <td>311500000.0</td>
      <td>2010</td>
      <td>May 7, 2010</td>
      <td>170000000</td>
      <td>621156389</td>
      <td>[Adventure, Action, Science Fiction]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[28, 878, 12]</td>
      <td>en</td>
      <td>27.920</td>
      <td>Inception</td>
      <td>8.3</td>
      <td>22186</td>
      <td>WB</td>
      <td>292600000.0</td>
      <td>535700000.0</td>
      <td>2010</td>
      <td>Jul 16, 2010</td>
      <td>160000000</td>
      <td>835524642</td>
      <td>[Action, Science Fiction, Adventure]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[16, 10751, 35]</td>
      <td>en</td>
      <td>24.445</td>
      <td>Toy Story 3</td>
      <td>7.7</td>
      <td>8340</td>
      <td>BV</td>
      <td>415000000.0</td>
      <td>652000000.0</td>
      <td>2010</td>
      <td>Jun 18, 2010</td>
      <td>200000000</td>
      <td>1068879522</td>
      <td>[Animation, Family, Comedy]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[16, 10751, 35]</td>
      <td>en</td>
      <td>23.673</td>
      <td>Despicable Me</td>
      <td>7.2</td>
      <td>10057</td>
      <td>Uni.</td>
      <td>251500000.0</td>
      <td>291600000.0</td>
      <td>2010</td>
      <td>Jul 9, 2010</td>
      <td>69000000</td>
      <td>543464573</td>
      <td>[Animation, Family, Comedy]</td>
    </tr>
  </tbody>
</table>
</div>




```python
final_merged_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 1093 entries, 0 to 1270
    Data columns (total 14 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   genre_ids          1093 non-null   object 
     1   original_language  1093 non-null   object 
     2   popularity         1093 non-null   float64
     3   title              1093 non-null   object 
     4   vote_average       1093 non-null   float64
     5   vote_count         1093 non-null   int64  
     6   studio             1093 non-null   object 
     7   domestic_gross     1093 non-null   float64
     8   foreign_gross      1093 non-null   float64
     9   year               1093 non-null   int64  
     10  release_date       1093 non-null   object 
     11  production_budget  1093 non-null   int32  
     12  worldwide_gross    1093 non-null   int64  
     13  genre_names        1093 non-null   object 
    dtypes: float64(4), int32(1), int64(3), object(6)
    memory usage: 123.8+ KB
    

# Data Modelling

## Popular Genre Combinations


```python
# Import necessary libraries
from ast import literal_eval

from sklearn.preprocessing import MultiLabelBinarizer

# Initialize the MultiLabelBinarizer
mlb = MultiLabelBinarizer()

# Binarize the genre labels
binary_genres = mlb.fit_transform(final_merged_df['genre_names'])

# Create a DataFrame from the binary labels
binary_genres_df = pd.DataFrame(binary_genres, columns=mlb.classes_)

# Compute the co-occurrence matrix
co_occurrence_matrix = binary_genres_df.T.dot(binary_genres_df)

# Set the diagonal to zero, since we're not interested in how often each genre co-occurs with itself
np.fill_diagonal(co_occurrence_matrix.values, 0)

# Display the co-occurrence matrix
plt.figure(figsize=(12, 12))
sns.heatmap(co_occurrence_matrix, annot=True, fmt='d', cmap='YlGnBu')
plt.title('Genre Co-occurrence Matrix')
plt.show()
```


    
![png](student_files/student_75_0.png)
    


The heatmap above is a genre co-occurrence matrix, which shows how often each pair of genres appears together in the same movie. The color of each cell represents the number of times the corresponding genres co-occur, with darker colors indicating higher co-occurrence.

From the heatmap, we can observe that certain genres tend to co-occur more frequently. For example, 'Adventure' and 'Action', 'Adventure' and 'Fantasy', 'Adventure' and 'Science Fiction' are some pairs of genres that often appear together.

---

## Most Popular Genres


```python
#countplot showing the number of movies per genre
sns.set(style='darkgrid')
sns.set_context('talk')
fig, ax = plt.subplots(figsize=(14,7))
ax = sns.countplot(x=movies['genres'], order=movies['genres'].value_counts().index)
ax.set_title('Number of movies per genre')
ax.set_xlabel('genres')
plt.xticks(rotation=60);
```


    
![png](student_files/student_78_0.png)
    


## Most profittable Genres

## Release period vs worldwide gross


```python
# Convert 'release_date' to datetime
final_merged_df['release_date'] = pd.to_datetime(final_merged_df['release_date'])

# Extract the month from the release date
final_merged_df['release_month'] = final_merged_df['release_date'].dt.month

# Compute the average profit per month
profit_per_month = final_merged_df.groupby('release_month')['worldwide_gross'].mean()

# Plot the average profit per month
plt.figure(figsize=(12, 4))
sns.lineplot(x=profit_per_month.index, y=profit_per_month.values)
plt.xlabel('Month')
plt.ylabel('Average worldwide_gross')
plt.title('Average worldwide_gross by Month of Release')
plt.xticks(np.arange(1, 13), ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.show()
```


    
![png](student_files/student_81_0.png)
    


Movies released in the months of May, June, and July tend to yield higher profits, which coincides with long summer holidays for the northern hemisphere. There is also a noticeable peak in November due to the winter holiday season.
***


```python
# Calculate the average worldwide_gross per year
average_budget_per_year = final_merged_df.groupby('year')['production_budget'].mean().reset_index().astype(int)
print(average_budget_per_year)
```

       year  production_budget
    0  2010           42637680
    1  2011           44871451
    2  2012           47829734
    3  2013           55422764
    4  2014           45764469
    5  2015           45485365
    6  2016           49578625
    7  2017           61046153
    8  2018           55816049
    


```python
# Plot average domestic_gross, foreign_gross and worldwide_gross per year
plt.figure(figsize=(10, 6))
sns.set_style("dark")
sns.lineplot(x='year', y='production_budget', data=average_budget_per_year, marker='o', label='Average Budget')

plt.xlabel('Year', fontweight='bold')
plt.ylabel('Average Budget (in billions)', fontweight='bold')
plt.title('Average Budget per Year', fontsize=15, fontweight='bold')
plt.legend()
plt.tight_layout()
plt.grid(False)

plt.show()
```


    
![png](student_files/student_84_0.png)
    



```python
# Calculate the average worldwide_gross per year
average_worldwide_gross_per_year = final_merged_df.groupby('year')['worldwide_gross'].mean().reset_index().astype(int)
print(average_worldwide_gross_per_year)
```

       year  worldwide_gross
    0  2010        118771030
    1  2011        124462583
    2  2012        151840688
    3  2013        165568944
    4  2014        168044034
    5  2015        157725107
    6  2016        164661788
    7  2017        211274993
    8  2018        221532470
    


```python
# Plot average domestic_gross, foreign_gross and worldwide_gross per year
plt.figure(figsize=(10, 6))
sns.set_style("white")
sns.lineplot(x='year', y='worldwide_gross', data=average_worldwide_gross_per_year, marker='o', label='Average Woldwide Gross')

plt.xlabel('Year', fontweight='bold')
plt.ylabel('Average Gross (in billions)', fontweight='bold')
plt.title('Average Worldwide Gross per Year', fontsize=15, fontweight='bold')
plt.legend()
plt.tight_layout()
plt.grid(True)

plt.show()
```


    
![png](student_files/student_86_0.png)
    



```python
# Group the data by 'season' and 'year', and count the number of movie releases for each combination
movie_releases_per_year = final_merged_df.groupby('year').size().reset_index(name='count')
print(movie_releases_per_year)

```

       year  count
    0  2010    139
    1  2011    155
    2  2012    132
    3  2013    123
    4  2014    118
    5  2015    123
    6  2016    131
    7  2017     91
    8  2018     81
    


```python
# Create the line plot
sns.set(font_scale=1.2)
sns.set_style('darkgrid')
plt.figure(figsize=(10, 6))
sns.lineplot(x='year', y='count', data=movie_releases_per_year, marker='o')
plt.xlabel('Year', fontweight='bold')
plt.ylabel('Number of Movie Releases', fontweight='bold')
plt.title('Trend of Movie Releases per Year', fontsize=15, fontweight='bold')
plt.tight_layout()
plt.grid(True)

plt.show()
```


    
![png](student_files/student_88_0.png)
    


## Return on Investment

This will analyze if there is a relationship between the production budget and the worldwide gross. The analysis will be limited since there are more costs involved in the process after production such as the marketing budget, and residuals which are yments are calculated on the basis of sales performance using rules that are set out in industry negotiated agreements.


```python
# Calculate ROI
final_merged_df['ROI'] = ((final_merged_df['worldwide_gross'] - final_merged_df['production_budget']) / final_merged_df['production_budget']) * 100

# Compute the correlation
correlation = final_merged_df[['production_budget', 'ROI']].corr()

# Create budget categories
bins = [0, 1e6, 1e7, 1e8, np.inf]
names = ['<1M', '1M-10M', '10M-100M', '>100M']

final_merged_df['Budget_Range'] = pd.cut(final_merged_df['production_budget'], bins, labels=names)

# Create a boxplot
plt.figure(figsize=(12,6))
sns.boxplot(x='Budget_Range', y='ROI', data=final_merged_df, order=names, showfliers=False)

plt.title('Distribution of ROI for Different Budget Ranges')
plt.xlabel('Production Budget Range')
plt.ylabel('Return on Investment')

plt.show()
```


    
![png](student_files/student_90_0.png)
    



```python
# Scatter plot of average rating in tmdb_movies
plt.figure(figsize=(10, 6))
plt.scatter(tmdb_movies['vote_average'], tmdb_movies['vote_count'])
plt.xlabel('Average Rating')
plt.ylabel('Vote Count')
plt.title('Scatter Plot of Average Rating and Vote Count')
plt.show()
```


    
![png](student_files/student_91_0.png)
    



```python
# Box plot of average rating in tmdb_movies
plt.figure(figsize=(10, 6))
plt.boxplot(tmdb_movies['vote_average'].dropna())
plt.ylabel('Average Rating')
plt.title('Box Plot of Average Rating')
plt.show()
```


    
![png](student_files/student_92_0.png)
    



```python
#Explode the "genre" column to create a new row for each genre of a movie
rt_movie_exploded = rt_movie.explode('genre')

# Group the data by genre and calculate the sum of box office earnings for each genre
genre_box_office = rt_movie_exploded.groupby('genre')['box_office'].sum().sort_values(ascending=False)

# Select the top 5 genres
top_5_genres = genre_box_office.head(5)

# Plot the top 5 genres with colors using Seaborn
plt.figure(figsize=(12, 6))
sns.barplot(x=top_5_genres.values, y=top_5_genres.index, palette='viridis')
plt.ylabel('Genre')
plt.xlabel('Total Box Office Earnings')
plt.title('Top 5 Genres by Box Office Earnings')
plt.xticks(rotation=90)
plt.tight_layout()
plt.show()
```


    
![png](student_files/student_93_0.png)
    



```python
# # Display the highest earning movies per year

# Find the index of the row with the highest gross earnings for each year
max_gross_movies_indices = final_merged_df.groupby('year')['worldwide_gross'].idxmax()
# Select the rows corresponding to the highest earning movies for each year
highest_gross_movies = final_merged_df.loc[max_gross_movies_indices]

highest_gross_movies[['year', 'title', 'worldwide_gross']].sort_values(by='worldwide_gross', ascending=False)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>title</th>
      <th>worldwide_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1191</th>
      <td>2018</td>
      <td>Avengers: Infinity War</td>
      <td>2048134200</td>
    </tr>
    <tr>
      <th>843</th>
      <td>2015</td>
      <td>Jurassic World</td>
      <td>1648854864</td>
    </tr>
    <tr>
      <th>1105</th>
      <td>2017</td>
      <td>The Fate of the Furious</td>
      <td>1234846267</td>
    </tr>
    <tr>
      <th>545</th>
      <td>2013</td>
      <td>Iron Man 3</td>
      <td>1215392272</td>
    </tr>
    <tr>
      <th>971</th>
      <td>2016</td>
      <td>Captain America: Civil War</td>
      <td>1140069413</td>
    </tr>
    <tr>
      <th>357</th>
      <td>2011</td>
      <td>Transformers: Dark of the Moon</td>
      <td>1123790543</td>
    </tr>
    <tr>
      <th>403</th>
      <td>2012</td>
      <td>Skyfall</td>
      <td>1110526981</td>
    </tr>
    <tr>
      <th>715</th>
      <td>2014</td>
      <td>Transformers: Age of Extinction</td>
      <td>1104039076</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>Toy Story 3</td>
      <td>1068879522</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the highest earning movies per year
plt.figure(figsize=(12, 6))
sns.set_style('white')
sns.barplot(data=highest_gross_movies, x='year', y='worldwide_gross', hue='title', dodge=False)
plt.xlabel('Year', fontweight='bold')
plt.ylabel('Gross Earnings (Hundred Millions)', fontweight='bold')
plt.title('Highest Earning Movies per Year', fontsize=15, fontweight='bold')
plt.grid(axis='y')
plt.legend(title='Movie Title', bbox_to_anchor=(1, 1))

plt.show()
```


    
![png](student_files/student_95_0.png)
    



```python
# Create a new column 'gross_profit' which is the difference between worldwide_gross and production_budget
final_merged_df['gross_profit']= final_merged_df['worldwide_gross'] - final_merged_df['production_budget']
```


```python
# Display movies with the highest gross profit per year

# Find the index of the row with the highest domestic gross earnings for each year
max_grossprofit_movies_indices = final_merged_df.groupby('year')['gross_profit'].idxmax()

# Select the rows corresponding to the highest earning movies for each year
highest_grossprofit_movies = final_merged_df.loc[max_grossprofit_movies_indices]

highest_grossprofit_movies[['year', 'title', 'gross_profit', 'studio']].sort_values(ascending=False, by='gross_profit')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>title</th>
      <th>gross_profit</th>
      <th>studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1191</th>
      <td>2018</td>
      <td>Avengers: Infinity War</td>
      <td>1748134200</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>843</th>
      <td>2015</td>
      <td>Jurassic World</td>
      <td>1433854864</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>545</th>
      <td>2013</td>
      <td>Iron Man 3</td>
      <td>1015392272</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1105</th>
      <td>2017</td>
      <td>The Fate of the Furious</td>
      <td>984846267</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>357</th>
      <td>2011</td>
      <td>Transformers: Dark of the Moon</td>
      <td>928790543</td>
      <td>P/DW</td>
    </tr>
    <tr>
      <th>403</th>
      <td>2012</td>
      <td>Skyfall</td>
      <td>910526981</td>
      <td>Sony</td>
    </tr>
    <tr>
      <th>715</th>
      <td>2014</td>
      <td>Transformers: Age of Extinction</td>
      <td>894039076</td>
      <td>Par.</td>
    </tr>
    <tr>
      <th>971</th>
      <td>2016</td>
      <td>Captain America: Civil War</td>
      <td>890069413</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>Toy Story 3</td>
      <td>868879522</td>
      <td>BV</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the movies with the highest gross profit per year
plt.figure(figsize=(12, 6))
sns.set_style('white')
sns.barplot(data=highest_grossprofit_movies, x='year', y='gross_profit', hue='title', dodge=False)
plt.xlabel('Year', fontweight='bold')
plt.ylabel('Gross Profit (Billions)', fontweight='bold')
plt.title('Most Profitable Yearly', fontsize=15, fontweight='bold')
plt.grid(axis='y')
plt.legend(title='Movie Title', bbox_to_anchor=(1, 1))

plt.show()
```


    
![png](student_files/student_98_0.png)
    


## Studios: Top 20 Releases Over the Years

We look at the number of movie releases per studio for each year and find that Buena Vista/Walt Disney, Paramount, Universal Studios, Warner Brothers, and Fox are consistently been among the highest number of movie producers over the 8-year period.


```python
# Display number of movies per studio each year
movies_per_studio_per_year = final_merged_df.groupby(['studio', 'year']).size()\
                .reset_index(name='count').sort_values(by='count', ascending=False)
movies_per_studio_per_year
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>year</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>238</th>
      <td>Uni.</td>
      <td>2015</td>
      <td>17</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Fox</td>
      <td>2013</td>
      <td>14</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Fox</td>
      <td>2015</td>
      <td>13</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Fox</td>
      <td>2011</td>
      <td>13</td>
    </tr>
    <tr>
      <th>236</th>
      <td>Uni.</td>
      <td>2013</td>
      <td>13</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>108</th>
      <td>LD</td>
      <td>2012</td>
      <td>1</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Jan.</td>
      <td>2011</td>
      <td>1</td>
    </tr>
    <tr>
      <th>106</th>
      <td>IW</td>
      <td>2010</td>
      <td>1</td>
    </tr>
    <tr>
      <th>105</th>
      <td>IM</td>
      <td>2012</td>
      <td>1</td>
    </tr>
    <tr>
      <th>279</th>
      <td>Yash</td>
      <td>2013</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>280 rows × 3 columns</p>
</div>




```python
# Plot the top 20 number of movies per studio each year
plt.figure(figsize=(12, 6))
sns.set_style('white')
sns.barplot(x='year', y='count', hue='studio', data=movies_per_studio_per_year.head(20), dodge=False)
plt.title('Top 20: Number of Movies per Studio Each Year', fontsize=15, fontweight='bold')
plt.xlabel('Year', fontweight='bold')
plt.ylabel('Number of Movies', fontweight='bold')
plt.grid(axis='y')
plt.legend(title='Studio', loc='upper right')

plt.show()
```


    
![png](student_files/student_102_0.png)
    


## Studios: Highest Earning Per Year

Looking at the highest earners year on year, domestically Walt Disney (BV) and Warner Bros. (WB) appear to have been the most successful with their releases making them the highest earners in 3 out of the 9 years.

Overseas Walt Disney has had even more success with their productions as they have been the highest earning studio for 5 out of the 9 years. Also successful with their production in the foreign market is Universal, being the highest earner in 2 different years.

Looking at global earnings in totality, the data infers that Walt Disney is the most successful production studio between 2010 and 2018 having been the highest earner in 3 different years, followed thereon by Universal Studios.



```python
# Find the studio with the highest domestic_gross earnings each year
# Group by 'year' and find the index of the maximum domestic gross earnings for each year
max_dom_studio_indices = final_merged_df.groupby('year')['domestic_gross'].idxmax()

# Select the studios with the highest domestic gross earnings for each year
studio_dom_earnings_per_year = final_merged_df.loc[max_dom_studio_indices, ['year', 'studio', 'domestic_gross']]\
                                .sort_values(by='domestic_gross', ascending=False)

studio_dom_earnings_per_year
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>studio</th>
      <th>domestic_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1191</th>
      <td>2018</td>
      <td>BV</td>
      <td>678800000.0</td>
    </tr>
    <tr>
      <th>843</th>
      <td>2015</td>
      <td>Uni.</td>
      <td>652300000.0</td>
    </tr>
    <tr>
      <th>982</th>
      <td>2016</td>
      <td>BV</td>
      <td>532200000.0</td>
    </tr>
    <tr>
      <th>396</th>
      <td>2012</td>
      <td>WB</td>
      <td>448100000.0</td>
    </tr>
    <tr>
      <th>556</th>
      <td>2013</td>
      <td>LGF</td>
      <td>424700000.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>BV</td>
      <td>415000000.0</td>
    </tr>
    <tr>
      <th>1121</th>
      <td>2017</td>
      <td>Sony</td>
      <td>404500000.0</td>
    </tr>
    <tr>
      <th>357</th>
      <td>2011</td>
      <td>P/DW</td>
      <td>352400000.0</td>
    </tr>
    <tr>
      <th>724</th>
      <td>2014</td>
      <td>WB</td>
      <td>350100000.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the studios with the highest domestic gross earnings for each year
plt.figure(figsize=(10,6))
sns.set_style('white')
sns.barplot(x='year', y='domestic_gross', hue='studio', data=studio_dom_earnings_per_year, dodge=False)
plt.title("Studios with Highest Domestic Gross Earnings Per Year", fontsize=15, fontweight='bold')
plt.ylabel("Domestic Gross Earnings (Hundred Millions)", fontweight='bold')
plt.xlabel("Year", fontweight='bold')
plt.grid(axis='y')

plt.show()
```


    
![png](student_files/student_106_0.png)
    



```python
# Find the studio with the highest foreign_gross earnings each year
# Group by 'year' and find the index of the maximum foreign gross earnings for each year
max_for_studio_indices = final_merged_df.groupby('year')['foreign_gross'].idxmax()

# Select the studios with the highest foreign gross earnings for each year
studio_for_earnings_per_year = final_merged_df.loc[max_for_studio_indices, ['year', 'studio', 'foreign_gross']]\
                                .sort_values(by='foreign_gross', ascending=False)

studio_for_earnings_per_year
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>studio</th>
      <th>foreign_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>830</th>
      <td>2015</td>
      <td>BV</td>
      <td>946400000.0</td>
    </tr>
    <tr>
      <th>1201</th>
      <td>2018</td>
      <td>Uni.</td>
      <td>891800000.0</td>
    </tr>
    <tr>
      <th>715</th>
      <td>2014</td>
      <td>Par.</td>
      <td>858600000.0</td>
    </tr>
    <tr>
      <th>545</th>
      <td>2013</td>
      <td>BV</td>
      <td>805800000.0</td>
    </tr>
    <tr>
      <th>191</th>
      <td>2011</td>
      <td>BV</td>
      <td>804600000.0</td>
    </tr>
    <tr>
      <th>403</th>
      <td>2012</td>
      <td>Sony</td>
      <td>804200000.0</td>
    </tr>
    <tr>
      <th>1133</th>
      <td>2017</td>
      <td>Uni.</td>
      <td>770200000.0</td>
    </tr>
    <tr>
      <th>971</th>
      <td>2016</td>
      <td>BV</td>
      <td>745200000.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>BV</td>
      <td>652000000.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the studios with the highest foreign gross earnings for each year
plt.figure(figsize=(10,6))
sns.set_style('white')
sns.barplot(x='year', y='foreign_gross', hue='studio', data=studio_for_earnings_per_year, dodge=False)
plt.title("Studios with Highest Foreign Gross Earnings Per Year", fontsize=15, fontweight='bold')
plt.ylabel("Foreign Gross Earnings (Billions)", fontweight='bold')
plt.xlabel("Year", fontweight='bold')
plt.grid(axis='y')

plt.show()
```


    
![png](student_files/student_108_0.png)
    



```python
# Find the studio with the highest worldwide_gross earnings each year
# Group by 'year' and find the index of the maximum worldwide gross earnings for each year
max_ww_studio_indices = final_merged_df.groupby('year')['worldwide_gross'].idxmax()

# Select the studios with the highest worldwide gross earnings for each year
studio_ww_earnings_per_year = final_merged_df.loc[max_ww_studio_indices, ['year', 'studio', 'worldwide_gross']]\
                                .sort_values(by='worldwide_gross', ascending=False)

studio_ww_earnings_per_year
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>studio</th>
      <th>worldwide_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1191</th>
      <td>2018</td>
      <td>BV</td>
      <td>2048134200</td>
    </tr>
    <tr>
      <th>843</th>
      <td>2015</td>
      <td>Uni.</td>
      <td>1648854864</td>
    </tr>
    <tr>
      <th>1105</th>
      <td>2017</td>
      <td>Uni.</td>
      <td>1234846267</td>
    </tr>
    <tr>
      <th>545</th>
      <td>2013</td>
      <td>BV</td>
      <td>1215392272</td>
    </tr>
    <tr>
      <th>971</th>
      <td>2016</td>
      <td>BV</td>
      <td>1140069413</td>
    </tr>
    <tr>
      <th>357</th>
      <td>2011</td>
      <td>P/DW</td>
      <td>1123790543</td>
    </tr>
    <tr>
      <th>403</th>
      <td>2012</td>
      <td>Sony</td>
      <td>1110526981</td>
    </tr>
    <tr>
      <th>715</th>
      <td>2014</td>
      <td>Par.</td>
      <td>1104039076</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>BV</td>
      <td>1068879522</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the studios with the highest worldwide gross earnings for each year
plt.figure(figsize=(10,6))
sns.set_style('white')
sns.barplot(x='year', y='worldwide_gross', hue='studio', data=studio_ww_earnings_per_year, dodge=False)
plt.title("Studios with Highest Worldwide Gross Earnings Per Year", fontsize=15, fontweight='bold')
plt.ylabel("Worldwide Gross Earnings (Billions)", fontweight='bold')
plt.xlabel("Year", fontweight='bold')
plt.grid(axis='y')

plt.show()
```


    
![png](student_files/student_110_0.png)
    


## Studios: Distribution of Earnings

The order of the top 10 studios in worldwide earnings follows that of the highest-earning studios domestically. Walt Disney (BV) is consistently the highest earner in all markets. Domestically, Universal Studios is the second highest-earning studio but sits third in the foreign market. The inverse is true for Fox who sits second in earnings overseas but third domestically.

All studios in the top 10 earned above USD 1 billion.


```python
earnings_df_filtered = final_merged_df.copy(deep=True)
```


```python
# Find the highest domestic earning studios
studio_dom_earnings = earnings_df_filtered.groupby('studio')['domestic_gross'].sum().reset_index()\
        .sort_values(by='domestic_gross', ascending=False)

studio_dom_earnings
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>domestic_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>14</th>
      <td>BV</td>
      <td>1.088350e+10</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Uni.</td>
      <td>9.960338e+09</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Fox</td>
      <td>9.025100e+09</td>
    </tr>
    <tr>
      <th>82</th>
      <td>WB</td>
      <td>8.095700e+09</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Sony</td>
      <td>6.332800e+09</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>TFA</td>
      <td>3.229900e+04</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Alc</td>
      <td>1.750000e+04</td>
    </tr>
    <tr>
      <th>56</th>
      <td>P4</td>
      <td>8.500000e+03</td>
    </tr>
    <tr>
      <th>10</th>
      <td>BBC</td>
      <td>7.500000e+03</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ALP</td>
      <td>2.800000e+03</td>
    </tr>
  </tbody>
</table>
<p>87 rows × 2 columns</p>
</div>




```python
# Plot the top 10 studios with the highest domestic earnings
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x='domestic_gross', y='studio', data=studio_dom_earnings.head(10))
plt.title('Top 10 Studios: Highest Domestic Gross Earnings', fontsize=15, fontweight='bold')
plt.xlabel('Domestic Gross Earnings (Ten Billions)', fontsize=15, fontweight='bold')
plt.ylabel('Studio', fontsize=15, fontweight='bold')
plt.grid(axis='x')

plt.show()
```


    
![png](student_files/student_115_0.png)
    



```python
# Find the highest foreign earning studios
studio_foreign_earnings= earnings_df_filtered.groupby('studio')['foreign_gross'].sum().reset_index()\
                            .sort_values(by='foreign_gross', ascending=False)

studio_foreign_earnings
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>foreign_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29</th>
      <td>Fox</td>
      <td>1.702373e+10</td>
    </tr>
    <tr>
      <th>14</th>
      <td>BV</td>
      <td>1.629304e+10</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Uni.</td>
      <td>1.259867e+10</td>
    </tr>
    <tr>
      <th>82</th>
      <td>WB</td>
      <td>1.218978e+10</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Sony</td>
      <td>9.667100e+09</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Cleopatra</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>10</th>
      <td>BBC</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Saban</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>17</th>
      <td>CJ</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Cohen</td>
      <td>0.000000e+00</td>
    </tr>
  </tbody>
</table>
<p>87 rows × 2 columns</p>
</div>




```python
# Plot the top 10 studios with the highest foreign earnings
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x='foreign_gross', y='studio', data=studio_foreign_earnings.head(10))
plt.title('Top 10 Studios: Highest Foreign Gross Earnings', fontsize=15, fontweight='bold')
plt.xlabel('Foreign Gross Earnings (Ten Billions)', fontsize=15, fontweight='bold')
plt.ylabel('Studio', fontsize=15, fontweight='bold')
plt.grid(axis='x')

plt.show()
```


    
![png](student_files/student_117_0.png)
    



```python
# Find the highest worldwide earning studios
studio_ww_earnings= earnings_df_filtered.groupby('studio')['worldwide_gross'].sum().reset_index()\
                            .sort_values(by='worldwide_gross', ascending=False)

studio_ww_earnings.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>worldwide_gross</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>14</th>
      <td>BV</td>
      <td>28425191937</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Fox</td>
      <td>25955497683</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Uni.</td>
      <td>25793486598</td>
    </tr>
    <tr>
      <th>82</th>
      <td>WB</td>
      <td>20215015775</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Sony</td>
      <td>16097425939</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Par.</td>
      <td>13523291721</td>
    </tr>
    <tr>
      <th>83</th>
      <td>WB (NL)</td>
      <td>8361531561</td>
    </tr>
    <tr>
      <th>41</th>
      <td>LGF</td>
      <td>6572482511</td>
    </tr>
    <tr>
      <th>55</th>
      <td>P/DW</td>
      <td>5078027601</td>
    </tr>
    <tr>
      <th>40</th>
      <td>LG/S</td>
      <td>3031840907</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the top 10 studios with the highest worldwide earnings
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x='worldwide_gross', y='studio', data=studio_ww_earnings.head(10))
plt.title('Top 10 Studios: Highest Worldwide Gross Earnings', fontsize=15, fontweight='bold')
plt.xlabel('Worldwide Gross Earnings (Ten Billions)', fontsize=15, fontweight='bold')
plt.ylabel('Studio', fontsize=15, fontweight='bold')
plt.grid(axis='x')

plt.show()
```


    
![png](student_files/student_119_0.png)
    



```python
#Find the top 20 studios with the highest gross_profit
total_profit_per_studio = earnings_df_filtered.groupby('studio')['gross_profit'].sum()
top20_studios_profit = total_profit_per_studio.nlargest(10).reset_index()

top20_studios_profit
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>gross_profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BV</td>
      <td>19870391937</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Uni.</td>
      <td>19696286598</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fox</td>
      <td>18601497683</td>
    </tr>
    <tr>
      <th>3</th>
      <td>WB</td>
      <td>12760015775</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sony</td>
      <td>11532925939</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Par.</td>
      <td>9076291721</td>
    </tr>
    <tr>
      <th>6</th>
      <td>WB (NL)</td>
      <td>6271931561</td>
    </tr>
    <tr>
      <th>7</th>
      <td>LGF</td>
      <td>4619094861</td>
    </tr>
    <tr>
      <th>8</th>
      <td>P/DW</td>
      <td>3744027601</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Wein.</td>
      <td>1787662208</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the top 10 profitable studios
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x='studio', y='gross_profit', data=top20_studios_profit, palette='Blues_r')
plt.xlabel('Studios', fontsize=15, fontweight='bold')
plt.ylabel('Total Gross Profit (Ten Billions)', fontsize=15, fontweight='bold')
plt.title('Top 10 Most Profitable Studios', fontsize=15, fontweight='bold')
plt.xticks(rotation=45, ha='right')
plt.grid(axis='y')
plt.tight_layout()

plt.show()
```


    
![png](student_files/student_121_0.png)
    


## Studios: Top 20 Most Profitable Releases

Out of the top 20 gross worldwide earning movies, 17 are the most proitable. 65% of the most profitable movies are sequels.


```python
# Find the top 10 highest earning movies worldwide per studio
top20_worldwide_earners = earnings_df_filtered.nlargest(20, "worldwide_gross")\
        [["title", "worldwide_gross", "studio"]]
top20_worldwide_earners.head(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>worldwide_gross</th>
      <th>studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1191</th>
      <td>Avengers: Infinity War</td>
      <td>2048134200</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>843</th>
      <td>Jurassic World</td>
      <td>1648854864</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>846</th>
      <td>Furious 7</td>
      <td>1518722794</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>830</th>
      <td>Avengers: Age of Ultron</td>
      <td>1403013963</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1201</th>
      <td>Jurassic World: Fallen Kingdom</td>
      <td>1305772799</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>1199</th>
      <td>Incredibles 2</td>
      <td>1242520711</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1105</th>
      <td>The Fate of the Furious</td>
      <td>1234846267</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>545</th>
      <td>Iron Man 3</td>
      <td>1215392272</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>919</th>
      <td>Minions</td>
      <td>1160336173</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>1197</th>
      <td>Aquaman</td>
      <td>1146894640</td>
      <td>WB</td>
    </tr>
    <tr>
      <th>971</th>
      <td>Captain America: Civil War</td>
      <td>1140069413</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>357</th>
      <td>Transformers: Dark of the Moon</td>
      <td>1123790543</td>
      <td>P/DW</td>
    </tr>
    <tr>
      <th>403</th>
      <td>Skyfall</td>
      <td>1110526981</td>
      <td>Sony</td>
    </tr>
    <tr>
      <th>715</th>
      <td>Transformers: Age of Extinction</td>
      <td>1104039076</td>
      <td>Par.</td>
    </tr>
    <tr>
      <th>396</th>
      <td>The Dark Knight Rises</td>
      <td>1084439099</td>
      <td>WB</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Toy Story 3</td>
      <td>1068879522</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>982</th>
      <td>Rogue One: A Star Wars Story</td>
      <td>1049102856</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>191</th>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>1045663875</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1133</th>
      <td>Despicable Me 3</td>
      <td>1034727750</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>1009</th>
      <td>Finding Dory</td>
      <td>1021215193</td>
      <td>BV</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the top 20 highest earning movies worldwide per studio
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x="worldwide_gross", y="title", orient="h", data=top20_worldwide_earners, hue="studio",
           dodge=False)
plt.title("Top 20: Woldwide Movie Earnings Per Studio", fontsize=15, fontweight='bold')
plt.xlabel("Worldwide Gross Earnings (Billions)", fontweight='bold')
plt.ylabel("Movie Titles", fontweight='bold')
plt.grid(axis='x')

plt.show()
```


    
![png](student_files/student_125_0.png)
    



```python
# Display the studios with the top 20 most profitable movie releases
top20_profitable_movies = earnings_df_filtered.nlargest(20, "gross_profit")
top20_profitable_movies[['title', 'gross_profit', 'studio']]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>gross_profit</th>
      <th>studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1191</th>
      <td>Avengers: Infinity War</td>
      <td>1748134200</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>843</th>
      <td>Jurassic World</td>
      <td>1433854864</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>846</th>
      <td>Furious 7</td>
      <td>1328722794</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>1201</th>
      <td>Jurassic World: Fallen Kingdom</td>
      <td>1135772799</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>919</th>
      <td>Minions</td>
      <td>1086336173</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>830</th>
      <td>Avengers: Age of Ultron</td>
      <td>1072413963</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1199</th>
      <td>Incredibles 2</td>
      <td>1042520711</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>545</th>
      <td>Iron Man 3</td>
      <td>1015392272</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1197</th>
      <td>Aquaman</td>
      <td>986894640</td>
      <td>WB</td>
    </tr>
    <tr>
      <th>1105</th>
      <td>The Fate of the Furious</td>
      <td>984846267</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>1133</th>
      <td>Despicable Me 3</td>
      <td>959727750</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>357</th>
      <td>Transformers: Dark of the Moon</td>
      <td>928790543</td>
      <td>P/DW</td>
    </tr>
    <tr>
      <th>403</th>
      <td>Skyfall</td>
      <td>910526981</td>
      <td>Sony</td>
    </tr>
    <tr>
      <th>557</th>
      <td>Despicable Me 2</td>
      <td>899216835</td>
      <td>Uni.</td>
    </tr>
    <tr>
      <th>715</th>
      <td>Transformers: Age of Extinction</td>
      <td>894039076</td>
      <td>Par.</td>
    </tr>
    <tr>
      <th>971</th>
      <td>Captain America: Civil War</td>
      <td>890069413</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>1121</th>
      <td>Jumanji: Welcome to the Jungle</td>
      <td>874496193</td>
      <td>Sony</td>
    </tr>
    <tr>
      <th>976</th>
      <td>Zootopia</td>
      <td>869429616</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Toy Story 3</td>
      <td>868879522</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>982</th>
      <td>Rogue One: A Star Wars Story</td>
      <td>849102856</td>
      <td>BV</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the top 10 most profitable movies
plt.figure(figsize=(12, 6))
sns.set_style('white')
sns.barplot(x='gross_profit', y='title', orient='h', hue='studio', data=top20_profitable_movies, palette='muted', dodge=False)
plt.xlabel('Gross Profit (Billions)', fontweight='bold')
plt.ylabel('Movie Titles', fontweight='bold')
plt.title('Top 20 Most Profitable Movies', fontsize=15, fontweight='bold')
plt.xticks(rotation=45, ha='right')
plt.legend(title='Studio', loc='upper right', bbox_to_anchor=(1.15, 1.0))
plt.tight_layout()
plt.grid(axis='x')

plt.show()
```


    
![png](student_files/student_127_0.png)
    



```python
# Find the number of movies both in top20_worldwide_earners and top20_profitable_movies
common_movies = pd.merge(top20_worldwide_earners, top20_profitable_movies, on='title')
num_common_movies = len(common_movies)

print("Number of movies in both top20_worldwide_earners and top_20_profitable_movies:", num_common_movies)
```

    Number of movies in both top20_worldwide_earners and top_20_profitable_movies: 17
    

## Production Budget: Average Budget per Studio

On average Paramount DreamWorks (P/DW) allocated the highest budget towards their projects. While they have not been the top highest-earning or most profitable studio they have been consistently in the top 10.

Walt Disney, the highest and most profitable studio within the period on average allocates the second highest budget towards its projects, followed thereon by WB and Fox who have also consistently been among the highest earners and the most profitable.


```python
# Average production budget per studio

# Group by 'studio' and calculate the average production budget
avg_prod_budget_per_studio = earnings_df_filtered.groupby('studio')['production_budget'].mean().reset_index()\
                            .sort_values(by='production_budget', ascending=False)

# Rename the column for clarity
avg_prod_budget_per_studio.columns = ['studio', 'average_production_budget']

avg_prod_budget_per_studio.head(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>average_production_budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>55</th>
      <td>P/DW</td>
      <td>1.334000e+08</td>
    </tr>
    <tr>
      <th>14</th>
      <td>BV</td>
      <td>1.296182e+08</td>
    </tr>
    <tr>
      <th>82</th>
      <td>WB</td>
      <td>8.770588e+07</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Fox</td>
      <td>7.139806e+07</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Sony</td>
      <td>7.022308e+07</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Par.</td>
      <td>6.444928e+07</td>
    </tr>
    <tr>
      <th>83</th>
      <td>WB (NL)</td>
      <td>5.970286e+07</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Uni.</td>
      <td>5.806857e+07</td>
    </tr>
    <tr>
      <th>40</th>
      <td>LG/S</td>
      <td>5.518000e+07</td>
    </tr>
    <tr>
      <th>43</th>
      <td>MBox</td>
      <td>4.630000e+07</td>
    </tr>
    <tr>
      <th>44</th>
      <td>MGM</td>
      <td>4.300000e+07</td>
    </tr>
    <tr>
      <th>23</th>
      <td>ENTMP</td>
      <td>4.000000e+07</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Sum.</td>
      <td>3.634545e+07</td>
    </tr>
    <tr>
      <th>68</th>
      <td>STX</td>
      <td>3.565000e+07</td>
    </tr>
    <tr>
      <th>41</th>
      <td>LGF</td>
      <td>3.551614e+07</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Free</td>
      <td>3.416667e+07</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Studio 8</td>
      <td>3.000000e+07</td>
    </tr>
    <tr>
      <th>34</th>
      <td>GrtIndia</td>
      <td>3.000000e+07</td>
    </tr>
    <tr>
      <th>74</th>
      <td>TriS</td>
      <td>2.917500e+07</td>
    </tr>
    <tr>
      <th>26</th>
      <td>FD</td>
      <td>2.913889e+07</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot average production budget per year
plt.figure(figsize=(12, 8))
sns.set_style('white')
sns.barplot(data=avg_prod_budget_per_studio.head(20), x='studio', y='average_production_budget', palette='viridis')
plt.xlabel('Studio', fontweight='bold')
plt.ylabel('Average Production Budget (Hundred Millions)', fontweight='bold')
plt.title('Top 20 Studios: Average Production Budget', fontsize=15, fontweight='bold')
plt.xticks(rotation=90)
plt.grid(axis='y')

plt.show()
```


    
![png](student_files/student_132_0.png)
    


## Production Budget: Top 20 Studios with the Highest Budgets

Walt Disney, being the highest-earning studio and the most profitable is also the studio with the highest budget. We observe that the top 20 most profitable studios are also the same top 2 studios with the highest budget in the same order.


```python
#Find the studios with the highest production budgets
studio_prod_budgets = earnings_df_filtered.groupby('studio')['production_budget'].sum()
top20_studios_budgets = studio_prod_budgets.nlargest(20).reset_index()

top20_studios_budgets
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>studio</th>
      <th>production_budget</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BV</td>
      <td>8554800000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>WB</td>
      <td>7455000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fox</td>
      <td>7354000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Uni.</td>
      <td>6097200000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sony</td>
      <td>4564500000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Par.</td>
      <td>4447000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>WB (NL)</td>
      <td>2089600000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>LGF</td>
      <td>1953387650</td>
    </tr>
    <tr>
      <th>8</th>
      <td>LG/S</td>
      <td>1379500000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>P/DW</td>
      <td>1334000000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Focus</td>
      <td>764500000</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Wein.</td>
      <td>730500000</td>
    </tr>
    <tr>
      <th>12</th>
      <td>SGem</td>
      <td>727500000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Rela.</td>
      <td>641500000</td>
    </tr>
    <tr>
      <th>14</th>
      <td>ORF</td>
      <td>504200000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>STX</td>
      <td>499100000</td>
    </tr>
    <tr>
      <th>16</th>
      <td>TriS</td>
      <td>466800000</td>
    </tr>
    <tr>
      <th>17</th>
      <td>FoxS</td>
      <td>420410000</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sum.</td>
      <td>399800000</td>
    </tr>
    <tr>
      <th>19</th>
      <td>FD</td>
      <td>262250000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the top 10 studios with the highest production budgets
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x='production_budget', y='studio', data=top20_studios_budgets)
plt.title('Top 20 Studios with Highest Production Budgets', fontsize=15, fontweight='bold')
plt.xlabel('Production Budget (Billions)', fontweight='bold')
plt.ylabel('Studio', fontweight='bold')
plt.grid(axis='x')

plt.show()
```


    
![png](student_files/student_136_0.png)
    



```python
# Check find the number of studios in with the highest profit which are also the most profitable
common_studios = pd.merge(top20_studios_budgets, top20_studios_profit, on='studio')
num_common_studios = len(common_studios)

print("Number of studios in top20_studios_budgets that are also in top20_studios_profit:", num_common_studios)
```

    Number of studios in top20_studios_budgets that are also in top20_studios_profit: 10
    

## Production Budget: Movies with the Largest Budget


Only 4 of the movies with the highest production budget are among the top 20 most profitable movies out of which 3 are produced by Walt Disney and the other by Universal Studios.

From the top 20 movies with the largest production budgets, Walt Disney's movies make up 45%, followed thereafter by Warner Bros. productions at 20%, Warner Bros. Netherlands at 15% and the other spots occupied by Sony and Universal Studio's movies.


```python
# Get the top 10 movies with the largest production budgets
movies_largest_budget = earnings_df_filtered.nlargest(20, 'production_budget')\
                        [["title", "production_budget", "studio"]]

movies_largest_budget.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>production_budget</th>
      <th>studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>191</th>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>410600000</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>830</th>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>BV</td>
    </tr>
    <tr>
      <th>832</th>
      <td>Spectre</td>
      <td>300000000</td>
      <td>Sony</td>
    </tr>
    <tr>
      <th>1103</th>
      <td>Justice League</td>
      <td>300000000</td>
      <td>WB</td>
    </tr>
    <tr>
      <th>1191</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>BV</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the top 20 movies with the largest production budgets
plt.figure(figsize=(12, 7))
sns.set_style('white')
sns.barplot(data=movies_largest_budget, x='production_budget', y='title', orient='h', hue='studio', dodge=False)
plt.xlabel('Movie Title', fontweight='bold')
plt.ylabel('Production Budget (Hundred Millions)', fontweight='bold')
plt.title('Top 20:Movies with the Largest Production Budgets', fontsize=15, fontweight='bold')
plt.grid(axis='x')
plt.tight_layout()

plt.show()
```


    
![png](student_files/student_141_0.png)
    


## Gross Profit & Production Budget

The data infers that the average gross profit earned increases within budget.

We observe that movies in the low-budget category have brought in the lowest aggregate gross profit, followed thereon by the medium and high-budget categories. The high-budget category on average has earned over 3 times the gross profit that the low-budget category has earned.

The very high budget category far surpasses the rest in earnings as movies within this category on average bring in over USD 22.5 million above the high budget category. Over and above the budget various other factors would result in such blockbuster films earning enormous profits which include the storyline, actors, and marketing, among others.



```python
# Categorize the movies into budget categories
production_budget_quantiles = earnings_df_filtered['production_budget'].quantile([0.25, 0.5, 0.75])
low_budget = production_budget_quantiles[0.25]
medium_budget = production_budget_quantiles[0.5]
high_budget = production_budget_quantiles[0.75]


# Group the data by budget category
budget_category = pd.cut(earnings_df_filtered['production_budget'],
                         bins=[0, low_budget, medium_budget, high_budget, float('inf')],
                         labels=['Low Budget', 'Medium Budget', 'High Budget', 'Very High Budget'])

# Calculate the average gross profit for each budget category
average_gross_profit_by_budget = earnings_df_filtered.groupby(budget_category)['gross_profit'].mean().reset_index()

average_gross_profit_by_budget
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production_budget</th>
      <th>gross_profit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Low Budget</td>
      <td>2.150830e+07</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Medium Budget</td>
      <td>4.363772e+07</td>
    </tr>
    <tr>
      <th>2</th>
      <td>High Budget</td>
      <td>7.850167e+07</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Very High Budget</td>
      <td>3.010253e+08</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the average gross profit for each budget category
plt.figure(figsize=(10, 6))
sns.set_style('white')
sns.barplot(x='production_budget', y='gross_profit', data=average_gross_profit_by_budget)
plt.xlabel('Budget Category', fontweight='bold')
plt.ylabel('Average Gross Profit (Hundred Millions)', fontweight='bold')
plt.title('Average Gross Profit by Budget Category', fontsize=15, fontweight='bold')
plt.grid(axis='y')

plt.show()
```


    
![png](student_files/student_145_0.png)
    


## Analyzing Sequels

### Define the Function to Find Similar Movie Titles


```python
# Define the function to find similar movie titles
def find_similar_movies(title, all_titles):
    similar_movies = process.extract(title, all_titles, limit=5)
    return [movie[0] for movie in similar_movies]
```


```python
title_to_search = 'Trek'
all_movie_titles = earnings_df_filtered['title'].unique()

similar_movies = find_similar_movies(title_to_search, all_movie_titles)
print(similar_movies)
```

    ['Star Trek Into Darkness', 'Star Trek Beyond', 'Spectre', 'Shrek Forever After', 'True Grit']
    

### Comparing the Gross Profit

All sequel releases are very profitable and are a great way for studios to earn profits by further developing existing productions. The performance between earlier and later releases varies from movie to movie. The contributors to why this may be difficult to determine from the data available but may be attributable to the success of the preceding movie, script, and marketing.


```python
# Create a list of movie titles and dataframes for each movie
movies_data = {
    'Avengers': earnings_df_filtered[earnings_df_filtered['title'].isin(['Avengers: Age of Ultron', 'Avengers: Infinity War'])],
    'Jurassic World': earnings_df_filtered[earnings_df_filtered['title'].isin(['Jurassic World', 'Jurassic World: Fallen Kingdom'])],
    'Fast & Furious': earnings_df_filtered[earnings_df_filtered['title'].isin(['Furious 7', 'The Fate of the Furious', 'Fast Five'])],
    'Iron Man': earnings_df_filtered[earnings_df_filtered['title'].isin(['Iron Man 2', 'Iron Man 3'])],
    'Despicable Me': earnings_df_filtered[earnings_df_filtered['title'].isin(['Despicable Me', 'Despicable Me 2', 'Despicable Me 3'])],
    'Transformers': earnings_df_filtered[earnings_df_filtered['title'].isin(['Transformers: Dark of the Moon', 'Transformers: Age of Extinction', 'Transformers: The Last Knight'])],
    'Captain America': earnings_df_filtered[earnings_df_filtered['title'].isin(['Captain America: The First Avenger', 'Captain America: The Winter Soldier', 'Captain America: Civil War'])],
    'Star Wars': earnings_df_filtered[earnings_df_filtered['title'].isin(['Rogue One: A Star Wars Story', 'Solo: A Star Wars Story'])],
    'Pirates of the Caribbean': earnings_df_filtered[earnings_df_filtered['title'].isin(['Pirates of the Caribbean: On Stranger Tides', 'Pirates of the Caribbean: Dead Men Tell No Tales'])]
}

# Concatenate all movie data into a single dataframe
all_movies_data = pd.concat(movies_data.values(), ignore_index=True)
```


```python
# Plot the gross profit earned for the selected movies
sns.set(font_scale=3.5)
sns.set_style('white')
plt.figure(figsize=(35, 35))

ax = sns.barplot(x='gross_profit', y='title', data=all_movies_data, orient='h', hue='title', palette='Set1', dodge=False)
plt.xlabel('Gross Profit (Billions)', fontsize=50, fontweight='bold')
plt.ylabel('Movie Title', fontsize=50, fontweight='bold')
plt.title('Gross Profit Comparison of Selected Sequels', fontsize=55, fontweight='bold', pad=20)
plt.grid(axis='x', color='grey', linestyle='--', linewidth=0.5)
plt.legend([],[], frameon=False)

ax2 = ax.twinx()

for idx, movie_data in all_movies_data.iterrows():
    release_year = movie_data['year']
    ax2.vlines(movie_data['gross_profit'], idx - 0.4, idx + 0.4, linestyle='--')
    ax2.text(movie_data['gross_profit'], idx, str(release_year), va='center', ha='left', color='black', fontsize=40)

ax2.set_ylim(ax.get_ylim())

ax2.set_yticklabels([])

plt.show()
```


    
![png](student_files/student_152_0.png)
    



```python
# Read the compressed CSV data files into a data frame.
imbd_tite_basics_df = pd.read_csv('title.basics.csv')
tmdb_movies_df = pd.read_csv('tmdb.movies.csv', index_col=0)
tn_movie_budgets_df = pd.read_csv('tn.movie_budgets.csv')
bom_gross_earnings_df = pd.read_csv('bom.movie_gross.csv')
```


```python
bom_gross_earnings_df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>title</th>
      <th>studio</th>
      <th>domestic_gross</th>
      <th>foreign_gross</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Toy Story 3</td>
      <td>BV</td>
      <td>415000000.0</td>
      <td>652000000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alice in Wonderland (2010)</td>
      <td>BV</td>
      <td>334200000.0</td>
      <td>691300000</td>
      <td>2010</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Harry Potter and the Deathly Hallows Part 1</td>
      <td>WB</td>
      <td>296000000.0</td>
      <td>664300000</td>
      <td>2010</td>
    </tr>
  </tbody>
</table>
</div>




```python
tn_movie_budgets_df.head(3)
```


```python
tmdb_movies_df.head(3)
```


```python
imbd_tite_basics_df.head(3)
```


```python
# Merge earnings_df_filtered and tmdb_movies_df on the 'title' column
genres_df = earnings_df_filtered.merge(tmdb_movies_df[['title', 'popularity', 'vote_average', 'vote_count']], on='title', how='inner')


genres_df.head(3)
```


```python

```


```python

```


```python
imbd_tite_basics_df.info(3)
```

## Conclusions

The movie industry is highly lucrative, earning the major studios hundreds of millions of dollars in profit. Despite the downward trend in the number of movie releases, the future of the industry remains promising as earnings continue to skyrocket.

The industry is currently dominated by 10 major studios whose movies are consistently among the highest-earning and most profitable. A portion of the success of these studios can be attributed to their ability to turn already existing material such as previous movies, comic books and Booka into franchise films as well as make multiple sequels from their original creations enabling them to squeeze every last drop in earnings from those storylines.

The leading studios in the industry have done their homework, majorly focusing their productions of the most lucrative genres, Action, Adventure, Sci-Fi, Animation, and Comedy - a recipe for success. Additionally putting major focus on sequels and franchise productions enabling to capitalize on the development of one storyline and character development to keep movie goers in their seats.

The international market is critical for the success of any major film studio and thus must not be neglected.

The impact of the Covid-19 pandemic through 2020 and 2021 was that numerous films have had their theatrical releases canceled worldwide, resulting in an alternative announcement pattern with delayed releases. In the US film industry, more than 155 films were delayed to 2020 and produced in 2021 or 2022.

Across the world, and to varying degrees, cinemas and movie theaters were closed, festivals were cancelled or postponed, and film releases were moved to future dates or delayed indefinitely. Due to cinemas and movie theaters closing, the global box office dropped by billions of dollars, streaming saw a significant increase in popularity, and the stock of film exhibitors dropped dramatically. This in turn created openings for independent cinema productions to receive wider exposure.

## Business Recommendations

1. Alternative investment options: It may be a feasible alternative for Microsoft to invest in one of the more successful studios in the business because it is significantly less risky and guarantees Microsoft a fair return on investment. Considering potential partnerships, marketing strategies, and distribution channels to ensure film and financial success.

2. Create Long-Lasting Storylines: Franchise films such as comic book adaptations, sci-fi, and fantasy films have become increasingly profitable over the years and have contributed significantly to the success of other major studios.

3. Industry and Competitor Analysis: Given the dominance of major studios like Walt Disney, Universal Studios, Warner Bros., Sony, and Fox, a thorough analysis of the industry and competitors is important. Their sustained success serves as a reminder of how difficult it is to compete in the market.

4. International Market Focus: The success of large studios like Disney has been influenced by the international market. Success can be increased overall by considering global audiences and creating material that suits their tastes.

5. Create film genres, themes, and formats that align with market demand and audience preferences. By buying rights to popular book titles, introducing international stories, remaking great foreign films, and tapping into otherwise unexplored cultures like African, Arab, and indeginous/first people stories.

6. Audience Segmentation: Segment the movie-going audience based on demographics, psychographics, and viewing habits. Understand the unique preferences and demands of different audience segments. Explore potential synergies between Microsoft's existing customer base and film preferences.

## Recommendations for Future Analysis

1. Demographic Trends in Film Preferences:
Explore how age, gender, and geographic location influence audience preferences for film genres, themes, and content. This analysis can help in tailoring marketing strategies and content creation.

2. Economic Factors and Box Office Performance:
Investigate how economic indicators such as GDP, inflation, and consumer spending impact box office performance. Understanding the economic context can assist in releasing timing and pricing strategies.

3. Influence of Awards and Critical Acclaim:
Analyze the relationship between film awards (e.g., Oscars, Cannes) and a film's box office success. Determine if critical acclaim and awards influence audience choices.

4. Impact of Directors and Filmmakers:
Investigate how the reputation and record of directors, producers, and writers affect a film's success. Identify which filmmakers have a strong influence on box office performance.

5. Marketing and Promotion Strategies:
Analyze the correlation between marketing budgets, promotional strategies, and a film's box office success. Determine the most effective marketing tactics.

6. Impact of social media and Online Communities:
Examine the influence of social media platforms and online film communities on a film's success. Analyze the relationship between social media engagement and box office performance.

7. Technology and Production Quality:
Investigate how advancements in technology, such as special effects and production quality, contribute to a film's success. Determine audience expectations regarding production value.

8. Adoption of Sustainable Practices in Filmmaking:
Explore how the adoption of environmentally friendly and sustainable practices in film production impacts audience perception and success, especially in the era of increasing environmental awareness.

9. Streaming Service Original Content Strategy:
Analyze the strategies employed by streaming services in creating and promoting original content. Understand how these strategies compare to traditional studio production.


