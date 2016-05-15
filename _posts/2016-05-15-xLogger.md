---
title:  "BigFont.cs"
header:
  teaser: ""
categories: "tools"
tags:
---

While implementing some detailed logging for a project I found myself constantly running the application, stopping it, opening the generated log file, then scrolling back and forth until I found the section for which I was adding messages to view the output.

This led me to think; I should log something really conspicuous before and after this section so I can scroll to it quickly.  I did that, and it certainly helped, however as I moved to the next section and implemented more conspicuous lines the ambiguity returned.

It occurred to me that I was suffering from a readability issue, not a logging issue.  The difference in this situation is that I was limited by a few constraints:

* The logging tool (NLog) is only capable of producing plain text logs (by default)
* The log files produced by the tool need to be viewable by plain text viewers

My first idea was to write a wrapper for NLog that produced HTML, however this would take a lot of work to not only implement the markup but to reinvent the file management functionality of NLog.  The produced HTML wouldn't be viewable from a console so I'd need to log plain text to that target and my HTML to a file, and a web browser would be required for viewing log files which would prevent some challenges on some platforms.  HTML logs simply weren't a fit.

## Introducing BigFont.cs

The primary reason for wanting an HTML log file is the ability to add markup and switch typeography to improve readability.  I decided that if HTML wouldn't work, I'd try to implement those features
