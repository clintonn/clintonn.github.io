---
layout: post
title: "A brief history of monospace fonts"
date: 2017-03-27-15:47:40
categories: [code, ephemera]
---

![A Kanye tweet cross-stitched. It says: "Sometimes I get emotional over fonts"](http://i.imgur.com/AcHb5ui.jpg)

Of all the minutia developers often obsess over — the tools, code cleanliness, how to needlessly complicate simple solutions to move the needle forward — there's remarkably little talk about fonts. Seriously. Fonts are the easiest paths towards self-aggrandizement, particularly in the graphic design world. And yet, here we are.

For much of recent history — about the last 800-900 years or so — proportional fonts dominated the landscape thanks to hand-lettering, manual typesetting via printing presses, and calligraphy. Handwriting has always been proportional; humans don't write in grids and spacing letters evenly produces more work, not less.

But at the advent of the 20th century, when Remington typewriters started to come into vogue, consumers started to see monospaced fonts more often.

![A letter written out on a Remington typewriter](http://i.imgur.com/RCGgPFV.jpg)

Typewriters work by mapping keys to sets of metal arms called typebars. Analogous to the printing press, these arms would have one or two metal cast "types," which were single sculpted glyphs. When you press a key, the weight would swing the arm, imprinting ink onto the page.

Because of the way the typebars were designed, the easiest way for them to be laid out to minimize mechanical failures (leading differences, jumbled-up keybar alignments, etc) was to make sure every type had a fixed width. The type guide thus only had to move one full character's width every time a key was pressed instead of some variable amount for different characters' kerning values.

Use of these fonts expanded mid-century, when optical character recognition (OCR) began developing. Because early OCR devices were rudimentary — they couldn't closely kerned newspaper text, for instance — it was necessary to develop standardized, universal OCR-recognizable typefaces.

Adrian Frutiger, the Swiss typeface designer behind Avenir, Univers, Frutiger, and the scads of other sans-serif geometrics you learn about in design school — designed OCR-A, and OCR-B. The two fonts, both monospaced, were designed to be easily readable by machines. They became widely used in passports, financial documents and banknotes.

Soon enough, computers came around.

In the 70s and 80s, before displays started measuring pixels in the tens of thousands, the standard outputs of programs were measured in grids.

![Commodore emulator on Windows featuring the C64 monospace font](http://i.imgur.com/PIsEjvY.jpg?1)

Some of the major fonts to come into use in this era were the Commodore 64 font (a faithful repro of which you can [find here](http://style64.org/c64-truetype)), Courier, which became the Times New Roman for playwrights, Lucida Console, and Monaco.

Even with support for proportional type in word processors in the mid-90s, monospaced fonts found their way into programming for very obvious reasons: it was easier to read, and when you're looking at blocks of code, it's easier to read text in consistently aligned columns.

It also helps that most characters that end up being confused — 0 (zero) and O (capital o), 1 (numeral one) and l (lowercase L) are often designed to look distinct in monospace typefaces.

As such, monospace fonts have maintained plenty of popularity within the past few decades. With more advanced type features, such as ligatures, fonts like [Fira Code](https://github.com/tonsky/FiraCode) and [Hasklig](https://github.com/i-tu/Hasklig) can make reading text even easier for programmers by merging commonly combined characters, like <=, >=, ==, ===, and even language-specific operations like the spaceship operator (<=>), hashrockets, and arrow functions.

That's a lot of fuss over cosmetic minutia that's fundamentally remained unchanged for decades. But when you start getting paid professionally to look at this sort of thing for 8 hours a day, it becomes easier and easier to let yourself get a little more opinionated, even about the most trivial of tools.

![Courier font in Atom, which looks a little dated imo](http://i.imgur.com/DLlB4iP.png)
Courier

![Fira Code font in Atom, with ligatures, is a pleasure to read](http://i.imgur.com/5EOyplF.png)
Fira Code
