---
layout: post
title:  "Play Ball! Gathering Data for MLB Ballpark Attendance"
date:   2022-10-18
author: Jackson Radandt
description: A guide to gathering data on attendance at MLB stadiums from the web
image: /assets/images/mlb_stadium.jpeg
---

October is a great month for sports fans. Football season is in full swing, NBA and NHL games are starting, local high school and college games are ongoing and are great evening activities. But my favorite sports event of October is the MLB Playoffs. Playoff baseball is exciting, and if you watch an MLB playoff game, you can feel the energy that the home crowd brings, the "home field advantage". So which team has the best home field advantage? This post will explore how to go about gathering data to answer this question.

### Where to get the data
The first question we have to answer is where can we get this data? And will you be shocked when I tell you that I just did a Google search?

![Google search](https://raw.githubusercontent.com/jdradandt/stat386-projects/main/assets/images/Screen%20Shot%202022-10-18%20at%207.35.26%20PM.png)

5.59 million results! Looks like it shouldn't be too hard to find data. The first link is for ESPN, a trustworthy source for sports data. (See the webpage [here](https://www.espn.com/mlb/attendance/_/year/2022)) Let's see how it looks:

![ESPN table](https://raw.githubusercontent.com/jdradandt/stat386-projects/main/assets/images/Screen%20Shot%202022-10-18%20at%207.55.40%20PM.png)

This looks great. It has info on home games, road games, and overall numbers for each team, and there are multiple years of data. This should be perfect for our analysis.

### Make sure data can legally be used
We need to make sure that it is okay for this data to be scraped from ESPN. I did some previous research and ESPN fully supports scraping data from it's webpage. In fact, ESPN's API can be found [here](http://www.espn.com/apis/devcenter/docs/) if you want to get some more data for yourself.

### Get the data into a dataframe
Now we get to scrape the data and put it into a dataframe. First thing to do is import the packages we need into our Python enviornment.
```
import numpy as np
import pandas as pd
from functools import reduce
```
If we inspect the HTML of our webapage, we will see that each table is under a `<table>` tag. This means we can use `pd.read_html()` to read in this data. The next thing we need are the URLs for each year we want. Going through each table, we can see that the URL is `https://www.espn.com/mlb/attendance/_/year/` and then whatever year we want data for. We can create a list of these URLs using a `for` loop:
```
years = []
for i in range(2012,2023):
    years.append(str(i))
    
urls = []
for year in years:
    url = 'https://www.espn.com/mlb/attendance/_/year/'+year
    urls.append(url)
```
*Note: ESPN has data going back to 2001. We will use data from 2012-2022 because the Miami Marlins, the last baseball team to have a name change, changed in 2012.*

We are going to end up with a lot of tables that we will then combine into one dataframe. Let's look at one table to figure out how they will look:
```
url_test = 'https://www.espn.com/mlb/attendance/_/year/2012'
test_df = pd.read_html(url_test)
test_df[0].head()
```
The resulting dataframe looks like this:

![test dataframe](https://raw.githubusercontent.com/jdradandt/stat386-projects/main/assets/images/Screen%20Shot%202022-10-21%20at%202.43.10%20PM.png)

This data looks clean, the only thing I want to change are the column names. Because we will have multiple years in the dataframe, it would be nice if the columns were called YEAR_VARIABLE_LOCATION, where location is either home, road, or overall. We also don't care about the RK column. All this can be done in a nested `for` loop that will scrape each table, change the column names, then add them to a list of all the dataframes.

```
dfs = [] # create empty list to hold all the tables
i = 0 # iterator to iterate years list
loc = ['home', 'road', 'overall'] # labels of variables to add to headers
for url in urls:
    df = pd.read_html(url)[0] # store table from url
    new_header = df.iloc[1] #grab the second row for the header
    k = 0 # counter to put home, road, and overall in correct place 
    for j in range(2,len(new_header)): # loop to create correct headers
        if k == 0 or k == 1 or k == 2 or k == 3: # first 4 rows are home game data
            new_header[j] = years[i]+'_'+new_header[j]+'_'+loc[0]
        elif k == 4 or k == 5 or k == 6: # next 3 rows are road game data
            new_header[j] = years[i]+'_'+new_header[j]+'_'+loc[1]
        else: # rest of rows are overall data
            new_header[j] = years[i]+'_'+new_header[j]+'_'+loc[2]
        k = k + 1
    df = df[2:] #take the data less the header row
    df.columns = new_header # name columns with the new headers
    del df['RK'] # delete 'RK' column
    dfs.append(df) # add table to list
    i = i+1
```

Now let's check that the dataframes look like what we want.

```
dfs[0].head()
```

![new dataframe](https://raw.githubusercontent.com/jdradandt/stat386-projects/main/assets/images/Screen%20Shot%202022-10-21%20at%202.50.48%20PM.png)

This looks exactly how we want it to. Now we can combine all the dataframes into one by running the following lines:
```
ballparks = reduce(lambda  left,right: pd.merge(left,right,on=['TEAM'], how='outer'), dfs)
ballparks.drop(ballparks.tail(1).index,inplace=True) # drop last row that has no data
```

And that's it! We have gathered data from 2012-2022 for each MLB team, and cleaned it to make it easier to work with. Next blog post will be an EDA where we will perform some analysis on this data, check back in for it!

Check out the code and final dataset in [this repo](https://github.com/jdradandt/mlb_ballpark_analysis.git).
