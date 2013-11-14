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

## JSON and the Twitter Stream

When you connect to the Twitter API, the data stream you receive is in Javascript Object Notation, commonly known as JSON. The amount of metadata available for each tweet in the stream is huge. It's useful to examine a tweet and its entire JSON structure. To examine a single tweet's JSON, let's modify our previous script to save a single tweet, in JSON format, to a file. Paste this into a new file and save as `stream_simple_json.py`. 

{% highlight python %}
# Import the library that you need to stream tweets with Twython

from twython import TwythonStreamer
import json

# Save your app keys and authentication tokens as constant variables

APP_KEY            = 'INSERT YOUR CONSUMER KEY HERE'
APP_SECRET         = 'INSERT YOUR CONSUMER SECRET HERE'
OAUTH_TOKEN        = 'INSERT YOUR ACCESS TOKEN HERE'
OAUTH_TOKEN_SECRET = 'INSERT YOUR ACCESS TOKEN SECRET HERE'

# Subclass the TwythonStreamer so that you can change the 
# behavior when you receive tweets and errors.

class MyStreamer(TwythonStreamer):

    # Do this stuff when you receive a good tweet
    def on_success(self, data):
        if 'text' in data:
            myFile = open("json_example.json", "ab+")
            json.dump(data,myFile)
            myFile.close()
            print "json_example.json file is complete."
            self.disconnect()

    # Do this if there is an error
    def on_error(self, status_code, data):
        print "There was an error\n"
        print status_code, data

# Create an instance of your subclass of the TwythonStreamer

stream = MyStreamer(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)

# Tell your stream object what you want to track
# If you want to track multiple topics, separate them with a comma

stream.statuses.filter(track='haiyan,typhoon')
{% endhighlight %}

Run the script in Powershell by navigating to the proper directory and then entering the command

{% highlight text %}
python stream_simple_json.py
{% endhighlight %}

The Powershell console should show text similar to this when the script is complete:
{% highlight text %}
PS C:\Users\clay\Desktop\py\reference> python stream_simple_json.py
json_example.json file is complete.
PS C:\Users\clay\Desktop\py\reference>
{% endhighlight %}

For reference, here is the text of the single tweet that just saved as JSON to my computer:

> RT @IDFSpokesperson: Soldiers now boarding a plane to the typhoon-struck #Philippines. Follow @IDFrescue for updates on #IDFinPhilippines

Next, use Sublime Text to open up the file `json_example.json` that should now be in the same directory as your python script. You will see something like this:

