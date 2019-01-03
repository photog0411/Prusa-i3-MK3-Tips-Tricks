# Prusa i3 MK3 Tips and Tricks

Resources for Prusa i3 Mk3 Printer. The following information are things that I have learned from my 3D printing journey with the Prusa Mk3. I don't claim that everything here is 100% factual, and if you think I am providing any incorrect information, please let me know by posting on my Github Issues list!


# Table of Contents  


**Getting Up And Running**

• [Calibrating Live Z My Way](#livez)

• [Calibrating Extruder Idler Tension](#tension)

• [Calibrating Extruder E-Steps](#esteps)

**Troubleshooting**

• [First Layer Adhesion Issues And How To Fix Them](#flai)

• [Problems Printing PETG Unless Very Slow Speed Used](#petg)

• [Need More Help?](#help)

**Take Things To The Next Level**

• [My Optimized Startup GCODE](#gcode)

• [Slic3r PE On Meth](#meth)

• [Achieving Better Print Quality](#quality)

• [Recommended Mods For Your Mk3](#mods)






# Calibrating Live Z My Way
<a name="livez"/>

Calibrating your Live Z is the most important thing you can do, because your first layer is the most important layer and is what supports all of your other layers. Unfortunately, the Prusa Mk3's built-in live Z calibration has many flaws. For one, it only works with .40mm nozzle size (stock nozzle). Second, the little square it prints at the end for inspection is very small and prints too fast to make any last minute adjustments. I've found this to be the best way to Calibrate Live Z:

1. Download my [100x100x0.20 calibration STL](https://github.com/photog0411/Prusa-i3-Mk3-Resources/blob/master/Calibration%20STLs/LiveZ100x100.stl) file.
2. Open it into Slic3r and slice it with the following settings:
* 0.20mm Layer Height
* Infill Before Perimeters
* Rectilinear top/bottom infill with fill angle of 90 degrees.
* Bed temp at the bed temp you will use for printing the majority of your things.
3. Now print it! It will begin printing a big live Z calibration square that is much easier to see what's going on than with the built-in Prusa one. Because you did infill before perimeters, it will start much quicker because you don't have to wait for the uneeded permieter to get laid down first. And because you have the infill angle set to 90 degrees, it won't waste filament in the corner of the square where you can't see whta's going on.
4. Press the knob on the front of your printer and enter the live Z adjustment mode.
5. Adjust the live Z number until each line that gets laid down has no gaps between it and the last one. You don't want to smush the plastic down too much where the plastic layer developes ridges in it, but really do not want any gaps. I would actually recommend starting at -0.300 and working your way up from there in -0.100 increments. Once you get to a number that looks good (it was -0.700 for me) you can adjust in smaller increments of -0.025 to get it perfect.
6. If you use multiple nozzle sizes, be sure to make a calibration GCODE for each of your nozzles! I personally have a /calibration/ folder on my SD card and calibrate the live Z whenever I swap nozzles.

# Calibrating Extruder Idler Tension
<a name="tension"/>

It's a good idea to make sure your idler in your extruder has proper tension - too loose and it won't grab the filament well enough, too tight and it will crush the filament and cause extrusion issues. It's very simple to get it tensioned properly!

1. With filament loaded and printer preheated for your filament, undo the two screws (or one screw if you are using bear extruder) that hold the idler door in place. These are the screws with the springs on them.
2. Swing the door open fully, make a black mark somewhere on the filament with a sharpie marker for visual reference.
3. Make your printer move the extruder quite a bit, you can do this from inside the printer menu. At this point, the filament should not be moving even though your extruder motor is moving.
4. Close the idler door lightly, and then start screwing the screws in slowly. You want to keep screwing them in until they JUST start to grab the filament and the filament starts moving and extruding.
5. Once it has just grabbed the filament, tighten the screws just a bit more (a few more complete turns).

Now your extruder idler should be properly tensioned!


# Calibrating Extruder E-Steps
<a name="esteps"/>

It's important that when you tell your printer to extrude 100mm of filament, it actually does extrude 100mm of filament. If it's extruding more than you request, it's over-extruding, and if it's extruding less than you request, it's under-extruding. Overextrusion can make for worse print quality on the ouside edges and cause bridges to sag too much, and under-extrusion can cause your prints to be weaker or not have proper infill. 

This is all controlled in your printer firmware by a thing called E-Steps. By default, the Prusa MK3 E-Steps are set to 280, which should be perfect for most printers. But you should still double check it! Here's how:

1. With filament loaded, move your extruder by hand so that it's directly below the filament spool and the filament is straight up and down.
2. Measure 120mm from where the filament enters your extruder and put a mark 120mm up on the filament with a black sharpie marker.
3. Connect the printer to your computer via USB, open Pronterface, and connect to the printer in Pronterface. 
4. Preheat your printer, wait for extruder to be warmed up, and then use the extrude button and settings in Pronterface to extrude 100mm of filament at 100mm / minute. 
5. Once it's done extruding, measure from the top of the extruder to the black mark you made. It should measure exactly 20mm, because you marked out 120mm but asked the printer to extrude 100mm.

Did you get 20mm left? If so, great! If not, remember the amount you measured and follow the steps below to calibrate:

1. Mark the filament again 120mm from the top of the extruder. 
2. In pronterface, issue M503 command in the terminal. It should return steps value, you want to remember the one that says "EXXX" where XXX is the steps number for extruder. If you have never done this before, it should be 280. Those are your extruder steps value currently.
3. Now subtract the amount that was left over from 120mm in the first instructions. So for example if you had 10mm left over instead of the correct 20mm, you would do 120-10=110. This means that your extruder actually extruded 110mm of filament when you asked it to do 100mm.
4. Multiply your current extruder steps value by 100. So if it was at the default of 280, you would do 280x100=28000.
5. Now divide that big number (28000 in example) by the real amount your extruder did. In above example, we said 110 so you would do 28000/110 = 254.54. This will be your new E-Steps value instead of 280. Now you need to actually change this value and save it in your firmware.
6. In Pronterface terminal, issue command M92 EXXX.XX where the X's are your number. In above example, you would do M92 E254.54.
7. Now issue command M500 to save it to your firmware, and you can also do M503 again to verify that the changes were saved.

Your extruder should now be calibrated and pushing 100mm of filament when you ask for it. But you need to verify that it's correct now!  Just start these steps all over again and mark out the 120mm on the filament, and measure when it's done extruding. You should now have 20mm left when you asked it to extrude 100mm.

# First Layer Adhesion Issues And How To Fix Them
<a name="flai"/>

Before trying the steps below, make sure you have [calibrated your live Z correctly](#livez). Ignore the Prusa video where Josef says it should be a value of around -1.200. Your value will depend on how far up your PINDA is mounted and how thick your bed plate is. Your live Z value is adjusted properly when the lines of plastic that are laid down do not have any gaps in between them. If you think your live Z is adjusted properly, you may begin the following steps: 

1. Clean your bed plate with [99.9% Isopropyl alcohol](https://www.amazon.com/dp/B005DNQX3C/ref=cm_sw_r_cp_apap_2tu8Mct2ObEU7) and UNSCENTED paper towels. Do not use anything less than 99.9% and do not rub it in a circular motion, rather, rub it from side to side. Your bed plate has oils on it from the manufacturing process, and from whenever you touch it with your oily hands. Your goal is getting the oil off, not spreading it around! It's very important to also use unscented paper towels with no coloring designs as these may have oils in them.  

2. If the isopropyl alcohol fails to help, use unscented dish soap and hot water to clean your bed plate in the sink. Scrub it well, and dry with unscented paper towels. Be sure to not touch the clean bed plate with your hands once washed (grip the edges only). Soap breaks down grease and oils even better than Isopropyl alcohol, but is obviously a more annoying process. If you don't touch your bed plate too much, you should be fine with washing your plate in the sink once per month, and rubbing it with isopropyl alcohol once a day before your first print of the day. 

3. If you are still having issues, try printing your first layer hotter. Hotter plastic is more "liquidy" and will spread on the bed easier, as well as stick better. I personally use 220c for PLA and 265c for PETG first layers. 

4. Make sure fan is disabled for first layer in slic3r. You might even want to disable it for the first 2 or 3 layers for  smaller prints, as your plastic being laid down might be cooling too quickly for it to stick. 

5. Try a warmer bed temperature. I personally find that my bed is actually 5c colder than the printer reports after measuring it with an IR thermometer. So I print with my bed temp at 65c for PLA instead of the default of 60c. 

6. If you are using a smooth PEI sheet, rough it up gently with a scotchbrite pad or fine grit sandpaper. It won't affect your print quality and the rough surface will create more friction for the hot plastic to stick to. 

7. Run the temperature calibration procedure in your printer's options. It will calibrate your PINDA to be more accurate which will help your bed to be more level.

8. Use my startup GCODE below. I've highly optimized it to give the best possible first layer results.

9. If all else fails, buy a powder coated sheet. You can get an aftermarket one on Amazon called [thekkiinngg v3](https://www.amazon.com/Thekkiinngg-Double-Sided-Textured-Powder-Coated-Version/dp/B07HQZCWDV/ref=sr_1_1?s=hpc&ie=UTF8&qid=1546403022&sr=8-1&keywords=prusa+powder+coated) for $60. It's a bit pricey, but PLA sticks amazingly well to this sheet,  and I love the textured finish it gives my prints. Once Prusa begins selling their own powder coated sheets again, they will be a better option.

# Problems Printing PETG Unless Very Slow Speed Used
<a name="petg"/>

I had major issues printing PETG using the default Prusa profiles - the infill would just crumble and break. After much trial and error, I discovered the only way to get it to print was to lower the infill speed down ridiculously low to like 15mms. Obviously, this was not preferred. But I was able to eventually get it solved! The easy solution is to just print at 265c for PETG. The Prusa default of 235c is too cold. As someone on the Prusa discord server said to me, "PETG likes it hot". This is absolutely true. After switching to 265c for PETG, it prints faster and more beautiful for me than ever before.



# My Optimized Startup GCODE
<a name="gcode"/>

The following startup GCODE is my custom version which has several advantages over the default Prusa startup gcode. First, it waits until the PINDA is at or below 35c before it does anything, and it enables the print fan to help cool it down quicker in case it's already too warm. It then proceeds to preheat the nozzle to warm temperature (but not the actual print temperature) so that when the printer is doing the automated bed leveling, you do not get plastic leaking everywhere. It sets a standardized bed temp of 65c for the automated bed leveling procedure (change this to whatever bed temperature you do your live Z adjust at) because studies have shown that doing the mesh bed leveling at the same temperature that you did your live Z adjust will be the most accurate. Then, it warms up and waits for your PINDA to reach 35c because studies have also shown that the PINDA is the most accurate after it has warmed up to 35c or warmer and does the mesh bed leveling. Finally, it warms everything up to your real printing temperatures, turns the print fan on so you don't get any ooze, runs an advanced purge line that purges better and is easier to remove from your bed (PETG!), and finally your print begins. 

```M83  ; extruder relative mode

; PINDA cooling
M104 S0 ; Turn off nozzle heat if it's on
M140 S0 ; Turn off bed heat if it's on
M106 S255 ; turn on fan to cool PINDA
M860 S35 ; wait until PINDA is <= 35C
M106 S0 ; Turn cooling fan off if PINDA is cooled

; Standardized bed temp and nozzle preheating
M104 S150 ;preheat nozzle temp for no-ooze and heat up PINDA in the process
M140 S65 ; Set standardized bed temp for mesh leveling and heat up PINDA in the process
G28 W ; go home without mesh bed leveling


; Mesh bed leveling with standardized bed temp and warm PINDA
M190 S65 ; wait for bed temp to finish
M860 S35 ; wait until PINDA is >= 35C
G80 ; mesh bed leveling

; Goto start corner and come up to final temp
G1 Y-3.0 F1000.0 ; go outside print area
M106 S255 ; Turn cooling fan on to prevent oozing and make purge line easier to remove
M104 S[first_layer_temperature] ; set extruder temp for printing
M140 S[first_layer_bed_temperature] ; set bed temp for printing
M109 S[first_layer_temperature] ; wait for extruder temp
M190 S[first_layer_bed_temperature] ; wait for bed temp to finish

; Start the improved purge line
G92 E0.0 ; reset extrusion distance
G1 E2 F1000 ; de-retract and push ooze
G1 X20.0 E6  F1000.0 ; fat 20mm intro line @ 0.30
G1 X60.0 E3.2  F1000.0 ; thin +40mm intro line @ 0.08
G1 X100.0 E6  F1000.0 ; fat +40mm intro line @ 0.15
M106 S0 ; Turn cooling fan off now for enough time to stop spinning
G1 E-0.8 F2100; retract to avoid stringing
G1 X99.0 E0 F1000.0 ; -1mm intro line @ 0.00
G1 X110.0 E0 F1000.0 ; +10mm intro line @ 0.00
G1 E0.6 F1500; de-retract
G92 E0.0

; And a beep to let us know the print is starting!
M300 S100 P10 ; chirp
```


# Slic3r PE On Meth
<a name="meth"/>

A wonderful github user named [Supermerill](https://github.com/supermerill) has created a custom version of Slic3r PE called [Slic3r++](https://github.com/supermerill/Slic3r). I highly recommend using this version, because it has a lot of new features that the official Slic3r PE is lacking, such as but not limited to top surface ironing, XY hole compensation, continuous perimeter loops, interior top layer supports, avoiding unsupported perimeters, and more! Supermerill  keeps his version current with the latest release of Slic3r PE (so you won't miss out on anything from the main version!) and he's really smart (he's actually the guy that invented Gyroid infill). 

# Achieving Better Print Quality
<a name="quality"/>

There are many things you can do to your Prusa MK3 printer to make it print objects at higher quality. Mostly, it will be print settings in Slic3r that you will have to tweak with trial and error on a per object basis. But let's discuss acheiving better quality other ways!

* Tensioning The Belts (WIP)
* Lubricating The Bearings(WIP)
* Buying Better Bearings (WIP)
* And More (WIP)

The Prusa printers are known to be cursed with a problem called the [602 Inconsistent Extrusion Issue](https://github.com/prusa3d/Prusa-Firmware/issues/602). Follow that link for example photos and more information. Basically, it leads to layer lines on the sides of your prints sticking out our being recessed in at spots which is very noticable. This is very hard to correct 100%, but there are some things you can do to help fix this problem a bit - those are listed below. The main cause of this issue is inadequate heatbreak cooling.

* [Sunon Extruder Fan](https://all3dmakers.com/products/sunon-mk2-5-mk3-5v-hotend-cooling-fan). This is a replacement fan for the default Noctua fan that ships with the printer. It is much more powerful, at the expense of being louder. But it will help your 602 Issue very much by cooling your heatbreak much better!
* [Bear Extruder and X Axis](https://www.thingiverse.com/thing:3226689). This is a whole new extruder and X-axis for your Mk3, but everything is printable. It's a big project, but very much worth it. It has better cooling for the heatbreak and will make your 602 Issue not as noticable, as well as make the printer much easier to disassemble in the future for troubleshooting. 
* [Bondtech BMG Extruder](https://www.bondtech.se/en/product/prusa-i3-mk2-5-mk3-extruder-upgrade/). This is a whole new extruder  for your Mk3, and it's expensive. But because it has much better cooling than the stock extruder and a better gearing ratio, it has been shown to almost completely eliminate the 602 Issue.
* [Better Thermal Paste for your Heatbreak](https://www.amazon.com/Thermal-Grizzly-Kryonaut-Grease-Paste/dp/B011F7W3LU). Since we know that one of the leading causes of the 602 Issue is bad heatbreak cooling, it couldn't hurt to put a higher quality thermal paste on your heatbreak. Simply unscrew the heatsink from the heatbreak, remove the old thermal paste, and apply the new paste gingerly to the threads. This probably won't help a whole lot, but it can't hurt! The link above is for Thermal Grizzly Kryonaut paste which is very good and what I'm using, but if you want the very best paste look into something called CooLaboratory Liquid Pro - it's the very best paste you can buy, but very expensive.


# Recommended Mods For Your Mk3
<a name="mods"/>

Below are mods I highly recommend you make or purchase for your Mk3. They will either make your life easier, or improve the quality of your prints. Maybe both.

* [Bear Extruder and X Axis](https://www.thingiverse.com/thing:3226689). This is a whole new extruder and X-axis for your Mk3, but everything is printable. It's a big project, but very much worth it. It will increase your print quality, make it easy to tension your X axis belt, and also make your printer easier to take apart or fix in the future. I highly recommend it, but make sure to print a [compatible fan duct](https://www.thingiverse.com/thing:3320490) for it too!
* [Taurus Y Axis](https://www.thingiverse.com/thing:3269389). This is a new Y motor mount and belt system, mostly all printable as well. It is much stronger than the default Y axis parts, and allows you to easily tension your Y axis belt. You may notice a slight increase in print quality with this.
* [Mk3 Dust Filter](https://www.thingiverse.com/thing:2983334). Easily print and make a dust filter for your extruder. This will help prevent nozzle clogs from happening by filtering dust from your filament. This is only compatible with stock extruder, not bear extruder.
* [Extruder Visualizer](https://www.thingiverse.com/thing:2638857). Decorate your extruder, but at the same time be able to see what your motor is doing. Great for troubleshooting extruder movements. There are many other design available on thingiverse, or you make your own. All you need is a magnet and some glue.
* [E3D Nozzle Holder](https://www.thingiverse.com/thing:3280665). A great way to store your E3D nozzles if you have a bunch of them.
* [E3D Nozzle Change Tool](https://www.thingiverse.com/thing:3277211). Change your nozzles very easily if you have multiple nozzles.
* [Prusa Mk3 Top Tool Holder](https://www.thingiverse.com/thing:3175038). Conveniently store and access the most frequently used tools.




# Need More Help?
<a name="help"/>

Check out the Prusa Owner's Wiki:

https://github.com/mcm001/prusaOwnersWiki


Or ask a question in the 602 Wasteland Discord Chat:

https://discord.gg/hYUjSnW
