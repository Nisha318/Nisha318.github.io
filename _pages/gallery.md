---
permalink: /gallery/
title: "Gallery"
images:
  - 5 - Reconnaissance.png
  - Identify Protect Detect Respond Recover - NIST Cyber Framework.png
  - event 2.jpg
  - event 3.jpg
---

<style>
.gallery{
    display: flex;
    flex-direction: row;
    width: 100%;
}
.image-timeline:nth-child(n){
  margin: 0px 0px 20px 0px;
  border-radius: 5px;
  box-shadow: 2px 2px 8px #dadada;
  width: 250px;
  height: 200px;
}
</style>

 <h3 class="image-a">Nisha's Gallery</h3>
{% capture notice-5 %}
{% for image in page.images %}
    <img class="image-timeline" src="/assets/images/{{ image }}" />
{% endfor %}

{% endcapture%}
<div class="gallery">{{ notice-5 | raw }}</div>

