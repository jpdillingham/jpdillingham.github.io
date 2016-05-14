---
title:  "Hello World!"
header:
  teaser: ""
categories:
tags:
---

# Hello, World!

This is the compulsary first post on my new blog.

For those curious, I've built the blog using [GitHub Pages](https://pages.github.com/) for hosting and the [Minimal Mistakes](https://mademistakes.com/work/minimal-mistakes-jekyll-theme/) theme for [Jekyll](http://jekyllrb.com/) for the layout and internals.

## Testing

Here's some code so I can get the syntax coloring the way I want it:

```c#
/// <summary>
/// The alphabet.
/// </summary>
public static Dictionary<Tuple<char, FontSize>, string[]> Alphabet { get; private set; }


/// <summary>
/// Generates a larged, stylized string of characters corresponding to the input phrase.
/// </summary>
/// <param name="phrase">The phrase to generate.</param>
/// <param name="size">The size of the font to use.</param>
/// <returns>A string array containing the stylized output.</returns>
public static string[] GenerateStyled(string phrase, FontSize size = FontSize.Large)
{
  string[] r = new string[(int)size + 2];
  string[] g = Generate(phrase, size);

  r[0] = header;

  for (int i = 1; i <= (int)size; i++)
    r[i] = linePrefix + g[i - 1];

  r[(int)size + 1] = footer;

  return r;
}
```
