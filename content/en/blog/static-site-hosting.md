+++
title = "Firebase vs Github vs Netlify vs Render vs Clouflare"
subtitle = "Which is best for Free Static Site Hosting?"
banner = "/img/firebasenetlify.jpg"
description = "We tried different static site hosts and liked Cloudflare the best. See the pros and cons of each."
tags = ['Static Site Hosting']
date = 2021-11-15
+++

One of the early mistakes that we made as a startup was to use one system on one server for both web app and website. 

We thought that this would be sustainable, especially since we relied on mostly on interns and volunteers. Everyone would learn one framework and work on one repository and one branch.

The mistake exposed itself when our server actually got website views and web app users. This overloaded our server causing it to either crash or incur charges when it auto-scaled to accomodate the traffic. 

The problem is that we had a lot of content waiting to be written for [Superphysics](https://superphysics.one) and [Pantrynomics](https://pantrypoints.com) which were the theory behind the apps. The website would use resources that were supposed to be for the web app users. In addition, our chosen framework was built for web apps and not for CMS or websites. 

<br>

## Solution: Static Site Hosting

I first heard of static site hosting when Github came out with Github Pages. Back then, static site generators were limited and all you had was HTML and Javascript. From 2015, Netlify popularized the concept of Jamstack, which added API -- Javascript + API + Markup. This is commonly seen in chat widgets, comment forms, and 'like' buttons on a webpage.  

Back in 2015, we had very little content, so we put it on [wordpress.com](http://socioecons.wordpress.com) as drafts. Over the years, we refined the content and gradually put it as styled pages in our web app which acted like an internal mini-CMS.

The need to keep costs low, while improving SEO and managing content, led us to separate the content to static site generators. We forked our mini-CMS as a [blogging feature](https://pantrypoints.com/beacon) in Pantry. 

We chose Hugo as the easiest and fastest static site generator. We then deployed our Hugo sites on different hosts that didn't require credit cards to see the performance and maintainability:

Host | Pros | Cons 
--- | --- | ---
Github Pages | easy to deploy | content must be public
Cloudflare Pages | easy to deploy, integrates with Cloudflare | content must be public
Firebase | easiest to deploy, content isn't public, creates Google Analytics | needs to install Firebase console
Netlify | Nice UI | automated deploy makes git submodules complicated, content must be public
Render | Nice UI  | lets you set up paid services and incur charges 

<br>

[Firebase](https://firebase.com) is the easiest to deploy -- just ```hugo && firebase deploy```. However, this might be a problem if your internet connection is slow.

[Github Pages](https://github.com) is great even with slow internet connection -- just take the public files and push.

[Cloudflare Pages](https://pages.cloudflare.com/) deploys automatically and makes it easy to configure DNS. The site loads a bit faster thanks to Cloudflare's CDN

[Netlify](https://netlify.com) automates the deploy just like Cloudflare Pages, which is great for adding content. But this is a problem if you want to change or edit the theme.

[Render](https://render.com) is easier to deploy than Netlify with Hugo themes, but be careful about creating services as they let the meter run even after the trial period! I actually made this mistake when I thought that a certain Javascript framework was categorized as a static site generator (it wasn't) and so I let it run, thinking it would be free. (Thanks to John of Render for waiving the fee.) 

<br>

## Clouflare is Great

We decided to use Cloudflare because it's fast and is easy to deploy. Unlike the others, it does not have bandwidth limits. Instead, it limits the deploys to 500 per month. 

For people without coding skills, there are services like [forestry.io](https://forestry.io) that allow users to add and edit content just like any CMS.

<!--  
For now, the plan* is to start with Github Pages and then move on to Firebase when the site visitors increase. Firebase has [a calculator](https://firebase.google.com/pricing#blaze-calculator) to estimate the cost based on GB used.  -->
