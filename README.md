# vWAN-Dual-Hubs-with-ExpressRoute-Bow-Tie

# Intro
In this article we are going to explore multiple traffic scenarios with vWAN hubs and express-route circuits. I will show various scenarios with circuits hooked to single vhub, then move to multiple vhubs and doing bow-tie. We will also toggle the Hub Routing Preference (HRP) on/off for each vhub and show how that effets the route and AS-PATH for the traffic flows! 

# Final Base Topology
In this topology we are using the default route table only no custom route tables and no Routing-Intent! Its important to note, if these were secured vhubs, the vhubs default route table would ONLY show rfc1918 prefixes for private traffic. If internet traffic was enabled, there would be 0.0.0.0/0 next hop AzFW or NVA in the vhub! You would still be able to see the specific routes if you were to choose AzFW as the drop down on view effective routes!
![image](https://github.com/user-attachments/assets/d5787596-56f7-4d30-83f2-6e6c2ee283fd)

**The 172.16.1.0/30 (West Circuit) and 172.16.2.0/30 (East Circuit) are the P2P interfaces that was used to setup the express-route circuts in Azure that you will notice in the proceding route tables**

# Scenario 1: Hooking up Express-Route circuit to WestUS vhub only! (East vHub does not have a circuit yet!)
The first question then becomes, since we only have an express-route circuit connected to our west vhub, will the east vhub still learn the on-prem west route of (192.168.0.0/24)? Will it still learn the west vhub spoke routes as well?
![image](https://github.com/user-attachments/assets/dfb2f776-be51-4b48-90b8-f38946cf7d01)
As we can see, even though the Express-Route circuit is only hooked up to the West vhub, the East vhub has still learned our on-prem route of 192.168.0.0/24. **Normally we would also see the remote spokes of 10.3.0.0/16 and 10.4.0.0/16 but I was messing with labels on those. In a normal scenario, we would learn all the remote vhub spokes as well :)** So, even though we only have a circuit connected to west vhub, the east vhub has still learned our on-prem routes!

# Scenario 2: Hooling up the Express-Route circuit to EastUS vhub now! (So both sides have Express-Route connected!)
Now, the question becomes both sides should see the remote side routes still, lets check!
Default Route Table on West vhub:
![image](https://github.com/user-attachments/assets/95fd2945-dfba-4722-b186-e940b3f53e58)
<br>
Default Route Table on East vhub:
![image](https://github.com/user-attachments/assets/ebb4816b-836b-41f4-8fe3-9362c04e126d)


