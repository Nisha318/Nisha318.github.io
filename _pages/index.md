---
cover: true
title: Recent Posts
classes:
  - wide
permalink: /
author_profile: false
toc: false
toc_label: "Unique Title"
toc_icon: "heart"  # corresponding Font Awesome icon name (without fa prefix)
header:
  overlay_image: /assets/images/lwa.png
  overlay_filter: rgba(0, 255, 0, 0.8) # same as adding an opacity of 0.5 to a black background  
  actions:
    - label: "About Author"
      url: "/about"   
# images:
#   - 385228851317440512-BVibmVqCcAAAyvc.jpg
#   - 131216876364775424-AdItDciCQAAKJqk.jpg
#   - 131799095508598784-AdQ-lD3CAAAfsdk.jpg
#   - 227948859786158080-AynWR77CYAA2cfq.jpg
#   - 474662062900391938-BpZWoW4CAAAbU41.jpg
#   - 474640379149438976-BpZC6KhCMAAlywq.jpg
---
<style>
/* CSS code must be placed here to differentiate index page from other pages */
.sidebar.sticky {
   display: none !important;
}
@media (min-width: 1025px) {
  .page {
    float: none;
    width: calc(100% - 80px) !important;
    margin-left: 95px !important;
}
}
/* Media query for tablets */
@media (max-width: 768px) {
    .post-list {
        grid-template-columns: repeat(2, 1fr);
    }
}

/* Media query for mobile */
@media (max-width: 42em) {
    .post-list {
        grid-template-columns: 1fr;
    }
   .image-timeline:nth-child(n){
    margin: 0px 0px 20px 0px;
    border-radius: 5px;
    box-shadow: 2px 2px 8px #dadada;
    width: 100% !important;
}
#main {
    margin-top: 35px !important;
    clear: both;
    margin-left: 10% !important;
    margin-right: 10%;
    padding-left: 0em;
    padding-right: 0em;
    -webkit-animation: intro .3s both;
    animation: intro .3s both;
    max-width: 100%;
    -webkit-animation-delay: .15s;
    animation-delay: .15s;
}
 .page {
    float: none;
    width: calc(100% - 0px) !important;
    margin-left: -25px !important;
    margin-top: 6em;
}

}
/* Media query for mobile */
@media (max-width: 64em) {
    .post-list {
        grid-template-columns: 1fr;
    }
   .image-timeline:nth-child(n){
    margin: 0px 0px 20px 0px;
    border-radius: 5px;
    box-shadow: 2px 2px 8px #dadada;
    width: 100% !important;
}
#main {
    clear: both;
    margin-top: 120px !important;
    margin-left: -5% !important;
    margin-right: 10%;
    padding-left: 0em;
    padding-right: 0em;
    -webkit-animation: intro .3s both;
    animation: intro .3s both;
    max-width: 100%;
    -webkit-animation-delay: .15s;
    animation-delay: .15s;
}
.page .page__inner-wrap {
    float: left;
    margin-top: -4em;
    margin-left: 4rem;
    margin-right: 0;
    width: 100%;
    clear: both;
}
}

/* Complete styling for the index page */

.image-timeline:nth-child(n){
  margin: 0px 0px 20px 0px;
  border-radius: 5px;
  box-shadow: 2px 2px 8px #dadada;
  width: 250px;
  height: 200px;
}
.recent-post__text{
  font-family: sans-serif;
  background-color: #a8edd5;
  border: 1px dashed #05A069;
  padding: 10px 20px 10px 20px;
  margin-bottom: 30px;
  border-radius: 5px;
  box-shadow: 10px 10px 20px #b6e7d6;
}
.definition{
  background-color: rgb(212, 242, 255);
  padding: 10px 20px 10px 20px;
  border: 1px dashed #05A069;
  box-shadow: 10px 10px 20px #b6e7d6;
  border-radius: 5px;
}
.definition a{
  color: black !important;
  font-weight: bold;
}
.recent-post__first{
  margin-top: 50px;
  background-color: rgb(212, 255, 241);
  padding: 10px 20px 10px 20px;
  border: 1px dashed #49b9de;
  box-shadow: 10px 10px 20px #b6e7d6;
  border-radius: 5px;
}
.recent-post__first a{
  font-weight: bold;
}
.about_website{
  background: linear-gradient(to right, #d3e8f6, #aeffd6); /* You can adjust the color stops as needed */
  padding: 20px 40px 20px 40px;
  border-radius: 5px;
  box-shadow: 10px 10px 20px #b6e7d6 ;
  margin-bottom: 40px;
  border: 1px dashed #05A069;
}


.post-catalog_order {
padding: 0;
margin: 0;
max-width: 100%;
position: relative;
list-style: none;
}

.post-catalog_order::before {
content: '';
width: 0.5rem;
height: 100%;
position: absolute;
top: 0;
left: 8%;
background: rgb(212, 255, 230);
z-index: -1;
}

.post-catalog_list {
padding: 0.5rem 1.5rem 1rem;
border-radius: 10px;
background: rgb(212, 255, 241);
border: 1px dashed #05A069;
  box-shadow: 10px 10px 20px #b6e7d6;
}

.post-catalog_list + .post-catalog_list {
margin-top: 1rem;
  border: 1px dashed #05A069;
}

.post-catalog_list::marker {
font-weight: 600;
color: #05A069;
font-size: 1.2rem;
}

.see-all_text{
  display: block;
  margin-top: 30px;
  background-color: rgb(212, 255, 241);
  padding: 10px 20px 10px 20px;
  border: 1px dashed #49b9de;
  box-shadow: 10px 10px 20px #b6e7d6;
  border-radius: 5px;
}
.see-all_text a{
  color: #2e6ac5;
  font-weight: bold;
}
.notice--info a {
    color: #0950bb;
}
.line-intro{
    display: block;
    margin: 1em 0;
    border: 9;
    border-radius: 2px;
    border-top: 5px solid #05A069;
    margin-top: -20px;
}

.image-makeover-1{
  width: 180px;
  height: auto;
  left: -28%;
  position: absolute;

}
.page__inner-wrap img{
    border: 0px dashed #49b9de !important;
    border-radius: 0px;
    box-shadow: 0px 0px 0px #b6e7d6;
    margin: 0px;
}
@media (min-width: 80em) {
    #main{
      margin-left: -50px !important;
      margin-top: 6em; 
    }
}
@media (min-width: 64em){
#main {
    margin-left: -50px !important;
    padding-left: 3em !important;
    padding-right: 3em !important;
}
}
.recent-post__text-social-media{
  background-color: #121b37 !important;
  color: white !important;
}
.post-catalog_list__social-media{
  background-color: #0e203d !important;
  color: white !important;

}
.see-all_text__social-media{
  background-color: #0e203d !important;
  color: white !important;
}

