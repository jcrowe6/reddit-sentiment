# Stock Sentiment Analysis on Reddit
Author: Jeremiah Crowell (jcrowe6)

## Intro
In this project I provide code to source a large batch of social media (Reddit) comments, and discover the overall sentiment of the community about a number of topics (companies on the stock market).

This demonstrates the application of NLP principles including information retrieval and sentiment analysis.

## Files / How To
- `410 Project.ipynb`
  - This is the source code. An interactive notebook, it includes the code and some results, and requires Jupyter to run for yourself.
      - The Reddit API keys have been omitted
      - The visualizations are not saved here
- `notebook_export.html`
  - The notebook state after being run on my machine and exported to HTML - this can be displayed with any browser.
    - This includes the visualizations
- `company_tickers.json`
  - Dataset of companies on the US stock exchanges' stock tickers / IDs. Sourced from https://www.sec.gov/files/company_tickers.json
 
## Implementation
The overall picture of my code is as follows.
1. Data Loading
2. Retrieval of Revelant Comments
3. Sentiment Analysis of Comments
4. Visualization

### 1. Data Loading
For this, I found that Reddit has a good free-tier API that just requires registration. Also, the python library `PRAW` made it easy to write code to programmatically source many comments.

On reddit, there are many topic-focuesed subreddits, which contain posts, which contain comments. To get a good sample of a particualr community, I wrote a function `getAllCommentsFromHotPosts` to pull the top K posts from a given subreddit, and return all their comments as a list of Comment objects. This will be the test corpus going forward.

I also write some helper functions to deal with these batches of Comments, as well as preprocess the text to remove junk and normalize the text.

### 2. Retrieval of Revelant Comments
For this project, I am interested only in comments that could be about companies on the stock market. So, using a list of company names and their stock tickers, I wrote code to "bucket" comments into groups, based on whether they contain those key words.

However, this alone was not sufficient due to the classic NLP problem of lexical ambiguity. That is, some stock tickers are the same as very common words, such as "It", "Well", and "Shop". 

To address this, I pulled a large sample of comments from the top posts across all of Reddit, and learn the "background frequency" of all words. Then, I hand-picked a threshold frequency, and only use keywords with frequencies below that to bucket comments.

This worked fairly well, and now we have a current sample of comments, grouped based on their relevance to specific companies. Now we can perform sentiment analysis!

### 3. Sentiment Analysis of Comments

For sentiment analysis, I decided to use the `transformers` library and a pretrained HuggingFace model 'cardiffnlp/twitter-roberta-base-sentiment-latest'. This is trained to predict the overall sentiment of posts from Twitter, into negative, neutral, or positive.

I reasoned that as both Twitter and Reddit are social media, the model would generalize to this text data. Also, the transformer architecture can directly handle changes in meaning due to syntactic structure which is highly desirable. 

So, I run the model on all of the comments, and store the respective scores along with the comment and the ticker mentioned in the comment. 

### 4. Visualization

Now, to get an actual picture of the overall sentiment, I decided to use a TreeMap chart to plot a summary of this data. `plotly.express` implements a TreeMap plotting function that I decided to use.

This kind of chart allows me to show the number of comments about a particular stock as the area of a rectangle, and the average sentiment of the stock as the color of the rectangle.

## Conclusion

Overall this was an engaging project for me, and I see many ways it can be improved. Particularly, to handle cases where a comment contains multiple contrasting opinions on different stocks, the sentiment analysis method would benifet from using a different model to extract a sentiment *about a stock* from a comment, rather than just an overall sentiment.

Thank you!

