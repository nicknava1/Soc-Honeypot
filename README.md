<h1>Creating an SOC and Honeypot in Azure</h1>
<h2>Project Description</h2>

<p>In this project involves setting up an SOC on Microsoft Azure consisting of a Log Analytics Workspace, Virtual Machine (Honeypot), and Microsoft Sentinel (SIEM). Then, I will use powerscript to export geographical data from failed login attempts into my Log Analytics Workspace. Finally, I will use Microsoft Sentinel to create a threatmap and geolocate our attackers. The purpose of this project is to better understand the technique, tactics and procedures utilized by hackers to take advantage of vulnerable systems.</p>

<h1>Skills and Tools used</h1>

- Microsoft Azure (VLAN, Network Security Group, Defender for Cloud)
- Microsoft Sentinel
- Virtual Machine Deployment
- Log Analysis
- PowerShell Scripting
- Windows Remote Desktop

<h1>Setting up the SOC</h1>

<p>The first step is deploying a virtual machine (VM) in Azure.</p>

![0](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/0.png)

![1](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/1.png)

<p>It is important to remember the username and password for this VM because later I will be connecting to it using Windows Remote Desktop</p>

![2](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/2.png)

<p>After hitting next, I confirmed the disk settings were properly configured, then I made my way to the Network Security Group (NSG) configuration. To make this VM a honeypot I need to define custom network rules. I added a rule to allow all incoming traffic.</p>

![3](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/3.png)

<p>Now that the NSG has been defined, I will review and create my VM. While waiting for it to finish deploying, I began setting up the Log Analytics Workspace (LAW).</p>

![4](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/4.png)

<p>For the next step I needed to connect the LAW to the VM I configured earlier. Fortunately the honeypot was finished deploying.</p>

![5](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/5.png)

![6](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/6.png)
