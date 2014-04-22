# CIS4301 Exam 3 Notes
__Compiled by: Andrew Kerr | [www.andrewjkerr.com](www.andrewjkerr.com)__

![Doge loves Databases!](https://scontent-a-dfw.xx.fbcdn.net/hphotos-frc3/t31.0-8/1498042_10201569571896659_1592321872_o.jpg)

These notes are compiled from various places such as the course textbook, the various slides, [Ryan Roden-Corrent's really fantastic class notes](https://github.com/murphyslaw480/cis4301-notes), and the Internet.

_Disclaimer: I am not responsible for any misinformation. If you use my notes and get a problem wrong because of it, it's not my fault. Seriously._

## Table of Contents

* [HTML](#html)
	* [Forms](#forms)
	* [#id](#pound-sign)

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