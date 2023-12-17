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

/* Responsive display for mobile 676px */
 @media screen and (max-width: 42em) {
    .gallery{
    width: 100%;
}
  .image-timeline:nth-child(n){
  margin: 20px 0px 20px 0px;
  border-radius: 5px;
  border: 1px dashed #f01367;
  width: 100% !important;
  height: auto;
}
 }
 @media screen and (max-width: 64em) {
    .gallery{
    width: 100%;
}
  .image-timeline:nth-child(n){
  margin: 20px 0px 20px 0px;
  border-radius: 5px;
  border: 1px dashed #f01367;
  width: 49%;
  height: auto;
}
}
 /* Responsive display for tablet minimum 1024px */
  @media screen and (min-width: 64em) {
    .gallery{
    width: 140% !important;
}
    .image-timeline:nth-child(n){
  margin: 0px 0px 20px 15px;
  border-radius: 5px;
  border: 1px dashed #f01367;
  width: 32%;
  height: auto;
}
 }
 /* Responsive display for desktop 1280px */
  @media screen and (min-width: 80em) {
    .gallery{
    width: 140%;
}
  .image-timeline:nth-child(n){
  margin: 0px 0px 20px 15px;
  border-radius: 5px;
  border: 1px dashed #f01367;
  width: 32%;
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

