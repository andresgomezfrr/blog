---
layout: page
title: Sobre mí
permalink: /about/
---

Andrés Gómez Ferrer

<ul>

{% if site.twitter_username %}
  <li>
    <a href="https://twitter.com/{{ site.twitter_username }}">
      <i class="fa fa-twitter"></i> Twitter
    </a>
  </li>
{% endif %}

{% if site.github_username %}
  <li>
    <a href="https://github.com/{{ site.github_username }}">
      <i class="fa fa-github"></i> GitHub
    </a>
  </li>
{% endif %}

{% if site.linkedin_username %}
  <li>
    <a href="https://linkedin.com/in/{{ site.linkedin_username }}">
      <i class="fa fa-linkedin"></i> LinkedIn
    </a>
  </li>
{% endif %}

{% if site.email %}
  <li>
      <i class="fa fa-envelope"></i> {{ site.email }}
  </li>
{% endif %}
</ul>
