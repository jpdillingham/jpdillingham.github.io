---
title:  "BigFont.cs Update"
excerpt: "A C# class that transforms strings into large, stylized characters."
header:
  teaser: "bigfont-teaser.PNG"
categories: "Tools"
tags: "Beginner"
---

I [previously posted](http://dillingham.ws/tools/BigFont/) about a class I had written to support an NLog wrapper by allowing me to print strings as large characters.  I've made a couple of updates to the code which I thought warranted an update.

Note that the BigFont binaries can now be found on NuGet [here](https://www.nuget.org/packages/BigFont/).

## Updated "Alphabet"

Previously, the "Alphabet" was comprised a ```Dictionary``` using a two-tuple ```Tuple``` as the key.  To support additional fonts, I've expanded the key to a three-tuple ```Tuple```:

```c#
Alphabet = new Dictionary<Tuple<char, Font, FontSize>, string[]>();
```

The second tuple corresponds to a new ```Font``` enumeration:

```c#
/// <summary>
/// Font type enumeration
/// </summary>
public enum Font
{
    Default,
    Block,
    Graffiti
}
```

These changes simply allow for a third dimension for the "Alphabet", which allows letters to be stored by font type as well as size.

## New Font

I didn't feel that the previous font style was quite as professional as it could be, so I've introduced a new, plain, block-lettering style font.  Here's a sample in the Large, Medium and Small sizes:

```
███    ███ ████████ ███       ███        ▄██████▄         ███         ███  ▄██████▄  ████████▄   ███       ████████▄
███    ███ ███      ███       ███       ███    ███        ███         ███ ███    ███ ███    ███  ███       ███   ▀███
███    ███ ███      ███       ███       ███    ███        ███         ███ ███    ███ ███    ███  ███       ███    ███
███▄▄▄▄███ ███▄▄▄   ███       ███       ███    ███        ███         ███ ███    ███ ███    ███  ███       ███    ███
███▀▀▀▀███ ███▀▀▀   ███       ███       ███    ███        ███   ▄█▄   ███ ███    ███ ████████▀   ███       ███    ███
███    ███ ███      ███       ███       ███    ███        ███  ▄█▀█▄  ███ ███    ███ ███▀██▄     ███       ███    ███
███    ███ ███      ███       ███       ███    ███        ███ ▄█▀ ▀█▄ ███ ███    ███ ███  ▀██▄   ███       ███   ▄███
███    ███ ████████ █████████ █████████  ▀██████▀         █████▀   ▀█████  ▀██████▀  ███    ▀██▄ █████████ ████████▀

██   ██ ██████ ██       ██       ▄██████▄      ██      ██ ▄██████▄ █████▄ ██       ██████▄
██   ██ ██     ██       ██       ██    ██      ██      ██ ██    ██ ██  ██ ██       ██   ▀██
██▄▄▄██ ██▄▄   ██       ██       ██    ██      ██      ██ ██    ██ ██  ██ ██       ██    ██
██▀▀▀██ ██▀▀   ██       ██       ██    ██      ██ ▄██▄ ██ ██    ██ █████▀ ██       ██    ██
██   ██ ██     ██       ██       ██    ██      ██▄█▀▀█▄██ ██    ██ ██▀█▄  ██       ██   ▄██
██   ██ ██████ ████████ ████████ ▀██████▀      ███▀  ▀███ ▀██████▀ ██ ▀██ ████████ ██████▀

██  ██ █████ ██     ██     ▄█████▄    ██     ██ ▄█████▄ █████▄ ██     █████▄
██▄▄██ ██▄▄  ██     ██     ██   ██    ██     ██ ██   ██ ██  ██ ██     ██   ██
██▀▀██ ██▀▀  ██     ██     ██   ██    ██ ▄█▄ ██ ██   ██ ████▀  ██     ██   ██
██  ██ █████ ██████ ██████ ▀█████▀    ███▀ ▀███ ▀█████▀ ██ ▀█▄ ██████ █████▀
```

Readability is also greatly improved, especially the small size, when compared to the previous "graffiti" style font.

## Defaults

The default size was previously hard-coded in the method signature for the ```Generate()``` method.  I've changed a few things around and in the updated code both the font and the font size are configurable by changing a pair of corresponding variables in the ```Variables``` region near the top of the class:

```c#
#region Variables

/// <summary>
/// The default font.
/// </summary>
private const Font defaultFont = Font.Block;

/// <summary>
/// The default font size.
/// </summary>
private const FontSize defaultFontSize = FontSize.Large;

#endregion
```

If one of the ```Generate()``` overloads is invoked without supplying a font or font size, the default values will be substituted as configured.

## Generate() Overloads

To support backwards-compatibility and to allow for the substitution of default values for font and font size parameters, an additional overload for ```Generate()``` has been added.  This method now has two signatures:

```c#
public static string[] Generate(string phrase, FontSize size = FontSize.Default)
```

This signature provides backward compatibility with the previous version of the code.  The Font parameter will default to the configured font, and the size can either be specified in the invocation or will be substituted with the default configured value.

```c#
public static string[] Generate(string phrase, Font font = Font.Default, FontSize size = FontSize.Default)
```

This signature allows for the explicit specification of both font and font size.  

## Updates

I plan to continue tweaking the new block font as I continue to use the class, however I don't plan to continue to post updates unless another major change is made.

The latest code can always be found on GitHub [here](https://github.com/jpdillingham/BigFont).
