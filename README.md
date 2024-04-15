<h1>Creating an SOC and Honeypot in Azure</h1>
<h2>Project Description</h2>

<p>This project involves setting up an SOC on Microsoft Azure consisting of a Log Analytics Workspace, Virtual Machine (Honeypot), and Microsoft Sentinel (SIEM). Then, To ensure the VM is properly set up, I'll connect to it using Windows Remote Desktop and check out the event viewer. Afterwords, I will use PowerShell to extract security log data and extrapolate the latitude and longitude of attackers that try to access the honeypot. The purpose of this project is to create a test environment so I can better understand the technique, tactics and procedures utilized by hackers to take advantage of vulnerable systems.</p>

<h1>Environments and Tools</h1>

- Microsoft Azure (VLAN, Network Security Groups, Virtual Machines)
- Microsoft Sentinel
- PowerShell
- Log Analysis
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

Here we can see the IP address associated with this failed login attempt. Security logs contain a staggering amount of data about users who try to connect to a device.

<h1>Visualizing Threats using Microsoft Sentinel</h1>

By inspecting the event properties, I can see the source network address that causes this security log. This IP Address information should be in every audit failure that occurs on this device. Unfortunately for would-be hackers, I can use this information to figure out their location. Using this IPgeolocation API, which you can get limited access to for free, inside a PowerShell script will enable me to create a custom log file I can upload into Azure.

![2](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/1.png)

After creating my account, I am given my own unique API access code. The code you will see in this tutorial is no longer working, so you will have to get your own if you intend to duplicate this project. Now I load up PowerShell inside of the VM.

![3](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/2.png)

The full script is viewable [HERE](https://github.com/nicknava1/Threatmapping/blob/main/Log_Collector.ps1). When I run this script it will create a custom log file called "failed_rdp.log". Inside, it will document identifying geographic data about every failed login attempt on this device. This includes: Source IP address, State, Country, Latitude and Longitude. Let's run it now.

![4](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/3.png)

It's working! The purple text at the bottom of the screen shows the log information being recorded into "failed_rdp.log". We can see attempts from all of the world already! Let's check out that custom log file.

![5](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/4.png)

The script is doing its job and collecting the geographical data we need to upload into our Log Analytics Workspace (LAW). Now I'm going to take us back over to my desktop and load up Azure.

<h2>Importing Custom Logs into Sentinel</h2>

In my Azure client, I navigate to the Log Analytics Workspace I set up earlier. Then I create a custom log table. The collection path needed to match the directory where my "failed_rdp.log" is located on the VM.

![6](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/5.png)

Now that my custom table is created, I can search for the log data using my custom log name inside of the LAW. We should see the latitudes and longitudes generated by my PowerShell script.

![7](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/6.png)

With log data from my VM being collected into Azure, it is time to bring it all together in Microsoft Sentinel. Im going to create a SIEM query using our custom log name and specify the information I want Sentinel to extract.

![8](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/7.png)

Finally, I'll specify that I want it to visualize this information as a map.

![9](https://github.com/nicknava1/Threatmapping/blob/main/Threatmapping/8.png)

Now our Threat Map is complete! We can see that most of the attacks are coming from Delhi and Florida.

<h2>Conclusion</h2>

In this project, I employed a combination of PowerShell scripting, IP geolocation APIs, and Microsoft Azure services to enhance our security measures through threatmapping using Microsoft Sentinel. By extracting geographical data from failed login attempts and visualizing them on a threatmap, we gained valuable insights into the origins of potential security threats.

The process began with setting up a mini SOC and honeypot to attract and capture threat actor activities, providing us with the necessary log information for analysis. Leveraging PowerShell scripts, we extracted vital geographic data from these logs, including source IP addresses, states, countries, and coordinates.

With the collected data, we imported it into our Log Analytics Workspace (LAW) in Azure, enabling us to centrally manage and analyze the information. We then utilized Microsoft Sentinel to create a SIEM query to extract specific details from our custom logs and visualize them on a map.

The resulting threat map provided a clear visualization of attack origins, highlighting areas of concern such as Delhi and Florida in our case. This visual representation equips stakeholders with valuable insights for making informed decisions regarding security and risk management strategies.

Overall, this project demonstrates the effectiveness of utilizing advanced tools and techniques to enhance cybersecurity posture and proactively mitigate potential threats. Through continuous monitoring and analysis, we can better safeguard our systems and data against evolving security risks.
