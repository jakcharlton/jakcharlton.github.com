---
layout: post
title: "Carrierwave errors with HTTP_PROXY"
date: 2012-09-14 10:13
comments: true
categories: [Ruby, Rails, Tips and Tricks]
---
We have a corporate firewall, that loves to stop us accessing external sites and resources. Although our production servers sit outside this, when developing internally we have to ensure we are using our proxy server for all HTTP requests.

When installing Carrierwave to use Fog and S3 this week, I got a weird error relating to my HTTP_PROXY being incorrect. I spent a good couple of hours on this, trying to find a way of specifying a proxy to
[Carrierwave](https://github.com/jnicklas/carrierwave "Carrierwave"),
or to [Fog](https://github.com/fog/fog "Fog"),
or to [Excon](https://github.com/geemus/excon "Excon") (the underlying gem for Fog), all searches came up totally blank.

The code worked just fine on my laptop, over a mobile phone, where I needed no proxy set.
So, it was definitely a problem with proxy settings on the corporate network, but all other gems, and applciations,
and command line tools were working just fine with the network settings on my Mac, and with the
export settings in my ~/.zshrc file:

``export http_proxy=http://proxy.company.com:8080``


I left the problem, and switched to file storage so I could get on with development.

Accidentally today I came across a similar proxy problem with a python script trying to retrieve a github repository,
but it helpfully expalined to me that the http_proxy setting was incorrectly formatted. A quick Google later, and the example I got
for setting the http_proxy had one small difference from mine:

``export http_proxy=http://proxy.company.com:8080/``

A quick export later, and problem resolved, in the python script and in Carrierwave. It seems both of them were
sensitive to the trailing slash, despite a dozen other gems and programs working just fine without it.

