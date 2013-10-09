APIs (Application Programming Interface)
########################################

:date: 2013-07-30 14:00
:slug: API
:summary: Notes on API use and python
:author: Alex Button

INTRO
-----

An API is a protocol intended to be used as an interface by software components
to communicate with each other. An API is a set of  programming instructions and
standards for accessing web based software applications (such as above).

With API's applications talk to each other without any user knowledge or
intervention.
Often, companies like Google, Vimeo and Twitter releases it's API to the public
so that developers can develop products that are powered by its service.

It is important to know that an API is a software-to-software interface,
not a user interface.

THE DOCUMENTATION
-----------------

The Documentation is the most important part for a person interacting with an API.
It essentially tells you what you can and can not do.  The docs are also good 
referencepoints with examples of what the output will look like when you make a 
call to the sever.

Here are some examples:

.. _Twitters API: https://dev.twitter.com/docs/api/1.1
.. _Youtubes API: https://developers.google.com/youtube/
.. _NPRs API: http://www.npr.org/api/index
.. _Reddits API: http://www.reddit.com/dev/api

Interacting with an API Using Python
------------------------------------

An API can be called from a server using HTTP. By calling a certain url the response
back has information we can use. By using code such as:

.. code-block:: python
    
    import requests 

    # Make a GET request here and assign the result to kittens: 
    kittens = requests.get('http://placekitten.com') 
      
    print kittens.text[559:1000]


