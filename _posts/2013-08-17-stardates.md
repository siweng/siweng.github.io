---
layout: post
---

Over the past few years, I've been re-watching every episode of Star Trek again. Almost all of it is brand new content for me, though. When The Next Generation first aired, I was only [13 months](http://www.wolframalpha.com/input/?i=aug+4+1986+-+premiere+of+star+trek+the+next+generation) old. Even when I was a kid, I remember being intrigued by the way the stardate system worked. So, being the curious guy that I am, I tried to decipher it so I could better understand the time references used in the show. Surprisingly, I wasn't able to find a lot of definitive information around how the stardate system works.

## History of Stardates

The method for calculating stardates has changed three times over the course of Star Trek history. TOS employed a 4 digit stardate that was completely arbitrary; the "calculation" for which involved the current position of the Enterprise in the galaxy, in addition to the speed at which the ship was travelling, etc. In reality, that explanation was made up to deflect the fact that the stardates for individual episodes were completely made up by the writers.

In The Next Generation, a new stardate system was created which was significantly more consistent. The method for calculation used the first digit as the century marker ("4," since it takes place in the 24th century), the second digit as the current season of the series ("1"), and the final 3 digits as the current progression through the year, assuming there are 1000 stardate units per year. For instance, the stardate for the first episode of TNG was 41153.7.

Unfortunately, the original idea for this system broke down when stardate 49999 was reached, since the century digit should stay at "4," while the season digit should increment to "10." To accommodate for this, the rules for generating stardates were modified to have the first two digits become a two digit number that would increase every year (i.e.: from "49" to "50").

## Proving it

In the first season of The Next Generation we are told that the year is 2364 (TNG: The Neutral Zone). The stardate mentioned in Riker's log during that episode is 41986.

Calculating the exact date on the Gregorian calendar would require that we know the date that coincides with a new stardate "year," which has never been mentioned during any of the episodes. However, Star Trek: The Next Generation Technical Manual gives us enough information to tie everything together.

According to the manual, the Enterprise D was launched on October 4, 2363, which is the 277th day of the start of Gregorian calendar year. In addition, the plaque on the bridge of the Enterprise notes that it was launched on stardate 40759.5. Doing some more math, we are able to test if the beginning of the stardate year is the same as the start of the Gregorian calendar year:

![759.5 / 1000 = x / 365.256363](http://latex.codecogs.com/gif.latex?\frac{759.5}{1000}=\frac{x}{365.256363})

Solving for ![x](http://latex.codecogs.com/gif.latex?x) returns a value of 277.412, confirming the hypothesis to be correct.

## Additional Nerdiness

We can determine the length of each stardate unit by dividing the approximate length of a sidereal year by 1000. Doing this calculation reveals that each unit in the stardate system is 8 hours, 45 minutes, and 58.1498 seconds.
