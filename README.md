# Edgerouter 4/6P Config for Google Fiber
> Free yourself from the 'Network Box'

This guide will take you through, step by step, setting up your Edgerouter 4 or Edgerouter 6P for use with Google Fiber's gigabit service. Some of the guide will be using EdgeOS to configure the masic setup, but we will need to use the CLI for the last bit. Some level of knowledge regarding routers and the use of the CLI is assumed for this guide.  
  
**For this config to work properly, your Edgerouter must be at factory default settings**

---

## Step 1 - EdgeOS

- First, ensure your Edgerouter is set to factory default settings. Then, you'll want to connect your computer to the eth0 port on the front of the Edgerouter via an ethernet cable.  
We need to mnually set the IP address of our computer to be on the same subnet as the router. By default, the router is on the subnet 192.168.1.0/24  
We can choose any IP we like in this range, apart from 192.168.1.1, which is the Edgerouter itself. I'll use 192.168.1.10   
On a Mac, your settings for your connection should look like this:

![](https://i.imgur.com/EH5iUdt.png)

Be sure to hit `Apply` after changing your settings.  
Once you're on the right network, you can access EdgeOS in your web browser at this address:

```
https://192.168.1.1
```

Sign in using the default username and password:
```
username: ubnt
password: ubnt
```

- Next, let's click on the `Wizards` tab above the dashboard.  
We'll be using the `WAN+2LAN2` wizard for our inital setup:

![](https://i.imgur.com/OlirjnF.png)

- Once in the `WAN+2LAN2` wizard, we need to configure a few things.  
For the first box labeled `Internet Port`, set your port to `eth0`.  
Check the box labeled `Internet Connection is on VLAN`, and set the value to `2`.  
We can leave the rest of the settings as they are:

![](https://i.imgur.com/ZY9CkXt.png)

- For the next two boxes, you can leave them at their default values if you like.  
Enabling Bridging will let you use both the `eth1` and `eth2` ports for your wired network, however; this will theoretically reduce the performance of your router since it will handling switching with the software. I'm going to leave it unchecked.  
The `LAN Ports` box will allow you to set the IP range of your wired network. Unless you know what you're doing, you should leave this as it is. I also recommend leaving `DHCP` enabled. This means your router will be handing out IPs to the devices on your network.

![](https://i.imgur.com/2MQYvqS.png)

- The last box in this wizard is very important. For security, you should absolutely create a new admin account and password on your router.  
Check the option labeled `Create new admin user` and enter a new username and password:

![](https://i.imgur.com/dx5fV18.png)

- Now click the `Apply` button, and reboot your router when prompted.

**At this point, you now need to switch your computer from using the `eth0` port on the router to the `eth1`port.  
You also need to switch your network settings on your computer back to using DHCP to automatically get an IP address:**

![](https://i.imgur.com/Qhp6Jj3.png)

- Once your router reboots we'll move on to the CLI commands. You can either access the CLI in EdgeOS by going to this address in your browser
```
https://192.168.1.1
```
and clicking on the `CLI` button in the top right,  
or you can use SSH to access the router in your terminal/command line
```
ssh yourusername@192.168.1.1
```

## Step 2 - CLI

Your router should now be able to connect to Google Fiber! If you tested your connection at this point though, you would realize that you aren't getting the full gigabit speeds. We need to run two sets of commands in the CLI to letus take full advantage of our connection.  
**For each set of commands, copy/paste each line individually**

- Before we start changing our configuration, we need to enter edit mode. Run this command:

```
configure
```

You should now see `[edit]` in the terminal after every command you run. This lets you know you are still in edit mode.

- The first command will set some QoS settings so that Google Fiber prioitizes our data correctly.

```
set interfaces ethernet eth0 vif 2 egress-qos "0:3 1:3 2:3 3:3 4:3 5:3 6:3 7:3"
```

- Now, we probably want to enable hardware offloading to ensure we're getting the best performance from our router. Run the following commands:

```
set system offload ipv4 forwarding enable
set system offload ipv4 gre enable
set system offload ipv4 pppoe enable
set system offload ipv4 vlan enable

set system offload ipv6 forwarding enable
set system offload ipv6 pppoe enable

set system offload ipsec enable
```

- And lastly, we need to commit our configuration and save it to the router. Run this command:

```
commit ; save
```


## Step 3 - Setting up your LAN
That's it! Not so bad huh?  
The very last thing you need to do is finish setting up your network, wether that's connecting a switch so you can hardwire some devices, or setting up your wireless access point for WiFi.  
**Make sure that whatever you plug into the router, you connect it to the `eth1` port**  
If you're connecting a wireless access point, a switch, a switch with a wireless access point connected to it, just be sure your local network starts at the `eth1` port of your Edgerouter.  
**Also, if you're connecting a device for WiFi, I recommend you set it to 'Bridge Mode'**  
If you're using a typical 'Wireless Router' for WiFi, setting it to 'Bridge Mode' will turn off it's DHCP server, so it won't also try to hand out IP addresses. You've got your Edgerouter to take care of that for you.  

Enjoy!  

---

## Author

Bobby Craig – [@bobbyrc](https://github.com/bobbyrc)

Distributed under the MIT license. See ``LICENSE`` for more information.


## Contributing

If you see something that needs to be corrected, or something that could be improved, feel free to fork this repo and submit a PR.  
You can also open an Issue and I'll get it fixed/added.

<!-- Markdown link & img dfn's -->
[wiki]: https://github.com/yourname/yourproject/wiki
