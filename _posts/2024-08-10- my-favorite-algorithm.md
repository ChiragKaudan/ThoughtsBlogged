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

An undergraduate student of computer science has to take several algorithms courses where they are introduced to classic algorithms that efficiently solve important problems and the clever people that came up with them. This practice is often compared to an English student studying classical English literature. They study literature to better understand 
the world around us and how people of the past have voiced their opinions about it. But a student of English also writes and contributes to the conversation themselves. Algorithms should be
treated in a similar way; studying the classics is good, and it's a neat way to get early undergraduates to think about the world of (math) problems rigorously and algorithmically. But 
algorithm design is what must be studied for a student to convert their knowledge into eventually synthesizing novel solutions to problems, to contribute to the conversation themselves.

The Stable Matching Problem is certainly one of those classics but also provides a great domain to first reason with combinatorial algorithms and practice formulating problems in a mathematically precise way. It's why Kleinberg and Tardos open up their 800+ page textbook _Algorithm Design_ (a fantastic algorithms textbook, by the way) with this problem. This blog post takes inspiration from this first section of their book.

Kleinberg and Tardos spend so much inkspace talking about the history and motivations of this problem and why anybody would even want to think about the ideas 
that the problem captures before formulating a mathematical problem that is solvable via an algorithm. Within the first 10 pages of the book, they emphasize that the title of their book, and the subject we are studying, is algorithm **design** and that decision was not unintentional.

But preachy pedagogy aside, I do not (nor will I ever) have either the prose or the knowledge of Kleinberg and Tardos so this blog won't be as inspirational and eloquent as their textbook, but hopefully you get a sense of why this topic is something I'm passionate about.

## Background
The Stable Matching Problem partly originated in 1962 when mathematicians David Gale and Lloyd Shapley wondered if it was possible to design a job recruiting process that didn't devolve into chaos. They wanted a process that was **self-enforcing**. 

Imagine a group of friends, all applying to companies for jobs. An application process is going to involve these two different parties; a set of applicants and a set of companies. Each applicant will have some preference ordering/ranking of the companies they apply to and each company will have a preference ordering of applicants, once they have all been submitted. Based on their preferences, the companies are the ones that reach out to applicants to extend offers to some of their applicants, and then applicants choose which of their offers (if any) they will accept. 

This is at the core of designing an application process, the two parties involved do not function in exactly the same way; they are different and this will be important for most observations that follow, so it's good to drill home early. Gale and Shapley realized many things could go wrong with such a system if there is nothing to maintain the status quo.

### Chaotic Scenarios 

Consider the following scenario. Say one of the applicants, Ferdinand, accepts a job at the large company DiscoveryEnterprises. A couple days later, the much smaller GlobalVentures, finalizing the offers they are sending out, reaches out to Ferdinand and makes him an offer. Ferdinand actually prefers the more innovative, explorative spirit at GlobalVentures over DiscoveryEnterprises. And so he may end up retracting his acceptance of DiscoveryEnterprises and accept GlobalVentures's offer instead. DiscoveryEnterprises are now frantically looking for candidates to fill the hole that Ferdinand left, and so they reach out to one of their waitlisted applicants, Christopher, who immediately accepts their offer, ditching his arrangement to work for PioneerPathways. This situation is horrible, things can change at the last second by these seemingly unfair "outside deals" going on.

Looking at it from the other perspective, we see a situation that could be even worse. Ferdinand's friend Vasco, who has just arranged to work at PioneerPathways, hears of Ferdinand's story and wants to do the same, and so he calls up GlobalVenture and tells them that he would much rather work for them instead of PioneerPathways. He is very convincing and upon further consideration, GlobalVenture realizes that they do actually prefer Vasco to some other applicant that is already scheduled to start working at GlobalVenture. If GlobalVenture were an unethical company (which might be tautological), they would consider finding some excuse to boot that applicant out and make an offer to Vasco instead. GlobalVenture and Vasco simultaneously screw over both some other applicant and PioneerPathways with one deal that neither the applicant nor PioneerPathways had any say in.

The theme here is that we want to avoid any deals from being made outside of the application system to prevent unfairness. But applicants and especially companies will always act in self-interest, so we cannot stop these under-the-table deals from being made by simply relying on the honor system or trying to fruitlessly eliminate people from acting in self-interest. 

What if we had a system where self-interest itself is what stops the outside deals from being made in the first place? We'd prefer a system where every applicant is happy with the company they get initial offers from or where every company extends offers to candidates they'd prefer over everybody else in the field. Such a system would be self-enforcing, because let's say an applicant who has arranged to work for GlobalVenture tries to be slick and make an outside deal with DiscoveryEnterprises, but then DiscoveryEnterprises can just say "Nope, we prefer every candidate we've extended offers to over you, sorry". Or if a company reaches out to its best applicants that ended up going somewhere else and getting the response "No, I like where I am" from each of them. No outside deals can be made, such a system is stable.

