# CIS4301 Exam 3 Notes
__Compiled by: Andrew Kerr | [www.andrewjkerr.com](www.andrewjkerr.com)__

![Doge loves Databases!](https://scontent-a-dfw.xx.fbcdn.net/hphotos-frc3/t31.0-8/1498042_10201569571896659_1592321872_o.jpg)

These notes are compiled from various places such as my own knowledge, Piazza, the course textbook, the various slides, [Ryan Roden-Corrent's really fantastic class notes](https://github.com/murphyslaw480/cis4301-notes), and the Internet.

_Disclaimer: I am not responsible for any misinformation. If you use my notes and get a problem wrong because of it, it's not my fault. Seriously._

## Contributors
* [@andrewjkerr](https://www.github.com/andrewjkerr)
* [@chelseametcalf](https://www.github.com/chelseametcalf)
* [@murphyslaw480](https://www.github.com/murphyslaw480)
* Eric Gross
* Dania Durnas
* [@meghanking](https://www.github.com/meghanking)

## Table of Contents

* [HTML](#html)
	* [Forms](#forms)
	* [#id](#pound-sign)
* [JDBC](#JDBC)
	* [Example Code](#example-code)
	* [Error Handling](#error-handling)
	* [Cursor](#cursor)
	* [Prepared Statements](#prepared-statements)
		* [Examples](#prepared-statement-examples)
	* [Reflection Statement](#reflection-statement)
* [EXPLAIN](#explain)
* [ANALYZE](#analyze)
* [K-Means](#k-means)
	* [Steps](#steps)
	* [Use-Cases](#use-cases)
* [MapReduce](#mapreduce)
	* [Map](#map)
	* [Reduce](#reduce)
	* [Google MapReduce Framework](#framework)
	  * [Redundancy](#redundancy)
	* [Functional MapReduce](#functional)

## HTML

### Forms

The basic structure of a form is the following:

```html
<form action="something.php" method="POST">
	<input type="text" name="some-text-input" />
	<input type="submit" />
</form>
```

What this above form does is submit a POST request to something.php with the "some-text-input" being passed to the something.php script. Breaking down the form, the `<form>` tag normally has two "parameters" (as we'll call them): action and method.

The action is _normally_ some sort of script. In our class, we've been using PHP scripts.

Now, the method is either POST/GET. POST passes the parameters in a POST request that hides the information from the user. (_Note: you can easily view POST requests with Chrome Inspector, Firebug, Wireshark, and the Burp Suite. You can even change POST parameters with the Burp Suite._) GET requests pass the parameters in the URL. If we changed POST to GET in this request, when we submit the form, the user will be sent to `something.php?some-text-input=USERINPUT`.

Christan recommends the following usage of GET/POST:
* Use GET requests for pulling information from the database
* Use POST requests when inserting information into the database

### Pound Sign

In order to make the user jump to a certain id on the page, use a pound sign (a.k.a. a hashtag - for you tweeters out there) in the URL.

For example, in order to jump to `<div id="testing">`, the URL would have to be `www.example.com/#testing`.
	
## JDBC

_Note: Christan explicitly said that there would be no coding problems on the exam, so you should be able to ignore syntax._

### Example Code

```java
import java.sql.DriverManager;
import java.sql.Connection;
import java.sql.SQLException;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class JDBCTest {

  public static void main(String[] argv) {

    System.out.println("-------- PostgreSQL "
        + "JDBC Connection Testing ------------");

    try {

      // Load class
      Class.forName("org.postgresql.Driver");

    } catch (ClassNotFoundException e) {

      System.out.println("Where is your PostgreSQL JDBC Driver? "
          + "Include in your library path!");
      e.printStackTrace();
      return;

    }
    System.out.println("PostgreSQL JDBC Driver Registered!");

    Connection connection = null;

    try {
      connection = DriverManager.getConnection(
          "jdbc:postgresql://localhost:5432/my","user", "password");

    } catch (SQLException e) {
      System.out.println("Connection Failed! Check output console");
      e.printStackTrace();
      return;
    }

    // Did it connect?
    if (connection != null) {
      System.out.println("You made it, take control your database now!");
    } else {
      System.out.println("Failed to make connection!");
    }
    

    // Create Prepated Statement
    try {
      PreparedStatement ps =
        connection.prepareStatement("SELECT created_at, twtext FROM (SELECT * FROM tweets LIMIT 50) t WHERE twtext iLIKE ? LIMIT 1");

      ps.setString(1, "%Dolphins%");

      System.out.println(ps.toString());
      System.out.println("Executing....");
      ResultSet rs = ps.executeQuery();

      while (rs.next()) {
        // Indexed from 1
        System.out.println(rs.getDate(1) + "|" + rs.getString(2));
      }
      rs.close();
 
    } catch (SQLException e) {
      System.out.println("Connection Failed! Check output console");
      e.printStackTrace();
      return;
    }


  }

}
```

You can compile the above code by using `javac JDBC.java` and run it by using `java -classpath .:postgresql-9.1-903.jdbc4.jar JDBCTest` (make sure you download the jar from [here](http://jdbc.postgresql.org/download.html) first!)

We'll be analyzing the code in the next few sections.

### Error Handling

According to the example, errors need to be handled when you:
* Load the PostgreSQL class (catch (ClassNotFoundException e))
* Connect to the database (catch (SQLException e))
* Query the datbase (catch (SQLException e))

### Cursor

The ResultSet that is created when the query is excuted (`ResultSet rs = ps.executeQuery();`) contains a "cursor" that acts kind of like an iterator. This cursor moves through the results with next() (`while (rs.next())`) which iterates through the query or modifies the table.

### Prepared Statements

(Thanks to [Chelsea](https://twitter.com/swordsncarrots) for providing most of the following information on prepared statements! Some more on prepared statements added by [Meghan](https://github.com/meghanking/) to expand on the subject and fixing an error below)

In Java, a prepared statement is created by `PreparedStatement ps = connection.prepareStatement("SELECT * FROM table WHERE something=?");` where the ? is a parameter. The parameter is then set by `ps.setString(1, "the parameter input");`

Prepared statements can have more than one ? (where the ? is a parameter as before) such as `PreparedStatement ps = connection.prepareStatement("SELECT * FROM table WHERE something=? AND somethingelse=?");`. To denote the different parameters(aka, the ?'s), change the parameter index (the first arguement of the set method) which starts at 1.

Prepared Statements can also set more than just strings. You can set most foundational datatypes by calling each by their associated method defined on PreparedStatement, such as `ps.setInt(2, 15);` or `ps.setBoolean(3, true);`. This also makes PreparedStatements powerful since it allows for type-checking before an execution (see Security below).

These are executed by calling `ps.executeQuery();` which returns a ResultSet. Be warned, this can throw SQLExceptions since you will be interacting with the database.

A regular statement builds a query then sends it to a database then the database parses the statement and turns it into a query plan (Query -> Parser -> Rewriter -> Planner.) However, a prepared statement is already partially formed which is better for performance.

Advantages to using a prepared statement include:

* Speed: Prepared statements are faster because they are already partially compiled - all that's missing are the parameters!
* Security: Prepared statements know when to accept paramters which helps prevent against SQL injections.
* Overhead: Prepared statements have less overhead due to the fact that they are compiled and optimized only once and can be executed multiple times.

The way that prepared statements work are:

1. The DBMS parses, compiles, and performs query optimization on the statement template and stores the result without excuting it
2. At a later time, the application supplies (or binds) values to the parameters and the DBMS will then execute the statement.

#### Prepared Statement Examples

Prepared Statement for INSERT:
	
```sql 
PREPARE fooplan (int, text, bool, numeric) AS 
INSERT INTO foo VALUES($1, $2, $3, $4); 
EXECUTE fooplan(1, 'Hunter Valley', 't', 200.00); 
```

Prepared Statement for SELECT:

```sql
PREPARE usrrptplan (int) AS 
	SELECT * FROM users u, logs l WHERE u.usrid=$1 AND 
	u.usrid=l.usrid AND l.date = $2; 
EXECUTE usrrptplan(1, current_date); 
```

### Reflection Statement

A reflection statement tells the program where the driver is. In this case, it's telling the program where the PostgreSQL driver is.

## EXPLAIN

(Thanks to [Chelsea](https://twitter.com/swordsncarrots) for providing most of the following information on EXPLAIN!)

EXPLAIN provides information on the query plan and gives a query tree as well. This information includes start up cost, estimated cost, and rows/width in bytes.

The query tree of a query plan is a tree of plan nodes. The nodes are the bottom level are scan nodes: they return raw nodes from a table. 

The output of EXPLAIN has one line for each node in the plan tree and shows the basic node type and the cost estimates that the planner made for the execution of that plan node. Additional indented lines may appear that show additional properties of the node. However, the very first line (the summary line for the topmost node) has the estimated total execution cost for the plan. It is this number that the planner seeks to minimize.

Example:

```
EXPLAIN SELECT * FROM foo;

                       QUERY PLAN
---------------------------------------------------------
 Seq Scan on foo  (cost=0.00..155.00 rows=10000 width=4)
(1 row)
```

## ANALYZE

ANALYZE runs the query and shows performance stats which basically just gives back an analysis of the query plan. The main difference between ANALYZE and EXPLAIN is that ANALYZE actually causes the query to be executed - not just planned.

## K-Means

K-Means are used to group together data with smiliar information which can be used to identify distinct groups that the data represents.

-Good video on K-Means: [https://www.youtube.com/watch?v=zHbxbb2ye3E](https://www.youtube.com/watch?v=zHbxbb2ye3E)(Thanks Dania!) If you skip to 1:33, there's a great demo using playing cards.

### Steps

1. Choose K - # of clusters.
2. Randomly place centroids.
3. Assign Points to centroids.
4. Average of all the points for each centroid.
5. Update the location of centroids.
6. Repeat 3 - 6 until no more updates on centroids.

### Use-Cases
* Group together data with similar information and how they're relevant.
* Can group what type of data is being grouped togehter 
    * i.e., types of dogs, age and salary of people.
* Things within a cluster are similar.
* Things between clusters are dis-similar.
* When do you have the best K?
	* Check the average distance between the points and the centroids.
	* If the distances withinthe sae cluster are close, then it's a good K.
	* Find the K that is right at the downslope of the curve.

[Example here](http://home.deib.polimi.it/matteucc/Clustering/tutorial_html/AppletKM.html) (if you've installed Java, haha)

## MapReduce
### Map
Given a function `f(x)` and a list `[a1, a2, a3]`, return a list
containing the elements `[f(a1), f(a2), f(a3)]`. For example (ruby syntax):

```
irb(main):001:0> [1,2,3].map {|x| x + 5}
=> [6, 7, 8]
```

### Reduce
Given a function `f(x,y)` a list `[a1, a2, a3]`, and some initial value a0,
return the value `f(f(f(a0, a1), a2), a3)`. In other words, start with some
initial value (which could be the first value in the set or some other value),
put that value in an **accumulator**, and apply the function f (which acts as a
binary operator) between the accumulated value and each array element in turn.
Reduce is harder to explain in words, examples may help (ruby syntax)

Compute the sum (1 + 2) + 3
```
irb(main):002:0> [1,2,3].reduce {|accumulator, element| accumulator + element}
=> 6
```
Compute the sum ((10 + 1) + 2) + 3 (using 10 as the initial accumulator value)
```
irb(main):003:0> [1,2,3].reduce(10) {|accumulator, element| accumulator + element}
=> 16
```
Compute the same sum, but just specify a binary operator
```
irb(main):004:0> [1,2,3].reduce(:+)
=> 6
```

### Framework
Google noticed that they frequently had to perform a map followed by a reduce
on huge sets of data and created a framework for performing this kind of work
in a distributed manner.
The framework takes in a list of (key,value) pairs and a map operation to be
performed on each pair. These pairs are divided up among different machines to
have map operations performed on them. When the map operations are complete,
the results are grouped by keys and sent off to machines that perform the
reduction. This is called the **shuffle** step.

### Functional
A map or reduce does not necessarily have to return a 'value', it could return
a function (one might even argue that a function **is** a value).
Functional programming refers to a style in which functions are treated as
first-class values - functions can take other functions as arguments and have
functions as return values.

The examples above demonstrate this. The code between curly braces forms a
`block` (similar to a **lambda** or **anonymous function**). In the first
example, the block `{|x| x + 5}` takes a single arguments (which it calls `x`)
and returns the value `x + 5`. This block is passed as an argument to the `map`
function, a function that expects some `enumerable` (in this case, the array
`[1,2,3]`, and a block which it is to apply to each array element.

Here is an example of a function that returns a function which will add some
value to another:
```
def make_adder val
  # return a procedure that will add val to whatever is passed in
  return proc {|x| x + val}
end
```
Lets make an adder that will add 3 to anything we give it
```
irb(main):012:0> adder = make_adder 3
=> #<Proc:0x0000000215d928@(irb):9>
rb(main):016:0> adder.yield 5
=> 8
irb(main):017:0> adder.yield -5
=> -2
```

A program might chain together many such functions to eventually construct a
function that does what you want.
