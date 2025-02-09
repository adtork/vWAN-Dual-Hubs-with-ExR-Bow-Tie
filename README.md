# vWAN-Dual-Hubs-with-ExpressRoute-Bow-Tie Modifying HRP

# Intro
In this article we are going to explore multiple traffic scenarios with vWAN hubs and express-route circuits. I will show various scenarios with circuits hooked to single vhub, then move to multiple vhubs and doing bow-tie. We will also toggle the Hub Routing Preference (HRP) on/off for each vhub and show how that effets the route and AS-PATH for the traffic flows! 

# Final Base Topology
In this topology we are using the default route table only no custom route tables and no Routing-Intent! Its important to note, if these were secured vhubs, the vhubs default route table would ONLY show rfc1918 prefixes for private traffic. If internet traffic was enabled, there would be 0.0.0.0/0 next hop AzFW or NVA in the vhub! You would still be able to see the specific routes if you were to choose AzFW as the drop down on view effective routes!
![image](https://github.com/user-attachments/assets/d5787596-56f7-4d30-83f2-6e6c2ee283fd)

**The 172.16.1.0/30 (West Circuit) and 172.16.2.0/30 (East Circuit) are the P2P interfaces that was used to setup the express-route circuts for each region. You will notice both the /30s in the route tables below!** 

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
![image](https://github.com/user-attachments/assets/e326bc31-8599-4d29-8032-335bc1519fc5)
You will notice again on the East vhub, its still not learning the 10.3.0.0/16 and 10.4.0.0/16. I mentioned above I was messing with labels and I was not propogating those to default route table and put them in a custom route table. In further tests I change that and remove the custom route table for 10.3.0.0/16 and 10.4.0.0/16 and propogate again to default route table. We can see though how that affects the routes, by putting 10.3.0.0/16 and 10.4.0.0/16 in their own custom route table and not propogating to default, the other side does not learn it!

# Scenario 3: Lets cross connect one side and hook up WestUS circuit to EastUS vhub!
Default Route Table on WestUS vhub:
![image](https://github.com/user-attachments/assets/c2943e77-18dd-45fb-b56e-eb4e08fc6dba)
<br>
Default Route Table on EastUS vhub:
![image](https://github.com/user-attachments/assets/924978ca-89af-4a0d-af96-8c73c7e3f168)
So, you notice the EastUS vhub is now learning again the 10.3.0.0/16 and 10.4.0.0/16 because I removed the custom route table and put it back to default. But you will also notice something else, now those routes are learned via 12076-12076 and not 65520-65520! Why is that, because since we are doing the bow-tie now and we have the HRP set to Express-Route (Default Setting), it's learning those routes via the MSEE hairpinning and NOT via interhub ASN of 65520! You will see the same for the on-prem prefixes as well! 

# Scenario 4: Same as above, but change the HRP to AS-PATH on both sides!
Default Route Table on WestUS vhub:
![image](https://github.com/user-attachments/assets/5047b0ca-92e1-41b1-b088-9ac7724737e6)
You will notice now WestUS vhub is learning the remote spokes again via interhub, 65520-65520 INSTEAD of 12076-12076 because we changed the HRP to AS-PATH. This will be the same behavior as EastUS vhub learning the WestUS vhub spoke prefixes!
![image](https://github.com/user-attachments/assets/53404525-a888-4d1d-b994-2ae72fb72a09)
So by changing the HRP to AS-PATH is honoring the AS-PATH and not taking the MSEEs and circuit to reach the remote spokes!

# Scenario 5: Lets cross-connect (bow-tie) BOTH sides and put HRP back to Express-Route!
Default Route Table on WestUS vhub:
![image](https://github.com/user-attachments/assets/e67535b1-c9af-4261-b67d-cee9ab52fc93)
We can see now by putting HRP back to Express-Route, interhub is learned again via 12076-12076 and not 65520-65520. This is the same behavior for EastUS vhub learning WestUS vhub routes!
Default Route Table on EastUS vhub:
![image](https://github.com/user-attachments/assets/7f56e1f1-38e2-43f6-846c-fd2aae15812c)







