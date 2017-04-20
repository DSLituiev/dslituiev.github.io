---
layout: post
title:  "Autoencoders: a bibliographic survey"
date:   2017-04-19 11:39:16 -0700
categories: jekyll update
---
<!-- _includes/base.html -->
{% assign base = '' %}
{% assign depth = page.url | split: '/' | size | minus: 1 %}
{% if    depth <= 1 %}{% assign base = '.' %}
{% elsif depth == 2 %}{% assign base = '..' %}
{% elsif depth == 3 %}{% assign base = '../..' %}
{% elsif depth == 4 %}{% assign base = '../../..' %}{% endif %}


Recently I was preparing for [a lecture on autoencoders](https://github.com/DSLituiev/teaching/blob/master/autoencoders/autoencoders.md) and I asked myself what is the relevant background literature one needs to read to have a general overview of the topic.
The [Ian Goodfellow's and colleagues' book](http://www.deeplearningbook.org/) came to my mind first. 
It is a good book, still books can be biased due to authors' research preferences.

An unbiased approach would be to look at bibliographic data. I went through Google Scholar and Microsoft Academic Knowledge base to get a list of highly cited papers. Below are results,.


### a clickable `Citation` ~ `Year` plot for Autoencoder-related papers

<iframe src="{{ site.url }}/img/autoenc_scatter_d3.html" marginwidth="0" marginheight="0" scrolling="no"  width="960" height="500"></iframe>


---
<br/>

### Top-20 references:

<table>
  <thead>
    <tr>
<td>Citations</td> 
<td> Title </td> 
<td> Year </td> 
    </tr>
  </thead>
  <tbody>
    {% for row in site.data.autoencoders.content_hash limit: 20%}
    <tr>
  <td> {{ row.num_citations }}</td>
  <td>
  <a href="{{ row.url }}">{{ row.title }}</a>
  </td>
  <td> {{ row.year }}</td>
    </tr>
    {% endfor %}
  </tbody>
</table>