{% highlight json %}
{"contributors": null, "truncated": false, "text": "RT @IDFSpokesperson: Soldiers now boarding a plane to the typhoon-struck #Philippines. Follow @IDFrescue for updates on #IDFinPhilippines h\u2026", "in_reply_to_status_id": null, "id": 400622448913428480, "favorite_count": 0, "source": "<a href=\"http://twitter.com\" rel=\"nofollow\">Twitter Web Client</a>", "retweeted": false, "coordinates": null, "entities": {"symbols": [], "user_mentions": [{"id": 18576537, "indices": [3, 19], "id_str": "18576537", "screen_name": "IDFSpokesperson", "name": "IDF"}, {"id": 2190620018, "indices": [94, 104], "id_str": "2190620018", "screen_name": "IDFrescue", "name": "IDF Rescue"}], "hashtags": [{"indices": [73, 85], "text": "Philippines"}, {"indices": [120, 137], "text": "IDFinPhilippines"}], "urls": []}, "in_reply_to_screen_name": null, "id_str": "400622448913428480", "retweet_count": 0, "in_reply_to_user_id": null, "favorited": false, "retweeted_status": {"contributors": null, "truncated": false, "text": "Soldiers now boarding a plane to the typhoon-struck #Philippines. Follow @IDFrescue for updates on #IDFinPhilippines http://t.co/0yatoZ5Nr2", "in_reply_to_status_id": null, "id": 400621959597137920, "favorite_count": 7, "source": "<a href=\"http://www.tweetdeck.com\" rel=\"nofollow\">TweetDeck</a>", "retweeted": false, "coordinates": null, "entities": {"symbols": [], "user_mentions": [{"id": 2190620018, "indices": [73, 83], "id_str": "2190620018", "screen_name": "IDFrescue", "name": "IDF Rescue"}], "hashtags": [{"indices": [52, 64], "text": "Philippines"}, {"indices": [99, 116], "text": "IDFinPhilippines"}], "urls": [], "media": [{"expanded_url": "http://twitter.com/IDFSpokesperson/status/400621959597137920/photo/1", "display_url": "pic.twitter.com/0yatoZ5Nr2", "url": "http://t.co/0yatoZ5Nr2", "media_url_https": "https://pbs.twimg.com/media/BY9LjIgCEAExvX5.jpg", "id_str": "400621958808604673", "sizes": {"large": {"h": 600, "resize": "fit", "w": 800}, "small": {"h": 255, "resize": "fit", "w": 340}, "medium": {"h": 450, "resize": "fit", "w": 600}, "thumb": {"h": 150, "resize": "crop", "w": 150}}, "indices": [117, 139], "type": "photo", "id": 400621958808604673, "media_url": "http://pbs.twimg.com/media/BY9LjIgCEAExvX5.jpg"}]}, "in_reply_to_screen_name": null, "id_str": "400621959597137920", "retweet_count": 17, "in_reply_to_user_id": null, "favorited": false, "user": {"follow_request_sent": null, "profile_use_background_image": true, "default_profile_image": false, "id": 18576537, "verified": true, "profile_image_url_https": "https://pbs.twimg.com/profile_images/1672972843/avatar_normal.jpg", "profile_sidebar_fill_color": "EDEDED", "profile_text_color": "363636", "followers_count": 225689, "profile_sidebar_border_color": "FFFFFF", "id_str": "18576537", "profile_background_color": "000000", "listed_count": 4365, "profile_background_image_url_https": "https://si0.twimg.com/profile_background_images/364949675/bg2.jpg", "utc_offset": 7200, "statuses_count": 9827, "description": "Official Israel Defense Forces Twitter: real-time information and updates from the IDF. (RT, following does not constitute endorsement)", "friends_count": 97, "location": "Israel", "profile_link_color": "C7A348", "profile_image_url": "http://pbs.twimg.com/profile_images/1672972843/avatar_normal.jpg", "following": null, "geo_enabled": false, "profile_banner_url": "https://pbs.twimg.com/profile_banners/18576537/1377414439", "profile_background_image_url": "http://a0.twimg.com/profile_background_images/364949675/bg2.jpg", "name": "IDF", "lang": "en", "profile_background_tile": false, "favourites_count": 7, "screen_name": "IDFSpokesperson", "notifications": null, "url": "http://www.idfblog.com", "created_at": "Sat Jan 03 08:53:43 +0000 2009", "contributors_enabled": false, "time_zone": "Jerusalem", "protected": false, "default_profile": false, "is_translator": false}, "geo": null, "in_reply_to_user_id_str": null, "possibly_sensitive": false, "lang": "en", "created_at": "Wed Nov 13 13:51:33 +0000 2013", "in_reply_to_status_id_str": null, "place": null}, "user": {"follow_request_sent": null, "profile_use_background_image": true, "default_profile_image": false, "id": 44212132, "verified": false, "profile_image_url_https": "https://pbs.twimg.com/profile_images/378800000713818279/1d29d8f9d2db2222811be95031dea0f4_normal.jpeg", "profile_sidebar_fill_color": "C0DFEC", "profile_text_color": "333333", "followers_count": 814, "profile_sidebar_border_color": "FFFFFF", "id_str": "44212132", "profile_background_color": "022330", "listed_count": 40, "profile_background_image_url_https": "https://si0.twimg.com/profile_background_images/378800000112583937/94b0dc02f2118f6e9fdeb3ec5867ace7.jpeg", "utc_offset": -18000, "statuses_count": 12626, "description": "Strategic planning, development and stabilization of humanitarian emergencies - Intelligence, Defense, Law Enforcement Fusion.", "friends_count": 713, "location": "USA", "profile_link_color": "0084B4", "profile_image_url": "http://pbs.twimg.com/profile_images/378800000713818279/1d29d8f9d2db2222811be95031dea0f4_normal.jpeg", "following": null, "geo_enabled": false, "profile_background_image_url": "http://a0.twimg.com/profile_background_images/378800000112583937/94b0dc02f2118f6e9fdeb3ec5867ace7.jpeg", "name": "H-II OPSEC ", "lang": "en", "profile_background_tile": false, "favourites_count": 5, "screen_name": "HIIOPSEC", "notifications": null, "url": "http://www.H-II.org", "created_at": "Tue Jun 02 21:23:59 +0000 2009", "contributors_enabled": false, "time_zone": "Eastern Time (US & Canada)", "protected": false, "default_profile": false, "is_translator": false}, "geo": null, "in_reply_to_user_id_str": null, "lang": "en", "created_at": "Wed Nov 13 13:53:29 +0000 2013", "filter_level": "medium", "in_reply_to_status_id_str": null, "place": null}
{% endhighlight %}

