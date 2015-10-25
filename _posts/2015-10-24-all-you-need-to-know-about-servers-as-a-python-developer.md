---
layout: post
title:  "All you want to know about servers as a python developer"
date:   2015-10-24
---

##Introduction
Servers have confused me quite a bit. I'm sure there will be many python developers who share the same predicament. So let me try to unveil the mist of confusion by sharing everything I know about Servers.
##HTTP: Protocol which rules the world wide web!
HTTP (Hypertext Transfer Protocol) is a communication protocol and it is used to send and receive webpages and other data files on the internet. It is the set of rules and specs which govern the transfer of webpages and other data files on the internet.   
A browser is an HTTP client because it sends requests to an HTTP server (Web server), which then sends responses back to the client. The standard (and default) port for HTTP servers to listen on is 80, though they can use any port. This [article](http://geekexplains.blogspot.in/2008/06/whats-http-explain-http-request-and.html) explains HTTP really well. Please do go through it.

If you want to be geeky about it check the [HTTP 1.1 specs](http://www.w3.org/Protocols/rfc2616/rfc2616.html) which was superseded by multiple RFCs (7230-7237). Search for these RFC's at [ietf](http://tools.ietf.org/html/)
##HTTP Server
So HTTP Request and Response have a format! When a user enters a web site, their browser makes a connection to the site’s web server (this is called the request). The server looks up the file in the file system and sends it back to the user’s browser, which displays it (this is the response). This is roughly how the underlying protocol, HTTP, works. Seems simple? 

Dynamic web sites are not based on files in the file system, but rather on programs which are run by the web server when a request comes in, and which generate the content that is returned to the user. They can do all sorts of useful things, like display the postings of a bulletin board, show your email, configure software, or just display the current time.   

Irrespective of how the client or the server has been implemented, there will always be a way to form a valid HTTP Request for the client to work and similarly the server needs to be capable of understanding the HTTP Requests sent to it and form valid HTTP Responses to all the arrived HTTP Requests. Both the client and the server machines should also be equipped with the capability of establishing the connection to each other (in this case it'll be a TCP reliable connection) to be able to transfer the HTTP Requests (client -> server) and HTTP Responses (server -> client).

The Http Server( a program ) will accept this request and will let your python script get the Http Request Method and URI. The HTTP server will handle many requests from images and static resources. What about the dynamically generated urls ? 

```python
@app.route('\displaynews\<name_of_category>',methods=['GET'])
```
You might have used this decorator in Flask. [Flask](http://flask.pocoo.org/) is a microframework for python. Flask will pattern match this route with the request from the browser.  But how does flask parse the http request from the browser? The Http Server passes the dynamically generated urls to the application server. Whoa ! wait .... What are application servers now?

Apache HTTPD and nginx are the two common web servers used with python.

##Application Servers
Most HTTP servers are written in C or C++, so they cannot execute Python code directly – a bridge is needed between the server and the program. These bridges, or rather interfaces, define how programs interact with the server. This is the application server. The dynamically generated urls are passed from the WebServer to the Application server. The application servers matches to url and runs the script for that route. It then returns the response to the WebServer which formulates an HTTP Response and returns it to the client. 

There are many application servers for python. This [link](https://en.wikipedia.org/wiki/Comparison_of_application_servers#Python)  enlists the different application servers.

Initially python developers used low-level gateways for deployment.
#### Common Gateway Interface (CGI)
This interface, most commonly referred to as “CGI”, is the oldest, and is supported by nearly every web server out of the box. Programs using CGI to communicate with their web server need to be started by the server for every request. So, every request starts a new Python interpreter – which takes some time to start up – thus making the whole interface only usable for low load situations.

If you want to learn to write one. Follow this [tutorial](http://www.jmarshall.com/easy/cgi/) by JM Marshall. 

####mod_python
mod_python is an Apache HTTP Server module that integrates the Python programming language with the server. For several years in the late 1990s and early 2000s, Apache configured with mod_python ran most Python web applications. However, mod_python wasn't a standard specification. There were many [issues](https://docs.python.org/2/howto/webservers.html#mod-python) while using mod_python. A consistent wat to execute Python code for web applications was needed.

[FastCgi and SCGI](https://docs.python.org/2/howto/webservers.html#fastcgi-and-scgi) were other low level gateways used for deployment. They tried to solve the performance problem of CGI.

These low-level gateway interfaces are language agnostic.

##Rise of WSGI
A Web Server Gateway Interface (WSGI) server implements the web server side of the WSGI interface for running Python web applications. WSGI scales and can work in both multithreaded and multi process environments. We can also write middlewares with WSGI. Middlewares are useful for session handling, authentication and many more. You can read about how to write your WSGI implementation on [Armin's blog](http://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi/). A comparison between different WSGI implementations is given at [this link](https://www.digitalocean.com/community/tutorials/a-comparison-of-web-servers-for-python-based-web-applications).

####Gunicorn and uWSGI
Gunicorn and uWSGI are two different application servers.   
[Gunicorn 'Green Unicorn'](http://gunicorn-docs.readthedocs.org/en/latest/) is a Python WSGI HTTP Server for UNIX. It is very simple to configure, compatible with many web frameworks and its fairly speedy. This [article](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-apps-using-gunicorn-http-server-behind-nginx) by digitalocean shows how to configure gunicorn with nginx.

[uWSGI](https://uwsgi-docs.readthedocs.org/en/latest/) is another option for an application server. uWSGI is a high performance and a powerful WSGI server. There are many configuration options available with uWSGI. This [article](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-applications-using-uwsgi-web-server-with-nginx) by digitalocean shows how to configure uWSGI with nginx.


##Apache vs Nginx
Anturis has explained quite lucidly the differences between the two on their [blog](https://anturis.com/blog/nginx-vs-apache/). This post explains how apache and nginx work.

To summarize:   
1. Apache creates processes and threads to handle additional connections. While  Nginx is said to be event-driven, asynchronous, and non-blocking. 
2. Apache is powerful but Nginx is fast. Nginx serves up static content quicker.
3. Nginx includes advanced load balancing and caching abilities.
4. Nginx is a lot lighter than Apache

The organicagency benchmarked the performances of Apache and nginx. The results are available [here](http://www.theorganicagency.com/apache-vs-nginx-performance-comparison/)

##What I use
I use Nginx because it is fast , light and I find the configuration to be much easy. Gunicorn is very simple to configure. So I use gunicorn. uWsgi is also used a lot instead of gunicorn.

Please share with me what do you prefer for your python applications? 

