---
layout: post
title:  "Autoencoders: a bibliographic survey"
date:   2017-04-19 11:39:16 -0700
categories: deeplearning
---

Recently I was preparing for [a lecture on autoencoders](https://github.com/DSLituiev/teaching/blob/master/autoencoders/autoencoders.md) and I asked myself what is the relevant background literature one needs to read to have a general overview of the topic.
The [Ian Goodfellow's and colleagues' book](http://www.deeplearningbook.org/) came to my mind first. 
It is a good book, still books can be biased due to authors' research preferences.

An unbiased approach would be to look at bibliographic data. I went through Google Scholar and Microsoft Academic Knowledge base to get a list of highly cited papers. Below I show some results using Google Scholar, as it has a biggest data base.

This first-order analysis though not looking into clustering of papers or applied (out-of the group) or fundamental (within group) citations is still a useful tool to get a general overview of the field.


### a clickable `Citation` ~ `Year` plot for Autoencoder-related papers

<iframe src="{{ site.url }}/img/autoenc_scatter_d3.html" marginwidth="0" marginheight="0" scrolling="no"  width="960" height="500"></iframe>


---

<br/>

### Top-20 references:
 
<table style="min-width:800px">
  <thead>
    <tr>
<td>Citations</td> 
<td> Title </td> 
<td style="min-width:200px"> Authors </td> 
<td> Year </td> 
    </tr>
  </thead>
  <tbody>
{% for row in site.data.autoencoders limit:20 %}
    <tr>
<td>{{ row.num_citations }}</td> 
<td> <a href="{{ row.url }}">{{ row.title }}</a> </td> 
<td> {{ row.authors }}</td> 
<td>{{ row.year }}</td> 
    </tr>
{% endfor %}
  </tbody>
</table>

<br/>