That is difficult to read. Luckily, there are a lot of JSON formatters online that will make the tweet more legible for you. For this example, I used [this one](http://jsonformatter.curiousconcept.com/). Take the entire text from `json_example.json` and paste it into an online formatter. You'll receive back the same tweet, with the JSON formatted to be easier to read. The formatted JSON is below.


{% highlight json %}
{
   "contributors":null,
   "truncated":false,
   "text":"RT @IDFSpokesperson: Soldiers now boarding a plane to the typhoon-struck #Philippines. Follow @IDFrescue for updates on #IDFinPhilippines h\u2026",
   "in_reply_to_status_id":null,
   "id":400622448913428480,
   "favorite_count":0,
   "source":"<a href=\"http://twitter.com\" rel=\"nofollow\">Twitter Web Client</a>",
   "retweeted":false,
   "coordinates":null,
   "entities":{
      "symbols":[

      ],
      "user_mentions":[
         {
            "id":18576537,
            "indices":[
               3,
               19
            ],
            "id_str":"18576537",
            "screen_name":"IDFSpokesperson",
            "name":"IDF"
         },
         {
            "id":2190620018,
            "indices":[
               94,
               104
            ],
            "id_str":"2190620018",
            "screen_name":"IDFrescue",
            "name":"IDF Rescue"
         }
      ],
      "hashtags":[
         {
            "indices":[
               73,
               85
            ],
            "text":"Philippines"
         },
         {
            "indices":[
               120,
               137
            ],
            "text":"IDFinPhilippines"
         }
      ],
      "urls":[

      ]
   },
   "in_reply_to_screen_name":null,
   "id_str":"400622448913428480",
   "retweet_count":0,
   "in_reply_to_user_id":null,
   "favorited":false,
   "retweeted_status":{
      "contributors":null,
      "truncated":false,
      "text":"Soldiers now boarding a plane to the typhoon-struck #Philippines. Follow @IDFrescue for updates on #IDFinPhilippines http://t.co/0yatoZ5Nr2",
      "in_reply_to_status_id":null,
      "id":400621959597137920,
      "favorite_count":7,
      "source":"<a href=\"http://www.tweetdeck.com\" rel=\"nofollow\">TweetDeck</a>",
      "retweeted":false,
      "coordinates":null,
      "entities":{
         "symbols":[

         ],
         "user_mentions":[
            {
               "id":2190620018,
               "indices":[
                  73,
                  83
               ],
               "id_str":"2190620018",
               "screen_name":"IDFrescue",
               "name":"IDF Rescue"
            }
         ],
         "hashtags":[
            {
               "indices":[
                  52,
                  64
               ],
               "text":"Philippines"
            },
            {
               "indices":[
                  99,
                  116
               ],
               "text":"IDFinPhilippines"
            }
         ],
         "urls":[

         ],
         "media":[
            {
               "expanded_url":"http://twitter.com/IDFSpokesperson/status/400621959597137920/photo/1",
               "display_url":"pic.twitter.com/0yatoZ5Nr2",
               "url":"http://t.co/0yatoZ5Nr2",
               "media_url_https":"https://pbs.twimg.com/media/BY9LjIgCEAExvX5.jpg",
               "id_str":"400621958808604673",
               "sizes":{
                  "large":{
                     "h":600,
                     "resize":"fit",
                     "w":800
                  },
                  "small":{
                     "h":255,
                     "resize":"fit",
                     "w":340
                  },
                  "medium":{
                     "h":450,
                     "resize":"fit",
                     "w":600
                  },
                  "thumb":{
                     "h":150,
                     "resize":"crop",
                     "w":150
                  }
               },
               "indices":[
                  117,
                  139
               ],
               "type":"photo",
               "id":400621958808604673,
               "media_url":"http://pbs.twimg.com/media/BY9LjIgCEAExvX5.jpg"
            }
         ]
      },
      "in_reply_to_screen_name":null,
      "id_str":"400621959597137920",
      "retweet_count":17,
      "in_reply_to_user_id":null,
      "favorited":false,
      "user":{
         "follow_request_sent":null,
         "profile_use_background_image":true,
         "default_profile_image":false,
         "id":18576537,
         "verified":true,
         "profile_image_url_https":"https://pbs.twimg.com/profile_images/1672972843/avatar_normal.jpg",
         "profile_sidebar_fill_color":"EDEDED",
         "profile_text_color":"363636",
         "followers_count":225689,
         "profile_sidebar_border_color":"FFFFFF",
         "id_str":"18576537",
         "profile_background_color":"000000",
         "listed_count":4365,
         "profile_background_image_url_https":"https://si0.twimg.com/profile_background_images/364949675/bg2.jpg",
         "utc_offset":7200,
         "statuses_count":9827,
         "description":"Official Israel Defense Forces Twitter: real-time information and updates from the IDF. (RT, following does not constitute endorsement)",
         "friends_count":97,
         "location":"Israel",
         "profile_link_color":"C7A348",
         "profile_image_url":"http://pbs.twimg.com/profile_images/1672972843/avatar_normal.jpg",
         "following":null,
         "geo_enabled":false,
         "profile_banner_url":"https://pbs.twimg.com/profile_banners/18576537/1377414439",
         "profile_background_image_url":"http://a0.twimg.com/profile_background_images/364949675/bg2.jpg",
         "name":"IDF",
         "lang":"en",
         "profile_background_tile":false,
         "favourites_count":7,
         "screen_name":"IDFSpokesperson",
         "notifications":null,
         "url":"http://www.idfblog.com",
         "created_at":"Sat Jan 03 08:53:43 +0000 2009",
         "contributors_enabled":false,
         "time_zone":"Jerusalem",
         "protected":false,
         "default_profile":false,
         "is_translator":false
      },
      "geo":null,
      "in_reply_to_user_id_str":null,
      "possibly_sensitive":false,
      "lang":"en",
      "created_at":"Wed Nov 13 13:51:33 +0000 2013",
      "in_reply_to_status_id_str":null,
      "place":null
   },
   "user":{
      "follow_request_sent":null,
      "profile_use_background_image":true,
      "default_profile_image":false,
      "id":44212132,
      "verified":false,
      "profile_image_url_https":"https://pbs.twimg.com/profile_images/378800000713818279/1d29d8f9d2db2222811be95031dea0f4_normal.jpeg",
      "profile_sidebar_fill_color":"C0DFEC",
      "profile_text_color":"333333",
      "followers_count":814,
      "profile_sidebar_border_color":"FFFFFF",
      "id_str":"44212132",
      "profile_background_color":"022330",
      "listed_count":40,
      "profile_background_image_url_https":"https://si0.twimg.com/profile_background_images/378800000112583937/94b0dc02f2118f6e9fdeb3ec5867ace7.jpeg",
      "utc_offset":-18000,
      "statuses_count":12626,
      "description":"Strategic planning, development and stabilization of humanitarian emergencies - Intelligence, Defense, Law Enforcement Fusion.",
      "friends_count":713,
      "location":"USA",
      "profile_link_color":"0084B4",
      "profile_image_url":"http://pbs.twimg.com/profile_images/378800000713818279/1d29d8f9d2db2222811be95031dea0f4_normal.jpeg",
      "following":null,
      "geo_enabled":false,
      "profile_background_image_url":"http://a0.twimg.com/profile_background_images/378800000112583937/94b0dc02f2118f6e9fdeb3ec5867ace7.jpeg",
      "name":"H-II OPSEC ",
      "lang":"en",
      "profile_background_tile":false,
      "favourites_count":5,
      "screen_name":"HIIOPSEC",
      "notifications":null,
      "url":"http://www.H-II.org",
      "created_at":"Tue Jun 02 21:23:59 +0000 2009",
      "contributors_enabled":false,
      "time_zone":"Eastern Time (US & Canada)",
      "protected":false,
      "default_profile":false,
      "is_translator":false
   },
   "geo":null,
   "in_reply_to_user_id_str":null,
   "lang":"en",
   "created_at":"Wed Nov 13 13:53:29 +0000 2013",
   "filter_level":"medium",
   "in_reply_to_status_id_str":null,
   "place":null
}
{% endhighlight %}

That's a lot of data! Here's the thing about capturing Twitter data... if you only want a small number of tweets to analyze (say, 5000 or fewer), then keeping the entire JSON stream is a good idea because it gives you the flexibility to extract different fields at different times, depending on your analytic interest.

The problem with storing a huge amount of JSON is that you are storing more information than you likely need and you also are storing the field names with each record. That's incredibly inefficient. If you export the specific metadata that interests you and store it as CSV instead of JSON, you will save a significant amount of disk space and reduce your processing time.

### Exploring the Twitter JSON

In order to convert your tweet's JSON data to CSV, you need to know which keys to access in the JSON. Luckily, JSON is very similar to python's dictionary data format, so you can load your tweet as JSON and query it like you would a python dictionary to determine how to access the fields that you want.  
<br />

It's nice to use iPython, a tool that installs with many python distributions, to explore data in this manner. However, we also can simply use the python shell through Powershell (or Terminal on OS X). Since we've used Powershell so far in this tutorial, we'll continue to use it. In your Powershell, navigate to the directory where your `json_example.json` file is and then type `python` to launch interactive python. You can try typing `ipython` for an enhanced editor. 

First, import the JSON library by typing the following and pressing return.
{% highlight python %}
>>> import json
{% endhighlight %}

Next, open the example file into a variable called `f` by typing:
{% highlight python %}
>>> f = open("json_example.json")
{% endhighlight %}

Ask JSON to decode the contents of the file and store it as a variable called `tweet`.
{% highlight python %}
>>> tweet = json.load(f)
{% endhighlight %}

Now, you can query the `tweet` variable using the JSON keys that you see in the pasted JSON above. For instance, for the text of the tweet, type:
{% highlight python %}
>>> tweet['text']
{% endhighlight %}

You'll see a few things when this is printed to the screen. With my previous example, it prints like this:
{% highlight python %}
u'RT @IDFSpokesperson: Soldiers now boarding a plane to the typhoon-struck #Philippines. Follow @IDFrescue for updates on #IDFinPhilippines h\u2026'
{% endhighlight %}

The `u` at the beginning of this string indicates that it is unicode text. You also can tell by the embedded unicode at the end of the tweet `h\u2026`. Decoding unicode is important when you want to save the tweet properly. We will cover that later.

You can use the same format to pull out any of the values stored in JSON. Some of the relevant values are nested. For instance, the user name of the person who posted the tweet is nested in the JSON, so you access it like this:

{% highlight python %}
>>> tweet['user']['name']
{% endhighlight %}

To assign one of these values to a python variable, simply use this format, which assigned the user's name to a variable called `username`.

{% highlight python %}
>>> username = tweet['user']['name']
{% endhighlight %}

## Saving Tweets as CSV

There are several things that you have to do in order to convert tweets from JSON to CSV.

1. Decide which fields you want to save as CSV
2. Access the JSON fields 
3. Strip any hidden newline characters and delimiter characters
4. Encode relevant data as UTF-8
5. Save the selected fields out to a file as CSV

There are other enhancements that you can make to a script to make it more flexible and useful. Here are several ideas:

- Prompt the user for search terms
- Prompt the user for how many tweets they want to save
- Prompt the user for which fields to save

Ideally, your finally script will be flexible enough to use in a variety of scenarios. With flexibility comes increased code complexity, so I suggest starting small and adding features as you solidify the core concepts.

There are many ways to approach script writing. This is just one way -- feel free to take your own approach because it may better fit your workflow. 

To get started, I've created an additional class called `TweetMonkey` that will function to process incoming tweets and save them to CSV. When `MyStreamer` processes a tweet through `on_success()`, it dispatches that tweet to the instance of `TweetMonkey`, which takes over the processing. For the purposes of this example, I'm only saving the date/time, username, and text of the tweets. Here is the code:

{% highlight python %}
# Import the libraries that we need
from twython import TwythonStreamer
import json
import csv
import re

# These are the keys for your twitter application, as discussed earlier in the tutorial
APP_KEY            = 'INSERT YOUR CONSUMER KEY HERE'
APP_SECRET         = 'INSERT YOUR CONSUMER SECRET HERE'
OAUTH_TOKEN        = 'INSERT YOUR ACCESS TOKEN HERE'
OAUTH_TOKEN_SECRET = 'INSERT YOUR ACCESS TOKEN SECRET HERE'

# Prompt the user for the terms to track
track_terms = raw_input("What terms do you want to track? (separate multiple with a comma): ")

# Prompt the user for how many tweets they want to keep
keep_tweets = int(raw_input("How many tweets do you want to keep? (-1 for unlimited): "))

# Adjust the input number
if keep_tweets < 0:
    keep_tweets = 999999999
elif keep_tweets == 0:
    keep_tweets = 10

# This will cause the script to keep only english language tweets
# Change to 'all' to keep all tweets regardless of language
keep_lang = 'en'

# Counter for keeping track how many tweets we've saved
counter = 0

# Variable to track whether we've written the header to the CSV file
header_done = False

# Variable to use to name sequential files full of tweets
file_name_suffix = 0

# Prompt the user for how many tweets they want per sequential file
tweets_per_file = int(raw_input("How many tweets do you want to save per file? "))

if tweets_per_file <= 0:
    tweets_per_file = 50000

# This class will process incoming tweets and is called from MyStreamer
# in the on_success() method
class TweetMonkey:
    # Remove some nasty characters that can break the CSV
    def clean(self,text):
        text = text.replace("\n","; ")
        text = text.replace('"', "'")
        text = text.replace(','," ")
        return text

    # Method to create the CSV header in each file
    def create_header(self):
        global file_name_suffix

        header = []
        header.append("created_at")
        header.append("user_name")

        if keep_lang is not 'en':
            header.append("language")

        header.append("tweet")

        # Open the appropriate file and add the header
        tweets = open("tweets_" + str(file_name_suffix) + ".csv", 'ab+')
        wr     = csv.writer(tweets, dialect='excel')
        wr.writerow(header)
        tweets.close()

    # This is the method that does the heavy lifting for processing a tweet
    # and putting it into the CSV file
    def process(self, tweet):
        global header_done
        global file_name_suffix
        global counter
        global tweets_per_file

        # Print a statement to the console everytime 1000 tweets are processed
        if counter % 1000 == 0:
            print counter, "tweets processed..."

        # Increment the file name if we've surpassed the number of tweets per file
        # requested by the user
        if counter % tweets_per_file == 0:
            file_name_suffix += 1
            header_done = False # reenable if you want every file to include the header

        # Create the header if it is not done
        if not header_done:
            self.create_header()
            header_done = True

        # Create the file or append to the existing
        theOutput = []
        theOutput.append(tweet['created_at'])

        # The username can contain hidden characters -- try to strip them
        uname = tweet['user']['name'].encode('utf-8', 'replace')
        newuname = re.sub('\n','',uname)
        theOutput.append(newuname)

        # Keep track of the language if it is not English
        if keep_lang is not 'en':
            theOutput.append(tweet['lang'].encode('utf-8'))

        # Try to strip some hidden characters from the text of the tweet
        # It is redundant to try twice to remove \n newline characters,
        # but several were sneaking through for some reason, and this
        # takes care of them
        twt    = self.clean(tweet['text']).encode('utf-8', 'replace')
        newtwt = re.sub('\n','',twt)
        theOutput.append(newtwt)

        # Open the appropriate file and append this tweet to it
        tweets = open("tweets_" + str(file_name_suffix) + ".csv", 'ab+')
        wr     = csv.writer(tweets, dialect='excel')
        wr.writerow(theOutput)
        tweets.close() # Don't forget to close files after opening them

# This is the subclass of TwythonStreamer that handles incoming tweets
class MyStreamer(TwythonStreamer):
    # Do this if the tweet is successfully captured
    def on_success(self, data):
        global counter
        global keep_lang
        global keep_tweets
        if 'text' in data:
            if keep_lang == 'all' or data['lang'] == keep_lang:
                # Keep the CSV
                counter += 1
                writer   = TweetMonkey()
                writer.process(data)

        # Disconnect when we have the number of requested tweets
        if counter >= keep_tweets:
            self.disconnect()
            print "All done."

    # Do this if there's an error with the tweet
    def on_error(self, status_code, data):
        print "There was an error:\n"
        print status_code, data

# Create an instance of the MyStreamer class 
stream = MyStreamer(APP_KEY,APP_SECRET,OAUTH_TOKEN,OAUTH_TOKEN_SECRET)

# Tell the instance of the MyStreamer class what you want to track
stream.statuses.filter(track=track_terms)
{% endhighlight %}

Run this script by navigating in Powershell (or Terminal) to the directory where you save your script. I saved mine as `stream_simple_csv.py`. When I ran it in Powershell, I saw this:

{% highlight text %}
PS C:\Users\clay\Desktop\py\reference> python stream_simple_csv.py
What terms do you want to track? (separate multiple with a comma): haiyan
How many tweets do you want to keep? (-1 for unlimited): 50
How many tweets do you want to save per file? 26
All done.
PS C:\Users\clay\Desktop\py\reference>
{% endhighlight %}

In this instance, my script created two `.csv` files, `tweets_0.csv` and `tweets_1.csv`. The first few rows of `tweets_0.csv` read

{% highlight text %}
created_at,user_name,tweet
Wed Nov 13 19:29:28 +0000 2013,Daniel John Sobieski,@2LiveinLiberty 269- (IBD) #ClimateChange Con Artists Exploit Typhoon Haiyan To Push #GlobalWarming Fraud http://t.co/ZM1ztaggqO
Wed Nov 13 19:29:28 +0000 2013,Desert Southwest UMC,RT @UMCommunication: You can make a $10 donation to help relief efforts for Typhoon #Haiyan by texting 'UMCOR' to 80888. #umc
Wed Nov 13 19:29:29 +0000 2013,Lasia ;-),RT @DanielaRuah: CBS family is doing its part! Help the victims of Typhoon #Haiyan  Call 8188211080 or visit (cont) http://t.co/7flox5hJBO
Wed Nov 13 19:29:29 +0000 2013,SebastianKaczorowski,RT @Reuters: Before and After: Typhoon Haiyan Aftermath http://t.co/MKg74kkoWg http://t.co/FYwCmyHx6O
{% endhighlight %}

