# Phase 1 Project

# Objectives
    -Use data analytics skills acquired to identify the top 10 movies in terms of ratings, number of viewers and total revenue
    -Use visualization techniques to present the above in graphs
    -Recommend the best genre of movie in which to venture into

# Your code here - remember to use markdown cells for comments as well!
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns

#lets open and inspect our data files
dfbom = pd.read_csv('/Users/user/Phase1_Project/Movie_Analysis_Microsoft/dsc-phase-1-project/zippedData/bom.movie_gross.csv')
dfbom

dftitlebasics = pd.read_csv("/Users/user/Phase1_Project/Movie_Analysis_Microsoft/dsc-phase-1-project/zippedData/imdb.title.basics.csv.gz")
dftitlebasics

dftitleratings = pd.read_csv('/Users/user/Phase1_Project/Movie_Analysis_Microsoft/dsc-phase-1-project/zippedData/imdb.title.ratings.csv.gz')
dftitleratings

#checking missing values on dfbom
dfbom.isnull().sum()

dfbom.describe()

#replacing missing values with the mean
dfbom['domestic_gross'].fillna(dfbom['domestic_gross'].mean(), inplace= True)

dfbom.isnull().sum()

dfbom.info()

#we want to add a column that sums up the foreign gross and the domestic gross to a new column called total gross
#but first we have to cnvert the column foreign gross into a float to succed with the above step
dfbom['foreign_gross'] = dfbom['foreign_gross'].str.replace(",","").apply(float)

dfbom['foreign_gross'].dtype

dfbom["total_gross"] = dfbom["domestic_gross"]+dfbom["foreign_gross"]
dfbom

#for our second data set we check missing values first
dftitleratings.isnull().sum()

#we will also check for duplicates
dftitleratings.duplicated().value_counts()

dftitleratings.mean()

# add a movie name column in the ratigs table from the title basics table
extracted_col = dftitlebasics["primary_title"]
display(extracted_col)

titleratings_df = dftitleratings.join(extracted_col)
display(titleratings_df)

#on our new table titleratings_df, we'd like to add the domestic gross revenue to our data set
extracted_col2 = dfbom["total_gross"]
display(extracted_col2)

movie_data = titleratings_df.join(extracted_col2)
display(movie_data)

movie_data.isnull().sum()

movie_data.duplicated().value_counts()

# we'd like to replace the missing values in the total_gross column with the mean as below
movie_data['total_gross'] = movie_data['total_gross'].fillna(movie_data['total_gross'].mean())
display(movie_data)

movie_data.isnull().sum()

#we would also like to add a genre column to our movie_data file so as to have a complete data set
#first we begin by ectracting the column "genres" from our table titlebasics
extracted_col3 = dftitlebasics["genres"]

display(extracted_col3)

movie_data = movie_data.join(extracted_col3)

display(movie_data)

#checking for null values in our new data set
movie_data.isnull().sum()

#the next step would be to check the correlation between several variables
#first we will check the correlation between average rating and the number of votes.
# in this case we will assume that the number of voters also represent the number of views

print(movie_data['averagerating'].corr(movie_data['numvotes']))

#secondly we will check the correlation between the average rating and total gross earnings

print(movie_data['averagerating'].corr(movie_data['total_gross']))

#thirdly we will check the correlation between the number of views/votes and the total earnings
print(movie_data['numvotes'].corr(movie_data['total_gross']))


# first observation
from the above result, we can conclude that there's a weak positive correlation between 
the average rating and the number of votes. This is to say that an increase in the number of votes will not necessarily
increase the ratings of a particular movie.

# second observation
We have also observed that there's a very weak negative correlation 
between the average rating and the total gross earnings. This suggests that an increase in ratings
does not necessarily contribute to an increase in total earnings, rather the opposite might be true according
to the above result.

# third observation
with a correlation coefficient of 0.015, there is a very weak positive correlation between
the number of views and the total gross earnings. This translates to very minimal effect on the total gross earnings
with an increase or decrease in the number of viewers

#from the above three observations, we can conclude that these three variables do not affect each other significantly
#we will use the number of votes as our determinant on the success of a movie.
#we first start by sorting our data from the movie with the highest views to the lowest

movie_data = movie_data.sort_values(['numvotes'], ascending = False)
movie_data

#from the data set, we will now use our top 10 records to perform our analysis
movie_data_df = movie_data.head(10)
movie_data_df

#From the above table, we can observe that the top ten movies also have very high ratings out of 10.
#lets represent the above info on a graph for better visualization
#We will start by comparing the number of votes and the genre
fig, ax = plt.subplots(figsize=(10,5))
sns.barplot(x=movie_data_df['numvotes'],y=movie_data_df['genres'])

plt.xlabel('numvotes') 
plt.ylabel('genres') 
plt.title('Top Ten movie Genres')
plt.show()

# Conclusion
In conclusion, the visual representation above shows the top ten movie genres that the Microsoft team should venture into.
First is because of the relatively high ratings as compared to the number of viewers who voted for the movie with the least having a rating of 7.2.
Secondly, the above genres seem to be raking in high earnings both domestically and in foreign countries.

# Recommendations
Microsoft should focus more on the music genres movies since they contain the highest number of views and also rate very highly among the public.
In return, the music genre is one of the highest revenue generating genres and there's a business opportunity for Microsoft to tap into 
