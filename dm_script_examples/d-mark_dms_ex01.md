// Let's config what we are using, use tab as a separator for parameter, not space.

NTC.unit	1 		// we will calculate in degree F
NTC.constant_r0	100000	// we use  100 k-ohm NTC having B value 3950
NTC.constant_b	3950	//set the B value to 3950

mosfet_1.mode	digital	//set MOSFET output channel 1 to digital output mode

// Ready to start our smart toaster reflow controller	

:s1			//set a label for the first step heating return loop point as "s1"
if.ntc.gt	239,s1_soak	//if the NTC temperature is greater than 239 F, jump to routine "s1_soak"
mosfet_1.on		//enable the MOSFET output
goto	s1		//as long as the temperature is not reaching our tartget of 240 F, repeat the loop s1

s1_soak:			//s1_soak staring point

set.t0	0		//start the timer from 0 s

:s1_soaking		//set "s1_soaking" as a starting loop point for checking time and temperature condition
if.t0.gt	179,s2 		//If the soaking time reaches 180 seconds, jump to do the step 2 (ramp to 294 F)
if.ntc.gt	239,s1_done	//If the NTC temperature reaches 240 F, close the step 1 by jumping to "s1_done"
mosfet_1.on		//otherwise output will still be on
goto	s1_soaking	//loop s1_done as long as the condition is valid

:s1_done			//the end point of step 1 as the soaking time reaches 180 seconds
mosfet_1.digital	0	//turn off the output
goto	s1_soaking	//loop over the step 1 soaking routine 

:s2			//repeat the same procedure for the step 2 and 3 as the step 1 using different temperature and soaking time
if.ntc.gt	293,s2_soak
mosfet_1.on
goto	s2

s2_soak:
set.t0	0

:s2_soaking

if.t0.gt	59,s3
if.ntc.gt	293,s2_done
mosfet_1.digital	1
goto	s2_soaking

:s2_done
mosfet_1.digital	0
goto	s2_soaking

:s3
if.ntc.gt	419,s3_soak
mosfet_1.digital	1
goto	s3
s3_soak:
set.t0	0

:s3_soaking
if.t0.gt	29,turn_off
if.ntc.gt	419,s3_done
mosfet_1.digital	1
goto	s3_soaking

:s3_done			
mosfet_1.digital	0
goto	s3_soaking

:turn_off			
mosfet_1.digital	0	//when all the steps done, turn off the output
goto	turn_off
