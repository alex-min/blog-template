---
---
# Tags

<section class="tag-list" style="max-width: 1000px; margin: 0 auto">
{% assign tags = site.tags | sort %}
{% for tag in tags %}
  {% if tag[0].size > 1 %}
    <hr />
    {% assign id = tag[0] | replace: ' ', '-' %}
    <a href="#{{ id }}"><h2 id="{{ id }}">#{{ tag[0] }}</h2></a>
    {% assign posts = tag[1] | sort | reverse %}
    {% for post in posts %}
      {% unless post.hidden %}
        {% include featured.html post=post %}
      {% endunless %}
    {% endfor %}
  {% endif %}
{% endfor %}
</section>
