---
title: Sobre

# The About page
# v2.0
# https://github.com/cotes2020/jekyll-theme-chirpy
# © 2017-2019 Cotes Chung
# MIT License
---

  <div id="profile-wrapper" class="d-flex flex-column">
    <div id="avatar" class="d-flex justify-content-center">
      <a href="{{ site.baseurl }}/" alt="avatar">
       {% assign avatar_url = site.avatar %}
        {% capture start %}{{ site.avatar | slice: 0 }}{% endcapture %}
          {% assign avatar_url = avatar_url | prepend: site.baseurl %}
        <img src="{{ avatar_url }}" alt="avatar" onerror="this.style.display='none'">
        
[//]: <> (This is also a comment.)
        
```python
Nilsonlinux
email: nilsonlinux@gmail.com
Santa Inês - MA / Brasil
--------------------------
Desenvolvedor web e bots para Telegram
```
