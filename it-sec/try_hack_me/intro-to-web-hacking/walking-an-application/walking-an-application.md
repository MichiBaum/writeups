---
Author: Michael Baumberger
title: Walking An Application
parent: Intro to web hacking
ancestor: Try Hack Me Writeups
---

# Walking An Application

## Task 3 | Viewing The Page Source

Search through the html code and you will see a comment at the top with a endpoint to the side **'/new-home-beta'.
If you open this site you find a flag.**

In a a-tag you also find a **link to the site '/secret-page' with a flag**.

If you look from where the resources come you se that they all come from 'http://10-10-149-178.p.thmlabs.com/assets'
and if you **navigate there you find the file flag.txt** and you can open it.

On the home page you find a link to the framework page and in the 'Change Log' navigation you find a reference to the file '/tmp.zip'.
And because the page is on a old framework version we can **receive the zip with the url 'https://10-10-149-178.p.thmlabs.com/tmp.zip'**.
In the tmp.zip you fnid the file flag.txt with the flag.

In the navigation Documentation you find the page '/thm-framework-login' and the user admin and password admin.
You can now **navigate to 'https://10-10-149-178.p.thmlabs.com/thm-framework-login' and login with the user and password**.
But you dont have to input the flag anywhere on TryHackMe.

## Task 4 | Developer Tools - Inspector

On the Acme IT Support website, click into the news section, where you’ll see three news articles.
The first two articles are readable, but the third has been blocked with a floating notice above the content stating you have to be a premium customer to view the article.

Inspect the page and click on the div with the class 'premium-customer-blocker' and **change the 'display: block' to 'display: none'**.
Now you see the flag.

## Task 5 | Developer Tools - Debugger

On the page '/contact' you see asset with the name flash.min.js.
You can format the code, **set a breakpoint on line 48 'flash\['remove'\]()'** and you will see the flag.

## Task 6 | Developer Tools - Network

If you send the contact form with some dummy data you will see in the **network tab that it is sent to the page 'https://10-10-149-178.p.thmlabs.com/contact-msg'.
If you navigate to this site you find a flag**.