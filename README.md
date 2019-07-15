# MORINGA PREP - WEEK 4 INDEPENDENT PROJECT
    In this week's independent project, you will be working as a data scientist working for an electric car-sharing service company. You have been tasked to process stations data to understand electric car usage over time by solving for the following research question; 

## RESEARCH QUESTIONS: 
 
      Identify the most popular hour of the day for picking up a shared electric car (Bluecar) in the city of 
      Paris over the month of April 2018
      What is the most popular hour for returning cars?
      What station is the most popular?
      Overall?
      At the most popular picking hour?
      What postal code is the most popular for picking up Bluecars? Does the most popular station 
       belong to that    postal code?
      Overall?
      At the most popular picking hour?
      Do the results change if you consider Utilib and Utilib 1.4 instead of Bluecars? (that could be asked earlier to 
      push students to write modular code that can be used to query different things)
      Your final deliverable will be a data report which will comprise the following sections;

## Import the packages and libraries
    import numpy as np
    import pandas as pd
    import pandas_profiling
    import matplotlib.pyplot as plt
    import plotly as py
    import seaborn as sns 
    import os

## reading the dataset url
    url = 'http://bit.ly/Autolibdataset'
    url

## Creating a SQL ENVIRONMENT TO LOAD HUGE DATASET
    import os
    import csv
    import sqlite3
    from sqlalchemy import create_engine

    csv_database = create_engine('sqlite:////csv_database.db')
    chunk_size = 1000000
    i = 0
    j = 0
    for df in pd.read_csv(url, chunksize = chunk_size, iterator = True):
        df = df.rename(columns = {c: c.replace(' ','') for c in df.columns})
        df.index += j

    df.to_sql('auto', csv_database, if_exists = 'append' )
    j = df.index[-1]+1

    print(' | index: {}'.format(j))

## retreaving csv file into our sql lite environment 
    auto = pd.read_sql_query('SELECT * 
                             from auto 
                             WHERE city == "Paris" ', csv_database)
                         
## Finding the size of the dataset 
    auto.tail()

## preview of our dataset
    auto.head()

## retreaving csv file into our sql lite environment 
    auto = pd.read_sql_query('SELECT * from auto WHERE city == "Paris" ', csv_database)

## FORMATTING THE Date_time column
    auto = pd.read_csv('auto_clean.csv', dtype = {'year':str,'month':str,'day':str,'hour':str,'minute':str })
    auto['date'] = auto.year.str.cat([auto.month,auto.day], sep = '/')
    auto['time'] = auto.hour.str.cat([auto.minute], sep = ':')
    auto['date_time'] = auto.date.str.cat([auto.time], sep = ' ')

## Dropping the year month day minute and time columns 
    auto.drop(['year','month', 'day', 'minute',  'date', 'time'],axis = 1, inplace = True)

## Recreating a new csv dataset
    auto.to_csv('auto_clean.csv')

## ANSWERING THE RESEARCH QUESTIONS 
## 1. What was the most popular hour of the day for picking up a shared electric car (Bluecar) 
## in the city of Paris over the month of April 2018?
    auto[auto.City == 'Paris']  #ensuring the city is Paris 


## 2. What is the most popular postal code per hour for returning blue cars? 78005 most popular with 14419 units of cars 
    auto.groupby(['Postalcode',auto.date_time.dt.hour,'Bluecarcounter'])['Bluecarcounter'].count().sort_values(ascending = False).head(20)

## 3. Most Popular Station - for picking blue cars  
    auto.groupby(['Publicname','Bluecarcounter'])['Publicname'].count().sort_values(ascending = False).head(20)

## 4. Most Popular Station at the most popular picking hour for blue cars 
    auto.groupby(['Publicname',auto.date_time.dt.hour,'Utilibcounter'])['Utilibcounter'].count().sort_values(ascending = False).head()

## 5. Most Popular Station at the most popular picking hour for Utilib_counter 
    auto.groupby('public_name',auto.date.dt.hour.['utilib_counter'],['utilib_counter'].count().sort_values(ascending = False)
