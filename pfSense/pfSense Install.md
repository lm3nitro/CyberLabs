# PFSENSE FIREWALL:

![Screenshot 2024-09-22 at 3 01 29 PM](https://github.com/user-attachments/assets/64724515-819f-40b7-a199-9969810b870b)

The pfSense firewall is an open source firewall and router distribution based on FreeBSD. It is highly regarded for its flexibility, security, and rich feature set, making it suitable for a wide range of networking scenarios, from home networks to enterprise environments. pfSense can be installed on a physical machine or run as a virtual machine, providing advanced networking functions through an easy-to-use web interface.

Here are some key features and capabilities of pfSense:
+ Stateful Packet Inspection
+ Granular Rule Definition
+ Static and Dynamic Routin
+ Multi-WAN (Load Balancing and Failover)
+ OpenVPN
+ IPsec
+ Bandwidth Management
+ Packet Capturing and more

### Scope:
I will be installing the pfSense as part of my lab for learning, experimentation, and improving my network security. I will be demonstrating the steps used to get it installed and running. This will allow me to gain hands-on experience with firewall configuration, rules, and security measures like VPNs, IDS/IPS, and network segmentation.

### Tools and Technology:
Linux, pfSense, and VMWare ESXi

## Downloading Installer

In order to install pfSense, I needed to download the installer directly form the vendor:

![Pasted image 20240515202043](https://github.com/lm3nitro/Projects/assets/55665256/7badf55e-d26a-47eb-a979-62bb4bfb1d8c)

I first had to add the installer to the cart and than checkout:

![Pasted image 20240515202125](https://github.com/lm3nitro/Projects/assets/55665256/ea6c3982-964c-43c6-bf12-7bf68f0c81ee)

![Pasted image 20240515202317](https://github.com/lm3nitro/Projects/assets/55665256/6653e0dd-dba5-451d-8c8b-599b5422f3e6)

Once I enetered the needed information in the fields, I select complete order:

![Pasted image 20240515202628](https://github.com/lm3nitro/Projects/assets/55665256/dc73d68d-04b8-403b-ab93-e731874cf7cb)

I was then presented with the download option:

![Pasted image 20240515202956](https://github.com/lm3nitro/Projects/assets/55665256/6490a589-4d90-46b3-be44-930c6cf8a03b)

## VM Creation

In my VMWare ESXi server I selected to create a new VM and selected the respective ISO:

![Pasted image 20240515203449](https://github.com/lm3nitro/Projects/assets/55665256/2881875a-704c-420c-8c19-26f26487771a)

Chose a VM name:

![Pasted image 20240515203530](https://github.com/lm3nitro/Projects/assets/55665256/0f432590-1d22-4990-92b3-8ac0cbc14727)

Specified the VM capacity:

![Pasted image 20240515203557](https://github.com/lm3nitro/Projects/assets/55665256/ce82cad0-799b-449b-9c33-d6db2e176f8a)

Also added a new interface for the Firewall:

![Pasted image 20240515203856](https://github.com/lm3nitro/Projects/assets/55665256/bfb1a012-1a47-446c-874b-f2376fe1bf85)

## Installation:

Now that the VM is created, I started the VM and proceeded with the installation. The first step was accepting the EULA:

![Pasted image 20240515204005](https://github.com/lm3nitro/Projects/assets/55665256/da2798c5-bf16-4157-b9ab-ffa16a39485f)

THis is a new install, so selected the following opiton:

![Pasted image 20240515204032](https://github.com/lm3nitro/Projects/assets/55665256/6c26ad84-0415-413e-8f02-95308e5b6878)

Clicked OK

![Pasted image 20240515204054](https://github.com/lm3nitro/Projects/assets/55665256/baa72680-97f7-4aa6-b033-1da802fb74ee)

Selected the WAN interface:

![Pasted image 20240515204344](https://github.com/lm3nitro/Projects/assets/55665256/62829923-6842-449c-8d19-bb64d70f52ea)

Configuring WAN interface:

![Pasted image 20240515205042](https://github.com/lm3nitro/Projects/assets/55665256/c5e8e024-ca3f-4bd6-ae04-9b24459bd0f5)

Selecting LAN interface:

![Pasted image 20240515204509](https://github.com/lm3nitro/Projects/assets/55665256/0b276237-b781-4c5a-9dd3-1be2dba08a9c)

LAN setting information:

![Pasted image 20240515204546](https://github.com/lm3nitro/Projects/assets/55665256/6637eb78-d55c-4ce0-b213-a364e60adbf9)

Everything looked good, continued

![Pasted image 20240515204653](https://github.com/lm3nitro/Projects/assets/55665256/18a9f359-80ae-4d54-8a31-7ee1ac7467c7)

![Pasted image 20240515204717](https://github.com/lm3nitro/Projects/assets/55665256/58f180f4-02f4-43ef-bae0-1565eaac49b6)

Proceeded with the installation:

![Pasted image 20240515205236](https://github.com/lm3nitro/Projects/assets/55665256/1e951be0-40fc-47fe-9366-fceb6369608c)

Since this is a lab set up Stripe will be fine.

![Pasted image 20240515205301](https://github.com/lm3nitro/Projects/assets/55665256/e24027bf-9de9-42bb-9abd-5626d94be47f)

Selected the virtual drive:

![Pasted image 20240515205416](https://github.com/lm3nitro/Projects/assets/55665256/ea3e4e73-b336-4e7a-a871-eb1909557227)

Formatted the virtual disk:

![Pasted image 20240515205441](https://github.com/lm3nitro/Projects/assets/55665256/ac5798f9-00e5-43d6-afcd-795b0d7e2557)

I chose to go wtih the stable release:

![Pasted image 20240515205510](https://github.com/lm3nitro/Projects/assets/55665256/65d6ed71-5b7e-47af-ae6a-0cf9a9a25658)

## Installation Process:

![Pasted image 20240515205534](https://github.com/lm3nitro/Projects/assets/55665256/9cc21e81-72c9-4377-83c5-5b4a50869d82)

Installation completed:

![Pasted image 20240515205727](https://github.com/lm3nitro/Projects/assets/55665256/e3d50b21-726a-4709-a5ca-da030db99468)

Rebooting:

![Pasted image 20240515205754](https://github.com/lm3nitro/Projects/assets/55665256/99093631-caf9-4326-bada-45584a6d547d)

## Verifying Connectivity:

Interface information:

![Pasted image 20240515211405](https://github.com/lm3nitro/Projects/assets/55665256/a430c6bc-06d7-4118-82fb-5845445aa99f)

Verified network reachability of the host:

![Pasted image 20240515211600](https://github.com/lm3nitro/Projects/assets/55665256/fdd5af0f-dcf6-4e0f-9479-3618be7e645f)

## Login

After the installatio had compelted and the connectivity was good, I then procceed to sign in into the  WebUI. In my case I nagivated to: https://192.168.1.1

![Pasted image 20240515211726](https://github.com/lm3nitro/Projects/assets/55665256/99c9b650-8556-4339-982c-821bdad69597)

Upon login, I checked the Dashboard page and could see that I was receiving traffic on my respective interfaces (LAN and WAN):

![Pasted image 20240515212340](https://github.com/lm3nitro/Projects/assets/55665256/70ede94d-922e-4caa-975c-b57d3fd0123b)

## Summary:

In summary, installing pfSense in a lab helps improve networking and security skills, providing hands-on experience with enterprise-level firewall and router functions, network monitoring, and advanced security measures. It’s a cost-effective and powerful learning tool that prepares you for both real-world network management and professional certifications. I was able to successfully install and configure pfSense for my lab enviornment. Now that I have it up and running, I will be able to test some of its features and functionality. I also have othe firewalls (Palo Alto and Fortinet), and adding the pfsense wikl also server as comparison. 

By having a firewall like pfSense, I will be able to:

+ Reinforce my understand how firewalls enforce rules, block or allow traffic based on criteria like IP addresses, ports, and protocols.
+ Create secure networks by configuring inbound and outbound rules for different devices or network segments.
+ Learn more about network congestion, throttling, and how to balance network resources.
+ Create complex network topologies similar to what you would find in businesses, allowing me to gain practical experience in network design, configuration, and troubleshooting.

Installing pfSense in my lab allowed me to improve networking and security skills, providing hands-on experience with enterprise-level firewall and router functions, network monitoring, and advanced security measures. 