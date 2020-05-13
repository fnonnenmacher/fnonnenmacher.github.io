---
layout: post
title:  "Architecture Analysis of Micronaut"
date:   2020-05-01 20:00:00 +0200
categories: Java Micronaut Architecture Microservices
---

In Spring 2020 I attended the [Software Architecture class](https://desosa2020.netlify.app/about.html) at TU Delft. During this class, my team and I analyzed the architecture of Micronaut and wrote blog posts about it. In the following, I summarize our results.
 
<!--
I have a deep passion for Software architecture and are therefore reading a lot about it. For example, I am following different architects on Twitter and regularly watch their conference talks. 
In practice, software architecture means for me finding pragmatic solutions which fulfill the quality attributes. This does not need to be the most advanced technical solution. Therefore, my expectations to this class where quite high, and I also was a bit worried if an academic lecture is a good possibility for me to extend my knowledge. Thankfully, this class was no only focusing on concepts, but also give us guidance to analyze an available open source project. With my short working experience, I obviously have not seen many large software projects and therefore could learn a lot.-->

Together with [three other students](https://desosa2020.netlify.app/projects/micronaut/), we decided to analyze [Micronaut](http://micronaut.io/). Micronaut is a full-stack JVM framework for building modern applications especially Microservices. For me, with my Java background and strong interests in developing cloud-based applications, this was the ideal project to analyze.

Due to the Corona circumstances, we recorded our final presentation which summarizes our work well. _(I am talking from 2:21 to 5:55)_.
 
<iframe width="560" style="margin: 0 auto;display: block;" height="315" src="https://www.youtube.com/embed/kvqNdtkfRkQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I like especially [the blogpost](https://desosa2020.netlify.app/projects/micronaut/2020/03/17/from-vision-to-architecture.html) which shows inspired by the [Arc42 template](https://docs.arc42.org/home/) different architectural views on the system. We focus especially on the different components of the system and how they interact during Micronaut's _Dependency Injection_ implementation.

For the [last blog post](https://desosa2020.netlify.app/projects/micronaut/2020/04/09/microservices-in-detail.html), we changed our perspective and verified Micronaut's promise to be an ideal microservice framework. We used Eberhard Wolff's book [_Microservices: Flexible Software Architecture_](https://www.oreilly.com/library/view/microservices-flexible-software/9780134650449/) as a basis and showed which solutions Micronaut provides for microservice related challenges.

During the class, we were encouraged to participate in the open-source development of our chosen project. From the knowledge I gained through the analysis I was able to provide two contributions:

* [Property loader for cloudfoundry environment variables](https://github.com/micronaut-projects/micronaut-core/pull/2928)
* [Configuration of Consul related metadata](https://github.com/micronaut-projects/micronaut-core/pull/3038)

