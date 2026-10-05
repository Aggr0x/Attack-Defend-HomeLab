# Perimeter Firewall
This section will cover setting up a perimeter firewall. We'll be using a Next Generation Firewall (NGFW) as it inspects traffic by application, user, and content rather than only by port and protocol. We can use this to detect threats, conduct Deep Packet Inspection (DPI), and gain more visibility the same way an enterprise environment would.

# Fortigate
There are many reliable and effective security devices on the market. This lab utilizes the Fortigate Next Gen Firewall (NGFW) from Fortinet as it's one of the most common devices used in enterprise networks. 
***The device will be limited as it requires a license to use. Fortniet offers a free trial license, however the device will still provide optimal functionality for the scope of the lab.

# Download and Convert File
First, register for an account with [Fortinet](https://www.fortinet.com/)

Once registered and logged in, go to the VM Images section in the Forticloud dashboard. Locate the latest Fortigate image for you hypervisor (Oracle for this lab).
<img width="1655" height="757" alt="image" src="https://github.com/user-attachments/assets/64ddeb51-09b9-48d8-acdc-649fb6f1d6dd" />

Unzip the folder into your desired location. You'll find the .qcow2 file inside.
<img width="293" height="69" alt="image" src="https://github.com/user-attachments/assets/f0325ebc-3db0-4a5a-9342-707d1cbd7de5" />

the .qcow2 format is not supported by Virtualbox so we'll need to convert it to .vdi.

# Windows
