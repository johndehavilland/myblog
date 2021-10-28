---
layout: post
title: "Netflix IMDB Chrome Extension"
modified:
categories: blog
excerpt:
tags: ['chrome', 'chromeextension', 'netflix']
image:
  feature:
date: 2015-01-07T23:26:56-05:00
share: true,
comments: true
---

I created a simple Chrome extension today to make things a little easier when browsing Netflix. I always find myself having to go to IMDB.com to go and check out a movie's rating when I am looking for something to watch on Netflix. With this extension I bring that rating right into Netflix, meaning one less click for me.
<!--more-->
Creating a Chrome extension is easy enough. The [guides](https://developer.chrome.com/extensions/index) are pretty good.

For my extension I planned to use the [OMDB api](http://www.omdbapi.com) that provides the IMDB rating of a movie as part of its response.

The code for the query was relatively easy - with a chrome extension you can query the DOM of a tab and so, if the netflix site is visited I grab the movie title by parsing the DOM.

    var title = document.getElementsByClassName("title-wrapper")[0].getElementsByClassName("title")[0].innerText

Then I perform a simply XHR request:

    searchOnOMDB: function (title) {

        return 'http://www.omdbapi.com/?' +
            't=' + encodeURIComponent(title) + '&' +
            '&y=&' +
            'plot=short&' +
            'r=json&' +
            'type=movie';
    },

    requestDetails: function (title) {
        console.log("requesting details");
        var req = new XMLHttpRequest();
        req.open("GET", this.searchOnOMDB(title), true);
        req.onload = this.showFoundItem_.bind(this);
        req.send(null);
    }

and the response returned would be something like:

    {
        Title: "Blood Diamond",
        Year: "2006",
        Rated: "R",
        Released: "08 Dec 2006",
        Runtime: "143 min",
        Genre: "Adventure, Drama, Thriller",
        Director: "Edward Zwick",
        Writer: "Charles Leavitt (screenplay), Charles Leavitt (story), C. Gaby Mitchell (story)",
        Actors: "Leonardo DiCaprio, Djimon Hounsou, Jennifer Connelly, Kagiso Kuypers",
        Plot: "A fisherman, a smuggler, and a syndicate of businessmen match wits over the possession of a priceless diamond.",
        Language: "English, Mende, Afrikaans",
        Country: "Germany, USA",
        Awards: "Nominated for 5 Oscars. Another 9 wins & 22 nominations.",
        Poster: "http://ia.media-imdb.com/images/M/MV5BMTY5MTYyNjkwNV5BMl5BanBnXkFtZTcwODE3MTI0MQ@@._V1_SX300.jpg",
        Metascore: "64",
        imdbRating: "8.0",
        imdbVotes: "330,236",
        imdbID: "tt0450259",
        Type: "movie",
        Response: "True"
    }

from which I parse it and extract the rating. The nice thing with a Chrome extension is that you can manipulate the DOM of a given website if you so desire and so in this case, I manipulate the Netflix DOM to show the rating inline.

I published the extension to the [Chrome store](https://chrome.google.com/webstore/detail/netflix-imdb-lookup/jlnffkfebfjciachomeienhjladjhloi) in case others want something simple like this as well.

I do notice the OMDB api can be slow, and my extension has no bells or whistles.