Formulated a bit more precisely, the question Gale and Shapley are asking is this: Given a set of preferences for applicants and for employers, is there a way to assign applicants to employers such that for every employer $$E$$ and every applicant $$A$$ not working for $$E$$, we have at least one of the following be true
1. $$E$$ prefers every applicant they made an offer to over $$A$$
2. $$A$$ prefers her current employer over $$E$$

If these two conditions hold, the outcome will be stable. Gale and Shapley developed a neat solution to their question via an algorithm. 

## Problem Formulation

There are a bunch of complications with the scenarios we considered above. Every applicant wants one employer but employers are often looking for several applicants, and a given applicant usually does not apply to every employer. Additionally, there may be too many applicants or not enough. All these possibilities can be stripped away initially because they are not essential to the interplay between applicant and employer that we discussed above, as we'll end up seeing later. We want to strip away as many details as possible while still preserving the issues we discussed above. 

{: .box-note}
**Problem Assumptions:** We have $$n$$ applicants applying to each of $$n$$ companies and each company wants to select a single applicant. In this simplified version, we can view the problem as Gale and Shapley did, devising a system by which each of $$n$$ men and $$n$$ women can get married. The two different genders are natural analogues to applicants and employers and here everybody wants to be paired with exactly one member of the opposite gender.

Gale and Shapley studied other variants, like same-sex Stable Matching with only a single gender, since this comes up in applications, but it's mathematically a different problem. Using more math terminology, let $$M = $$ \{ $$m_1, m_2,..., m_n$$ \} be the set of men and $$W = $$ \{ $$w_1, w_2, ..., w_n$$ \} be the set of women. $$M \times W$$ denotes the cartesian product (a set of ordered pairs where the first element is a man from the set $$M$$ and the second element is a woman from the set $$W$$) of $$M$$ and $$W$$. A matching $$S$$ is a subset of $$M \times W$$ such that every man in $$M$$ and every woman in $$W$$ appear in at most one ordered pair of $$S$$. All this means is we are pairing up men and women in a way that prohibits polygamy (but not every man or woman in a matching will be paired necessarily). A perfect matching $$S'$$ is a matching where every man and every woman are in exactly one pair in $$S'$$. This is just a matching where we DO have every man or woman being in exactly one pair, so we are prohibiting polygamy and stopping any person from being single.

We need to add in the notion of preferences. Every man ranks every woman with no ties, and we say that a man $$m \in M$$ (this symbol just means a man from set $$M$$) _prefers_ a woman $$w$$ to $$w'$$ if he ranks $$w$$ higher than $$w'$$ in his _preference list_. Likewise, every woman ranks every man.

