Documentation Author: Niko Procopi 2020

This tutorial was designed for Visual Studio 2019
If the solution does not compile, retarget the solution
to a different version of the Windows SDK. If you do not
have any version of the Windows SDK, it can be installed
from the Visual Studio Installer Tool

Blur Optimization VR
Prerequisites
	All other VR tutorials
	
Every optimization has trade-offs
Rather than doing all blurring in one render pass,
let's divide into two render passes, one for vertical blur,
and one for horizontal blur

By adding extra render targets for a second blur render 
pass, this adds about 7mb of Video RAM usage, which is 
not expensive because modern computers have multiple 
gigabytes, and the whole demo uses ~150mb to begin with.
I recommend GPU-Z free software for benchmarking Video RAM

One render pass we calculate samples 81 times,
because "samples" is 9 by default

	// vertical
	for(int i = 0; i < samples; i ++)
	{
		// horizontal
		for(int j = 0; j < samples; j++) 
		{
			// ...
		}
	}
	
By dividing into two render passes, we only take
18 samples, because each pass will only have one
	
	// vertical -- first blur pass
	for(int i = 0; i < samples; i ++)
	{
	}
	
	// horizontal -- second blur pass
	for(int i = 0; i < samples; i ++)
	{
	}
	
While we decrease the amount of processing, we also
increase render time because we need to wait for 
the vertical blur to finish before doing horizontal blur,
this is a GPU stall

In theory, performance will increase if we save more time
by reducing processing, than the amount of time we spend
by adding a GPU stall.

Press I for no blur
Press O for one pass
Press P for two passes

Results:
	One pass (9x9 samples): 
		blur = 1.67ms
	Two pass (9 samples for each part):
		part1 = 0.24ms
		part2 = 0.23ms
		total = 0.47ms
		
Blurring with two passes is more optimal than one pass.
By default, Two-pass does not look as blurry as one-pass, 
but that can be fixed by adding more samples, and Two-pass
is still more optimal than One-pass.

After applying two-pass blurring, drawing all geometry
is 0.8ms and the total blur is 0.5ms, so the next step
is to optimize the geometry again

Bonus Challenge
Try adding a part3 and part4 to blurring, to blur
along diagonals. In theory, this should still be
faster than blurring in one pass. 2 * 0.47 is still
less than 1.67ms