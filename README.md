# AITA_classifier

Lately, it seems like there's been a massive push to use data intelligently in every industry, and sometimes that need to create an immeninetly useful, innovative use of all the data around us is overwhelming. So, we decided to do something fun! On Reddit, a social media website in which you can post text/media submissions in topic-focused online communities, called subreddits, there is a subreddit called 'r/Am I the Asshole?'. On this subreddit, users post personal moral quandries and other users vote on if they were the jerk, the other person was the jerk, no one is a jerk, or everyone is a jerk. As one of the most entertaining subreddits, in our humble opinion, we started thinking about how it would be neat if you could somehow predict what a post would classified as by users. With that in mind, we set to work! 

* [Data](#data)
  * [Source](#source)
  * [Features](#features)
* [EDA and Feature Engineering](#eda)
  * [Hand Crafted Features](#byhand)
  * [Features from Libraries](#fromlibraries)
* [Modeling](#modeling)
  * [Choosing the Model](#choosingthemodel)
  * [Interpretation of Feature Importance](#featureimportance)
  * [What the Model Got Wrong](#whatitgotwrong)
  * [Making Predictions](#predictions)
* [Closing Thoughts](#closing)
* [Next Steps](#nextsteps)

## Data <a name="data"></a>
### Source <a name="source"></a>
At first, Reddit's own API seemed like the obvious choice, but we then realized they only allow you to access 1000 recent posts, which was an issue for two reasons -- 1) 1000 posts just wasn't enough, especially considering 2) we knew we would need to filter out quite a few unclassified posts (that simply didn't have enough comments to be classified by the moderators). So, we found [pushshift.io](http://pushshift.io), which has been scraping various social media sites for a few years. Using their API, we got about 4500 initial observations. After removing posts without classifications and posts with meaningless classifications (there were must less strict rules back in 2013, and people would classify posts with weird, meaningless phrases!), we had about 2000 observations split amongst the following categories: NTA (not the asshole), YTA (you're the asshole), ESH (everyone sucks here), and NAH (no assholes here). We condensed these four categories into two categories -- the user is a jerk (ESH and YTA) and the user is not a jerk (NTA and NAH) -- since ESH and NAH had very few observations. 
### Features <a name="features"></a>

## EDA & Feature Engineering <a name="eda"></a>
### Hand Crafted Features  <a name="byhand"></a>
### Features from Libraries  <a name="fromlibraries"></a>
#### Semantic Analysis
#### TF-IDF, LSA, and LDA

## Modeling <a name="modeling"></a>
### Choosing the Model <a name="choosingthemodel"></a>
### Interpretation of Feature Importance  <a name="featureimportance"></a>
### What the Model Got Wrong  <a name="whatitgotwrong"></a>
### Making Predictions  <a name="predictions"></a>

## Closing Throughs <a name="closing"></a>

## Next Steps <a name="nextsteps"></a>
