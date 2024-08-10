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

But preachy pedagogy aside, I could never have the either the prose or the knowledge of Kleinberg and Tardos (seriously, for a flat out **better** coverage of this algorithm, read the first 10 pages of their book!) so this blog post won't do such a pretty algorithm justice.
