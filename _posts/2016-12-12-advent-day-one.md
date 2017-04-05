---
title: Advent of Code
date: 2016-12-12 00:20:00 Z
categories:
- code
- daily-report
layout: post
subtitle: ''
---

![](http://i.imgur.com/2SmSSjr.jpg)

While the last week has been solely based on creating functional code for real people, it's easy to miss the fact that you're working with a souped-up calculator.

Which is why I'm thankful to cut my teeth on [Advent of Code](http://adventofcode.com), an advent calendar that, instead of rewarding you with chocolate, gives you punishment in the form of increasingly more mundane coding challenges every day.

Here's my favorite one, from Day 3:

![](http://i.imgur.com/SYRxDZm.png)

The puzzle input gave me 1,992 lines of numbers, all of which were side lengths. They came in this format:

```
566  477  376
575  488  365
 50   18  156
558  673  498
133  112  510
...
```

Thing is, we've already coded *exactly* for this situation in a previous lab: the Triangle classification lab.

All I had to do was parse through, stick the triplets into an array, and run class methods to count how many invalid triangle lengths there were.

But wait! The challenges come in pairs. After the first challenge, there was another one telling me that you need to actually count triangle lengths in **columns**, not rows. Each three numbers going down would be considered triangle lengths.

![](http://i.imgur.com/SYRxDZm.png)

So in the code above, `[566, 575, 50], [477, 488, 18], [376, 365, 156]` would have been the first 3x3 of triangle lengths.

Turns out there's a handy, but super-specific Ruby method: `.transpose`

What `.transpose` does is take an array, like so:

```
arr =
[[0, 1, 2],
[2, 3, 4],
[6, 5, 3],
[9, 4, 3],
[5, 2, 7],
[6, 1, 8]]
```

...And flips the rows and columns. In the above array, there is an array of arrays, column length 6, row length 3. When transposed, it'll return an array that's row length 6, column length 3.

```
arr.transpose
# output:
# [[0, 2, 6, 9, 5, 6],
# [1, 3, 5, 4, 2, 1],
# [2, 4, 3, 3, 7, 8]]
```

So what I did was organize the numbers into rows of 3x3 arrays. So in the input snippet I had above, I just parsed the rows into arrays, then transposed them to get the right lengths to calculate:

```
arr = [
[566,  477,  376],
[575,  488,  365],
[50,   18,  156]].transpose

# output:
# [[566, 575, 50],
# [477, 488, 18],
# [376, 365, 156]]

```

Iterate enough to cover all 1,992 rows, run the Triangle.kind method on all of those rows, and you're golden.

The purpose of exercise — a refreshing one, really — isn't to make discrete class structures, pull from databases, or to craft some sort of project paradigm for future developers. You're free to just throw spaghetti at the wall until you come up with a right output.

![](http://i.imgur.com/viz1nqh.png)
