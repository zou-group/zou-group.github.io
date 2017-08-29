---
layout: page
title: "Our Team"
permalink: "/team/"
header:
    image_fullwidth: "header_unsplash_1.jpg"
---

<style>
    img.photo{
          object-fit: cover;
          object-position: center; 
          width:200px;
          height:200px;
    }
</style>

<div class="row">


    {% for person in site.data.people %}
    <div class="medium-4 columns t30" style="text-align: center">
    {%if person.url%}
    <img class="photo" src="{{person.url}}"> <br>
    {{person.name}} <br>
    {{person.title}} <br>
    {%if person.website%}
    <a href="{{person.website}}">website</a>
    {%else%}
    website
    {%endif%}
    </div>
    {%endif%}

    {% endfor %}    

</div>