</style>
<hr class="line-intro">

  <!-- <img class="image-makeover-1" src="./assets/images/apple-touch-icon1.png" alt=""> -->

{% capture notice-1 %}
 ___Alacrity, noun.__ From the Latin alacritas meaning promptitude. Similar in meaning to its synonyms Velocity and Celerity, all three mean quickness in action or movement. Alacrity stresses promptness in response to a suggestion or command, cheerful and eager willingness, appropriate quickness, and in general the beginning of fast movement._
{% endcapture%}

<div class="definition">{{ notice-1 | markdownify }}</div>

{% capture notice-2 %}
<b>Most Recent Post:</b>
<ul>
{% for post in site.posts limit: 1 %}
<li><b>{{ post.date | date: "%Y-%m-%d" }}:</b> <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
{% endcapture%}

<div class="notice--sucesss recent-post__first">{{ notice-2 | markdownify }}</div>

{% capture notice-3 %}
***Life with Alacrity*** is the personal blog of [Christopher Allen](/about/), covering topics of community, decentralized identity, collective choice, online privacy, and more. Popular articles include ["The Path to Self-Sovereign Identity"](https://www.lifewithalacrity.com/article/the-path-to-self-soverereign-identity/), ["The Dunbar Number as a Limit to Group Size"](https://www.lifewithalacrity.com/article/the-dunbar-number-as-a-limit-to-group-sizes/), ["Dunbar & World of Warcraft"](https://www.lifewithalacrity.com/article/dunbar-world-of-warcraft/), and the [Community by the Numbers series](https://www.lifewithalacrity.com/tags/#community-by-the-numbers).

_Christopher is available for personal consulting engagements on decentralized digital trust, online collaboration, identity management, digital assets, smart contracts, and human-rights privacy. Please [email](mailto:ChristopherA@LifeWithAlacrity.com) if you are interested._
{% endcapture%}

<div class="notice--info about_website introduction">{{ notice-3 | markdownify}}</div>



<h3 class="recent-post__text">All Recent Posts</h3>



<ul class="post-catalog_order" style="font-weight: bold">
{% for post in site.posts limit: 7 %}
<li class="post-catalog_list"><b>{{ post.date | date: "%Y-%m-%d" }}:</b> <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>



<span class="see-all_text">See all [Posts](/posts/) <i style="color: #05A069;" class="far fa-eye"></i></span>
<br><br>
<hr class="line-intro">
<br>
<h3 class="recent-post__text recent-post__text-social-media">All Recent Social Media Posts</h3>

<ul class="post-catalog_order post-catalog_order__social-media" style="font-weight: bold">

  {% assign reversed_posts = site.tweets-md | reverse %}
  {% for post in reversed_posts limit: 7 %}
    <li class="post-catalog_list post-catalog_list__social-media">
      <b>{{ post.date | date: "%Y-%m-%d" }}:</b> <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

<span class="see-all_text see-all_text__social-media ">See all [Social Media Posts](/tweets/) <i style="color: #05A069;" class="far fa-eye"></i></span>


<!-- <h3 class="image-author">Christopher Allen's Twitter Archive</h3>
{% capture notice-5 %}
{% for image in page.images %}
    <img class="image-timeline" src="./twitterarchive/ChristopherA/tweets_media/{{ image }}" />
{% endfor %}

{% endcapture%}
<div class="twitter-timeline">{{ notice-5 | raw }}</div>
 -->


<!-- <br>
<br>
<a class="twitter-timeline" style="display: none;" data-lang="en" data-width="auto" data-height="500px" data-tweet-limit="3" data-theme="dark" href="https://twitter.com/ChristopherA?ref_src=twsrc%5Etfw">Tweets by ChristopherA </a> -->