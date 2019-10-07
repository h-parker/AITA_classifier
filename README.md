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
* [Closing Thoughts](#closing)
* [Next Steps](#nextsteps)

## Data <a name="data"></a>
### Source <a name="source"></a>
At first, Reddit's own API seemed like the obvious choice, but we then realized they only allow you to access 1000 recent posts, which was an issue for two reasons -- 1) 1000 posts just wasn't enough, especially considering 2) we knew we would need to filter out quite a few unclassified posts (that simply didn't have enough comments to be classified by the moderators). So, we found [pushshift.io](http://pushshift.io), which has been scraping various social media sites for a few years. Using their API, we got about 4500 initial observations. After removing posts without classifications and posts with meaningless classifications (there were must less strict rules back in 2013, and people would classify posts with weird, meaningless phrases!), we had about 2000 observations split amongst the following categories: NTA (not the asshole), YTA (you're the asshole), ESH (everyone sucks here), and NAH (no assholes here). We condensed these four categories into two categories -- the user is a jerk (ESH and YTA) and the user is not a jerk (NTA and NAH) -- since ESH and NAH had very few observations. Of these two categories, there was a roughly 70/30 split between NAH and NTA, as seen below in the pie chart of class distribution.

[pie chart of class distribution] link to image goes here

### Features <a name="features"></a>

## EDA & Feature Engineering <a name="eda"></a>
### Hand Crafted Features  <a name="byhand"></a>
We started by putting together several features that seemed naturally like they could be indicative of a person being a jerk, and those features were
- Number of:
 - !
 - ?
 - " (divided by two, since quotes come in pairs)
 - ... 
- Number of words in all capitals
- Number of curses used (based on a [list of Google's banned words](https://raw.githubusercontent.com/RobertJGabriel/Google-profanity-words/master/list.txt))
- Number of first person pronouns
- Number of third person pronouns
- Difference between the number of first and third person pronouns
- Number of words in the description

### Features from Libraries  <a name="fromlibraries"></a>
Then we turned to Python libraries!
#### Semantic Analysis
Not knowing what would give us the best results, we tried semantic analysis of title and description using TextBlob, VADER, and Afinn.
- TextBlob - we made features from title and description polarity and subjectivity, and the difference between the polarity of the title and description, and between the subjectivity of the title and description.
- VADER - VADER was more straightforward; we made features from the compound VADER scores for the title, description, and the difference between title and description compound score. 
- Afinn - similar to VADER, we made features from the Afinn score for the title, description, and the difference between their scores.

We looked at differences thinking that maybe the sentiments in the title and descriptions would be dramatically different if someone was being a jerk -- maybe they would pose their question innocently, then have a much different attitude in the description. 

#### TF-IDF, LSA, and LDA
Finally, we looked at the TF-IDF scores for the words that occured in over 10 articles and 70% or fewer articles. We did this to ensure that the words used weren't overly specific to a couple of submissions, but that they were still possibly indicative of what category the submission fell under. After that, we had about 2300 words, which was far too many words to stop there.

We first tried latent semantic analysis to reduce the dimension introduced by the TF-IDF scores. However, given that we didn't want to introduce more than 20 new variables (due to our data set's smaller size), and 20 variables only explained about 11% of the variance observed in all 2300 words' TF-IDF scores. So, we turned to latent Dirichlet allocation. Using LDA, we looked at using between 10 and 20 topics, and 11 topics gave the most even distribution of submissions across topics (more topics tended to have useless topics with only 2 or 3 submissions in several topics). We then created a new feature using these topics. Since each submission is assigned 1 or more topics and each topic has a probability of being in that topic, we selected the topic with the highest probability for each submission, and that became our topic feature. 

## Modeling <a name="modeling"></a>
After using SMOTE to account for class imbalance in our data (using the imblearn library), we dove into modeling.
### Choosing the Model <a name="choosingthemodel"></a>
We initially considered 6 different models -- logistic regression, KNN, random forest, XGBoost, SVM, and Naive Bayes. Given time constraints, we ran them out of the box and selected the models that balanced true positives and true negatives as well as maintaining a low false positive rate. This is because we thought classifying jerks and non-jerks were equally important, but that it was more egregious to classify a non-jerk as a jerk, rather than to classify a jerk as a non jerk. The one exception to this was the random forest, which we chose to parameter tune, despite it being vastly better at predicting non-jerks (NTA) than jerks, because it had a much lower false positive rate than all other models. In the end, we went with the tuned random forest because it balanced accuracy and false positve rates. The confusion matrix is shown below. From this we can see that it's still got a lot of room for improvement -- unfortunately, no model had a false negative rate lower than 50%!
[final model confusion matrix ] link to image goes here

### Interpretation of Feature Importance  <a name="featureimportance"></a>
From the feature importance graph (shown below), the top 5 most important features were curse count, quote count, Afinn title sentiment, question count, and VADER description sentiment. Curse count implies that either jerks curse a lot, or not that much, but either way, users should mind their vocabulary! We interpreted the importance of quotes as perhaps indicating that you were listening to the opposing party(ies) and can recall what they said (so perhaps you have listened closely to their POV and aren't only thinking about your own wants and reactions. As for question count, we thought that maybe users who seemed like they were trying to understand the other person's/people's side(s) (asking Reddit why they thought the other person was acting in a certain way or demonstrating that they asked the other person directly) would come off as less of a jerk. However, for all of these, the numeric relationship isn't clear -- we don't know that more question marks leads to a classification of NTA, for example. So, all of our interpretations are not explanations, but rather, our best guess at what could be going on in the trees that make up the random forest. Since we can't look at all 100 trees in our forest (given time constraints), we can't know where the cut-offs were for all of our important features. 
[feature importance] link to image goes here


## Closing Thoughts <a name="closing"></a>
This project is not done and over with, but it did teach us a lot! There's so much to learn about NLP, and this was our first foray into it, so we've got a lot of room to continue developing and bettering this project. Examining the feature importance was definitely one of the coolest parts, and based on feature importance, it may be the case that if you want Reddit to rate you as NTA, you should curse less! 

## Next Steps <a name="nextsteps"></a>
Moving forward, we definitely have a few things we'd really like to do:
1. Create more visualizations, like wordclouds for the words in NTA vs TA submissions and for the topics created by LDA
2. Examine what the model got wrong - get a feel for what it struggles with
3. Make predictions on new posts
4. Get more data! Scrape reddit for additional posts. 
