---
keywords: fastai
description: Using AWS Lambda to power up and down a Digital Ocean droplet.
title: Serverless scheduled server starts
toc: true 
badges: true
comments: true
categories: [AWS]
image: images/chart-preview.png
nb_path: _notebooks/2020-06-01-lambda-boot-up-droplet.ipynb
layout: post
---
As part of the Covid-19 effort, Digital Ocean donated some free credit to us to work on a local food delivery scheme.

To make that credit go as far as possible, we'd like to power up and down the servers according to a schedule.

Here's how to do that with AWS Lambda, with cloudfront events. We iterate on that to use the Serverless Framework.

This is a first post through Fastpages &amp; Jekyll and is _to be continued_