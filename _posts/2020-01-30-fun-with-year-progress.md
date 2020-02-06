---
layout: post
title: "Year Progress"
description: "Applications for realtime analytics are not easy to build. But one way to master is to build simple applications. I used the Time functions and the magic of Live Updates in Dash to get this one going"
thumb_image: "documentation/sample-image.jpg"
tags: [realtime, analytics]
---

#### Year Progress

Last week, I had some time on my hand and scrolling through Twitter when I came across [@year_progress](https://twitter.com/year_progress). Fascinating to see such great following on such simple concept. Decided to replicate it with a few enhancements. These include a Clock, Calendar Date and updates every second. 

{% include image.html path="year_progress.png" path-detail="year_progress.png" alt="Year Progress" %}

The project was relatively straight forward using simply ``datetime.datetime.now()`` and `strftime` method options. The real-time visualization piece is accomplished using [Dash](https://dash.plotly/live-updates). 

Some immediate enhancements I can think of - current location time, Atomic Clock API (currently there seem to be a 1 second delay) and label on the graphs in addition to the percentages.

The app is available [here](https://os-year-progress.herokuapp.com/)

@OS
