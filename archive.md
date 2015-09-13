---
layout: page
title: Blog Archive
permalink: /archive/
banner_image: library-banner.jpg
banner_cc_license: by-nc-sa
banner_cc_licenseVersion: 2.0
banner_cc_attributionName: UTSOA
banner_cc_attributionURL: https://www.flickr.com/photos/utsoa/
---

<div>
  {% for post in site.posts %}
    {% capture currentyear %}{{post.date | date: "%Y"}}{% endcapture %}
    {% if currentyear != year %}
      {% unless forloop.first %}
      </ul>
      {% endunless %}
      <h5>{{ currentyear }}</h5>
      <ul>
      {% capture year %}{{currentyear}}{% endcapture %} 
    {% endif %}
    <li><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></li>
{% endfor %}
</div>
