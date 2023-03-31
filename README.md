

<img width="1000" alt="image" src="https://user-images.githubusercontent.com/83147951/229229320-3fdc1ee5-943f-4ea7-bcf9-c229a54ac3ac.png">

# Problem : Why Recommender system

## Recommender systems are beneficial to both service providers and users

### Users: 
It helps users to get personalized recommendations, take better decision in their online transactions

### Businesses providers
it helps businesses in increasing their sales , retain the customers, redefine user’s web browsing experience and enhance their shopping experience by making suggestions for items that users might like .

### Types of recommendation system :
1.	Content Based Movie system recommends movie on the basis of the content like the content description/synopsis, cast, crew, director and genres of the     movie.
 <img width="158" alt="image" src="https://user-images.githubusercontent.com/83147951/195168216-305e1275-20c4-4535-94c4-06462649a030.png">

2.  Collaborative based movie system recommends movie based on
	  ratings by similar users and predicting movie rating from past user ratings 
    <img width="167" alt="image" src="https://user-images.githubusercontent.com/83147951/195168272-950b2ab0-c229-45fa-8433-6bcfdffe3143.png">

3.  Hybrid model : can be considered as the combination of the content and collaborative  filtering method


### Content-based movie recommender system
Content-based filtering makes recommendations by using keywords and attributes assigned to objects in a database (e.g., items in an online marketplace) and matching them to a user profile. The user profile is created based on data derived from a user’s actions, such as purchases, ratings (likes and dislikes), downloads, items searched for on a website and/or placed in a cart, and clicks on product links



Steps followed:
1. Data Acquisition
1.Kaggle link: https://www.kaggle.com/datasets/tmdb/tmdb-movie-metadata
<img width="368" alt="image" src="https://user-images.githubusercontent.com/83147951/195166192-423672bf-e09e-4cb0-8989-31da98c8829d.png">

 
Here data is available only up to year 2017

2.	Web scraping is done to get the list of movies from 2018 – 2022 from Wikipedia using url : 'https://en.wikipedia.org/wiki/List_of_American_films_of_{i} (where i is the value of year between 2018-2022 ) and ‘tmdbv3api’  library to  get genres of each movie . For this we need API key from TMDB website .

Model Architecture:

<img width="444" alt="image" src="https://user-images.githubusercontent.com/83147951/195166242-392e1cf5-1565-4fc5-bf37-21d61004893a.png">


Snapshot of data: 

<img width="370" alt="image" src="https://user-images.githubusercontent.com/83147951/195166287-8d6c098f-1fd2-4253-b9f4-41c4b05a03df.png">


How to find closest vectors : Here  i chose cosine similarity method over Euclidean distance . Mathematically, it measures the cosine of the angle between two vectors projected in a multi-dimensional space. The cosine similarity is advantageous because even if the two similar documents are far apart by the Euclidean distance (due to the size of the document), chances are they may still be oriented closer together. The smaller the angle, higher the cosine similarity.

<img width="240" alt="image" src="https://user-images.githubusercontent.com/83147951/195166334-4c39fea2-72ea-4f7f-a47d-51ebb781d261.png">

3.	Text Preprocessing:
	a.	 Text Cleanup : Basic preprocessing to remove special characters and html tags is done using regular expression
4.	Feature Engineering:
	a.	Director is extracted from crew column where job description is ‘director’
	b.	For cast we need only top  three values which are most important for recommendation
	c.	All the values of cast, crew ,director and genres are  joined into a new column called ‘tag’
	d.	Stemming is then done on ‘tag’ column to get the root word using PorterStemmer of nltk library of NLP.
	e.	Now each row contains ‘tag’ column which represents ‘cast’, ‘crew’, director’ and genres of each movie
<img width="393" alt="image" src="https://user-images.githubusercontent.com/83147951/195166396-908ffed4-4213-47e1-ae6b-4a14ba539cfd.png">
 
5.	Model building:
Idea behind converting text into vectors:
All the words into ‘tag’ column of each movie are converted into a matrix called bag of words.Below is an example of BOW:

<img width="318" alt="image" src="https://user-images.githubusercontent.com/83147951/195184094-bf7f0556-780d-4b54-b933-c286bffb23b9.png">

 
	a.	This can be done using class sklearn.feature_extraction.text.CountVectorizer which would be in the shape of (rows=no. of rows in movie 	 		 dataset , columns= total no. of unique words In ‘tag’ text which can be restricted to max_features)

	b.	Second method to convert the text ‘tag’ into vectors is Tfidf Vectorizer (Term Frequency Inverse Document Frequency) which calculates the  		   term/word frequency of each unique word in the text.

	Formula for calculating TF(term frequency):
	Term frequency is defined as no. of times a word appear in a document (i) divided by total no. of words in the document(j):
	<img width="144" alt="image" src="https://user-images.githubusercontent.com/83147951/195166980-1c24022d-67fa-4f17-b3da-256efa608126.png">

 
	Formula for calculating Inverse document frequency :
	Log of total no. of documents divided by no. of documents that contains the word:
	<img width="167" alt="image" src="https://user-images.githubusercontent.com/83147951/195167028-35f2b401-1148-4191-bb53-d64df6b64165.png">

	Formula for TFidf is multiplying TF value with Idf value:

	<img width="152" alt="image" src="https://user-images.githubusercontent.com/83147951/195167247-f48e6b86-1d7b-4c6f-8a00-953d19cda306.png">
	<img width="371" alt="image" src="https://user-images.githubusercontent.com/83147951/195167265-f642b839-5c62-451d-8a69-bc44191bdbe7.png">


	c.	Now the distance between the vectors is calculated using cosine_similarity using cosine_similarity method of sklearn.metrics.pairwise   		class . 
	In order to get the similar movies similarity score between vectors is sorted in descending order to get top few recommendations.

	Results content based filtering:

	<img width="189" alt="image" src="https://user-images.githubusercontent.com/83147951/195167293-8f5c123d-b7d0-46e7-88e0-660d9318dd3f.png">


