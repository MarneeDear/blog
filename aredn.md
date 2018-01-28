# Why am I doing this?

I am into amateur radio. I have a [Technician Level](http://www.arrl.org/licensing-education-training) license, which means I can transmit on certain radio bands, including the bands used for high-speed data, like WiFi. We can use the unlicensed spectrum, like everyone else, and we can use special frequency bands allocated only to licensed Hams. This means we can get away from the noisy parts of the spectrum, and setup our own networks.

It is common to setup a WiFi network on the Ham bands for emergency communications. Sometimes Hams use these networks to do emergency support on long distance trail races, like 24 Hours in the Old Pueblo, to maintain region-wide mesh networks in the case of power outages or major network outages, and lots of other things.

You can read more about Ham radio on the [ARRL site.](http://www.arrl.org/)

Southern Arizona has a really big mesh network using [AREDN -- Amateur Radio Emergency Data Network](https://www.aredn.org/about-us). With a bit of equipment and firmware I can join this network. This is how I did it.

# Getting started

If you are interested in getting involved, or in Ham radio in general, the best place to start is joining your local Ham radio club where you will probably meet lots of [Elmers](http://www.eham.net/articles/29423) that can help you get started. This is how I did it. I belong to OVARC (Oro Valley Amateur Radio Club). Here I met an [Elmer](http://www.eham.net/articles/29423) who is highly active in the local Emergency Communication community. Through him I was able to get a pre-configured radio that I just needed to connect to an antenna and point at one of the tallest nodes in the city. I was even able to get a (free) used antenna from the Pima County Emergency Communication and Operations center. They were replacing some equipment and just giving away the flat panel antennas they were using. 

# What I used

* [Ubiquiti Bullet M2 Titanium outdoor radio](https://www.ubnt.com/airmax/bulletm/)
* A used flat panel, directional antenna
* Two ethernet cables
* Linksys WAP54G wireless access point
* A laptop with an Ethernet connection

## The Ubiquity Bullet M2 Titanium outdoor radio

I got a [Bullet M2 Titanium](https://www.ubnt.com/airmax/bulletm/) pre-configured with the [AREDN software](https://www.aredn.org/about-us) from a Ham in the OVARC Ham club, ready to go with the configuration for the [Southern Arizona mesh network.](https://sites.google.com/site/k7uazclub/mesh-network)

If you don't have a pre-configured Bullet, or your AREDN firmware is old, you can flash the firmware yourself by connecting your laptop to the Bullet with an Ethernet cable. You can download the latest version of the AREDN software [here](https://www.aredn.org/content/software). Scroll down to find the firmware for your radio. Then you can use the admin page for the Bullet to upload the new firmware. How you do this will depend on what is already installed on your Bullet. It does come with the airOS interface and the AREDN firmware comes with an admin interface where you can install and patch firmware.

## The flat panel antenna

I was lucky and was able to get a free antenna from the Pima County Emergency Communication and Operations center. If you know enough Hams you can probably either get a used antenna or borrow one. I am using a flat panel antenna, which means I have to point it directly at another node in order to pick up a signal.

I don't know the brand but it is basically [this](http://www.l-com.com/wireless-antenna-24-ghz-flat-panel-directional-antennas) and it works just great.

I had to do some modifications to get the Bullet installed. It had previously been used in a video system, so it had a plastic enclosure with an Ethernet controller inside. I removed the top of the enclosure, and the controller inside, and was then able to screw in the Bullet, like this.

![Imgur](https://i.imgur.com/El63k6l.jpg)

In order to make this stable, I should remove the rest of the enclosure and use the elbow connector that came with the Bullet.

The Bullet is supposed to be weather proof, so I should not need to put it in an enclosure (the weather in Southern Arizona is pretty mild most of the time, too).

Pro Tip: It took me a while to figure this out. There is a black cap with a rubber collar that you can wrap around the Ethernet cable. You can take out the rubber collar and open it up to wrap around the Ethernet cable and then screw the pieces together like this:

![Imgur](https://i.imgur.com/hDazMJx.jpg)

## The Wireless Access Point

To make it easier to get my computers on the mesh network, I decided to plug in one of the Linksys WAP54G wireless access points I got for a really good deal at the OVARC Hamfest.

After I configured my WAP54G, I just connected it to the LAN port on the Bullet POE box.

Now I can put any of my computers on the network. I plan on hooking up a Raspberry Pi 3 and using it to serve an application I have in mind.

Note: for some reason my WAP54G stops working and I have to cycle power to the WAP, so this might not be a long-term solution.

# Putting it all together

This is my setup. Now I just need to figure out how to mount this outside with line of sight to the central node (on the 5151 Broadway building).

![Imgur](https://i.imgur.com/kg2TnuP.jpg)

# What's next?

I want to build a messaging application that also use IPFS that can be used on the mesh network. I will probably use a Raspberry Pi 3 as the server.
