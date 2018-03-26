
<a id='top_cell'></a>

<h2>Distinguishing Sentiments - News Mood Analysis</h2>
<blockquote><p>For this project, I created a Python script to perform a sentiment analysis of the Twitter activity of various news outlets and present my findings visually. To accomplish this, I utilized the [Tweepy API](http://docs.tweepy.org/en/v3.5.0/) and [Vader library](https://pypi.python.org/pypi/vaderSentiment). </p></blockquote>

<strong>Analysis and Limitations</strong>
<ul>
<li>From the [Average Sentiment Data](#another_cell), I can see that a NYTimes had the most negative score while CBS has the most positive score.</li>
<li>From the [Bar Graph](#bar_cell), it appears that Fox News has the lowest compound sentiment and CBS has the highest. This matches my previous statement about the highest and negative scores. CNN has a score closest to zero, so it is the news outlet with the closest neutral sentiment. </li>
<li>From the [Sentiment Analysis scatterplot](#scatter_cell), it appears that CBS only had a few negative tweets (a score less than zero). There aren't any visible patterns on if the most positive or negative tweets happened 100 tweets ago or 1 tweet ago. </li>
<li>The validity of Vader analysis is questionable. There are negative events that have neutral Vader scores instead of negative scores. </li>
<li>The Vader analysis on news organizations depends on the news that happens on that particular day. A major news story might skew the results on a day. A more accurate analysis would occur over a larger period of time, such as a year. </li>
</ul>

<strong><p>News Outlets:</p></strong>
<ul>
<li>[BBC](https://twitter.com/BBC)</li>
<li>[CBS](https://twitter.com/CBS)</li>
<li>[CNN](https://twitter.com/CNN)</li>
<li>[Fox](https://twitter.com/FoxNews)</li>
<li>[New York Times](https://twitter.com/nytimes)</li>
</ul>

<strong><p>Additional Resources:</p></strong>
<ul>
<li>[Vader](https://pypi.python.org/pypi/vaderSentiment)</li>
<li>[Tweepy](http://docs.tweepy.org/en/v3.5.0/)</li>
</ul>


```python
# Dependencies
import tweepy
import json
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import time
import datetime as dt
# Import and Initialize Sentiment Analyzer
#pip install vaderSentiment==2.5
# from vaderSentiment.vaderSentment import SentimentIntensityAnalyzer
# analyzer = SentimentIntensityAnalyzer()
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer 
analyzer = SentimentIntensityAnalyzer()
# Twitter API Keys
consumer_key = 'SROxajTijhocLMb7r2YTWiaYn'
consumer_secret = 'lZ2BJSbJ0K7rxFOn1GHqKawQtCqyLfvKp8mizeNU3HiKNXQ683'
access_token = '975021481628438528-TxgPO0geJ2lobpUUztEr4zsf4yerD4p'
access_token_secret = 'TqY7bnmOhxOezrTasLxWnekKI9HfPfujkxOudopckr8ZS'
```


```python
# Setup Tweepy API Authentication
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth, parser=tweepy.parsers.JSONParser())
```


```python
bbc_scores = pd.DataFrame({})
```

<a id='another_cell'></a>


```python
# Target User Accounts
target_user = ("@BBC", "@CBS", "@CNN", "@FoxNews", "@nytimes")
# Variables for holding sentiments
compound_list = []
positive_list = []
negative_list = []
neutral_list = []
tweet_list=[]
sentiments = []
source_list = []
date_list = []
counter = 1
# Loop through each user
for user in target_user:
    # Loop through 5 pages of tweets (total 100 tweets)
    for x in range(5):
        # Get all tweets from home feed
        public_tweets = api.user_timeline(user)
        # Loop through all tweets
        for tweet in public_tweets:
            # Run Vader Analysis on each tweet
            compound = analyzer.polarity_scores(tweet["text"])["compound"]
            pos = analyzer.polarity_scores(tweet["text"])["pos"]
            neu = analyzer.polarity_scores(tweet["text"])["neu"]
            neg = analyzer.polarity_scores(tweet["text"])["neg"]
            tweets = tweet["text"]
            tweets_ago = counter
            createddt = tweet["created_at"]
            # Add each value to the appropriate array
            source_list.append(user)
            compound_list.append(compound)
            positive_list.append(pos)
            negative_list.append(neg)
            neutral_list.append(neu)
            tweet_list.append(tweet)
            date_list.append(createddt)
            
            sentiments.append({"Account": user,
                              "Created Date": createddt,
                              #"Date": tweet["created_at"], 
                              "Compound": compound,
                              "Positive": pos,
                              "Negative": neg,
                              "Neutral": neu,
                              "Tweets Ago": tweets_ago,
                              "Tweet Text": tweets})
            counter = counter + 1
 
 #Commented out printing the lists because the output is large.
#If you need the print, uncomment the code.
#     print("User: %s" % user)
#     print("Compound: %s" % positive_list)
#     print("Positive: %s" % positive_list)
#     print("Neutral: %s" % neutral_list)
#     print("Negative: %s" % negative_list)
#     print(tweet_list)
#Print the Averages - this was printed as a sanity check that the above code works.
    print("")
    print("User: %s" % user)
    print("Compound: %s" % np.mean(positive_list))
    print("Positive: %s" % np.mean(positive_list))
    print("Neutral: %s" % np.mean(neutral_list))
    print("Negative: %s" % np.mean(negative_list))
```

    
    User: @BBC
    Compound: 0.06825
    Positive: 0.06825
    Neutral: 0.88
    Negative: 0.0517
    
    User: @CBS
    Compound: 0.11215
    Positive: 0.11215
    Neutral: 0.854125
    Negative: 0.033675
    
    User: @CNN
    Compound: 0.100216666667
    Positive: 0.100216666667
    Neutral: 0.85055
    Negative: 0.0492
    
    User: @FoxNews
    Compound: 0.090275
    Positive: 0.090275
    Neutral: 0.8366
    Negative: 0.0731
    
    User: @nytimes
    Compound: 0.08274
    Positive: 0.08274
    Neutral: 0.83837
    Negative: 0.07886
    


```python
sentiments_df = pd.DataFrame(sentiments)
sentiments_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Account</th>
      <th>Compound</th>
      <th>Created Date</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>@BBC</td>
      <td>0.1280</td>
      <td>Sun Mar 25 19:44:01 +0000 2018</td>
      <td>0.077</td>
      <td>0.821</td>
      <td>0.101</td>
      <td>Could this be an answer to global water shorta...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>@BBC</td>
      <td>-0.7506</td>
      <td>Sun Mar 25 19:15:06 +0000 2018</td>
      <td>0.286</td>
      <td>0.714</td>
      <td>0.000</td>
      <td>Tonight, @regyates meets people whose lives ha...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>@BBC</td>
      <td>0.5719</td>
      <td>Sun Mar 25 18:40:04 +0000 2018</td>
      <td>0.000</td>
      <td>0.837</td>
      <td>0.163</td>
      <td>Tonight, @mcgregor_ewan and @McgColin celebrat...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>@BBC</td>
      <td>0.0000</td>
      <td>Sun Mar 25 18:13:03 +0000 2018</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The first ever statue of David Bowie has been ...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>@BBC</td>
      <td>0.5267</td>
      <td>Sun Mar 25 17:30:07 +0000 2018</td>
      <td>0.000</td>
      <td>0.815</td>
      <td>0.185</td>
      <td>When you're enjoying being single and people j...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Export dataframe into csv.
sentiments_df.to_csv("sentiments.csv")
```


```python
#Read the csv so that I don't have to run the API again for the data.
# data_file = "sentiments.csv"
# sentiments_csv = pd.read_csv(data_file)
#sentiments_csv.head
```

<h4>Plots</h4>


```python
#Extract a dataframe for each agency.
nytimes_df = sentiments_df[sentiments_df["Account"] == "@nytimes"]
nytimes_df.head()


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Account</th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>400</th>
      <td>@nytimes</td>
      <td>-0.0772</td>
      <td>0.071</td>
      <td>0.929</td>
      <td>0.000</td>
      <td>RT @nickconfessore: Facebook took out ads in t...</td>
      <td>401</td>
    </tr>
    <tr>
      <th>401</th>
      <td>@nytimes</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>Qantas Airways made a giant leap forward in lo...</td>
      <td>402</td>
    </tr>
    <tr>
      <th>402</th>
      <td>@nytimes</td>
      <td>0.0258</td>
      <td>0.109</td>
      <td>0.778</td>
      <td>0.113</td>
      <td>“I hope in the future for some form of reconci...</td>
      <td>403</td>
    </tr>
    <tr>
      <th>403</th>
      <td>@nytimes</td>
      <td>-0.5267</td>
      <td>0.186</td>
      <td>0.750</td>
      <td>0.064</td>
      <td>RT @nytopinion: Even if trans issues don’t top...</td>
      <td>404</td>
    </tr>
    <tr>
      <th>404</th>
      <td>@nytimes</td>
      <td>0.7506</td>
      <td>0.000</td>
      <td>0.670</td>
      <td>0.330</td>
      <td>“It’s true — Jared has been a positive influen...</td>
      <td>405</td>
    </tr>
  </tbody>
</table>
</div>




```python
cbs_df = sentiments_df[sentiments_df["Account"] == "@CBS"]
cbs_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Account</th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>100</th>
      <td>@CBS</td>
      <td>0.5319</td>
      <td>0.062</td>
      <td>0.780</td>
      <td>0.157</td>
      <td>Don’t miss a minute of the action. Stream the ...</td>
      <td>101</td>
    </tr>
    <tr>
      <th>101</th>
      <td>@CBS</td>
      <td>0.5399</td>
      <td>0.000</td>
      <td>0.801</td>
      <td>0.199</td>
      <td>RT @MomCBS: That's a wrap on the #Mom panel at...</td>
      <td>102</td>
    </tr>
    <tr>
      <th>102</th>
      <td>@CBS</td>
      <td>-0.3400</td>
      <td>0.185</td>
      <td>0.700</td>
      <td>0.115</td>
      <td>RT @MomCBS: A fan just commented that #Mom hel...</td>
      <td>103</td>
    </tr>
    <tr>
      <th>103</th>
      <td>@CBS</td>
      <td>0.7003</td>
      <td>0.000</td>
      <td>0.791</td>
      <td>0.209</td>
      <td>RT @MomCBS: "Go out for it anyway. If you're g...</td>
      <td>104</td>
    </tr>
    <tr>
      <th>104</th>
      <td>@CBS</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>RT @MomCBS: Mom Co-Creator @GemmaRBaker just p...</td>
      <td>105</td>
    </tr>
  </tbody>
</table>
</div>




```python
cnn_df = sentiments_df[sentiments_df["Account"] == "@CNN"]
cnn_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Account</th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>200</th>
      <td>@CNN</td>
      <td>0.5106</td>
      <td>0.000</td>
      <td>0.845</td>
      <td>0.155</td>
      <td>A driver's dramatic rescue from his SUV was ca...</td>
      <td>201</td>
    </tr>
    <tr>
      <th>201</th>
      <td>@CNN</td>
      <td>0.7469</td>
      <td>0.000</td>
      <td>0.586</td>
      <td>0.414</td>
      <td>The marching students wowed this security mom ...</td>
      <td>202</td>
    </tr>
    <tr>
      <th>202</th>
      <td>@CNN</td>
      <td>-0.4939</td>
      <td>0.225</td>
      <td>0.775</td>
      <td>0.000</td>
      <td>Why the Stormy Daniels interview should scare ...</td>
      <td>203</td>
    </tr>
    <tr>
      <th>203</th>
      <td>@CNN</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>A day after March for Our Lives, the Pope urge...</td>
      <td>204</td>
    </tr>
    <tr>
      <th>204</th>
      <td>@CNN</td>
      <td>-0.5267</td>
      <td>0.167</td>
      <td>0.833</td>
      <td>0.000</td>
      <td>"(President Trump) is either lying or he is co...</td>
      <td>205</td>
    </tr>
  </tbody>
</table>
</div>




```python
fox_df = sentiments_df[sentiments_df["Account"] == "@FoxNews"]
fox_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Account</th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>300</th>
      <td>@FoxNews</td>
      <td>0.8772</td>
      <td>0.000</td>
      <td>0.614</td>
      <td>0.386</td>
      <td>.@stevenmnuchin1: "This is a president that ab...</td>
      <td>301</td>
    </tr>
    <tr>
      <th>301</th>
      <td>@FoxNews</td>
      <td>-0.1531</td>
      <td>0.078</td>
      <td>0.922</td>
      <td>0.000</td>
      <td>.@DevinNunes: "We also found no collusion betw...</td>
      <td>302</td>
    </tr>
    <tr>
      <th>302</th>
      <td>@FoxNews</td>
      <td>0.0000</td>
      <td>0.000</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>On "Cavuto Live," @cvpayne gave his take on th...</td>
      <td>303</td>
    </tr>
    <tr>
      <th>303</th>
      <td>@FoxNews</td>
      <td>-0.6969</td>
      <td>0.200</td>
      <td>0.800</td>
      <td>0.000</td>
      <td>.@newtgingrich: "There are five cities in Amer...</td>
      <td>304</td>
    </tr>
    <tr>
      <th>304</th>
      <td>@FoxNews</td>
      <td>-0.6908</td>
      <td>0.251</td>
      <td>0.749</td>
      <td>0.000</td>
      <td>Drunk man steals beef jerky from 7-Eleven, bre...</td>
      <td>305</td>
    </tr>
  </tbody>
</table>
</div>




```python
bbc_df = sentiments_df[sentiments_df["Account"] == "@BBC"]
bbc_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Account</th>
      <th>Compound</th>
      <th>Negative</th>
      <th>Neutral</th>
      <th>Positive</th>
      <th>Tweet Text</th>
      <th>Tweets Ago</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>@BBC</td>
      <td>0.5719</td>
      <td>0.0</td>
      <td>0.837</td>
      <td>0.163</td>
      <td>Tonight, @mcgregor_ewan and @McgColin celebrat...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>@BBC</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>The first ever statue of David Bowie has been ...</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>@BBC</td>
      <td>0.5267</td>
      <td>0.0</td>
      <td>0.815</td>
      <td>0.185</td>
      <td>When you're enjoying being single and people j...</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>@BBC</td>
      <td>0.4588</td>
      <td>0.0</td>
      <td>0.833</td>
      <td>0.167</td>
      <td>🇺🇸🏝🇬🇧 Welcome to Tangier Island, the tiny US i...</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>@BBC</td>
      <td>0.0000</td>
      <td>0.0</td>
      <td>1.000</td>
      <td>0.000</td>
      <td>💬 We could listen to him speak all day. \n\n📽 ...</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create date format
date = dt.date.today
date = dt.datetime.today().strftime("%m/%d/%Y")
date
```




    '03/25/2018'



<a id='scatter_cell'></a>


```python
plt.scatter(x=nytimes_df["Tweets Ago"],
           y=nytimes_df["Compound"],
           marker='o', facecolors='grey', edgecolors="black",
           alpha=0.9, label="nytimes")

plt.scatter(x=cbs_df["Tweets Ago"],
           y=cbs_df["Compound"],
           marker='o', facecolors='orange', edgecolors="black",
           alpha=0.9, label="cbs")

plt.scatter(x=cnn_df["Tweets Ago"],
           y=cnn_df["Compound"],
           marker='o', facecolors='red', edgecolors="black",
           alpha=0.9, label="cbs")

plt.scatter(x=fox_df["Tweets Ago"],
           y=fox_df["Compound"],
           marker='o', facecolors='blue', edgecolors="black",
           alpha=0.9, label="cbs")

plt.scatter(x=bbc_df["Tweets Ago"],
           y=bbc_df["Compound"],
           marker='o', facecolors='green', edgecolors="black",
           alpha=0.9, label="cbs")
#plt.title("Sentiment Analysis of Tweets (%s) % (time.strftime("%x")))
plt.xlabel('Tweets Ago')
plt.ylabel('Tweet Polarity')
plt.title('Sentiment Analysis of Tweets ('+ date +")")
#plt.title("Sentiment Analysis of Tweets")
#plt.legend((a,b,c,d,e),('@BBC', '@CBS','@CNN', '@FoxNews', '@nytimes'),numpoints=1, loc='upper right', ncol=1, fontsize=8)
plt.legend(('@nytimes', '@CBS','@CNN',  '@FoxNews','@BBC'),numpoints=1, loc='upper right', ncol=1, fontsize=8,  title="Media Sources", fancybox=True)
lgd = plt.legend(('@nytimes', '@CBS','@CNN',  '@FoxNews','@BBC'),bbox_to_anchor=(1,1), title = "Media Sources")
plt.savefig('Sentiment_scatter_plot.png')
plt.show()
```


![png](output_18_0.png)



```python
#Store average compound scores into dataframe.
avgcompound = round(sentiments_df.groupby(["Account"]).mean()["Compound"],2)
avgcomp = pd.DataFrame(avgcompound)
avgcompound.head()
#avgcomp.head()
```




    Account
    @BBC        0.13
    @CBS        0.38
    @CNN        0.05
    @FoxNews   -0.11
    @nytimes    0.06
    Name: Compound, dtype: float64



<a id='bar_cell'></a>


```python
x_axis = np.arange(len(avgcompound))
xlabels = avgcompound.index
#xlabels = ["@BBC", "@CBS", "@CNN", "@FoxNews", "@nytimes"]
count = 0
for compound in avgcompound:
    plt.text(count, compound+0.1, str(round(compound,2)))
    count = count + 1
#avgcompound is a list of the the  averages of the compounds for each media agency
plt.bar(x_axis, avgcompound, tick_label = xlabels, color=["b", "silver", "r", "y", "c"], align="edge")
#tick_locations = [value+0.1 for value in x_axis]
#plt.xticks(tick_locations, avgcompound)
plt.xlim(-0.25, len(x_axis))
#plt.ylim(0, max(avgcompound)+0.4)
#plt.text('hi')
#plt.bar(x_axis, avgcompound, color='b', alpha=0.5, align="edge")
#plt.gca().invert_xaxis()
#plt.title("Overall Sentiment")
plt.title('Overall Media Sentiment based on Twitter ('+ date +")")
plt.xlabel("News Organization")
plt.ylabel("Tweet Polarity")
plt.savefig('Sentiment_barchart.png')
plt.show()
```


![png](output_21_0.png)


<div align="right">[Return to Top](#top_cell)</div>
<hr>

<h3>Testing</h3>


```python
#Simple Bar Graph
avgcomp.plot(kind='bar')
plt.title('Average Sentiment Score of News Organizations Last 100 Tweets ')
plt.xlabel('News Organization')
plt.ylabel('Average Sentiment Score')
#plt.savefig('Sentiment_AvgBar.png')
plt.tight_layout()
plt.show()
```


![png](output_24_0.png)



```python
# plt.scatter(compound_df["@BBC"],
#             compound_df["@CBS"])
x = np.arange(100,0,-1)
# a = plt.scatter(x, compound_df["@BBC"])
# b = plt.scatter(x, compound_df["@CBS"])
# c = plt.scatter(x, compound_df["@CNN"])
# d = plt.scatter(x, compound_df["@FoxNews"])
# e = plt.scatter(x, compound_df["@nytimes"])
#plt.title("Sentiment Analysis of All Tweets")

plt.plot(np.arange(len(compound_df["@BBC"])),
         compound_df["@BBC"], marker="o", linewidth=0.5, color = "red",
         alpha=0.8)

# plt.plot(np.arange(len(compound_df["@CBS"])),
#          compound_df["@CBS"], marker="o", linewidth=0.5, color = "orange",
#          alpha=0.8)

# # Incorporate the other graph properties
#plt.title("Sentiment Analysis of Tweets (%s) for %s" % (time.strftime("%x"), target_user))

#plt.ylabel("Wind Speed (mph)")
#plt.axvline(0, color = 'black', alpha = .25, label = 'Equator')
#plt.text(1,30,'Equator',rotation=90)
#plt.ylim(-5,40)
#plt.xlabel("Latitude")
#plt.xlim(-60,90)
plt.grid(True)
#plt.savefig("WeatherPyGraphs/LatitudeVsWindSpeed.png")
plt.show()
```


![png](output_25_0.png)



```python
# Target User Account - Used for testing purposes.
#Commented out this code and not displaying output because output is too long.
#If this needs to be used, uncomment it out.
# target_user = "@BBC"

# # Counter
# counter = 1

# # Loop through 5 pages of tweets (total 100 tweets)
# for x in range(5):

#     # Get all tweets from home feed
#     public_tweets = api.user_timeline(target_user)

#     # Loop through all tweets
#     for tweet in public_tweets:

#         # Utilize JSON dumps to generate a pretty-printed json
#         # print(json.dumps(
#         #     tweet, sort_keys=True, indent=4, separators=(',', ': ')))

#         # Print Tweets
#         print("Tweet %s: %s" % (counter, tweet["text"]))

#         # Add to Counter
#         counter = counter + 1
```


```python
# # Target User Account - used BBC for testing
# #Commented out this code and not displaying output because output is too long.
# #If this needs to be used, uncomment it out.
# target_user = "@BBC"

# # Counter
# counter = 1

# # Loop through 5 pages of tweets (total 100 tweets)
# for x in range(5):

#     # Get all tweets from home feed
#     public_tweets = api.user_timeline(target_user)

#     # Loop through all tweets
#     for tweet in public_tweets:

#         # Utilize JSON dumps to generate a pretty-printed json
#         # print(json.dumps(
#         #     tweet, sort_keys=True, indent=4, separators=(',', ': ')))
        
#         # Print Tweets
# #         print("Tweet %s: %s" % (counter, tweet["text"]))

# #         # Add to Counter
# #         counter = counter + 1
#         # Run analysis
#         compound = analyzer.polarity_scores(tweet["text"])["compound"]
#         pos = analyzer.polarity_scores(tweet["text"])["pos"]
#         neu = analyzer.polarity_scores(tweet["text"])["neu"]
#         neg = analyzer.polarity_scores(tweet["text"])["neg"]


#         # Print Analysis
#         print(tweet["text"])
#         print("Compound Score: %s" % compound)
#         print("Positive Score: %s" % pos)
#         print("Neutral Score: %s" % neu)
#         print("Negative Score: %s" % neg)

```

<div align="right">[Return to Top](#top_cell)</div>
