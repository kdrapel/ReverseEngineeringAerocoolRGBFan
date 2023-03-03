# Reverse Engineering of an AeroCool RGB Fan 

This article describes some analysis performed on a defective Aerocool fan. Model is WX-14025 (14 cm), "Made in China" with a sticker indicating 12V-DC 0.25A on the back. 
## Defect Description
The fan was part of a Trinity White Tower V3 (have doubt now whether this was really a V3) made by Aerocool and shipped with two front fans of 14 cm and one back fan of 12 cm, all of them with a flashy RGB effect out of the box. After only a few startups, the lower front fan was displaying a steady white color in one corner. The rest of the fans were not illuminated (hue in the upper fan in the picture is coming from some backlights). 

<img src="https://user-images.githubusercontent.com/12449790/222844467-a7b4a56f-69a4-4225-8512-cd9ee8be95d3.png" width="30%" >

## First Thoughts
After verifying all cables and trying several times to reconnect the SATA power, I came to the conclusion that some electronical issue was present and somehow explain the low cost of the whole unit. Each fan has four wires. The first fan has a special connector such that the daisy-chained set of fans can be powered by a single SATA power cable. The last wire, which is not wired to the SATA power connector, is clearly dedicated to some data line and this data line is shared across all the fans.

<img src="https://user-images.githubusercontent.com/12449790/222847108-f93eb58c-3733-4bc3-81fd-aeeb27f4ec28.png" width="20%" >

By default, the RGB effect on these fans is some kind of rotating rainbow. As no other device is attached to the fans (no hub or controller), *something* in one of the fan must be generating a signal that was used to drive the led stripes. Moreover the same exact effect was present in all fans and they were perfectly synchronized.
