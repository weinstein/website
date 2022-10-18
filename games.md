---
layout: page
title: Games
permalink: /games/
---

These are the games I've been working on:

{% for game in site.games %}
* <a href="{{ game.url }}"> {{ game.name }} </a>
  {% if game.description %} <p> {{ game.description }} </p> {% endif %}
{% endfor %}
