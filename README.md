# Reverse Engineering of an AeroCool RGB Fan 

This article describes some analysis performed on a defective Aerocool fan. Model is WX-14025 (14 cm), "Made in China" with a sticker indicating 12V-DC 0.25A on the back. 
## Defect Description
The fan was part of a Trinity White Tower V3 made by Aerocool and shipped with two front fans of 14 cm and one back fan of 12 cm, all of them with a flashy RGB effect out of the box. After only a few startups, the lower front fan was displaying a steady white color in one corner. The rest of the fans were not illuminated (hue in the upper fan in the picture is coming from some backlights). 

<img src="https://user-images.githubusercontent.com/12449790/222844467-a7b4a56f-69a4-4225-8512-cd9ee8be95d3.png" width="30%" >

## First Thoughts
After verifying all cables and trying several times to reconnect the SATA power, I came to the conclusion that some electronical issue was present and somehow explain the low cost of the whole unit. Each fan has four wires. The first fan has a special connector such that the daisy-chained set of fans can be powered by a single SATA power cable. The last wire, which is not wired to the SATA power connector, is clearly dedicated to some data line and this data line is shared across all the fans.

<img src="https://user-images.githubusercontent.com/12449790/222847108-f93eb58c-3733-4bc3-81fd-aeeb27f4ec28.png" width="20%" >

By default, the RGB effect on these fans is some kind of rotating rainbow. As no other device is attached to the fans (no hub or controller), *something* in one of the fan must be generating a signal that was used to drive the led stripes. Moreover the same exact effect was present in all fans before they failed and they were perfectly synchronized before the failure (see picture below) confirming that the 4th wire was actually used to share the same data across all fans. No information about the protocol being used on the data line is available and the documentation of Aerocool was not helpful to figure out anything regarding the pinouts.

<img src="https://user-images.githubusercontent.com/12449790/222851897-d08e5391-6db4-41fa-987f-d0d9c0ed86b9.png" width="20%" /> 

As I had little faith in being able to fix the problem - I was first suspecting a nasty microscopic shortcut in one of these wires - I decided to unmount all fans, order another set of hopefully better fans from a different manufacturer (Be Quiet) meanwhile and try to understand how this thing was supposed to work.

## Analysis of the (defective) Fan
I first focused my attention on the fan that looked to be defective and had some white leds that kept being on. This is also the only fan that has another set of wires going to a two pins connector which is supposed to be connected to the reset switch of the case (to change color modes) - note that the "manual" shipped with the case did not mention this... There are several wires arriving on the small PCB that is behind the motor.

From top to bottom on the picture:
* 12V
* -12V, wrongly marked as it's GND
* GND, yellow wire
* White
* +5V, black wire at bottom
<img src="https://user-images.githubusercontent.com/12449790/223825532-b59d7e27-6e44-4b00-95dc-e715f6ed67d0.png" width="20%" /> 

This pattern *confirms that the white must be some kind of serial line* dedicated to color commands as the other wires are only for power.

Next I cut open the whiteish circle band that covers the outer leds strip, near the point where the yellow/white/black wire are arriving from the central PCB. 
Two sections are visible. 

The first one is dedicated to the "color mode".
* GND --> going to reset switch
* Key --> going to reset switch

The 2nd part
* TB, purpose is unknown. Maybe for testing
* GND (yellow)
* IN (white), corresponds to the serial data line 
* +5V (red)
<img src="https://user-images.githubusercontent.com/12449790/223826454-7d1ffd0f-6624-44f2-a5d4-564fff089b72.png" width="20%" />

Next to these connections, I discovered a small IC with a very suspicious black spot on top. Scratching it with the finger nail leaves some charcoal residue, it is definitely burnt. Now I get a full picture of the failure and concludes that this IC is the *brain* and is directly commanding the data line. As this data line is then shared with other fans, it can send the same signal to all fans that are connected to the main one (and thus have colors in sync).

This IC was either defect, got shorted or subject to some current spikes and failed after a few startups.

<img src="https://user-images.githubusercontent.com/12449790/223828801-5eb8812a-428b-4933-a45e-4352ddba71d0.png" width="20%" />