I started an instance of this a few days ago, to capture tweets relevant to the Playstation 4, to track changes over the course of its release week. I set it to save 50,000 tweets per file and to capture an unlimited number of tweets. In the past 4 days, it has saved as CSV over 700,000 tweets! That's a lot of data to mine!
<br />

**Be aware** that Excel will not open a CSV file with UTF-8 encoding by default. If your CSV file contains Arabic, Chinese, Japanese, etc... characters, they will not appear properly in Excel unless you launch Excel, import your CSV file as text and set the encoding to UTF-8. Likewise, SAS has trouble importing UTF-8 characters by default. The import wizard in SAS Enterprise Guide will handle UTF-8 properly. See the SAS documentation for more information.
{: .notice}
<br />

### Additional Resources

Twitter provides a comprehensive set of documents on their [developer website](https://dev.twitter.com/docs) about how to access the API and the rate limits for user accounts. The streaming services are only a part of the functionality available through Twitter and Twython. The [Twython documentation](http://twython.readthedocs.org/en/latest/) provides insight into how to use different aspects of the Twitter API in python. For example, with simple code changes, you can track all tweets from a single user or perform searches that instantly will return thousands of tweets. You also can navigate into the [Examples directory on Twython's GitHub repository](https://github.com/ryanmcgrath/twython/tree/master/examples) to see future examples of how to use Twython to access Twitter.

For example, here is a short code snippet from the Twython repository that shows how to return 50 tweets in a search:

{% highlight python %}
from twython import Twython, TwythonError

# Requires Authentication as of Twitter API v1.1
twitter = Twython(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)
try:
    search_results = twitter.search(q='haiyan', count=50)
except TwythonError as e:
    print e

for tweet in search_results['statuses']:
    print 'Tweet from @%s Date: %s' % (tweet['user']['screen_name'].encode('utf-8'), tweet['created_at'])
    print tweet['text'].encode('utf-8'), '\n'
{% endhighlight %}

As mentioned above, Twitter imposes a limit on the number of tweets that you can access within a given time frame. The number varies based on your application. One nice feature of the streaming API is that the rate is limited by Twitter, so as a user (assuming you only use a single instance of your script at a time), you never will receive notices that you are outpacing the API limits. 

That's it for this introduction to capturing Twitter streams! I hope you found it useful.