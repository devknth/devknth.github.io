---
layout: post
title: From URL to Interactive
---

Imagine, if you will, that you’re behind the wheel of a gorgeous 1957 Chevy Bel Air convertible, making your way across the desert on a wide open highway. The sun is setting, so you’ve got the top down, naturally. The breeze caresses your cheek like a warm hand as your nose catches a faint whiff of … What was that?

The car lurches and chokes before losing all power. You coast, ever more slowly, to a stop. There’s steam rising from the hood. Oh jeez. What the heck just happened?

You reach down to pop the hood, and open the door. Getting out, you make your way around to the front of the car. As you release the latch and lift the bonnet, you get blasted in the face with even more steam. You hope it’s just water.

Looking around, it’s clear the engine has overheated, but you have no idea what you’re looking at. Back home you’ve got a guy who’s amazing with these old engines, but you fell in love with the luxurious curves, the fins, the plush interior, the allure of the open road.

A tumbleweed rolls by. In the distance a buzzard screeches.

### What’s happening under the hood?
Years ago, my colleague Molly Holzschlag used a variant of this story to explain the importance of understanding our tools. When it comes to complex machines like cars, knowing how they work can really get you out of a jam when things go wrong. Fail to understand how they work and you could end up, well, buzzard food.

At the time, Molly and I were trying to convince folks that learning HTML, CSS, and JavaScript was more important than learning Dreamweaver. Like many similar tools, Dreamweaver allowed you to focus on the look and feel of a website without needing to burden yourself with knowing how the HTML, CSS, and JavaScript it produced actually worked. This analogy still applies today, though perhaps more so to frameworks than WYSIWYG design tools.

If you think about it, our whole industry depends on our faith in a handful of “black boxes” few of us fully understand: browsers. We hand over our HTML, CSS, JavaScript, images, etc., and then cross our fingers and hope they render the experience we have in our heads. But how do browsers do what they do? How do they take our users from a URL to a fully-rendered and interactive page?

To get from URL to interactive, we’ve assembled a handful of incredibly knowledgeable authors to act as our guides. This journey will take place in four distinct legs, delivered over the course of a few weeks. Each will provide you with details that will help you do your job better.

### Leg 1: Server to Client
Ali Alabbas understands the ins and outs of networking, and he kicks off this journey with a discussion of how our code gets to the browser in the first place. He discusses how server connections are made, caching, and how Service Workers factor into the request and response process. He also discusses the “origin model” and how to improve performance using HTTP2, Client Hints, and more. Understanding this aspect of how browsers work will undoubtedly help you make your pages download more quickly.

[Read the article](/server-to-client)

### Leg 2: tags to DOM
In the second installment, Travis Leithead—a former editor of the W3C’s HTML spec—takes us through the process of parsing HTML. He covers how browsers create trees (like the DOM tree) and how those trees become element collections you can access via JavaScript. And speaking of JavaScript, he’ll even get into how the DOM responds to manipulation and to events, including touch and click. Armed with this information, you’ll be able to make smarter decisions about how and when you touch the DOM, how to reduce Time To Interactive (TTI), and how to eliminate unintended reflows.

Read the article

### Leg 3: braces to pixels
Greg Whitworth has spent much of his career in the weeds of browsers’ CSS mechanics, and he’s here to tell us how they do what they do. He explains how CSS is parsed, how values are computed, and how the cascade actually works. Then he dives into a discussion of layout, painting, and composition. He wraps things up with details concerning how hit testing and input are managed. Understanding how CSS works under the hood is critical to building resilient, performant, and beautiful websites.

Read the article

### Leg 4: var to JIT
One of JavaScript’s language designers, Kevin Smith, joins us for the final installment in this series to discuss how browsers compile and execute our JavaScript. For instance, what do browsers do when tearing down a page when users navigate away? How do they optimize the JavaScript we write to make it run even faster? He also tackles topics like writing code that works in multiple threads using workers. Understanding the inner processes browsers use to optimize and run your JavaScript can help you write code that is more efficient in terms of both performance and memory consumption.

Read the article

### Let’s get going
I sincerely hope you’ll join us on this trip across the web and into the often foggy valley where browsers turn code into experience.

by Aaron Gustafson October 25, 2018