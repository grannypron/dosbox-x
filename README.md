This project stemmed from a question on the Reddit r/goldbox forums about whether the Enlarge spell had any effect on a 18(00) character.

https://www.reddit.com/r/goldbox/comments/1914msa/how_does_enlarge_work/

Because the source is (hopefully) locked away somewhere, we can't see the exact game mechanics without going through the assembly line by line and trying to make sense of it.  But, I figured that an inductive approach may work.  Since PoR runs in DOSBox now, I figured we could somehow automate a battle and record the results each time to see, with and without Enlarge cast, what the damage would be.  This is that automation effort and its results.  

The idea was to use the DOSBox Save State feature (thanks to tikalat and ykhwong) to save state while in a battle right before an attack, execute the attack, and then look for the resulting hp of the enemy in memory and log it for statistics.  Then repeat the process X times.  

While this may seem like a lot of work, it was also just fun and C++ exercise and I could imagine how the approach could be useful for lots of other inductive tests on game mechanics.  At the time of this writing, the code is rough, hardcoded, and non-extensible, but it serves as a proof of concept in case others are interested.

The end results were that after about 250 test runs with vs. without enlarge on a 18(00) character, my findings show that the average damage without Enlarge rolls damage to be 10.93189964, but with Enlarge, 11.03555556.  I would chalk up the 1% difference to be %error due to sample size and say that casting Enlarge on an 18(00) character has NO effect on damage.

To run the test, I built the vs/dosbox-x.sln, then ran it with -conf to potint to the dosbox_por.conf at the root of this distro.  That conf file points to the sav.sav file at the root of this distro (copied from saves/DOSBOX/sav-enlarge.sav or saves/DOSBOX/save-no_enlarge.sav, whichever type of test I was executing).  Then, I clicked the "Begin Experiment" menu item under the Main menu of the built DOSBox-X (that I hijacked from some other menu option) that runs the test.

My test character was good ol' THRENDER GRONE, who I bumped up to 18(00) and level 7 via GBC:
| THRENDER GRONE| 
| STR 18(00)| 
| INT 12| 
| WIS 12| 
| DEX 17| 
| CON 16| 
| CHA 15| 
| THAC0 11| 
| BANDED MAIL| 
| LONG SWORD | 

I fought the notorious sack-throwing Trolls & Ogres in the Phlan Slums because 18(00) characters can hit big so I needed something with good hitpoints to absorb the high damage amounts, but then I had to level him up to level 7 for the THAC0 since Ogres are like AC4 and misses don't help me measure damage.


Notes:
 - I did not take into effect the to-hit rolls.  Only hit rolls where damage was inflicted are counted.
 - I logged the resulting hp in the DOSBox logger - not the most efficient for parsing out the results, but it worked.  Notepad++ has lots of great regex tools to pull the raw numbers out.
 - Memory locations that contain the resulting hp of the enemy I was attacking (ogre) are transient with each run of the game.  I had to find the location in memory bewteen the save game with and without Enlarge cast.
 - Threading got tricky: I figured that I had to use threading because the main game thread had to run the game and my thread had to send inputs and wait for the game to run them, and I can't wait on a the main thread if I am running from the main thread.  But, when I tried to load the state (via SaveState::instance().load()) from a thread other than the main SDL thread, it crashed.  So, I had to use the SDL_PushEvent / SDL_PollEvent method described at https://wiki.libsdl.org/SDL2/SDL_PushEvent#remarks to communicate between threads.
 - Randomess was interesting:  Early on, I found that if I loaded a state and then did the attack, I would get the same to-hit and damage result every time!   This was obviously bad for my testing.  After lots of trial and error and executing minor game mechanics to see if it would affect outcomes and then comparing memory dumps, I think I found the location in memory that contains a seed of some sort for RNG in the game.  So, to get random results I change that memory location with a std::random_device call before attacking and it seems to work pretty great.  It's possible that this approach somehow polluted my results, but I don't see how and if you do, message me somehow.  A little documentation can be found here:  https://forums.goldbox.games/index.php?topic=4471.0
 - I tried to run a batch of 1000 tests, but for some reason my event polling strategy stopped after about 350-375 runs for reasons that I don't understand.  It was sufficient to get results, but if someone else is using this, be prepared for that problem.  I am no expert in concurrency :/
 - There is some code in there about the game clock and waiting for text messages which is unused at the moment, but I thought it might be useful.
 - Game system clock of some sort seems to be at 0046C (or maybe that is just DOS, idk).  Game clock is found at 0:39ADE & 0:39AE2 - minutes and hours, respectively.
 - My PoR saves that have my characters are in saves/POOLRAD
