---
layout: mypost
title: 友情链接
---

```
名称：{{ site.title }}
描述：{{ site.description }}
地址：{{ site.domainUrl }}{{ site.baseUrl }}
头像：{{ site.domainUrl }}{{ site.baseUrl }}/static/img/logo.jpg
```

<ul>
  {% for link in site.links %}
  <li>
    <p><a href="{{ link.url }}" title="{{ link.desc }}" target="_blank" >{{ link.title }}</a></p>
  </li>
  {% endfor %}
</ul>
