---
layout: post
title: Using Python and Twython to Catch a Twitter Stream
description: "An example of how to monitor a Twitter stream with Python"
category: articles
tags: [python,twython,twitter,json,csv,data]
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Contents</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## Overview

*This article is a work in progress. I'll start with instructions for Windows and hope to add instructions of Mac OS X later.*
{: .notice}
<br />

This page provides an example of how to connect to Twitter's streaming API using a Python library called Twython. To follow along, you need several things:

- A local installation of Python version 2.7
- A local installation of the [Twython](https://github.com/ryanmcgrath/twython) library
- A developer account with Twitter
- A code editor. If you do not have one, I suggest [Sublime Text](http://www.sublimetext.com/), which has a free trial version (that does not expire, but will nag you from time to time).

We'll first walk through how to install Python and Twython.

### Installing Python and Twython on Windows

If you already have python installed on your machine, make sure that it is available in your system's path so that you can access and work with it through Powershell. To test whether it is available, go to your Start menu and search for *Powershell*. The first search result likely is "Windows Powershell." Select that to launch the Powershell application.

Once you have a command prompt in Powershell, type in `python`. If you see a bunch of error text, either you do not have python on your computer, or it is not in your path. If you know that you have it installed on your computer, visit the python website to learn how to [add it to your path](http://docs.python.org/2/faq/windows).

<br />
If you do not have python installed on your computer, I recommend that you install the Anaconda distribution, since it comes with a lot of handy data analysis packages. Visit the [Anaconda download page](http://continuum.io/downloads) to find the proper installation package for your machine. After installing, relaunch Powershell and again type in `python`. If it installed correctly and is in your path, you should see a python prompt that looks something like this:

{% highlight text %}
Python 2.7.5 |Anaconda 1.7.0 (64-bit)| (default, Jul  1 2013, 12:37:52) [MSC v.1500 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
{% endhighlight %}

For now, just type `quit()` and press enter to return to the standard Powershell prompt. The standard Powershell prompt will look something like this, but include your username instead of mine:
{% highlight text %}
PS C:\Users\clay>
{% endhighlight %}

Once python is installed and working in Powershell, type the following at the Powershell prompt (**not** the python prompt) and then press enter to install the Twython library:

{% highlight text %}
easy_install twython
{% endhighlight %}

Note: libraries such as Twython often are under active development and their usage may change. The Twitter API also may change, invalidating the instructions on this page. 
<br />

Now, let's test that you have what you need. In Powershell, type `python`. Once the python prompt appears, type the following and press enter:

{% highlight text %}
>>> import twython
{% endhighlight %}

You should see nothing but another python prompt (`>>>`) if it worked correctly. If it did not work correctly -- if Twython is not installed where your python can see it -- then you will see an error that looks like this:
{% highlight text %}
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ImportError: No module named twython
{% endhighlight %}

In that case, backstep through these instructions and try to install it again.

### Setup a Twitter Developer Account

You must authenticate with Twitter in order to connect to the Twitter streaming API. That means that you need a Twitter account and you must register that account to be a developer account. You can use any Twitter account to do this and developer registration is free. 
<br />

- Visit [https://dev.twitter.com/](https://dev.twitter.com/) and sign in with your Twitter account.
- After you log in, hover your mouse over your user profile picture in the upper-right corner of the page. Select *My Applications.* 
- You then need click the *Create a new application* button if you do not already have a Twitter application to use with this tutorial. Fill in the requested details, agree to the terms of use, and then click the *Create your Twitter application* button at the bottom. The name of the application is unimportant because nobody ever is going to see it unless you use it outside of this tutorial.
- Once your application exists, scroll to the bottom of its *Details* page and click the button that says *Create my access token*. It may take a minute or two to process. Give it a few seconds and then refresh the page.

Once Twitter generates your access token, you need to make note of four values on your application's *Details* page. I suggest launching Sublime Text, creating a blank document, and then copying them into there (with reference as to what they are). Do not share these values with others. Look for the following values:

- Consumer key
- Consumer secret
- Access token
- Access token secret

You now are ready to catch a Twitter stream with a python script.

### The Twitter Developer Documents

As you follow this tutorial and eventually extend your application, it is a good idea to read through the [Twython documents](https://github.com/ryanmcgrath/twython) and examples and through the [Twitter developer documents](https://dev.twitter.com/docs). There are many variations to the application we create here and the documentation will provide clear guidance to changing your script. When all else fails, visit [Stack Overflow](http://stackoverflow.com/) and search for answers there.

## A Simple Twython Streamer

It's time to launch Sublime Text and create a python script. Choose a directory where you would like for your script(s) to live. If you need a suggestion, create a folder called "streamer" inside of your home directory.

Next, create a blank document in Sublime Text and save it in the directory that you just created. Call it `stream_simple.py`. The `.py` extension denotes that it is a python script. Once you save the file with this extension, Sublime Text will start to use python-specific code syntax highlighting to make your code easier to read.
<br /><br />

Copy the following and paste it into your file and then save the file.
{% highlight python %}
from twython import TwythonStreamer

APP_KEY            = 'INSERT YOUR CONSUMER KEY HERE'
APP_SECRET         = 'INSERT YOUR CONSUMER SECRET HERE'
OAUTH_TOKEN        = 'INSERT YOUR ACCESS TOKEN HERE'
OAUTH_TOKEN_SECRET = 'INSERT YOUR ACCESS TOKEN SECRET HERE'

class MyStreamer(TwythonStreamer):
    def on_success(self, data):
        if 'text' in data:
            print data['text']
            self.disconnect()

    def on_error(self, status_code, data):
        print "There was an error\n"
        print status_code, data

stream = MyStreamer(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)

stream.statuses.filter(track='haiyan,typhoon')
{% endhighlight %}

Let's look at the code to see what it does... first of all, we import the Twython module that we need in order to use the TwythonStreamer object.
{% highlight python %}
from twython import TwythonStreamer
{% endhighlight %}

Next, you need to insert your keys from your Twitter app here. We're storing them as constant variables so that we can use them to initialize your streamer a bit later.
{% highlight python %}
APP_KEY            = 'INSERT YOUR CONSUMER KEY HERE'
APP_SECRET         = 'INSERT YOUR CONSUMER SECRET HERE'
OAUTH_TOKEN        = 'INSERT YOUR ACCESS TOKEN HERE'
OAUTH_TOKEN_SECRET = 'INSERT YOUR ACCESS TOKEN SECRET HERE'
{% endhighlight %}

This code creates a subclass of the TwythonStreamer class provided by Twython. A class is a type of code object that performs certain functions and stores certain types of data. The TwythonStreamer class was written to know how to connect to the Twitter streaming API. However, the author of Twython has no idea what you might want to do with the incoming stream of tweets, so he wrote the TwythonStreamer class so that users can create a subclass of it and override the default behavior -- to tell it how to act when a tweet comes through on the stream.

When you create a subclass of another class, your custom subclass inherits all of the behavior of the parent class. Here, we create a subclass of `TwythonStreamer` and call it `MyStreamer`. We then override the `on_success()` and `on_error()` methods (similar to functions) to tell the `MyStreamer` class what it should do when a tweet comes through. 

Some default functionality in the parent `TwythonStreamer` class knows to call either `on_success()` or `on_error()` based on what happens when it receives a tweet. Your subclass will execute the code that you put in these methods when a tweet comes through.
<br /><br />

Here, in the `on_success()` method of `MyStreamer`, we check to see if a tweet contains a key called `text` and if it does, then we print the value of that key to the console and then disconnect from the Twitter stream. It is a simple test that should print a single tweet.

In the `on_error()` method of `MyStreamer`, we print to the console that there was an error with the tweet and then print the status code of that error. Note that we do *not* disconnect from the stream if there is an error -- we keep monitoring the stream until a single tweet comes through successfully and only disconnect after we have printed that to the console.

{% highlight python %}
class MyStreamer(TwythonStreamer):
    def on_success(self, data):
        if 'text' in data:
            print data['text']
            self.disconnect()

    def on_error(self, status_code, data):
        print "There was an error\n"
        print status_code, data
{% endhighlight %}

We have to create an *instance* -- a single *object* -- of the `MyStreamer` class in order to use it to monitor a Twitter stream. We do that in this code. Here we use the keys that you generated when you created a Twitter app. The instance that we create is called `stream`. Note that the Twython documentation has information about how to create instances of TwythonStreamer objects.

{% highlight python %}
stream = MyStreamer(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)
{% endhighlight %}

Finally, we tell the `stream` instance of the `MyStreamer` class what we want to track.

{% highlight python %}
stream.statuses.filter(track='haiyan,typhoon')
{% endhighlight %}

We are receiving a stream tweets that have either or both of the terms `haiyan` or `typhoon`. Note that the basic ways to interact with the `track` parameter of the Twitter API (which are detailed in the [Twitter API documentation](https://dev.twitter.com/docs)) are the following:

- `track='haiyan,typhoon'`: To be part of our stream, a tweet must contain the word `haiyan` or the word `typhoon` or both.
- `track='haiyan typhoon'`: To be part of our stream, it must contain both of the words `haiyan` and `typhoon`, but those words do not have to appear in that order or even adjacent to each other in the text of the tweet.

### Running your Simple Streamer

We will work in Powershell (Windows) or Terminal (OS X) to run the streamer. Launch Powershell. You'll see a prompt that indicates that you are in your user folder (most likely). You need to navigate to the folder where you saved the python script. Assuming that you saved it in a subfolder of your home directory called "streamer," then type the following into Powershell to navigate to that directory.
{% highlight text %}
cd ~/streamer
{% endhighlight %}

`cd` means "change directory" and the `~` is a shortcut for your user directory. If you saved your script somewhere else, you will have to navigate to it using similar commands. 

**PS**: there is a useful feature in Powershell for opening a folder with Windows Explorer. Simply type `explorer .` at the prompt and the current folder will open.
{: .notice}
<br />

Once you are in the proper directory in Powershell, type `ls` to see a list of the files there. You should see `stream_simple.py` listed. If you do, then hop into Sublime Text and make sure that you saved your file after pasting in the code and inserting your Twitter keys. Then return to Powershell and type in the following to launch the script.

{% highlight text %}
python stream_simple.py
{% endhighlight %}

If you watch your Powershell console, you will see the text of a single tweet printed to the console. You may also see an error message. When I ran this script, I saw the following:

{% highlight text %}
PS C:\Users\clay\Desktop\py\reference> python stream_simple.py
There was an error

200 Unable to decode response, not vaild JSON.
@billclinton #Haiyan The Filipinos have been key 2 the GCC's development. It's time 2 aid rebuilding the homes of those
who built theirs..
{% endhighlight %}

**You can safely ignore the error** for right now, if you see one. Basically, a lot of people type tweets in languages that include non-English characters that are encoded with [UTF-8](http://en.wikipedia.org/wiki/UTF-8). We have yet to tell our streaming script how to handle those characters. Powershell does not handle them by default and does not know how to display them.

**To be continued...**