### Collaborative based movie recommender system

In this approach system filters out suggestions on the basis of similar users likes and reactions.System first looks at user likes and then adds up to find set of all other users with same taste. On the basis of that system recommends products ,movies they may like to watch, items they may like to buy etc. 
For this approach I would need dataset that contains list of items and ratings given by other usres.

There are many ways to find similar users , two of them are listed below:

1.	First approach’s : here function gets the movie title and rating given to that movie by  user as argument and recommend similar movies on the basis of rating given to that movie. 
	a.	If rating is high the function returns the movies which are highly correlated using Pearson correlation or cosine similarity score
	b.	If rating is low function returns those movies which are negatively correlated

2.	Second approach’s has two functions  
    First  Function : this approach predicts rating a user can give to  movies.
    it gets user_id as argument and finds similar users using features like age, gender and genres and compute predicted rating as average of these user’s     ratings.

    Second function : this approach works almost same way but predicts ratings as weighted average of other’s ratings on the basis of correlation             coefficient . 
    
#### Steps followed:

a.	Data Acquisition: Movies, Ratings and Users Data is downloaded from https://grouplens.org/datasets/movielens/

b.	Here we are working on ratings of users so basically text preprocessing is not done in this approach

c.	Here we merge all the datasets : movies, ratings and users together to get data of all the user’s and rating given to movies by those users.

d.	Now we get the similarity matrix of the above data using pivot table so that index contains the users_ids and column contains the features like age , gender and genres .

e.	With this similarity matrix we will get the similar users by computing the distance between each users by first converting into vectors whose dimensions are the features as above using cosine_similarity method of sklearn library.

f.	Based on this matrix movie rating is predicted by computing weighted  average  the ratings given by other users 

g.	Snapshot of results for predicting movie ratings by user : "2"

<img width="238" alt="image" src="https://user-images.githubusercontent.com/83147951/195167350-8ef9e1a4-5583-456c-9a27-120c8abfeef0.png">
 
Here rating_original is the original rating given by user and rating_pred is predicted rating by user 2.

Accuracy of model is computed by taking out square root of difference between true value and predicted value:
<img width="292" alt="image" src="https://user-images.githubusercontent.com/83147951/195167402-47b99311-c6f7-49d7-bc68-de50588b397b.png">
 

3.	Snapshot of results for predicting rating by user : 1 for movie_id : 1193:
<img width="279" alt="image" src="https://user-images.githubusercontent.com/83147951/195167433-1f535a42-8f22-4153-9788-8d92b5496922.png">



### Sentiment Analysis:
Another feature added to recommendation system is sentiment analysis which can be useful for a user to checkout positive and negative review given by other reviews.
Steps followed:
1. Data Collection:
   a. get reviews data from kaggle to train the models, 
      https://www.kaggle.com/code/lakshmi25npathi/sentiment-analysis-of-imdb-movie-reviews
   b. import movies dataset from above recommendation notebooks
2. Text Preprocessing for get text ready for prediction
	a. remove html tags and special characters using regular expression(get only alphanumeric characters)
	b. stemming of text to get the root word using PorterStemmer of nltk.stem class 
3. Convert text into vectors
        a. Now final text after text preprocessing is converted into vectors using CountVectorizer and Tfidf method of sklearn 
        b. train models using vectors 
    Models used for prediction:
	a. GaussianNB
	b. MultinomialNB
	c. BernoulliNB
4. accuracy of all the models is calculated using accuracy_score metrics:
<img width="364" alt="image" src="https://user-images.githubusercontent.com/83147951/195167473-d8a771e0-e781-458f-a23e-30d0addaf0ff.png">

 
5.Get reviews of top 5 movies from recommendation system using  TMDB Api call 
url: 'https://api.themoviedb.org/3/movie/{movie_id}/reviews?api_key={api_key}&language=en-US&page=1'
6.From accuracy score of above models it is clear that MultinomialNB is performing better than GaussianNB and BernoulliNB.
7.Snapshot of predicted sentiment behind reviews of movies using MultinomialNB :
<img width="262" alt="image" src="https://user-images.githubusercontent.com/83147951/195167555-a05e3051-ab7b-4b24-b935-3f0b10db08a5.png">

Future improvements:
1.	For more accuracy and better results surprise library which is Python scikit for recommender system  can be used .
2.	More advanced techniques like NMF can be used for better results
3.	.Results of both the models can be combined to get hybrid model recommender system


Notebooks:

movie_recom_content: Movie recommendation system : Content based

movie_recom_collab_mh: Collaborative based system (first approach as mentioned above)

pred_user_rating : Collaborative based system (second approach as mentioned above)

web_scraping: web scraping for movies data year 2018-2022

Sentiment_Analysis : sentiment analysis on movies reviews




