---
layout: post
title: "Twitter Hashtag Clustering Network: Visualizing Popular Twitter Hashtags"
usemathjax: false
image: /public/images/HRprofilepic.png
summary: This project showcases visualizing a dataset containing categorical and numerical data, and also build a pipeline that deals with missing data, imbalanced data and predicts a binary outcome. The original dataset can be found on Kaggle, and full details including all of my code is available in a notebook on Kaggle.
---
As life getting faster paced, hashtags and tweets are getting more popular on social media. This is perhaps caused by the conciseness of hashtags when delivering messages and information, which served the fast paced life well. 

It is not very hard to notice hashtags often co-occur in the same tweet. If you click on one hashtag on Twitter, a page with all the tweets containing the clicked hashtag and co-occurred hashtags would appear. This means the clicked hashtag can lead to many other hashtags. In this project, I will analyze and visualize the probability of one hashtag leading to another hashtag. 

## Overview of Pipeline 

1. **Download Tweet Data:**
	I first tried to get the "official" data from Twitter API. I sent a request to Twitter. However, Twitter never got back to me till this day...
	So I downloaded data using this package called ...
	The downloaded data contain many features including 'tweet_id', 'timezone', 'user_id', 'user_name', 'tweet', 'language' and etc... For the purpose of the project, only feature 'tweet_id', 'tweet' and 'hashtags' are included.

2. **Build SQL Database:**
	SQL DB is used to maintain a more organized and sustainable workflow. 

	The schema is shown below: ...

	I built three mySQL tables:
		- Table 'tweets' contains columns: 'tweet_id' (primary key), 'tweet'
		- Table 'hashtags' contains columns: 'hashtag_id' (primary_key), 'hashtag'
		- Relational Table 'tweet_hashtags' contains columns: 'tweet_id', 'hashtag_id', 'count' (count each pair of 'tweet_id' and 'hashtag_id')

3. **Insert Data into SQL Database:**
	I inserted data into the database in Python, and My sql package... is used to connect python to SQL.

	I actually found inserting data this way is not the most sustainable way to do it, as reconnecting to the DB is needed when any errors occurred before committing the changes to the database caused by using start_transaction(). 

	An example code for building table 'tweets' is below:...

	A screenshot of the tables:...

4. **Calculate the Probability of One Hashtag Leading to Another Hashtag:**
	Background Information: Two hashtags can be directly connected by appearing in the same tweet, or can be indirectly connected by connecting through one or more hashtags. In other words, in this example tweet "I love #dogs and #cats", #dogs and #cats are directly connected, because they appeared in the same tweet. If there is another tweet "#cats are the best #pets", this makes '#dogs' and '#pets' indirectly connected through '#cats'. For the simplicity of the project, I calculated the probability based only on the direct connection of two hashtags. 

	The probability of #A leads to #B is calculated by MATH !!! dividing the count of #B that occurred with #A in the same tweet by the total count of hashtags in all of the tweets that #A occurred. Note: This formula makes the probability bidirectional (P(#A --> #B) != P(#B --> #A))

	This calculation is done by joining a couple of SQL tables and the result is read into pandas dataframe, shown below:...

5. **Visualizing the Probability (Hashtag Clustering Network):**
	To make it easier to visualize the hashtag clustering network, only the hashtags having a sum(count) > 10 from all tweets are selected, making 166 hashtags are selected for visualization.

	
	Networkx (insert package!!!!) is used for visualization. 

	To visualize how close two hashtags should be in the graph, the edge weight between two hashtags is used and calculated as the average of P(#A --> #B) and P(#B --> #A). ForceAtLas2 (insert package!!!!) is used for determining the node(hashtag) positions given the edge weight between two hashtags. In general, ForceAtLas2 makes two nodes with bigger weights closer to each other. 

	community_louvain (insert package!!!!) is used for clustering based on how close the nodes are to each other. 

	There are 20 clusters in the graph. To help with visualization, node size is determined by the number of hashtags of all tweets (bigger size meaning more number). Weight edge line transparency is determined by the weight (more transparent meaning smaller weight/closer).

	The graph is suggesting hashtags having similar meaning or in similar categories are more likely to lead to each other (being grouped together in the graph). For example, #twitch is clustered with #gaming, #gamer, #nintendoswitch and etc. #covid19, #covid and #getvaccinated are grouped together. Bitcoin hashtags are also grouped together. This result perhaps indicates including popular hashtags with more relevance to the new hashtags can help increase the popularity of the new hashtags.
	

