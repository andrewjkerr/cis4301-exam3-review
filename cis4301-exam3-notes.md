# CIS4301 Exam 3 Notes
__Compiled by: Andrew Kerr | [www.andrewjkerr.com](www.andrewjkerr.com)__

![Doge loves Databases!](https://scontent-a-dfw.xx.fbcdn.net/hphotos-frc3/t31.0-8/1498042_10201569571896659_1592321872_o.jpg)

These notes are compiled from various places such as the course textbook, the various slides, [Ryan Roden-Corrent's really fantastic class notes](https://github.com/murphyslaw480/cis4301-notes), and the Internet.

_Disclaimer: I am not responsible for any misinformation. If you use my notes and get a problem wrong because of it, it's not my fault. Seriously._

## Table of Contents

* [HTML](#html)
	* [Forms](#forms)
	* [#id](#pound-sign)
* [MapReduce](#mapreduce)
	* [Map](#map)
	* [Reduce](#reduce)
	* [Google MapReduce Framework](#framework)
	  * [Redundancy](#redundancy)

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

#### Redundancy
One advantage of the MapReduce framework is to redundantly assign one
map/reduce operation to multiple machines. When working with huge data sets on
distributed hardware, failure rates can be high, so redundancy helps ensure
that a 'good' result gets through. Redundancy can also provide a performance
benefit. If a computation needs to be completed quickly, it can be mapped to
multiple machines that run in parallel -- just take the result that arrives
first.
