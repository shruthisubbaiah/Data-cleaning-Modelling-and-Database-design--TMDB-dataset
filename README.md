
Source of data:

The dataset was downloaded from Kaggle - https://www.kaggle.com/tmdb/tmdb-movie-metadata
This dataset contains details of around 5000 movies from The Movie Database website.

The columns in the movie dataset are:
"	homepage
"	id
"	original_title
"	overview
"	popularity
"	production_companies
"	production_countries
"	release_date
"	spoken_languages
"	status
"	tagline
"	vote_average

There is another credits dataset which has the following columns:
"	Movie_id
"	Movie_title
"	Cast
"	Crew
Production_companies, production_countries, cast and crew columns contain JSON. 

Validity/ accuracy:

The data was obtained using the Tmdb API. It is unclear what credit_id and cast_id denote. A few entries for crew were not well-formed, and as a result the csv file for credits had extra columns and rows with the crew details. There is no indication of what the cast_id and credit_id denote. 

Completeness:

The Tmdb website contains reviews of the movies and discussion threads on the movies. This dataset did not include the reviews, discussions and the viewers/reviewers details. The data reflects the data in the website and has not been tampered with. The values for gender of the cast and crew are 0,1,2- there is no indication of the notation. The budget and revenue for a few movies were 0 which are not valid values for the budget of any movie. These values were changed to Null in the database so that the zeros do not bring down the other statistics of the data. Changing them to Null enables us to perform analysis on the reasons for each movie and we may be able to predict the  budgets based on the correlation factors such as the genre, year of release, cast,etc.

Consistency/Uniformity:
The dataset was consistent in terms of the notations used. Spoken_languages had the language abbreviation, and not the entire language name as it appears on the website. The runtime was also converted to total minutes in the dataset. The dataset had consistent values for every column (disregarding the null values). However, there were values of 0 for revenue, budgets and runtimes of the movies- there was no way of estimating the values at this point. 

TABLES:

"	Movies - id(Primary Key), title, original_language, release_date, popularity, budget, revenue, runtime
Query- "Create table Movies(id int PRIMARY KEY,title varchar(50),original_language varchar(20),release_date datetime,popularity float,budget longint,revenue longint,runtime float)"

"	Genres- genre_id(Primary Key), genre_name
Query- "Create table Genres(genre_id int PRIMARY KEY,genre_name varchar(20))"

"	Tags- Tag_id(Primary Key), Tag_name
Query-"Create table Tags(Tag_id int PRIMARY KEY,Tag_name varchar(50))"

"	Movie_genres- movie_id, genre_id (Primary Key- movie_id and genre_id)
Query- "Create table Movie_Genres(movie_id int,genre_id int,PRIMARY KEY(movie_id,genre_id))"

"	Movie_Tags- movie_id, tag_id (Primary Key- movie_id and Tag_id)
Query- "Create table Movie_Tags(movie_id int,tag_id int,PRIMARY KEY(movie_id,tag_id))"

"	ProductionCompanies- prodComp_id(Primary Key), company_name
Query-"Create table ProductionCompanies(prodComp_id int PRIMARY KEY,company_name varchar(50))"

"	Movies_ProdCompanies- movie_id, prodComp_id(Primary Key- movie_id, prodComp_id)
Query-"Create table Movies_ProdCompanies(movie_id int,prodComp_id int,PRIMARY KEY(movie_id,prodComp_id))"

"	Cast- movie_id,actor_id, character (Primary key- movie_id,actor_id, character)
Query-"Create table Actors(actor_id int PRIMARY KEY, name varchar(50))"

"	Actors- actor_id(Primary Key), name
Query-"Create table Cast(movie_id int, actor_id int, character varchar(50),PRIMARY KEY(movie_id,actor_id,character))"


Use cases and SQL queries to implement them:

1. Get the genres of every movie in the database.
    Query- "SELECT m.title as 'Movie Title',group_concat(g.genre_name) as 'Genre' FROM Movies as m INNER JOIN Movie_Genres as gm ON gm.movie_id==m.id INNER JOIN Genres as g ON g.genre_id==gm.genre_id GROUP BY m.title"

2. Which are the production companies that have produced the movie with the highest budget?
    Query- "elect m.title as 'Movie name',group_concat(pc.company_name) as 'Production Companies',m.budget from ProductionCompanies as pc INNER JOIN Movies_ProdCompanies AS mp ON pc.prodComp_id==mp.prodComp_id INNER JOIN Movies as m ON m.id==mp.movie_id WHERE m.budget==(Select MAX(budget) FROM Movies) GROUP BY m.title"
    
3. What are the movies with a 'space travel' theme
     Query- "Select m.title from Movies as m INNER JOIN Movie_Tags AS tm ON m.id==tm.movie_id INNER JOIN Tags as t ON t.Tag_id==tm.tag_id WHERE t.Tag_name=='space travel'"
     
4. List the total number of movies that every actor has acted in. List the most experienced actor first
    Query- "Select a.name,COUNT(c.movie_id) as 'No. of movies' FROM Actors as a INNER JOIN Cast as c ON a.actor_id==c.actor_id GROUP BY a.actor_id ORDER BY COUNT(c.movie_id) desc"
    
5. What are the Action movies released in 2015?
    Query- "Select m.title,m.release_date FROM Movies as m INNER JOIN Movie_Genres as gm ON gm.movie_id==m.id INNER JOIN Genres as g ON gm.genre_id==g.genre_id WHERE g.genre_name LIKE '%action%' AND m.release_date between '2015-01-01' AND '2015-12-31'"
    
    
    
SQL Schema and Normalization:

    The primary key of the Movies table is ID. Every movie has a unique ID, used to distinguish it. The table has atomic values in every column, and no two columns store the same values. All the non-key attributes are completely dependent on the movie ID. There are no transitive dependencies.

    The primary key of the Genres table is genre_id. Every genre has a unique ID, used to distinguish it. The table has atomic values in every column, and no two columns store the same values. The genre_name is completely dependent on the genre ID. 

    The primary key of the Tags table is Tag_id. Every tag/keyword has a unique ID, used to distinguish it. The table has atomic values in every column, and no two columns store the same values. The Tag name is completely dependent on the movie ID.

    The primary key of the Movie_genres table is a composite key of movie_id and genre_id. The pair of movie and a particular genre is unique. The table has atomic values in every column, and no two columns store the same values.

    The primary key of the Movie_Tags table is a composite key of movie_id and tag_id. The pair of movie and a particular Tag/keyword is unique. The table has atomic values in every column, and no two columns store the same values.

    The primary key of the ProductionCompanies table is the ID of the production company prodComp_id. Every company has a unique ID, used to distinguish it. The table has atomic values in every column, and no two columns store the same values. The company name is completely dependent on the production company ID.

    The primary key of the Movies_ProdCompanies table is a composite key of movie ID and production company ID. Every movie has a unique ID, used to distinguish it. The table has atomic values in every column, and no two columns store the same values.All the non-key attributes are completely dependent on the movie ID. There are no transitive dependencies.

    The primary key of the Movies table is ID. The pair of movie and a particular Tag/keyword is unique . The table has atomic values in every column, and no two columns store the same values.

    The primary key of the Actors table is the actor ID. Every actor has a unique ID, used to distinguish him/her. The table has atomic values in every column, and no two columns store the same values. 

    The primary key of the Cast table is a composite key of movie ID, actor ID and character name. The combination movie id, actor id and the character name of the actor in that movie are unique. If an actor plays a double role in the same movie, he would only have a different character name.  The table has atomic values in every column, and no two columns store the same values.


