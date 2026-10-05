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

the .qcow2 format is not supported by Virtualbox so we'll need to convert it to .vdi using QEMU. If you don't already have it, QEMU has instructions on their [site](https://www.qemu.org/download/)

Go to the directory you unzipped your file and run this command in the CLI
Linux
```qemu-img convert -p -O vdi fortios.qcow2 fortios.vdi```

Windows
```qemu-img.exe convert -p -f qcow2 "C:\path\to\source.vmdk" -O vdi "C:\path\to\output.vdi"```

You should now have the following file

<img width="288" height="55" alt="image" src="https://github.com/user-attachments/assets/d47b00ee-2244-4bf8-9a7a-23d5ca74b1f1" />

# Install
Create a new Virtual Machine
Fortigate uses FortiOS which is a modified and hardened version of the Linux kernel. The distribution can be set to Linux and the OS can be set to Other Linux
<img width="910" height="749" alt="labshot 1" src="https://github.com/user-attachments/assets/3020d95e-9756-4a0d-8908-6b099b35a8b8" />

Minimum resource requirements
<img width="754" height="139" alt="image" src="https://github.com/user-attachments/assets/d8461439-a992-489b-b12f-e2e90dec9eaf" />

Click 'Use an existing virtual hard disk'
<img width="755" height="423" alt="image" src="https://github.com/user-attachments/assets/53a636f5-7700-4f9e-99d9-d76a797e6df6" />

If you do not see your vdi, you will need to add it by clicking on the folder icon. You will then add it on the following screen. Once it's added, it will be visible, but not Attached.

<img width="464" height="241" alt="image" src="https://github.com/user-attachments/assets/0617ef20-7f78-42b4-95b4-31ee7646120d" />