Picking up stuff from last section, given a perfect matching $$S$$, things can go wrong if we have the pair $$(m,w)$$ and $$(m',w')$$ in a matching with the property that $$m$$ prefers $$w'$$ to $$w$$ and $$w'$$ prefers $$m$$ to $$m'$$. Nothing is stopping $$m$$ and $$w'$$ from ditching their current partners and eloping. We call the ordered pair $$(m,w')$$ an _instability_ with respect to $$S$$. It's an ordered pair not in $$S$$ but each of $$m$$ and $$w'$$ prefer the other to their current partners in the matching $$S$$.

So the goal is to find a way of pairing off men and women with no instabilities. A matching is _stable_ if it is perfect and has no instabilities. Gale and Shapley's questions then become
1. Does a stable matching exist for every set of preferences?
2. Given a set of preferences, can we construct a stable matching quickly given that one exists?

I should be clear that it is not immediately obvious whether a stable matching would have to exist for a given set of preferences. This situation can become very complicated with $$n$$ different actors on each side and with so many ways to make preference lists. 

### Exploration and Examples

To make these definitions a bit more concrete, consider a simple scenario where we have a set of two men \{ $$m, m'$$ \} and two women \{ $$w, w'$$ \}. The preference lists are displayed in the table below, where the left-most column is the person whose preference list is displayed in order in the rest of the row.

| Person | Rank #1 | Rank #2 |
| :------ |:--- | :--- |
| $$m$$ | $$w$$ | $$w'$$ |
| $$m'$$ | $$w$$ | $$w'$$ |
| $$w$$ | $$m$$ | $$m'$$ |
| $$w'$$ | $$m$$ | $$m'$$ |

Here we have a situation of total agreement. The men agree on the order of the women and the women agree on the order of the men. There is a unique stable matching that exists here and that is the pairs $$(m,w)$$ and $$(m',w')$$. The other perfect matching, $$(m,w')$$ and $$(m',w)$$ would result in the instability $$(m,w)$$.

Now here's another example.

| Person | Rank #1 | Rank #2 |
| :------ |:--- | :--- |
| $$m$$ | $$w$$ | $$w'$$ |
| $$m'$$ | $$w'$$ | $$w$$ |
| $$w$$ | $$m'$$ | $$m$$ |
| $$w'$$ | $$m$$ | $$m'$$ |

The mens' preferences mesh perfectly with each other, as do the womens'. But the men and women have clashing preferences. In this case, two different stable matchings exist. The first is $$(m, w)$$ and $$(m', w')$$ where the men are as happy as possible and so none of them would want to leave their partner, creating stability. The second is $$(m, w')$$ and $$(m', w)$$ where the women are as happy as possible with their pick. This example shows us that more than one stable matching can exist, each with a different reason for why stability occurs.

## The Algorithm

Here we show that there does exist a stable matching for any set of preference lists among the men and women by giving an efficient algorithm that takes any set of preference lists and constructs a stable matching, simultaneously answering both our questions above. Here are some very important points to keep in mind (in main bullet points) and natural questions to ask or comments to make (in sub-bullet points) that come up when devising an algorithm.
- We have everybody start off as unmarried. Say we have an unmarried man $$m$$ that has an unmarried woman $$w$$ at the top of his preference list and let's say he proposes to her. We cannot immediately say that $$(m,w)$$ will be a pair in the final stable matching we are constructing, since $$w$$ may reject $$m$$ for some other man $$m'$$ that proposes to her in the future. If there is no other man that $$w$$ prefers over $$m$$ then of course we can say the pair $$(m,w)$$ will be in the final stable matching since both partners in the marriage are faithful to each other (in fact, this pair **must** be in any stable matching). But again in general, we cannot say this since the above is a special case. On the other hand, it is risky for $$w$$ to reject $$m$$ immediately, since she does not know if any man she prefers over $$m$$ will ever propose to her in the future. Because of this, it's natural to denote the pair $$(m,w)$$ in an intermediary state of **engagement**.
  - In the above situation, why can't $$w$$ herself go out and propose to some other man she ranks higher than $$m$$? Remember our discussion about the core of the application process; one of the parties (the companies/the men) are the ones who reach out and make offers (or proposals) to the other (the applicants/the women), who then have a choice between their offers. The choice for which gender corresponds to which party is arbitrary, but Gale and Shapley studied the problem with the men making the proposals (men as the companies), so that is the convention.
- Suppose now we have some men and women that are _free_ or not engaged, and others that are engaged. Based on what happened above, it's completely natural to suggest a next step: An arbitrary free man $$m$$ proposes the highest ranked woman $$w$$ that he has not proposed to yet. If $$w$$ is also free, then $$m$$ and $$w$$ become engaged to each other. Otherwise, there is some other man $$m'$$ to whom $$w$$ is engaged with, and it is then up to $$w$$ to decide which of $$m$$ and $$m'$$ rank higher on her preference list. Whichever man ranks higher is the one who will be engaged with $$w$$ and the other man becomes free.
  - We don't ever want a free man proposing to a woman that he has already proposed to, because this just means he got rejected in favor of some other man. 
- Finally we want the algorithm to terminate when nobody is free. When this happens, all engagements become marriages and no other proposals are allowed; the perfect matching that is formed is what is returned by the algorithm.
   - Notice I used the term "perfect matching" for the object, you should be asking yourself how we know this algorithm returns a perfect matching, it is not immediately obvious. In order for this algorithm to solve the problem we want it to solve, it should return a stable matching, which is even less immediately obvious to see. We will see resolutions to these questions shortly.

We can take these bullet points and make a concrete algorithm with it, described below with psuedocode. It uses coding syntax that should follow naturally from the English language (like "if" and "while") and typical, intuitive indentation.
~~~
Initially, all m in M and w in W are free.
While there is a man m who is free and has not proposed to every woman
  Choose such a man m
  Let w be the highest-ranked woman in m's preference list to which m has not yet proposed to
  If w is free then
    (m,w) become engaged
  Else w is engaged to some other man m'
    If w prefers m' to m then
      m remains free
    Else
      (m,w) become engaged
      m' becomes free
    ENDIF
  ENDIF
ENDWHILE
Return set S of engaged pairs
~~~

Again, one should think carefully about whether the set $$S$$ returned by this algorithm is a stable matching. We prove this now using observations about the algorithm.

## Algorithm Analysis

The Stable Matching Problem nicely displays common themes in algorithm design and is rooted in practical concerns from which a learner must spend some effort to formulate the setup precisely enough to ask concrete questions. They are then forced think about algorithms to solve those questions, proving non-obvious properties about the structure of the problem along the way. This cuts to the heart of what algorithm design is and showcases both the rigor and precision of math combined with the ingenuinty and creative freedom of design.

As in common in combinatorial problems, we considered discrete "moves" we could make and from there derived observations that were useful in studying properties about the underlying problem. We then used these properties and to eventually devise an algorithm and prove it works correclty.
