<h1>Creating an SOC and Honeypot in Azure</h1>
<h2>Project Description</h2>

<p>This project involves setting up an SOC on Microsoft Azure consisting of a Log Analytics Workspace, Virtual Machine (Honeypot), and Microsoft Sentinel (SIEM). Then, To ensure the VM is properly set up, I'll connect to it using Windows Remote Desktop and check out the event viewer. In part 2 of this project, I will use Powershell to export geographical data from failed login attempts into my Log Analytics Workspace. Finally, I will use Microsoft Sentinel to create a threatmap and geolocate our attackers. The purpose of this project is to better understand the technique, tactics and procedures utilized by hackers to take advantage of vulnerable systems.</p>

<h1>Skills and Tools used</h1>

- Microsoft Azure (VLAN, Network Security Group, Defender for Cloud)
- Microsoft Sentinel
- Virtual Machine Deployment
- Log Analysis
- PowerShell Scripting
- Windows Remote Desktop
- Event Viewer

<h1>Setting up the SOC</h1>

<p>The first step is deploying a virtual machine (VM) in Azure.</p>

![0](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/0.png)

![1](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/1.png)

<p>It is important to remember the username and password for this VM because later I will be connecting to it using Windows Remote Desktop</p>

![2](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/2.png)

<p>After hitting next, I confirmed the disk settings were properly configured. Then I made my way to the Network Security Group (NSG) configuration. To make this VM a Honeypot I need to define custom network rules. I added a rule to allow all incoming traffic.</p>

![3](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/3.png)

<p>Now that the NSG has been defined, I will review and create my VM. While waiting for it to finish deploying, I began setting up the Log Analytics Workspace (LAW).</p>

![4](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/4.png)

<p>I made sure to assign it to the same resource group as the VM. After confirming all settings, I selected review and create. For the next step I needed to connect the LAW to the VM I configured earlier. Fortunately the VM was finished deploying.</p>

![5](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/5.png)

<p>Finally I need to set up Sentinel and connect it to my Log Analytics Workspace.</p>

![6](https://github.com/nicknava1/Soc-Honeypot/blob/main/Honeypot/6.png)

<p>With the mini SOC completed, lets see if we can connect to the virtual machine using Windows Remote Desktop!</p>

<h1>Accessing the VM using Window Remote Desktop</h1>

First I get the IP address of the VM from Azure. Then I launch Windows Remote Desktop on my computer.

![0](https://github.com/nicknava1/Soc-Honeypot/blob/main/Remote%20Desktop%20login/0.png)

With the login credentials I saved from earlier, I log into my VM successfully. I'm going to open event viewer inside of the VM so I can see failed login attempts.

![1](https://github.com/nicknava1/Soc-Honeypot/blob/main/Remote%20Desktop%20login/1.png)

Now I'm going to switch back to my real computer and open another instance of Windows Remote Desktop. This time I'm going to purposely fail to log into the VM.

![2](https://github.com/nicknava1/Soc-Honeypot/blob/main/Remote%20Desktop%20login/2.png)

This will create a unique security log we can view inside of Event Viewer. Instead of nickadmin, we should see the account name in the security log is nickfail.

![3](https://github.com/nicknava1/Soc-Honeypot/blob/main/Remote%20Desktop%20login/3.png)

Let's dig a little deeper, and check out the event properties.

![4](https://github.com/nicknava1/Soc-Honeypot/blob/main/Remote%20Desktop%20login/4.png)

Here we can see the IP address associated with this failed login attempt. This is the information my Powershell Script will use to obtain our unsuspecting hackers' geographical location. That, however, will have to wait until next time! In part two, I will use Microsoft sentinel to create a Threat map of our hackers from around the globe.

<h1>Conclusion</h1>
In this project, I practiced utilizing Microsoft Azure to deploy a VM, VLAN, NSG, and SIEM tool. Afterwords, I connected to the virtual network using my desktop computer, and viewed a security log using Event Viewer.
