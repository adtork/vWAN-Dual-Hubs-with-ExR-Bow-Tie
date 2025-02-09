# vWAN-Dual-Hubs-with-ExpressRoute-Bow-Tie

# Intro
In this article we are going to explore multiple traffic scenarios with vWAN hubs and express-route circuits. I will show various scenarios with circuits hooked to single vhub, then move to multiple vhubs and doing bow-tie. We will also toggle the Hub Routing Preference (HRP) on/off for each vhub and show how that effets the route and AS-PATH for the traffic flows! 

# Final Base Topology
In this topology we are using the default route table only no custom route tables and no Routing-Intent! Its important to note, if these were secured vhubs, the vhubs default route table would ONLY show rfc1918 prefixes for private traffic. If internet traffic was enabled, there would be 0.0.0.0/0 next hop AzFW or NVA in the vhub! You would still be able to see the specific routes if you were to choose AzFW as the drop down on view effective routes!
![image](https://github.com/user-attachments/assets/d5787596-56f7-4d30-83f2-6e6c2ee283fd)

# Scenario 1: Hooking up Express-Route circuit to WestUS vhub only! (East vHub does not have a circuit yet!)
The first question then becomes, since we only have an express-route circuit connected to our west vhub, will the east vhub still learn the on-prem west route of (192.168.0.0/24)? Will it still learn the west vhub spoke routes as well?

