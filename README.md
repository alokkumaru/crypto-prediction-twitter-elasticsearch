# crypto-prediction-twitter-elasticsearch
An elasticsearch based analyzer to predict crypto market trades based on tweets


# Cryptocurrency is extremely volatile
Cryptocurrencies are extremely volatile. Recent market news has shown us how years of volatility in the market can be covered in a month of pricing movements in the cryptocurrency markets. Also, how these coins can massively fluctuate by tweets from influencers and celebrities. the primary channel of communication for cryptocurrencies. Many important news are relayed (retweeted) by thousands of user and can reach a very large audience. The goal of this analyzer is to visualize the correlation between cryptocurrencies' price in USD and a score based on the sentiment analysis of tweets, the number of followers of the user, the number of retweets and the number of likes.


# Analyser based on Elasticsearch to predicting crypto market behaviour with twitter sentiment analysis
The integrative and modular system is based on (i) a Elasticsearch-based architecture which handles the large volume of incoming data in a persistent and fault tolerant way; (ii) an approach that supports sentiment analysis which can respond to large amounts of natural language processing queries in real time; and (iii) a predictive method grounded on online learning in which a model adapts its weights to cope with new prices and sentiments.

**Retrieve tweets** - Retrieve tweets related to a cryptocurrency in a time frame
**Store and analyze** - Push the tweet data and also the market data to elasticsearch to analyze and find correlation between the two
**Visualize** - Visualize the data on Kibana


# Flow chart of the system
<img width="819" alt="image" src="https://user-images.githubusercontent.com/47447202/123686039-fa292480-d86c-11eb-91b1-3f9eab6caa22.png">


# Requirements
Technology used:
Elasticsearch 7.13.x
Kibana 7.13.x

Programming Languages: Python

Libraries:
elasticsearch python module
numpy python module
pandas python module
sklearn python module
nltk python module
requests python module
twint python module
cryptocompare api python client
beautifulsoup4 python module
textblob python module
vaderSentiment python module


# Data sources
**Twitter**
The twint API is the source for all the tweets. To make searches in the Twitter API you must use the query operators to match on multiple keywords. Here is an example of a query for the Bitcoin related tweets.
bitcoin OR #BTC OR #bitcoin OR $BTC OR $bitcoin
For each tweets we extracted the following informations:
ID
Text
Username
Number of followers of the user
Number of retweets
Number of likes
Creation date
At this step we also filtered the non english tweets by specifying it to the Twitter API.


**Cryptocurrencies data**
The API CryptoCompare (https://min-api.cryptocompare.com/) was used to retrieve the crypto currencies that we analyzed. Different endpoints are available to retrieve historical data every minute up until 7 days, after what we can retrieve currencies hourly or daily.
About 150 crypto currencies are available from the CryptoCompare API. We focused on Bitcoin, Zilliqa and Nexo as a starting point. The data retrieved contains the following fields:
time: when the data was emitted
close: the price at the end of the time frame (minutely, hourly or daily depending the targeted endpoint)
high: the highest price reached during the time frame
low: the lowest price reached during the time frame
open: the price at the opening of the time frame


# Preprocessing
For the preprocessing, we remove all of the useless data from the tweets, such as HTTP links, @pseudo tags, images, videos and hashtags (#happy->happy). 

Once the cleaned files are obtained, we process the sentiment analysis on each textual content of the tweet to obtain a sentiment score named compound. For the analysis of the sentiment we use the VADER algorithm. There is a great implementation in Python called vaderSentiment. This compound is then multiplied by the number of followers of the user and the number of likes to emphasize the importance of the sentiment. Here is the calculation made on the compound to obtain the tweet's score:
tweet's score = (#like + #follower) * compound

If the tweet comes from an influencer, the number of followers will be high, so the score will be. If it is retweeted by a lambda person with a dozen of followers, the score will be small. We don't consider the number of retweet because it is the same for the original tweet and for any of its retweet: we don't want that a lambda person could have an impact on the score by considering the number of retweet in the tweet's score calculation.

Finally, after the tweets have been fully processed, we end up with two features:
the creation date of the tweet
the score of the tweet where a negative value indicates a bad sentiment, a positive a good sentiment and a zero indicates a neutral sentiment.


# CORRELATION ANALYSIS OF PRICE AND SENTIMENT 
These sentiment of the tweet found using VADER and the processed crypto trading values are then fed to elasticsearch to find a correlation between the two.

If the previous day price is more than the current day price, the current day is marked with a numeric value of 0, else marked with a numeric value of 1. Now, this correlation analysis turns out to be a classification problem. The total positive, negative and neutral emotions in tweets in a 3 day period are calculated successively which are used as features for the classifier model and the output is the labeled next day value of bitcoin 0 or 1.The window size is experimented and best results are achieved when the sentiment values precede 3 days to the crypto price. 

Now we can run classification from Kibana to generate a model to predict whether the price will go up or down. We can select “Data Frame Analysis” from the “Machine Learning” menu and click the “Create analytics job” button as shown below. Select “classification” as the job type, and select “like” as the predicted target for the dependent variable. In the excluded fields, we can list fields that are not related to the characteristics of the movies, then click the “Create” button and then the “Start” button to run supervised learning.


# Summary
ML Capabilities of elasticsearch allows fast and easy classification for predicting future prices of a cryptocurrency
While the above system is not completely developed, the presentation provides an overview about how system can be implemented.
It could be used for more than finding sentiment of just crypto, it could be used to find sentiment of anything.

