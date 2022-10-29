---
---

# My Publications

{% for publication in site.publications %}
## [{{ publication.title }}]({{ publication.paper_url }})
{{ publication.excerpt }}
{% endfor %}