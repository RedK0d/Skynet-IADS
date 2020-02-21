# Skynet-IADS
![logo](https://github.com/walder/Skynet-IADS/raw/master/images/SA3_2.jpg)

An IADS (Integrated Air Defence System) script for DCS (Digital Combat Simulator).

# Abstract
This script simulates an IADS within the scripting possibilities of DCS. Early Warning Radar Stations (EW Radar) scan the sky for contacts. These contacts are correlated with SAM (Surface to Air Missile) Sites. If a contact is within firing range of the SAM Site it will become active. A modern IADS also depends on command centers and datalinks to the SAM Sites. The IADS can be set up with this infrastructure. Destroying it will degrade the capability of the IADS.

# IADS Elements

## IADS
The IADS doesn't exist as a physical object in the game world. Think of it as the network holding everything together. You can have multiple IADS instances in a DCS Mission. However individual IADS currently don't communicate between each other. Also don't add units to more than one Skynet IADS. You have seen the films, you know what happens when Skynet goes bananas.

## SAM Site
Skynet can handle 0-n Sam Sites, it will try and keep emissions to a minimum, therefore SAM sites will be turned on only if a target is in range. Every single launcher and radar unit's distance of a SAM site is analysed individually. If at least one launcher and radar is within range, the SAM Site will become active. This allows for a scattered placemend of radar and launcher units as in real life.

##  Early Warning Radar
Skynet can handle 0-n EW Radars. For detection of a target the DCS radar detection logic is used. You can use any type of radar for EW in Skynet. Some modern SAM units have longer range radars then the EW Radars, eg S300 vs EWR 55G6.

##  Power Sources
By default Skynet IADS will run without having to add power sources. You can add power sources to SAM Units, EW Radars and Command Centers.
Once a power source is fully damaged the Skynet IADS unit will stop working. You can add multiple power sources to a Skynet IADS unit.

Nice to know:
Taking out the power source of a command center is a real life tactic used in Suppression of Enemy Air Defence (SEAD).

## Connection Nodes
By default Skynet IADS will run without having to add connection nodes. You can add connection nodes to SAM Units, EW Radars and Command Centers.

When a connection node is fully damaged the the unit disconnected from the IADS will go in to autonomous mode. For a SAM Unit this means it will behave in its autonomous mode setting. If a command center looses its node all SAM Sites will go autonomous. If a EW Radar looses its node it will no longer contribute information to the IADS but otherwise the IADS will still work. 

Nice to know:
A single node can be used to connect an arbitrary number of Skynet IADS units. This way cou can add a single point of failure in to an IADS.

## Air Resources
Currently Skynet only works with ground based units. Incorporating air units is planned at a later date.

# Electronic Warfare
A simple form of jamming is part of the Skynet IADS package. It's off by default. The jamming works by setting the ROE state of a SAM Site. The closer you get to a SAM site the more ineffective the jamming will become. For the jammer to work it will need line of sight (LOS) to a radar unit. 
Older SAM sites are more susceptible to jamming. 

Here is a [list of SAM sites currently supported by the jammer](https://docs.google.com/spreadsheets/d/16rnaU49ZpOczPEsdGJ6nfD0SLPxYLEYKmmo4i2Vfoe0/edit#gid=0) and its effecitveness. 
When setting up a jammer you can decide which SAM Sites it can able to jam. For example you could design a mission in which the jammer is not able to jam a SA-6 but it is able to jam a SA-2. The jammer effeciveness is not based on any real world data I just read about the different types and made my own conclusions.
In the mission editro you add the jammer to a unit. I suggest you take an F-111 as jammer plattform and add it to your strike package.


# Using Skynet in the mission editor
Skynet requires MIST. A version is provided in this repository or you can download the most current version [here](https://github.com/mrSkortch/MissionScriptingTools). It's quite easy to setup an IADS have a look at the demo missions in the /demo-missions/ folder.

## Mission editor
This tutorial assumes you are familiar on how to set up a SAM site in DCS. If not I suggest you watch [this video](https://www.youtube.com/watch?v=YZPh-JNf6Ww) by the Grim Reapers.
Place the IADS elements you wish to add on the map. Currently only russian units have been tested altough western units should work just as fine.
![Mission Editor IADS Setup](https://github.com/walder/Skynet-IADS/raw/master/images/iads-setup.png)  

## Preparing a SAM site
There should be only be one SAM site type in per group. If differenct SAM sites are mixed in one group distance calculation for the IADS will be mixed up.
The skill level you set on a SAM Group is retained by Skynet. Make sure you name the SAM site **group** in a consistent manner with a prefix eg 'SAM-SA-2'.  
![Mission Editor add SAM site](https://github.com/walder/Skynet-IADS/raw/master/images/add-sam-site.png)  

## Preparing an EW radar
You can use any type of radar as an EW radar. Make sure you name the **unit** in a consistend manner with a prefix, eg 'EW-center3'.  
![Mission Editor EW radar](https://github.com/walder/Skynet-IADS/raw/master/images/ew-setup.png)  

## adding the Skynet IADS
For the IADS to work you just need three lines of code:

create an instance of the IADS:  
`iranianIADS = SkynetIADS:create()` 

Give all SAM groups you want to add a common prefix in the mission editor eg: 'SAM-SA-10 west', then add this line of code:  
`iranianIADS:addSamSitesByPrefix('SAM')` 

Same for the EW radars, name all units with a common prefix in the mission editor eg: 'EW-radar-south':  
`iranianIADS:addEarlyWarningRadarsByPrefix('EW')` 

Activate the IADS:  
`iranianIADS:activate()` 

## Advanced Features

### Adding a power source
You can add a Unit or Static Object ad a power source to a SAM site. If its destroyed the SAM site will shut down.

### Adding connection nodes

### Adding a SAM site manually

The advanced setup alows you to add a SAM Site individually and add connection nodes and power sources.

Add an early warning radar with a power source and a connection node. Make sure the Units and StaticObjects exist in the mission:  
```
earlyWarningRadar = Unit.getByName('EWR')  
ewPower = StaticObject.getByName("EW Power Source")
ewConnectionNode = StaticObject.getByName("EWR Connection Node")    
iranianIADS:addEarlyWarningRadar(earlyWarningRadar, ewPower, ewConnectionNode))
```

You can also just add an EW Radar omitting the power source and connection node:  
`iranianIADS:addEarlyWarningRadar(earlyWarningRadar)`

Add a SAM Site like this:  
```
powerSource = StaticObject.getByName("SA6-PowerSource") 
sa6Site2 = Group.getByName('SA6 Group2')
connectionNode = StaticObject.getByName("Connection Node") 
iranianIADS:addSamSite(sa6Site2, powerSource, connectionNode, SkynetIADSSamSite.AUTONOMOUS_STATE_DARK)
```

The autonomous mode options are:  
`SkynetIADSSamSite.AUTONOMOUS_STATE_DARK` = SAM Site will go dark if it looses connection to IADS  
`SkynetIADSSamSite.AUTONOMOUS_STATE_DCS_AI` = SAM Site will behave in the default DCS AI. Alarm State will be red and ROE weapons free.

You can also just add a SAM site omitting power source and connection node:  
```iranianIADS:addSamSite(sa6Site2)```

Add a command center (optional):  
```
commandCenter = StaticObject.getByName("Command Center") 
iranianIADS:addCommandCenter(commandCenter)
```

### debug infomation
When developing a mission I suggest you add debug output to check how the IADS reacts to threats:

```
local iadsDebug = iranIADS:getDebugSettings()  
	iadsDebug.IADSStatus = true  
	iadsDebug.samWentDark = true  
	iadsDebug.contacts = true  
	iadsDebug.samWentLive = true  
	iadsDebug.noWorkingCommmandCenter = true  
	iadsDebug.ewRadarNoConnection = true  
	iadsDebug.samNoConnection = true  
	iadsDebug.jammerProbability = true  
	iadsDebug.addedEWRadar = true
```

# Thanks
Special thaks to Spearzone and Coranthia for researching public available information on IADS networks and getting me up to speed on how such a system works.
Also I shamelessly incorporated Grimes SAM DB.