We get a response:
.. code-block:: console

                      \,`~"~` /
      .-=-.           /    . .\
     / .-. \          {  =    Y}=
    (_/   \ \          \      / 
           \ \        _/`'`'`b
            \ `.__.-'`        \-._
             |            '.__ `'-;_
             |            _.' `'-.__)
              \    ;_..-`'/     //  \
              |   /  /   |     //    |
              \  \ \__)   \   //    /
               \__)  


The response from the server often takes the form of JSON or XML.  What 
type of response we will get will usually be covered in the documentation of the API.

We will be focusing on JSON because it is the easier to parse and also because using
libraies like JSON or simpleJSON in python makes everything so much easier to deal with.

Basic API Call
--------------

The following script is an example of how an API can be called using python.  The script
uses the request library to make a call to the youtube API.  This call will return json 
containing the info we need.  Using the json library in python we can parse the data into
a dictionary that can used to further refine the data.  Here we just make it look a little
better with minor formatting.

This particular script will show the most popular videos on YouTube for today but looking 
through the API we could call many more things including our own specialized queries.

.. code-block:: python

 
    import requests 
    import json 
    from pprint import pprint 
       
    # Make it a bit prettier.. 
    print "-" * 30
    print "This will show the Most Popular Videos on YouTube Today"
    print "-" * 30
       
    # Get the feed 
    r = requests.get("http://gdata.youtube.com/feeds/api/standardfeeds/top_rated?v=2&alt=jsonc&time=today") 
    r.text 
       
    # Convert it to a Python dictionary 
    data = json.loads(r.text) 
      
    # Loop through the result.  
    for item in data['data']['items']: 
        print "Video Title: %s" % (item['title']) 
        print "Video Category: %s" % (item['category']) 
        print "Video ID: %s" % (item['id']) 
        print "Video Rating: %f" % (item['rating']) 
        print "Embed URL: %s" % (item['player']['default']) 
       
        print 

A sample of the output would look like:

.. code-block:: console
    
    ------------------------------
    This will show the Most Popular Videos on YouTube Today
    ------------------------------
    Video Title: "Minecraft Style" - A Parody of PSY's Gangnam Style (Music Video)
    Video Category: Comedy
    Video ID: b84Z3IRg3VA
    Video Rating: 4.946130
    Embed URL: http://www.youtube.com/watch?v=b84Z3IRg3VA&feature=youtube_gdata_player
    
    Video Title: Perfect Back to School Hair, Makeup & Outfit!
    Video Category: Howto
    Video ID: zm48WoRs0hA
    Video Rating: 4.961238
    Embed URL: http://www.youtube.com/watch?v=zm48WoRs0hA&feature=youtube_gdata_player
    
    Video Title: I GOT A GIRL PREGNANT
    Video Category: Entertainment
    Video ID: g7Qp1f4ADTQ
    Video Rating: 4.977647
    Embed URL: http://www.youtube.com/watch?v=g7Qp1f4ADTQ&feature=youtube_gdata_player


API Keys
--------
This script uses a API key.  Many APIs require an API key. Just as a real-world 
key allows you to access something, an API key grants you access to a particular 
API. Moreover, an API key identifies you to the API, which helps the API provider 
keep track of how their service is used and prevent unauthorized or malicious activity.

.. code-block:: python

    from urllib2 import urlopen
    from json import load
    
    key = "MDExODE1OTU3MDEzNzQ1NjYyNjdiZjllMA001"
    
    def build_api_call(key):
        zip_code = raw_input("Enter your zip code:")
        url = 'http://api.npr.org/stations?apiKey=' + key
        url+='&format=json' 
        url += "&zip=" + zip_code
        return url 
    
    def call_station_api(url):
        response = urlopen(url)
        j = load(response)
        return j
        
    def parse_station_json(json_obj):
        for station in json_obj['station']:
            print (station['callLetters']['$text']+ ": " + 
                   station['marketCity']['$text'] + ", " + 
                   station['state']['$text'])
            print "Frequency:" , station['frequency']['$text'] , station['band']['$text']
            
            
            if 'url' in station:
                print "MP3 Streams: "
                for link in station['url']:
                
                    if link["type"] == "Audio MP3 Stream":
                        print "\t" , link["title"], " - " , link["$text"]
    
    url = build_api_call(key)
    #print "URL: ", url
    json_obj = call_station_api(url)
    parse_station_json(json_obj)

The output of this code looks something like this.

.. code-block:: console

    Enter your zip code:94127
    KQED: San Francisco, CA
    Frequency: 88.5 FM
    MP3 Streams: 
        KQED Live Stream  -  http://www.kqed.org/radio/listen/kqedradio.pls
        KQED's 100 Essential Noise Pop Songs  -  http://npr.ic.llnwd.net/stream/npr_asc5.pls
    KALW: San Francisco, CA
    Frequency: 91.7 FM
    MP3 Streams: 
        KALW-FM Local Public Radio  -  http://live.str3am.com:2430/listen.pls
    KOSC: San Francisco, CA
    Frequency: 90.3 FM
    KPFA: Berkley, CA
    Frequency: 94.1 FM
    KCSM: San Mateo, CA
    Frequency: 91.1 FM
    MP3 Streams: 
        Jazz 91.1: The Bay Area's Jazz Station  -  http://www.abacast.com/media/pls/kcsm/kcsm-kcsm-sc64.pls


Interacting with the API using OAUTH
------------------------------------

Some APIs require authentication using a protocol called OAuth.  OAuth is an 
open standard for authorization. OAuth provides a method for clients to access 
server resources on behalf of a resource owner (such as a different client or 
an end-user).  It also provides a process for end-users to authorize 
third-party access to their server resources without sharing their credentials 
(typically, a username and password pair), using user-agent redirections.

While using OAuth it is usually best to use a third party library in python. In
the example we use a library known as python-twitter.  OAUth2 does have its own 
library used for python too.

The following code uses the Twitter API as an example of a system that uses OAuth.  The
script when run will make a call to twitter for a users friends list.  Then going through
the list it will see which ones have tweeted in the last month.  Finally it will list the
friends who have not tweeted and probably should be unfollowed.  (One of the downsides to 
OAUTH is that it makes it easy for twitter to rate limit the caller to the API, something 
that will happen if the user follows a lot of people)


.. code-block:: python

    import time 
    import twitter
    
    api = twitter.Api(consumer_key='insertyourshere', 
                    consumer_secret='insertyourshere', 
                    access_token_key='insertyourshere', 
                    access_token_secret='insertyourshere')
    users_to_unfollow = []
    oneMonthInSeconds = 2629743
    screen_name = raw_input("Enter your user name:")
     
    # We need the time in seconds since the epoch.
    now = time.time()
     
    # Let's make sure we can successfully log in.
    if api.VerifyCredentials():        
        print "Twitter credentials verified.\n"
    else:
        print "Error verifying twitter credentials.\n"
     
    #First get the user IDs of my friends (the people I'm following).
    friends = api.GetFriends(screen_name=screen_name)
     
    # For each of those friends, we need to get their last tweet, and whether it was more than a month ago.
    for friend in friends:
        lastStatus = api.GetUserTimeline(screen_name=friend.screen_name,count=1)
        for status in lastStatus:
            if status.created_at_in_seconds < (now-oneMonthInSeconds):
                users_to_unfollow.append(friend.name)
        time.sleep(15)
     
    # Now that we have a list of people whose last tweet is more than a
    #   month ago, we can print them out as a list of people to unfollow.
    print "Unfollow these folks: "
    for user in users_to_unfollow:
        print user


The output would look something like this.
.. code-block:: console

    Twitter credentials verified.
    
    Unfollow these folks: 
    Stephen Giusti
    chriscaceres
    your face
    blackscalelizardfolk
    sfsanta
    A Rose

