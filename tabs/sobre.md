---
title: Sobre

# The About page
# v2.0
# https://github.com/cotes2020/jekyll-theme-chirpy
# © 2017-2019 Cotes Chung
# MIT License
---

```python
Nilsonlinux
email: nilsonlinux@gmail.com
Santa Inês - MA / Brasil
--------------------------
Desenvolvedor web e bots para Telegram
```

<div id="nav-wrapper">
  <div id="profile-wrapper" class="d-flex flex-column">
    <div id="avatar" class="d-flex justify-content-center">
      <a href="{{ site.baseurl }}/" alt="avatar">
        {% assign avatar_url = site.avatar %}
        {% capture start %}{{ site.avatar | slice: 0 }}{% endcapture %}
        {% if start == '/' %}
          {% assign avatar_url = avatar_url | prepend: site.baseurl %}
        {% endif %}
        <img src="{{ avatar_url }}" alt="avatar" onerror="this.style.display='none'">
      </a>
    </div>

    <div class="profile-text mt-3">
      <div class="site-title">
        <a href="{{ '/' | relative_url }}">{{- site.title -}}</a>
      </div>
      <div class="site-subtitle font-italic">{{- site.tagline -}}</div>
    </div>
  </div>

    {% if item.path %}
      {% assign ref = ref | append: item.path | append: "/" %}
      {% if item.url %}
        {% assign ref = ref | append: item.url | append: "/" %}
      {% endif %}
    {% endif %}

    <li class="nav-item d-flex justify-content-center
      {% if item.url == page_urls.last
        or item.name == page.tab_active
        or item.name == "Home" and page.layout == "home" %}active{% endif %}">
      <a href="{{ ref }}" class="nav-link d-flex justify-content-center align-items-center w-100">
        <i class="fa-fw {{ item.icon }} ml-xl-3 mr-xl-3 unloaded"></i>
        <span>{{ item.name | upcase }}</span>
      </a>
    </li> <!-- .nav-item -->
  {% endfor %}
