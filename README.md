This project stemmed from a question on the Reddit r/goldbox forums about whether the Enlarge spell had any effect on a 18(00) character.

https://www.reddit.com/r/goldbox/comments/1914msa/how_does_enlarge_work/

Because the source is (hopefully) locked away somewhere, we can't see the exact game mechanics without going through the assembly line by line and trying to make sense of it.  But, I figured that an inductive approach may work.  Since PoR runs in DOSBox now, I figured we could somehow automate a battle and record the results each time to see, with and without Enlarge cast, what the damage would be.  This is that automation effort and its results.

The idea was to use the DOSBox Save State feature (thanks to tikalat and ykhwong) to save state while in a battle right before an attack, execute the attack, and then look for the resulting hp of the enemy in memory and log it for statistics.  Then repeat the process X times.



Notes:
 - I did not take into effect the to-hit rolls.  Only hit rolls where damage was inflicted are counted.
 - I logged the resulting hp in the DOSBox logger - not the most efficient for parsing out the results, but it worked.  Notepad++ has lots of great regex tools to pull the raw numbers out.
 - Memory locations that contain the resulting hp of the enemy I was attacking (ogre) are transient with each run of the game.  I had to find the location in memory bewteen the save game with and without Enlarge cast.
