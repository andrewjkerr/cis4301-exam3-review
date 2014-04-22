CIS4301 Exam 3 Review
=====================

![Doge loves Databases!](https://scontent-a-dfw.xx.fbcdn.net/hphotos-frc3/t31.0-8/1498042_10201569571896659_1592321872_o.jpg)
My Exam 3 review notes for CIS4301 Spring 2014. This repository is here in an effort to help crowdsource exam review notes. Please submit pull requests to improve/add content.

##Topics To-Do

Thanks to Eric Gross for the topic list:

* HTML
	* FORMS
	* SUBMIT
	* USING A METHOD (GET & POST)
	    * Know the difference between the two.
	    * Use POST for putting stuff into a database.
	    * Use GET to just retrieve data from database.
		* Users can easily change GET requests in the URL.
		* GET requests store the information in the URL.
	* #/some_URL - Takes you to the div(id = 'some_URL')
* JDBC
	* Cursor in database.
		* It is an iterator, also the same as the ResultSet(Java).
	* PreparedStatements are faster because it's already partially parsed, and all you have to do is just insert a value where the '?' is placed within the prepared statement.
	* Reflection Statement - Where the driver is.
* Query with EXPLAIN & ANALYZE 
	* EXPLAIN gives the tree of the query plan.
		* Start-up Cost | Estimated Cost | rows & width in bytes.
	* ANALYZE gives back the analysis of the query plan.
* Know steps of K-Means
	1. Choose K - # of clusters.
	2. Randomly place centroids.
	3. Assign Points to centroids.
	4. Average of all the points for each centroid.
	5. Update the location of centroids.
	6. Repeat 3 - 6 until no more updates on centroids.
* Know use-cases of K-Means.
	* Group together data with similar information and how they're relevant.
	* Can group what type of data is being grouped togehter 
	    * i.e., types of dogs, age and salary of people.
	* Things within a cluster are similar.
	* Things between clusters are dis-similar.
	* When do you have the best K?
		* Check the average distance between the points and the centroids.
		* If the distances withinthe sae cluster are close, then it's a good K.
		* Find the K that is right at the downslope of the curve.
* Map Reduce
	* Know the stages.
    	* Map
    	* Reduce
    	* Shuffle
	* Know what a functional map-reduce is.
		* Takes a function and does a map reduce of it.
		
##Topics Done

None! Please update when you submit a pull request with new information.