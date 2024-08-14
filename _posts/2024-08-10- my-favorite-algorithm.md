---
layout: post
title: My Favorite Algorithm
subtitle: The Gale-Shapley Algorithm as a brilliant example of the art of algorithm design 
tags: [test, math]
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

Imagine a group of friends, all applying to companies for jobs. An application process is going to involve these two different parties; a set of applicants and a set of companies. Each applicant will have some preference ordering/ranking of the companies they apply to and each company will have a preference ordering of applicants, once all applications have been submitted. Based on their preferences, the companies are the ones that reach out to extend offers to some of their applicants, and then applicants choose which of their offers (if any) they will accept. 

This is at the core of designing an application process, the two parties involved do not function in exactly the same way; they are different and this will be important for most observations that follow, so it's good to drill home early. Gale and Shapley realized many things could go wrong with such a system if there is nothing to maintain the status quo.

### Chaotic Scenarios 

Consider the following scenario. Say one of the applicants, Ferdinand, accepts a job at the large company DiscoveryEnterprises. A couple days later, the much smaller GlobalVentures, finalizing the offers they are sending out, reaches out to Ferdinand and also makes him an offer. Ferdinand actually prefers the more innovative, explorative spirit at GlobalVentures over DiscoveryEnterprises. And so he may end up retracting his acceptance of DiscoveryEnterprises and accept GlobalVentures's offer instead. DiscoveryEnterprises are now frantically looking for candidates to fill the hole that Ferdinand left, and so they reach out to one of their waitlisted applicants, Christopher, who immediately accepts their offer, ditching his arrangement to work for PioneerPathways. This situation is horrible, things can change at the last second by these seemingly unfair "outside deals" going on.

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

Gale and Shapley studied other variants, like same-sex Stable Matching with only a single gender, since this comes up in applications, but it's mathematically a different problem. Using more math terminology, let $$M = $$ \{ $$m_1, m_2,..., m_n$$ \} be the set of men and $$W = $$ \{ $$w_1, w_2, ..., w_n$$ \} be the set of women. $$M \times W$$ denotes the cartesian product (a set of ordered pairs where the first element is a man from the set $$M$$ and the second element is a woman from the set $$W$$) of $$M$$ and $$W$$. A matching $$S$$ is a subset of $$M \times W$$ such that every man in $$M$$ and every woman in $$W$$ appear in at most one ordered pair of $$S$$. All this means is we are pairing up men and women in a way that prohibits polygamy (but not every man or woman in a matching will have a partner necessarily). A perfect matching $$S'$$ is a matching where every man and every woman are in exactly one pair in $$S'$$. This is just a matching where we DO have every man or woman being in exactly one pair, so we are prohibiting polygamy and stopping any person from being single.

We need to add in the notion of preferences. Every man ranks every woman with no ties, and we say that a man $$m \in M$$ (this symbol just means a man from set $$M$$) _prefers_ a woman $$w$$ to $$w'$$ if he ranks $$w$$ higher than $$w'$$ in his _preference list_. Likewise, every woman ranks every man.

