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

5.59 million results! Looks like it shouldn't be too hard to find data. The first link is for ESPN, a trustworthy source for sports data. Let's see how it looks:

![ESPN table](https://raw.githubusercontent.com/jdradandt/stat386-projects/main/assets/images/Screen%20Shot%202022-10-18%20at%207.55.40%20PM.png)

This looks great. It has info on home games, road games, and overall numbers for each team, and there are multiple years of data. This should be perfect for our analysis.

### Make sure data can legally be used
We need to make sure that it is okay for this data to be scraped from ESPN. I did some previous research and ESPN fully supports scraping data from it's webpage. In fact, ESPN's API can be found [here](http://www.espn.com/apis/devcenter/docs/) if you want to get some more data for yourself.

### Get the data into a dataframe
Now we get to 

