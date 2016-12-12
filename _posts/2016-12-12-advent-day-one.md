---
layout: post
title: "One star in the advent"
subtitle: "NaNoWriMo for programmers"
date: 2016-12-12-00:20:00
categories: [code, daily-report]
---

![](http://i.imgur.com/2SmSSjr.jpg)

Hacker News recently posted a site called [Advent of Code](adventofcode.com) which is exactly what it sounds like. It's an advent calendar that, instead of rewarding you with chocolate, gives you punishment in the form of progressively more difficult Christmas-themed code exercises.

The first one is a bit of a beginner's staple. You're chasing after the Easter Bunny, and you need to find out how far off his headquarters is on a city grid.

The problem gives you a set of directions, coded as `L5, R10, L3`, etc, which mean turn left and move five blocks, turn right and move 10 blocks, and turn left and move three blocks respectively. A 19th-century German mathematician had an apt naming for this weird, hellish Bomberman-like system — [taxicab geometry](https://en.wikipedia.org/wiki/Taxicab_geometry).

There are only two coordinates in a two-dimensional Cartesian plane, but I found it was a little less confusion-inducing to map four different array indices for all cardinal directional movements. That way I'd know how far north of south you'd walked. I'd just need to subtract south from north and west from east to get the current position.

```
def move(i)
  if $D == 'N' # $D meaning cardinal direction
    if i[0] == 'R' # move right means change cardinal to east
      $D = 'E'
      $coords[2] += i[1..3].to_i # $coords = [N, S, E, W]
    else
      $D = 'W'
      $coords[3] += i[1..3].to_i
  elsif $D == 'E'
  ...
end
```

## A future project

Michael Stevens (you know, [the vsauce dude](https://www.youtube.com/user/Vsauce)) recently popped up in my feed with a year-old video. He [tk] that's really peculiar, one of those chin-stroking ideas that deconstruct your understanding of human behavior. — [Zipf's law](https://en.wikipedia.org/wiki/Zipf's_law)

<iframe width="800" height="450" src="https://www.youtube.com/embed/fCn8zs912OE?rel=0" frameborder="0" allowfullscreen></iframe>

George Zipf, an early 20th century linguist, found a surprising trend among all languages: word frequency follows a exponential distribution scale.

In the English language, the word "the" comes up twice as much as the next word ("be"). The third one comes up a third as much as the most popular word. You can read [more on it](https://colala.bcs.rochester.edu/papers/piantadosi2014zipfs.pdf) via this recently published paper here, but this image might suffice:

![](https://blogemis.files.wordpress.com/2015/09/graph-zipf.png)
