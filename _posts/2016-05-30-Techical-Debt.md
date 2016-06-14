---
title:  "Technical Debt"
header:
  teaser: "technical-debt-teaser.jpg"
categories:
tags:
---

I came across the term ["Technical Debt"](https://www.wikiwand.com/en/Technical_debt) in an article this evening and I was surprised to find that not only is there a Wikipedia entry for the term but it closely matches a concept I've been thinking a lot about while developing a personal project.

I've found that with past personal projects (and admittedly, usually professional ones) that quite frequently less than ideal decisions are made in the heat of the moment and that those decisions tend to pile up over the time.

If allowed to linger long enough these bad choices begin to lead to even worse decisions, and more often than not the developer is likely fully aware of how bad the situation has gotten but is unable to resolve it because the project is too far along in schedule, budget, or both, to allow for the time it would take to refactor the code and, for projects nearing completion, to introduce the risk that comes with sweeping changes to the code base.

## (Not So) Technical Debt
I recently came across a real-world example of technical debt while working with my Dad on a home improvement project.  This particular project, a basement remodel following a burst pipe, has been ongoing since 2005.  One of the final tasks was to install a drop ceiling in a corner bedroom, a task my Dad and I had both been avoiding since we struggled to install the same ceiling in another room eight or nine years ago.

After some deep introspection regarding previous failures we installed the support structures at the necessary intervals and we began installing the panels starting on one side of the room and working towards the opposite.  As we reached the final eight feet it became apparent that one of us had either incorrectly measured or cut the final two panels to width; they were too narrow.  After a spirited debate regarding the guilty party, two new panels were cut according to new measurements.  Again, the panels were the wrong size but this time were too wide rather than too narrow.  Further debate and some detailed investigation revealed the issue; the 2x4 stud used for the header over the closet was bowed by approximately three to four inches in the center.

We had discovered technical debt that my Dad had accrued in 1974 when the basement was originally finished.  Like most that accrue technical debt, my Dad had no recollection of having done so, however anyone that has worked with 2x4 studs can attest that the stud in question was almost certainly not straight when it was installed.

## The "Least Worst" Option
We were now faced with a dilemma.  Much like the developer facing similar issues with code, it simply wasn't feasible to tear out the wall and replace the bowed lumber.  It was late in the day, we were tired, and the project was nearing completion.  We took the pragmatic approach and cut lopsided panels to fit the existing opening.  Neither of us were pleased in doing so, however it was the "least worst" of the available options.

This unfortunate scenario plays out quite frequently;  I've paid my own technical debt many times.  I've paid others' debts, and others will some day pay (or are perhaps actively making payments on) technical debt that I've accrued.  A certain amount of technical debt is simply unavoidable given the uncertain nature of the world around us, and the nature of software in general.

Technical debt can be avoided, however in order to avoid it you must first understand the different ways it can manifest itself.

## The Nature of Technical Debt
Martin Fowler, one of my favorite technical bloggers, wrote a great article on technical debt back in 2009 in which he presents the following graphic:

![Technical Debt Quadrants](http://martinfowler.com/bliki/images/techDebtQuadrant.png)

This graphic depicts Martin's idea of "Technical Debt Quadrants".  The basic idea is that accruals of technical debt can be deliberate or inadvertent, reckless or prudent, or some mix of each.

### Deliberate
Deliberate accruals of technical debt are an unfortunate reality.  External pressures such as deadlines, shifting requirements, and client demands for progress updates sometimes puts software teams in a situation where they are forced to fudge something but are in the unenviable position that they know it is bad while they are doing it.  Whether reckless or prudent, there is little excuse for not immediately paying deliberate accruals of technical debt back at the earliest convenience.  Project managers should be pragmatic enough to tactically take on this type of debt but should also be careful to ensure it is resolved when the crunch is over, lest they pay interest on the debt later.

### Inadvertent
Inadvertent accruals of technical debt are more innocent, however generally they have a much higher impact on the quality of the software or system due to the fact that the debt may not be discovered until it has accrued significant interest.  If programmers are working around a technical debt accrued early in the project the effects of the debt will cascade through the rest of the system and will thus be much more difficult and costly to remove when it is discovered.

As even the most senior software developers will tell you, a certain amount of inadvertent technical debt is inevitable.  Many times the ideal design doesn't become obvious until a significant amount of code is written.  When these situations arise, project managers and lead architects have a difficult decision to make; refactor the code to avoid paying interest later, or move forward and deal with the consequences as they come up.  This is always a difficult decision and may things need to be considered, including the scale of the system, the expected longevity, the amount of anticipated support and/or future modifications, and the remaining schedule and budget should all be considered.

## Practical Advice for Avoiding Technical Debt
Frankly, to a large extent you simply can't avoid technical debt.  You can, however, largely avoid paying interest on technical debt.  

The key is to acknowledge the existence of the phenomenon, and to understand the different manifestations and what causes them.  With this information you can learn to identify when there has been an accrual and, most importantly, you can pay it off in a timely manner before it grows out of control.

---

*Reposted from my original LinkedIn post [here](https://www.linkedin.com/pulse/technical-debt-jp-dillingham).*
