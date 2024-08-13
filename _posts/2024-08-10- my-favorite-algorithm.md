---
layout: post
title: My Favorite Algorithm
subtitle: The Gale-Shapley Algorithm as a brilliant example of the art of algorithm design 
tags: [test]
comments: true
mathjax: true
author: Chirag Kaudan
---

{: .box-success}
The Gale-Shapley Algorithm not only masterfully and efficiently solves a problem (the Stable Matching Problem), but serves as a wonderful example of the process of algorithm design. I have written about this algorithm in my grad school apps, so this really does go beyond the algorithm itself for me. In this post I focus on not just the technicalities of the problem/algorithm but also computer science pedagogy.

An undergraduate student of computer science has to take several algorithms courses where they are introduced to algorithms that efficiently solve important problems and the clever people that came up with them. This practice is often compared to an English student studying classical English literature. They study literature to better understand 
the world around us and how people of the past have voiced their opinions about it. But a student of English also writes and contributes to the conversation themselves. Algorithms should be
treated in a similar way; studying the classics is good, and it's a neat way to get early undergraduates to think about the world of (math) problems rigorously and algorithmically. But 
algorithm design is what must be studied for a student to convert their knowledge into eventually synthesizing novel solutions to problems, to contribute to the conversation themselves.

The Stable Matching Problem nicely displays common themes in algorithm design and is rooted in practical concerns from which a learner must spend some effort to formulate the setup precisely enough to ask concrete questions. They are then forced think about algorithms to solve those questions, proving non-obvious properties about the structure of the problem along the way. This cuts to the heart of what algorithm design is and showcases both the rigor and precision of math combined with the ingenuinty and creative freedom of design. It's no wonder why Kleinberg and Tardos
open up their 800+ page textbook _Algorithm Design_ (a fantastic algorithms textbook, by the way) with this problem. 

Kleinberg and Tardos spend so much inkspace talking about the history and motivations of this problem and why anybody would even want to think about the ideas 
that the problem captures before formulating a mathematical problem that is solvable via an algorithm. Within the first 10 pages of the book, they emphasize 
that the title of their book, and the subject we are studying, is algorithm **design** and that decision was not unintentional.

But preachy pedagogy aside, I do not (nor will I ever) have either the prose or the knowledge of Kleinberg and Tardos so this blog won't be as inspirational and eloquent as their textbook, but hopefully you get a sense of why this topic is something I'm passionate about.

## Background
The Stable Matching Problem partly originated in 1962 when mathematicians/economists David Gale and Lloyd Shapley wondered if it was possible to design a job recruiting process that didn't devolve into chaos. They wanted a process that was **self-enforcing**. 

Imagine a group of friends, all applying to companies for jobs. An application process is going to involve these two different parties; a set of applicants and a set of companies. Each applicant will have some preference ordering/ranking of the companies they apply to and each company will have a preference ordering of applicants, once they have all been submitted. Based on their preferences, the companies are the ones that reach out to applicants to extend offers to some of their applicants, and then applicants choose which of their offers (if any) they will accept. 

This is at the core of designing an application process, the two parties involved do not function in exactly the same way; they are different and this will be important for most observations that follows, so it's good to drill home early. Gale and Shapley realized many things could go wrong with such a system if there is nothing to maintain the status quo.


Consider the following scenario. Say one of the applicants, Ferdinand, accepts a job at the large DiscoveryEnterprises. A couple days later, the much smaller GlobalVentures reaches out to Ferdinand and makes him an offer. Ferdinand actually prefers the smaller, innovative spirit at GlobalVentures over DiscoveryEnterprises. And so he may end up retracting his acceptance of DiscoveryEnterprises and accept GlobalVentures's offer instead. DiscoveryEnterprises are now frantically looking for candidates to fill the hole that Ferdinand left their company, and so they reach out to one of their waitlisted applicants, Christopher, who immediately accepts their offer, ditching his arrangement to work for PioneerPathways. This situation is horrible, things can change at the last second by these seemingly unfair "outside deals" going on.

Looking at it from the other perspective, we see a situation that could be even worse. Ferdinand's friend Vasco, who has just arranged to work at PioneerPathways, hears of Ferdinand's story and wants to do the same, and so he calls up GlobalVenture and tells them that he would much rather work for them instead of PioneerPathways. He is very convincing and upon further consideration, GlobalVenture realizes that they do actually prefer Vasco to some other applicant that is already scheduled to start working at GlobalVenture. If GlobalVenture were an unethical company (which might be tautological), they would consider finding some excuse to boot that applicant out and make an offer to Vasco instead. GlobalVenture and Vasco simultaneously screw over both some other applicant and PioneerPathways with one deal that neither the applicant nor PioneerPathways had any say in.

The theme here is that we want to avoid any deals from being made outside of the application system to prevent unfairness. But applicants and especially companies will essentially always act in self-interest, so we cannot stop these under-the-table deals from being made by simply relying on the honor system or trying to fruitlessly eliminate people from acting in self-interest. 

What if we had a system where self-interest itself is what stops the outside deals from being made in the first place? We'd prefer a system where every applicant is happy with the company they get initial offers from or where every company extends offers to candidates they'd prefer over everybody else in the field. Such a system would be self-enforcing, because let's say an applicant who has arranged to work for GlobalVenture tries to be slick and make an outside deal with DiscoveryEnterprises, but then DiscoveryEnterprises can just say "Nope, we prefer every candidate we've extended offers to over you, sorry". Or if some company reaches out to its best applicants that ended up going somewhere else and getting the response "No, I like where I am". No outside deals can be made, such a system is stable.

