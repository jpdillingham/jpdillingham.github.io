---
title:  "Understanding Open Source Licensing"
excerpt: "A quick look at the most popular Open Source licenses."
header:
  teaser: "understanding-open-source-licensing-teaser.PNG"
categories: "Editorial"
tags:
---

I've been doing a lot of research on Open Source Licensing the past few days and to say the least, it appears to be a very complicated issue at first introduction.  The legalese within the licenses is, by design I'd guess, difficult for the non-lawyer to read, and finding a source that cuts to the important parts can be challenging.

It is important for companies who produce software commercially to understand the nature of Open Source software and licensing as bits and pieces (or whole parts) of the software they produce likely have bits and pieces (or whole parts) of Open Source projects within them.  If the engineers copying the code aren't vigilant about reading and understanding the licensing for the code they are using the company and perhaps end client can be exposed to legal issues if licensing clauses are not satisfied.

I'd like to remind anyone reading this that I'm not a lawyer.  It seemed prudent to state this.

## Defining Open Source Software

First, I feel that it is important to share the definition of Open Source, as making the source code for a program publicly available does not necessarily make it Open Source; the license for the software is what determines the status.

The [Open Source Initiative](https://opensource.org/) (referred to as OSI from here on), the unofficial source on all things Open Source, has a [very specific definition](https://opensource.org/osd) of what Open Source means.  To paraphrase, an Open Source license must:

* Allow for free (as in speech) redistribution.  Anyone can give it away or sell it without restriction.
* Make the source code publicly available.
* Allow for modifications and derivations.
* Not discriminate against persons, groups, or fields of endeavor.  Anyone can use it for any purpose.

Without further explanation it may appear as though the definition of Open Source is exactly as it sounds, however there are a couple of important distinctions in the available licenses that change the meaning pretty significantly.

## Open Source Licenses

There are several dozen licenses that have been approved by the OSI.  A full list can be found [here](https://opensource.org/licenses/alphabetical), however I'll describe the most popular types later in this article.  The popular licenses fall into three major categories: Permissive, Weakly Protective and Strongly Protective:

![Open Source License Diagram](/images/open-source-licenses.png)

Important to note here is that licenses are simply text, and to adopt a license a copyright holder simply needs to choose one that fits their needs and include that license along with their source code.  

The verbiage of the licenses linked above has been evaluated by the OSI and found to comply with the rules for Open Source licenses.  A copyright holder is always free to create their own license however they wish, but in the spirit of Open Source it is preferable to choose an existing license as it is easier for both the owner and consumer of the software to determine their legal rights and obligations.

### Permissive Licenses

Permissive licenses are designed to fully embrace the spirit of Open Source by placing very few restrictions on how the software can be used and distributed.  The most popular permissive licenses are the MIT, BSD, and Apache 2.0 licenses.

[MIT](https://opensource.org/licenses/MIT)

The MIT license, properly known as the X11 license, requires only that copies of the software are distributed with a copy of the license attached.  

[BSD](https://opensource.org/licenses/BSD-2-Clause)

The preferred BSD license is also known as the BSD 2-Clause, the Simplified BSD, the Modified BSD and the FreeBSD license.  This version of the BSD license removes a clause forbidding advertisements from the previous BSD license.

Like the MIT license, the BSD license requires that the license is propagated along with both the source code and the binary form of the program.

[Apache 2.0](https://opensource.org/licenses/Apache-2.0)

The Apache 2.0 license is considered permissive, however it has a few additional clauses than the two previous examples.  Like the MIT and BSD licenses, the original license must be distributed along with the software and source.  The Apache license also requires that if any changes to the source were made, a list of those changes is also distributed.

The license includes verbiage restricting use of any trademarks from the original work among derived works, meaning that using software under this license doesn't necessarily grant you permission to use the name.

Lastly, the license also includes a license to any patents that may be in effect for the licensed work, and states that if you (the user) brings a suit against anyone with a patent claim for the work, your patent license is immediately revoked.  This verbiage is designed to protect the original contributors and end users from patent related legal issues.

### Weakly Protective 

[GNU LGPL 3.0](https://opensource.org/licenses/LGPL-3.0)

The GNU LGPL (Lesser General Public License) 3.0 license is a "copyleft" license, meaning any derivations of the original work must also be released under the LGPL 3.0 license.

Like the Apache 2.0 license, any changes to the software must be listed and distributed along with the source, and a patent license is included with the software license.

The LGPL license includes a clause which allows for libraries (typically, a compiled .DLL) to be linked in binary form to a work without requiring a copyleft license or disclosure of source code.  Simply stated, a library created under the LGPL can be used freely without any strings attached in your program as long as it is in the compiled form.

### Strongly Protective

[GNU GPL 3.0](https://opensource.org/licenses/GPL-3.0)

The GNU GPL (General Public License) 3.0 is the same as the LGPL variant without the clause allowing linking for libraries.

[GNU AGPL 3.0](https://opensource.org/licenses/AGPL-3.0)

The GNU AGPL (Affero General Public License) 3.0 includes the GPL license and adds one important clause, sometimes called the "loophole" clause, which extends the "copyleft" requirement to software that connects to the licensed work via a network.

This means that if a program relies on an AGPL licensed program, and the two programs communicate to one another via a network, the licensed program is considered to be contained within the new program and is therefore required to propagate the AGPL license and all of its requirements.

This additional clause was primarily added for web sites, however it applies to things like microservices and classical client/server applications as well.

## Dual Licensing

Dual licensing refers to the practice of licensing a work under an Open Source license and also allowing licenses allowing for the proprietary use (and inclusion of in derived works) the work to be sold.

This strategy allows a copyright holder to participate in the Open Source community while also allowing other businesses to purchase and use the software in a closed-source manner.

The Dual Licensing strategy complicates contributions to an Open Source project; contributors must relinquish any right they have to their contribution to the copyright holder by way of a Contributor License Agreement.

## Avoiding Legal Issues

The proliferation of Open Source software requires commercial companies to take greater care in the way they use code found on the internet.  Without the proper vigilance, an engineer can innocently violate an Open Source license and expose their company and client to legal issues and significant rework.

Engineers should be educated on the different types of licensing and the requirements for satisfying the terms.  Furthermore, guidelines should be created either on a per-project or company wide basis to establish which types of code may be reused.

Companies should be aware that if any code licensed under a GNU GPL license makes its way into any of their work that they have legal obligations which they may not be able to feasibly satisfy.  

In this scenario the two possible outcomes are most likely either a lawsuit or a complete rework of the program to remove the licensed portion of the code.  Unless the original licensed work was created using a dual licensing strategy, once anyone other than the original creator makes a contribution to the code it is no longer owned by anyone and therefore cannot be sold, meaning the only way to buy the way out of the problem is via a lawsuit settlement.

Technically a third possible outcome is that you release anyway and hope nobody finds out.  I wouldn't suggest this strategy but again, I'm not a lawyer.

---

*Reposted from my original LinkedIn post [here](https://www.linkedin.com/pulse/understanding-open-source-licensing-jp-dillingham).*

 

*Header image by Johannes Spielhagen, Bamberg, Germany - Provided as files by the author to be published by OSBF e.V. under an open license., CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=27179850*