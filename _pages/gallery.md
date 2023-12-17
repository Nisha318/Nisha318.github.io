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
    width: 100%;
}

/* Responsive display for mobile 676px */
 @media screen and (max-width: 42em) {
  .image-timeline:nth-child(n){
  margin: 20px 0px 20px 0px;
  border-radius: 5px;
  box-shadow: 2px 2px 8px #dadada;
  width: 90%;
  height: auto;
}
 }
 @media screen and (max-width: 64em) {
  .image-timeline:nth-child(n){
  margin: 20px 0px 20px 0px;
  border-radius: 5px;
  box-shadow: 2px 2px 8px #dadada;
  width: 90%;
  height: auto;
}
}
 /* Responsive display for tablet miniimum 1024px */
  @media screen and (min-width: 64em) {
    .image-timeline:nth-child(n){
  margin: 0px 0px 20px 15px;
  border-radius: 5px;
  box-shadow: 2px 2px 8px #dadada;
  width: 28%;
  height: auto;
}
 }
 /* Responsive display for desktop 1280px */
  @media screen and (min-width: 64em) {
  .image-timeline:nth-child(n){
  margin: 0px 0px 20px 15px;
  border-radius: 5px;
  box-shadow: 2px 2px 8px #dadada;
  width: 28%;
  height: auto;
}
 }
</style>

 <h3 class="image-a">Nisha's Gallery</h3>
{% capture notice-5 %}
{% for image in page.images %}
    <img class="image-timeline" src="/assets/images/{{ image }}" />
{% endfor %}

{% endcapture%}
<div class="gallery">{{ notice-5 | raw }}</div>

