Lets learn how to scrape a website and build an API out of it! For educational purposes, lets scrape [Reddit](http://www.reddit.com/). We will be using Beautiful Soup and Flask in this tutorial. Python is truly beautiful. Don't believe me ? Stay with me till the end of this tutorial.

If you want the entire code check my [github repo](https://github.com/jjerry09/buildredditapi/blob/master/redditapi.py).
##Know the tools and terms
####Whats Scraping?
HTML tags are the building blocks of any website. Web scraping is extracting data out of these html tags for various purposes. It's used to extract information from websites. It is very useful in automating daily tasks and yeah! it saves a lot of time. Many similar websites can be scraped and analysis can be done on the the data obtained. A simple usecase of scraping would be scraping various shopping websites to obtain the best deal on an item or scrape a website periodically to check availibility of seats.   
####So what's Beautiful Soup4?
Beautiful simplifies extracting, navigating and analysing the HTML to get data out of it. Read the [documentation](http://www.crummy.com/software/BeautifulSoup/bs4/doc/) for Beautiful Soup4. Its short and amazing.
For example :    
```python
htmldoc="""
<html><head><title>The Dormouse's story</title></head>
<p class="title"><b>The Dormouse's story</b></p>
<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>
<p class="story">...</p>
"""
```
Suppose this is the html doc. Consider this to be a parse tree with HTML tags.

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(htmldoc)
soup.title
# <title>The Dormouse's story</title>

soup.p['class']
# u'title'

soup.find_all('a')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```
Thats how simple it is. Go through the [doc](http://www.crummy.com/software/BeautifulSoup/bs4/doc/) to unleash the true power of BeautifulSoup4.

####Whats an API?
Application Programming Interface(API) is a set of rules between two programs to communicate with each other. Hmmmmm, seems like a wiki answer! But who uses these API's? Okay I'll try to explain it with our example.    

Consider we successfully built a RedditApi . Now Steve wants to build an android app which displays reddit posts in realtime. But, Carra wants to build a website with a certain section which displays reddit posts in some other format! Now is there a need to build two different backends? No. what they need is data from reddit in realtime. That is exactly what our API will provide.    

Like in the definition of an API, it said rules. What are these rules? Other programs (an android app or a website) or anything which needs access to Reddit posts will need to ping the api at a particular webaddress. Suppose my python app which powers the reddit api is hosted at redditapi.com. An example of a rule is - To get the top posts on reddit the app will have to call the webaddress redditapi.com/gettopposts. Any program which gets the data can represent this data in anyform. 
Got it??? 

If someone is interested in reading about rest I found this article [How I Explained REST to My Wife](http://www.looah.com/source/view/2284) which explains REST in layman terms.

####Flask
Flask is a microframework for Python web applications. Its amazing!
A simple hello World program for Flask
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

@app.route("/sayHi")
def hello():
    return "Hi"

if __name__ == "__main__":
    app.run()
```
In the above example we have an endpoint at '/' . So suppose our application is hosted at xyz.com . When a user goes to xyz.com/ it returns Hello World and similarly when someone visits xyz.com/sayHi Hi is returned. [Json](http://json.org/) is mostly prefered format for communication

##Setup your environment
Enough of theory lets code!
If pip isn't shipped with your version of Python please install pip. Check this [link](https://pip.pypa.io/en/stable/installing.html)
Using a [virtualenv](http://docs.python-guide.org/en/latest/dev/virtualenvs/) is a good practice. So lets use it.
Install virtualenv, flask , requests and beautiful soup in your environment 
```
pip install virtualenv
mkdir redditapi
cd redditapi
virtualenv venv
pip install flask requests beautifulsoup4
```

Install Jsonview extension from [chromestore](https://chrome.google.com/webstore/category/apps?utm_source=chrome-app-launcher-info-dialog) or [firefox store](https://addons.mozilla.org/en-us/firefox/addon/jsonview/). It lets you see json in a readable format.
Lets start coding!
## Scrape with Beautiful Soup
Lets hit the REPL. Type python in your terminal.
REPL is a great way to experiment.
```python 
>>>import bs4
>>>index_url="http://www.reddit.com/"
```
To get the html from [Reddit](http://www.reddit.com) we will use the requests library.
```python 
>>>import requests
>>>response=requests.get(index_url)
```
The response object contains all the html from reddit.You can check by printing response.text .    
Visit [Reddit](http://www.reddit.com/) . Hover over the first post and inspect element using Chrome Dev tools or Mozilla Firebug.
Every website has a particular structure of html tags.    
The entire reddit content is under the 
```html
<div class="content" role="main">... </div> tag.     
```
But all the posts are under the 
```html
<div id="siteTable">...</div>
```
tag which is inside the content div.

So lets use Beautiful Soup to get everything under the div with id = siteTable.   

First lets get a BeautifulSoup4 obejct by passing the html to the constructor.

```python
>>>soup = bs4.BeautifulSoup(response.text)
```
You can print the soup object and can view the html in a 'pretty' way using
```python
>>>soup.prettify()
```
Now lets find the div with id='siteTable'. We can use the method [select](http://www.crummy.com/software/BeautifulSoup/bs4/doc/#css-selectors)
```python
>>>posts=soup.select("div#siteTable")
```
Hmmm, we still didnt get the posts. On careful observation we see there are many divs inside the siteTable div.    
So lets get those divs.
```python
>>>posts=soup.select("div#siteTable div")
```
What this does is extracts all the divs within the siteTable div into an list. 
On close observation we can see that the last div within the siteTable div doesn't contain any post. So lets omit that.    
But how to? The answer is observation :p
All the posts have a class called thing with the divs. Lets use it.
```python
>>>posts=soup.select("div#siteTable div.thing")
```
Now if you want to confirm whether everything is working fine. Print the first element of posts.

#### Get one post
```python
>>>posts[0]
```
We can see the entire div for the first post is printed!

Till now all good. But we don't want to return the html. For now lets return a list of dictionaries with the title, link and name of the subreddit. You can add whatever you want till the time it fits the structure.

To get the title of the post and the link of the post, we see its under the paragraph tag with class title.
```html
<p class="title">
<a href="link if the post"> Title of the post</a>
```
Lets extract the title and link. Since we want the first anchor tag lets extract it using the select method
```python
>>>info=posts[0].select("p.title a")[0]
>>>title=info.text
>>>link=info["href"]
```

Now lets get the subreddit. On observation we see that subreddit is under the paragraph with class tagline. The name of the subreddit is under the second anchor tag.
```python
subreddit=post[0].select("p.tagline a")[1]
```
But, we need to extract the text . So lets do it!
```python
subreddit = post[0].select("p.tagline a")[1].text
```
We get the text in the unicode format so lets omit the first three characters. 
```python
subreddit = post[0].select("p.tagline a")[1].text[3:]
```
Lets capitalize the first letter
```python
subreddit = post[0].select("p.tagline a")[1].text[3:].capitalize()
```
Python oh python!! It's too easy. Isn't it?

#### Get info from all posts.
We have everything what we need.
Lets populate the title, link and subreddit for all the posts now. Enough of REPL! lets open a file redditapi.py and refactor our code
```python
import requests
import bs4

def get_news():
    index_url="http://www.reddit.com/"
    response=requests.get(index_url)
    ret=[]
    soup = bs4.BeautifulSoup(response.text)
    posts=soup.select("div#siteTable div.thing")
    for post in posts:
        info=post.select("p.title a")[0]
        subreddit=post.select("p.tagline a")[1].text[3:].capitalize()
        ret.append({"title":info.text,"link":info['href'],"subreddit":subreddit})
    return json.dumps(ret)
    
print get_news()
```
We create a list of dictionaries which contains title , link and name of the subreddit.   
We also serialize the object into json formatted string using json.dumps.

Till now we just got posts from the reddit hot list at [http://www.reddit.com/](http://www.reddit.com/).

To get the top, controversial or rising news will we have a to repeat the entire process? Luckily No. Reddit uses a consistent structure throught their website. 

For top news, we only need to change the index_url to the [http://www.reddit.com/top/](http://www.reddit.com/top/) and run .

Buuuuyaaa! We get the top news in a json format! We can do this with all the other categories. 

Instead of writing code again and agian lets modularize.

```python

def get_news(category=''):
    index_url="http://www.reddit.com/"
    response=requests.get(index_url+category)
    ret=[]
    soup = bs4.BeautifulSoup(response.text)
    posts=soup.select("div#siteTable div.thing")
    for post in posts:
        info=post.select("p.title a")[0]
        subreddit=post.select("p.tagline a")[1].text[3:].capitalize()
        ret.append({"title":info.text,"link":info['href'],"subreddit":subreddit})
    return json.dumps(ret)
    
def get_hot():
    return get_news()

def get_rising():
    return get_news('rising/')

def get_top():
    return get_news('top/')
```
##Flask is so simple!
Now lets plugin flask to this code.   
Lets start with adding importing Flask and creating an object. 
```python
from flask import Flask
app = Flask(__name__)
```
We use the route decorator to tell flask what function to call on what route. We also mention the REST method. Since we are always extracting data we will use the GET method.

Add the decorators now!

```python 

@app.route('/gethot', methods=['GET'])
def get_hot():
    return get_news()

@app.route('/getrising', methods=['GET'])
def get_rising():
    return get_news('rising/')

@app.route('/gettop', methods=['GET'])
def get_top():
    return get_news('top/')
```

Isn't flask very simple!!
####Basic Error handler
Lets add a 404 page not found error handler. Also import jsonify and make response
```python

from flask import jsonify , make_response
@app.errorhandler(404)
def not_found(error):
    return make_response(jsonify({'error': 'Not found'}), 404)
```
#### Let it rip!

Finally we use the run() function to run the local server with our application. The if __name__ == '__main__': makes sure the server only runs if the script is executed directly from the Python interpreter and not used as an imported module.

```python
if __name__ == '__main__':
    app.run(debug=True)
```

Now hit the terminal and type 

```
python redditapi.py
```
The app will run on port 5000 .Open your browser and go the url as per your code.
For json results for top news. visit [http://127.0.0.1:5000/gettop](http://127.0.0.1:5000/gettop). Similarly try the other endpoints.

Wasn't that fun?

##Subredit and Final Refactor
The format on reddit is www.reddit.com/r/name_of_subreddit .   
But the structure remains the same :)    
Lets exploit this.   
Lets pass 'r/movies/'    

Oops theres an error!  This is because on a subreddit page we wont get a subreddit tag on every post! How stupid of me!

So lets refactor the code by using an if condtion.

The entire code is only 46 lines!
```python 
#!venv/bin/python
import requests
import json
import bs4
from flask import Flask, jsonify
from flask import make_response

app = Flask(__name__)

@app.route('/gethot', methods=['GET'])
def get_hot():
    return get_news()

@app.route('/getrising', methods=['GET'])
def get_rising():
    return get_news('rising/')

@app.route('/gettop', methods=['GET'])
def get_top():
    return get_news('top/')

@app.route('/r/<string:subreddit>', methods=['GET'])
def get_funny(subreddit):
    return get_news('r/'+subreddit+'/',1)

def get_news(category='',cattype=0):
    index_url="http://www.reddit.com/"
    response=requests.get(index_url+category)
    ret=[]
    soup = bs4.BeautifulSoup(response.text)
    posts=soup.select("div#siteTable div.thing")
    for post in posts:
        info=post.select("p.title a")[0]
        if cattype==0:
            subreddit=post.select("p.tagline a")[1].text[3:].capitalize()
            ret.append({"title":info.text,"link":info['href'],"subreddit":subreddit})
        else:
            ret.append({"title":info.text,"link":info['href']})
    return json.dumps(ret)

@app.errorhandler(404)
def not_found(error):
    return make_response(jsonify({'error': 'Not found'}), 404)

if __name__ == '__main__':
    app.run(debug=True)
```

That was fun and easy. Go ahead scrape and build more endpoints for your cool api. Scraping comes down to observation and python coding.




















