Picking up stuff from last section, given a perfect matching $$S$$, things can go wrong if we have the pair $$(m,w)$$ and $$(m',w')$$ in a matching with the property that $$m$$ prefers $$w'$$ to $$w$$ and $$w'$$ prefers $$m$$ to $$m'$$. Nothing is stopping $$m$$ and $$w'$$ from ditching their current partners and eloping. We call the ordered pair $$(m,w')$$ an _instability_ with respect to $$S$$. It's an ordered pair not in $$S$$ but each of $$m$$ and $$w'$$ prefer the other to their current partners in the matching $$S$$.

So the goal is to find a way of pairing off men and women with no instabilities. A matching is _stable_ if it is perfect and has no instabilities. Gale and Shapley's questions then become
1. Does a stable matching exist for every set of preferences?
2. Given a set of preferences, can we construct a stable matching quickly given that one exists?

I should be clear that it is not immediately obvious whether a stable matching would have to exist for a given set of preferences. This situation can become very complicated with $$n$$ different actors on each side and with so many ways to make preference lists. 

### Exploration and Examples

To make these definitions a bit more concrete, consider a simple scenario where we have a set of two men \{ $$m, m'$$ \} and two women \{ $$w, w'$$ \}. The preference lists are displayed in the table below, where the left-most column is the person whose preference list is being displayed in order across the row.

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

Here we show that there does exist a stable matching for any set of preference lists among the men and women by giving an efficient algorithm that takes any set of preference lists and constructs a stable matching, simultaneously answering both our questions above. Here are some very important points to keep in mind (in main bullet points) and some natural questions to ask or comments to make (in sub-bullet points) that come up when devising an algorithm.
- We have everybody start off as unmarried. Say we have an unmarried man $$m$$ that has an unmarried woman $$w$$ at the top of his preference list and let's say he proposes to her. We cannot immediately say that $$(m,w)$$ will be a pair in the final stable matching we are constructing, since $$w$$ may reject $$m$$ for some other man $$m'$$ that proposes to her in the future. If there is no other man that $$w$$ prefers over $$m$$ then of course we can say the pair $$(m,w)$$ will be in the final stable matching since both partners in the marriage are faithful to each other (in fact, this pair **must** be in any stable matching). But again in general, we cannot say this since the above is a special case. On the other hand, it is risky for $$w$$ to reject $$m$$ immediately, since she does not know if any man she prefers over $$m$$ will ever propose to her in the future. Because of this, it's natural to denote the pair $$(m,w)$$ in an intermediary state of **engagement**.
  - In the above situation, why can't $$w$$ herself go out and propose to some other man she ranks higher than $$m$$? Remember our discussion about the core of the application process; one of the parties (the companies/the men) are the ones who reach out and make offers (or proposals) to the other (the applicants/the women), who then have a choice between their offers. The choice for which gender corresponds to which party is arbitrary, but Gale and Shapley studied the problem with the men making the proposals (men as the companies), so that is the convention.
- Suppose now we have some men and women that are _free_ or not engaged, and others that are engaged. Based on what happened above, it's completely natural to suggest a next step: An arbitrary free man $$m$$ proposes to the highest ranked woman $$w$$ that he has not proposed to yet. If $$w$$ is also free, then $$m$$ and $$w$$ become engaged to each other. Otherwise, there is some other man $$m'$$ to whom $$w$$ is engaged with, and it is then up to $$w$$ to decide which of $$m$$ and $$m'$$ rank higher on her preference list. Whichever man ranks higher is the one who will be engaged with $$w$$ and the other man becomes free.
  - We don't ever want a free man proposing to a woman that he has already proposed to, because this just means he got rejected in favor of some other man. 
- Finally we want the algorithm to terminate when nobody is free. When this happens, all engagements become marriages and no other proposals are allowed; the perfect matching that is formed is what is returned by the algorithm.
   - Notice I used the term "perfect matching" for the object, you should be asking yourself how we know this algorithm returns a perfect matching, it is not immediately obvious. In order for this algorithm to solve the problem we want it to solve, it should return a stable matching, which is even less immediately obvious to see. We will see resolutions to these questions shortly.

We can take these bullet points and make a concrete algorithm with it, described below with psuedocode. It uses coding syntax that should follow naturally from the English language (like "if" and "while") and typical, intuitive indentation.

~~~
Gale-Shapley Algorithm
________________________________________
Initially, all m \in M and w \in W are free.
While there is a man m who is free and has not proposed to every woman
  Choose such a man m
  Let w be the highest-ranked woman in m's preference list to which m has not yet proposed
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

Again, one should think carefully about whether the set $$S$$ returned by this algorithm is a stable matching. We prove this now using observations about the algorithm. One should also note that this algorithm is underspecified... we simply choose a free man $$m$$ that has not proposed to every woman yet somehow without specifying how. Therefore, different choices made characterize different executions of the Gale-Shapley algorithm, which is a good thing to keep in mind.

## Algorithm Analysis

Consider an execution of this algorithm from the point of view of a woman $$w$$. She starts off free until some man $$m$$ proposes to her, at which point her and $$m$$ become engaged. Later on in the execution of the algorithm, some free man $$m'$$ comes along and proposes to $$w$$. There are only two possibilities here; one is that $$m'$$ remains free as $$w$$ prefers her current partner to $$m'$$ and the other is that $$m$$ becomes free, being ditched for a partner that $$w$$ prefers over $$m$$. In both cases, $$w$$ remains engaged and not only that, the ranking of the partners she becomes engaged to can only increase. Assuming she has already been proposed to at least once, the ranking of her partners never falls as she always remains engaged and always has the option to reject proposals from men that are lower rated than her current partner.

{: .box-note}
**Observation 1:** $$w$$ remains engaged from when she receives her first proposal and the sequence of her partners throughout the execution can only get better for her.


This is certainly not true for the men. A man $$m$$ is free _until_ he proposes to the highest ranked woman on his list that he has not yet proposed to. He may or may not become engaged, this is up to the woman. As time passes, he may switch back and forth between being free and being engaged at a moment's notice. 

{: .box-note}
**Observation 2:** The sequence of women $$m$$ proposes to gets worse in terms of $$m$$'s preference list as time goes on.

It seems like this situation sucks for the men and the women have a lot of power to pick and discard men given the information of proposals coming their way. Observation 1 is very comforting for a woman in this setup, as it intuitively seems like the longer you remain free in the run of the algorithm, the worse your potential partners get. We will come back to this seeming phenomenon of "unfairness", but by then we will have a more complete understanding and our intuition may not tell us the same thing! 

But we're getting ahead of ourselves, we have to show that the algorithm actually terminates (pro-tip, very important!!). 

{: .box-note}
**Proposition 3:** The Gale-Shapley Algorithm terminates after at most $$n^2$$ iterations of the while loop.

**Proof:** We're trying to upper bound the running time of an algorithm here, and one way we could do that is to find some measure of progress. We want a precise way of being able to say that each step taken by the algorithm brings it closer to termination. Each iteration of the while loop of our algorithm consists of a man proposing to a woman that he has never proposed to for the only time. If we let $$P(k)$$ be the number of pairs $$(m,w)$$ such that $$m$$ has proposed to $$w$$ by the end of iteration $$k$$, we see that $$P(k+1)$$ must be strictly greater than $$P(k)$$. But there are only $$n^2$$ pairs of men and women, so $$P$$ can only increase by at most $$n^2$$ over the course of the algorithm. Since every man and woman start off as free, $$P(0) = 0$$ and thus we know that after at most $$n^2$$ iterations the algorithm terminates. 

$$\tag*{$\blacksquare$}$$

**Proof commentary**: In the proof, $$m$$ and $$w$$ seen in the definition of $$P(k)$$ are not referring to specific men and women, we are considering all pairs of men and women in which the man has proposed to the woman by the end of the $$k$$th iteration. If, for example, we were considering a specific man $$m$$ then it is certainly not true that the value of $$P(k+1)$$ is always greater than the value of $$P(k)$$, since this would require a specific man to keep making proposals one iteration after the other and the man making the proposal on any given iteration is completely arbitrary. 

This idea of finding a measure of progress is interesting, and even for this algorithm there are many measures you could propose that would not work. We need something that strictly increases in each iteration so quantities like the number of free individuals and the number of engaged pairs don't work since they can stay constant from one iteration to the next. There are still arguments that one could make with these quantities, but they won't fall through as easily as above to prove your upper bound on the max number of iterations.

Next we establish that the set $$S$$ returned by the algorithm is indeed a perfect matching. Intuitively, we don't want a man to run out of women to propose to on his preference list. Observe that in the algorithm, the while loop terminates if for every man $$m$$, either $$m$$ is not free or $$m$$ has proposed to every woman. We want to prove that actually, the only way the while loop exits is when no free man exists, in which case we certainly know that no free woman could exist since the returned set is a matching in which every man is involved, resulting in a perfect matching (the returned set is a matching since only a free man can propose meaning a man could only be married to at most one woman and women always choose one man over others proposing to her, meaning a woman could only be married to at most one man). As it stands, we have not proved this and for all we know the while loop **could** exit with a free man that simply has proposed to every woman. So if we can prove the following, we are done via the above logic.


{: .box-note}
**Proposition 4:** If $$m$$ is free at some point in the execution of the algorithm, there is a woman to whom he has not yet proposed.

**Proof**: For contradiction, suppose at some point there exists a man $$m$$ who is free and has proposed to every woman. By observation 1, all women are thus currently engaged to some man. The set of engaged pairs forms a matching (by the previous paragraph) and there are $$n$$ women, so since in a matching nobody is allowed to have more than one partner, there must be at least $$n$$ engaged men. But there are only $$n$$ men total, and $$m$$ is free, so this is a contradiction.

$$\tag*{$\blacksquare$}$$

This proof might seem like overkill for such a simple idea but since this post contains my pedagogical opinions, dislpays the process of careful algorithm design and rigorous math, and is not written for a mathematical audience, it's important to be very careful. For example, for one of the previous paragraphs I could have simply said "Since we have a bipartite graph with equal sized parts $$U$$ and $$W$$, it suffices to show that a matching saturates all of $$U$$", but that's neither instructive nor elucidating for certain audiences. This is a great way playground to start messing with combinatorial definitions and seeing how much precision needs to be involved.

Now we can easily show that $$S$$ is a perfect matching.

{: .box-note}
**Proposition 5:** The returned set $$S$$ is a perfect matching. 

**Proof:** The set of engaged pairs are always a matching. Suppose for contradiction that the algorithm returns with a free man $$m$$. $$m$$ must have proposed to every woman since this is required for termination given that $$m$$ is free, but this contradicts proposition 4 which states that there cannot exist a free man who has proposed to every woman. Thus the algorithm can never terminate with a free man and so the set of engaged pairs returned at termination, $$S$$, is a perfect matching.

$$\tag*{$\blacksquare$}$$

As stated above, we are eliminating the possibility of a free man at termination by squeezing the 2nd condition of while loop termination (the "if a man $$m$$ has proposed to every woman" part) by proving that if this happens, it must be the case that $$m$$ is also not free and therefore no free man escapes the algorithm. Which we then combine with the fact that the set of engaged pairs is always a matching to prove $$S$$ is a perfect matching. This precise progression of statements and observations are pretty neat when you slow down the pace and break it down as much as I have here (but probably infuriatingly slow for some readers).

### Proving That $$S$$ is a Stable Matching 

We've finally built up to the meat of proving the algorithm is correct, verifying that the set $$S$$ returns is in fact a stable matching. With all the observations and propositions we've made leading up to this, the proof becomes much more straightforward. 

Intuitively, no instability $$(m,w')$$ could exist with respect to $$S$$, because of the way that men propose in the algorithm. $$m$$ would have proposed to $$w'$$ before proposing to the partner that he ended up with, call her $$w$$, and thus $$w'$$ must have rejected him in favor of some man $$m''$$ because $$m$$ and $$w'$$ did not end up with each other. We don't know if $$w'$$ ended up with $$m''$$, but we do know that whoever she ended up with must have ranked at least as high as $$m''$$ (and consequently, higher than $$m$$) on the preference list of $$w'$$. Essentially, women are happy because the algorithm ensures they end up with the best partner that proposed to them during the execution of the algorithm, whereas men are stuck with the partners they end up with because every woman they prefer to their current partner must have rejected them in favor for somebody else at some point in time.

{: .box-warning}
**Theorem 6:** Consider an execution of the Gale-Shapley algorithm that returns a set of pairs $$S$$. Then $$S$$ is a stable matching.

**Proof:** We already know that $$S$$ is a perfect matching due to observation 5 so we simply need to show that no instabilities with respect to $$S$$ exist. We assume there does exist an instability with respect to $$S$$ and derive a contradiction. Recall that such an instability involves two pairs $$(m,w)$$ and $$(m',w')$$ in $$S$$ with the properties that $$m$$ prefers $$w'$$ to $$w$$ and $$w'$$ prefers $$m$$ to $$m'$$. 

In the execution of the algorithm that leads to producing set $$S$$, the last proposal $$m$$ made must have been to $$w$$ by definition. But $$m$$ must have proposed to $$w'$$ before he proposed to $$w$$, since $$m$$ prefers $$w'$$ to $$w$$. Therefore, he must have been rejected by $$w'$$ in favor of some other man $$m''$$. Because $$w'$$ ended up with $$m'$$ as a final partner, either $$m'' = m'$$ or by observation 1, $$w'$$ prefers $$m'$$ over $$m''$$. In either case, we know $$w'$$ prefers $$m'$$ over $$m$$, because there are no ties allowed in rankings (our ranking for men and women is a strict totally ordered relation), which contradicts the assumption that $$w'$$ prefers $$m$$ over $$m'$$. It now follows that $$S$$ is a stable matching.

$$\tag*{$\blacksquare$}$$ 

## Further Considerations
We have just proven the big conclusion through a series of intermediate propositions and observations, but now we turn to some of the comments we made on those intermediate propositions themselves. They turn out to be just as interesting, if not more interesting, than this main theorem.

### Life is Unfair

Do you remember that example that was brought up where multiple stable matchings could exist? It was this example, where two stable matchings exist; $$(m,w)$$ and $$(m',w')$$ or $$(m,w')$$ and $$(m',w)$$. 

| Person | Rank #1 | Rank #2 |
| :------ |:--- | :--- |
| $$m$$ | $$w$$ | $$w'$$ |
| $$m'$$ | $$w'$$ | $$w$$ |
| $$w$$ | $$m'$$ | $$m$$ |
| $$w'$$ | $$m$$ | $$m'$$ |

In any execution of the Gale-Shapley algorithm, $$m$$ will become engaged to $$w$$ and $$m'$$ will become engaged to $$w'$$ (in some order), and things just stop there. The other stable matching, where women are most happy, cannot be produced from any execution of the Gale-Shapley algorithm.
This is strange, the last time we discussed "unfairness" and which gender ends up worse off it seemed like the men were getting screwed over by the algorithm but here we just said no execution of Gale-Shapley results in the desired result for women. I told you we would come back to this fairness phenomenon. The root of this unfairness is the fact that some stable matchings are not attainable with Gale-Shapley (and some others aren't attainable by any simple algorithms like Gale-Shapley at all).

### But Why is Life Unfair?
When and why does this occur? When the men's preferences mesh perfectly with each other (no two men have the same woman ranked first in their lists) then in every run of Gale-Shapley, every man will get their top preference without any regard to the womens' preferences. In the case where the womens' preferences completely clash with the mens', this is the worst case for the women. This example we just gave shows us that somebody is going to be unhappy in the world. Men are unhappy when women propose and women are unhappy when men propose. But this is only in the case where the men's preferences mesh together, not in general. How general is this phenomenon? 

We begin answering this by looking at something I mentioned earlier; Gale-Shapley is underspecified and this is why we kept having to say "Consider an execution of Gale-Shapley that returns set $$S$$" instead of "Consider the set $$S$$ returned by Gale-Shapley". A very natural question is whether all executions of Gale-Shapley actually end up producing the same matching $$S$$. This is a very important type of question to ask in computer science: when we have an algorithm that involves independent components making different actions that interact with each other in complex ways, it's good to know how much variability can be caused in the final outcome of the algorithm from one run to another. 

In our case, we have a very nice answer; all executions of Gale-Shapley result in the same matching. This starts to spell doom for the women (or whichever party is not the proposer when you run the algorithm).

#### It's All the Same

There are several ways we could try to prove that every execution produces the same matching, but they get complicated very quickly. The cleanest way is to give a characterization of the matching obtained and then show that every execution results in the matching that has this characterization. Characterization is a term in math which means a set of conditions or properties for a mathematical object that differs from its definition but is logically equivalent to it (for example, a characterization of a rational number is this property "A number has a finite or repeating decimal expansion". The statement "A number is rational if and only if it has a finite or repeating decimal expansion" is true but the definition of a rational numbers is different, involving expressing the number as a ratio of two integers).

What is this characterization? We will show that every man always ends up with the best possible partner (and not just in the special case where the men's preferences mesh).

### Possible Extensions
There are many extensions, variants, etc. to this problem and we have just discussed the simplest one. Some other questions that come to mind is what if we allow some notion of collusion between one of the parties? We have seen that the men are favored in the Gale-Shapley algorithm, but what if the women were allowed to pool together and create preference lists to counteract the innate unfairness of Gale-Shapley? Surely something as strong as being able to look at another people's preference list would be able to pull the situation into the women's favor, right?

## Concluding Thoughts
The Stable Matching Problem nicely displays common themes in algorithm design and is rooted in practical concerns from which a learner must spend some effort to formulate the setup precisely enough to ask concrete questions. They are then forced think about algorithms to solve those questions, proving non-obvious properties about the structure of the problem along the way. This cuts to the heart of what algorithm design is and showcases both the rigor and precision of math combined with the ingenuinty and creative freedom of design.

As is common in combinatorial problems, we considered discrete "moves" we could make and from there derived observations that were useful in studying properties about the underlying problem. We then used these properties and to eventually devise an efficient algorithm and prove it works correclty. But we continued onwards, analyzing the algorithm to prove some further properties about it and from there, asking questions related to these properties. How general are these properties, what would happen if we tweaked this part or introduced another notion into the problem? All this is part of the design aspect of algorithm design, and hopefully you can see the creativity and ongoing quest for knowledge.

The Stable Matching Problem and Gale-Shapley are such rich breeding grounds for combinatorial reasoning, rigorous definitions and arguments, surprising results, interesting properties, cool extensions and even ventures quickly into unknown research problems. This one problem and the algorithm used to efficiently solve it opens up so much about the world of algorithm design.
