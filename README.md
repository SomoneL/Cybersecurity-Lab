# Cybersecurity Lab
# Integrating Linux & Windows Systems with Advanced Monitoring Tools
<h2>Description</h2>
This project sets up a virtual environment using Oracle VM, VirtualBox with Windows 10, Kali Linux, Windows Server, and Ubuntu Server VMs. Network configurations enable communication via IP addresses and NAT Networks. Security includes Splunk Server for log analysis, Universal Forwarder for data forwarding, and Sysmon for endpoint monitoring. Testing involves Crowbar for brute force attacks, Atomic Red Team (ART) for general tests, and Splunk log analysis. Windows machines join an Active Directory domain with Remote Desktop enabled. PowerShell scripting automates tasks for a hands-on exploration of cybersecurity concepts and tools in a controlled environment.

<h2>Objective </h2>
The objective of the lab is to provide a hands-on learning experience in setting up a virtualized environment for cybersecurity testing and exploration. By creating and configuring multiple virtual machines (VMs) including Windows 10, Kali Linux, Windows Server, and Ubuntu Server, the lab aims to teach skills such as network configuration, security tool installation (Splunk, Sysmon), endpoint monitoring, and security testing (using Crowbar for brute force attacks). Joining Windows machines to an Active Directory domain and enabling Remote Desktop also adds to the learning objectives. PowerShell scripting is used for automation tasks. Overall, the lab enables participants to gain practical experience in cybersecurity concepts, tools, and techniques within a controlled environment.


<br />


<h2>Step 1: VM Installation </h2>
<ol>
   <li>Install Oracle VM VirtualBox</li>
   <ul>
      <li>Navigate to <a href="https://www.virtualbox.org/"> VirtualBox</a>, download the compatible version, and install it with dependencies.</li>
   </ul>
   <li>Download and Install Windows 10 to your VM</li>
   <ul>
      <li> Vist<a href="https://www.microsoft.com/en-ca/software-download/windows10/"> Microsoft's </a>Website, get "Create Windows 10 installation media", click "Download tool now", choose "Create installation media (USB flash drive, DVD, or ISO file) for another PC", then select "ISO file" and save it. </li>
   </ul>
   <ul> 
      <li>In Oracle VM VirtualBox Manager, click "Add" to create a new VM, name it, choose the previously downloaded Windows 10 ISO, select 4096MB RAM, 1 CPU, 50GB virtual disk, and finish. Start the VM, follow the installation prompts, select "Custom: Install Windows only (advanced)", and let Windows 10 install. </li>
   </ul>
   <li>Download and Install Kali Linux to your VM</li>
   <ul>
      <li>Get <a href ="https://www.kali.org/">Kali Linux</a> and download the VM version, and also download/install <a href "https://www.7-zip.org/> 7-zip</a>. Extract Kali Linux via 7-zip, import it into Oracle VM VirtualBox Manager, and start the VM.</li>
      </ul>
   <li>Download and Install Windows Server 2022</li>
   <ul>
      <li>Download <a href = "https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022">Windows Server 2022 ISO </a> and fill out the form, download "64-bit edition", create a new VM in Oracle VM VirtualBox Manager with the ISO, 4096MB RAM, 1 CPU, 50GB virtual disk, and finish. Start the VM, select "Install now", choose "Windows Server 2022 Standard Evaluation (Desktop Experience)", customize settings, create a password, and finish..</li>
   </ul>
     <li>Download and Install Ubuntu Server</li>
   <ul>
      <li>Navigate to the <a href = "https://ubuntu.com/server"> Ubuntu </a> download. For this lab, we will use version 22.04.4 LTS.  </li>
     
   </ul>
   <ul><li>
     Create a new VM in Oracle VM VirtualBox Manager with the ISO, 8192MB RAM, 2 CPUs, 100GB virtual disk, and finish. Start the VM, select "Try or Install Ubuntu Server", continue through a series of "Done" and "Continue", then fill out the form before continuing the installation. 
   </li></ul>
   <ul><li>
      Finally, reboot. Error messages are expected. After rebooting, login and run sudo apt-get update && sudo apt-get upgrade -y. After this completes, hit "Enter". 
   </li></ul>
   
</ol>
     <li>Summary: You should now have Oracle VM VirtualBox Manager installed with four VMs running Windows 10, Kali Linux, Windows Server, and Splunk Server.</li> 

<h2>Step 2: Configure the Network</h2>
<ol>
   <li>Setup Communications</li>
   <ul>
      <li>Navigate to Tools > Network > NAT Networks > Create. Provide a name and IPv4 Prefix (in this lab, we will use 192.168.10.0/24), then apply. Navigate to each VM > Settings > Network, change "Attached to: NAT Network" and assign the name to the NAT Network you just created. Run the Splunk VM and type sudo nano /etc/netplan/00-installer-config.yaml. You should modify and save to look like this:</li>
   [ADD PIC HERE]
   </u>
   </ul>
   <ul>
      <li>Then run sudo netplan apply to make changes. Now run ip a, you should see the IP address set to 192.168.10.10/24. Verify connecting by running ping google.com.</li>
   </ul>
   <li>Download and Install Splunk to your VM</li>
   <ul>
      <li>Navigate to https://www.splunk.com/ and download a free trial of Splunk Enterprise for Linux (.deb). Navigate back to Splunk and run sudo apt-get install virtualbox-guest-additions-iso.</li>
   </ul>
      <ul>
      <li>Now navigate to Devices > Shared Folders > Shared Folders Settings > Create new Shared Folder. Navigate to the directory where you installed Splunk, check all three boxes, and continue.</li>
   </ul>
      <ul>
      <li>Reboot the virtual machine with sudo reboot. Run sudo apt-get install virtualbox-guest-utils then reboot once more, and then sudo adduser <your username> vboxsf. Run mkdir share to create a new directory called "share".</li>
   </ul>
      <ul>
      <li>Now run sudo mount -t vboxsf -o uid=1000,gid=1000 <directory name where .deb file is located> share/.</li>
   </ul>
      <ul>
      <li>Verify completion with ls -la, "Share" should be highlighted. Navigate to the share directory using cd share/ and run ls -la once more to view all the files listed in that directory. Install splunk by running sudo dpkg -i splu<hit tab to auto-complete></li>
   </ul>
      <ul>
      <li>Navigate via cd /opt/splunk/ and run ls -la. Change into the user Splunk by running sudo -u splunk bash. Run cd bin/. Run ./splunk start, to continue press q followed by y and [ENTER].</li>
   </ul>
      <ul>
      <li>To finalize this step, exit, cd bin, and finally, sudo ./splunk enable boot-start -user splunk. This will allow Splunk to start on boot as the user Splunk.</li>
   </ul>
   <li>Configure Windows Machine</li>
   <ul>
      <li>In the Start Menu search for "About" > Rename this PC. Rename it to whatever you'd like, in this lab, I will name it "target-PC". Restart the system. Open the Command Prompt run ipconfig and view the current IPv4 Address. Navigate to the network icon at the bottom right of the window. Right click > Open Network & Internet Settings > Change adapter options > Right click the adapter > Properties > Double click on "Internet Protocol Version 4 (TCP/IPv4) Properties > Select Use the following IP address. Set IP Address to 192.168.10.100, Subnet mask to 255.255.255.0, Default gateway to 192.168.10.1, and lastly the Preferred DNS server to 8.8.8.8. Running ipconfig again should showcase the new IP address.</li>
      </ul>
         <ul>
      <li>If your Splunk server is running, you can visit it via the browser on your target machine's browser by typing 192.168.10.10:8000 in as the URL. In the target machine visit https://www.splunk.com/ and navigate to Products > Free Trials & Downloads > Universal Forwarder > Get my free download and download the correct version for your target machine. Double-click the installed MSI file, set up basic information but don't create a password. Skip deployment server, but for Receiving Indexer set the IP/port to 192.168.10.10:9997 and install.</li>
   </ul>
   <ul>
      <li>Now install Sysmon by navigating to https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon. Next, navigate to https://github.com/olafhartong/sysmon-modular scroll down and select sysmonconfig.xml. Click "raw", and save the file. Extract the sysmon file, copy URL of the extracted directory, and open Powershell as administrator, and navigate to that directory. Run .\Sysmon64.exe -i ..\sysmonconfig.xml, then click agree.</li>
   </ul>
   <ul>
      <li>Now install Sysmon by navigating to https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon. Next, navigate to https://github.com/olafhartong/sysmon-modular scroll down and select sysmonconfig.xml. Click "raw", and save the file. Extract the sysmon file, copy URL of the extracted directory, and open Powershell as administrator, and navigate to that directory. Run .\Sysmon64.exe -i ..\sysmonconfig.xml, then click agree.</li>
      <li>Add PIC HERE</li>
   </ul>
   <ul>
      <li>Save this file as all file types in the local folder accessed previously as "inputs.conf".</li>
   </ul>
   <ul>
      <li>Open Services as administrator, navigate and double click SplunkForwarder, log on, and check Local System Account. Right-click Splunk Forwarder, and restart. Now, navigate to 192.168.10.10:8000 and login. Navigate to Settings > Indexes > New Index > name it "endpoint" > Save. Now navigate Settings > Forwarding & receiving > Configure receiving > New Receiving Port > Set port to 9997. Now navigate Apps > Search & Reporting and search for "index=endpoint".</li>
   </ul>
         
<li>Configure Windows Server</li>
   <ul>
      <li>In the Start Menu search for "About" > Rename this PC. Rename it to whatever you'd like, in this lab, I will name it "ADDC01". Restart the system. Open the Command Prompt run ipconfig and view the current IPv4 Address. Navigate to the network icon at the bottom right of the window. Right click > Open Network & Internet Settings > Change adapter options > Right click the adapter > Properties > Double click on "Internet Protocol Version 4 (TCP/IPv4) Properties > Select Use the following IP address. Set IP Address to 192.168.10.7, Subnet mask to 255.255.255.0, Default gateway to 192.168.10.1, and lastly the Preferred DNS server to 8.8.8.8. Running ipconfig again should showcase the new IP address.</li>
      </ul>
         <ul>
      <li>If your Splunk server is running, you can visit it via the browser on your target machine's browser by typing 192.168.10.10:8000 in as the URL. In the target machine visit https://www.splunk.com/ and navigate to Products > Free Trials & Downloads > Universal Forwarder > Get my free download and download the correct version for your target machine. Double-click the installed MSI file, set up basic information but don't create a password. Skip deployment server, but for Receiving Indexer set the IP/port to 192.168.10.10:9997 and install.
</li>
   </ul>
   <ul>
      <li>Now install Sysmon by navigating to https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon. Next, navigate to https://github.com/olafhartong/sysmon-modular scroll down and select sysmonconfig.xml. Click "raw", and save the file. Extract the sysmon file, copy URL of the extracted directory, and open Powershell as administrator, and navigate to that directory. Run .\Sysmon64.exe -i ..\sysmonconfig.xml, then click agree.
</li>
   </ul>
   <ul>
      <li>Now for the most important step, navigate to File Explorer> Local Disk (C:) > Program Files > SplunkUniversalForwarder > etc > system > local. Open Notepad as administrator and enter the following</li>
      <li>Add PIC HERE</li>
   </ul>
   <ul>
      <li>Save this file as all file types in the local folder accessed previously as "inputs.conf".</li>
   </ul>
   <ul>
      <li>Open Services as administrator, navigate and double click SplunkForwarder, log on, and check Local System Account. Right-click Splunk Forwarder, and restart. Now, navigate to 192.168.10.10:8000 and login. Now navigate Apps > Search & Reporting and search for "index=endpoint".</li>
   </ul>
  <ul> <li>Summary: When viewing your Splunk server from either of your Windows machines, you should be able to see under "Selected fields" > "Host" two Values (your VMs) TARGET-PC and ADDC01.</li> </ul> </ol>
   


<h2>Step 3: Active Directory and Control Domain </h2>
<ol>
   <li> Setup Active Directory Domain Services on the Windows Server</li>
   <ul>
      <li>On your Windows server, open up Server Manager. Navigate to Manage > Add Roles and Features. Select Next > Next, and check Active Directory Domain Services > Add Features. Advance until you can select install.</li>
   </ul>
   <ul>
      <li>Once you receive the message "Configuration required. Installation succeeded on ADDC01, you can advance to the next steps. Locate the flag icon at the top of the window, and select "Promote this server to a domain controller". Select "Add a new forest", because we are creating a brand new domain. We will name it demodomain.local. On the next page, leave all defaults and create a password. Advance until the Configuration Wizard validates prerequisites, and then hit install. This should trigger you to be signed out and have the server restart.</li>
   </ul>
   <ul>
      <li>When the machine is back on, you should see DEMODOMAIN\Administrator as the user. In Server Manager, navigate to Tools > Active Directory Users and Computers.</li>
   </ul>
   <ul>
      <li>Right-click demodomain.local > New > Organizational Unit > Name: "IT". Right-click in the space of this new OU > New > User. First name: "Jenny", Last name: "Smith", Full name: "Jenny Smith", User logon name: "jsmith". Create a password, then finish. Create a new OU called "HR" and create another different user.</li>
   </ul>
   <li>Windows 10 Machine Joins New Domain</li>
   <ul>
      <li>From the target machine, open Windows search and enter "Advanced System Properties" > Computer Name > Change. Check "Domain:" and enter "DEMODOMAIN.LOCAL". Navigate and right-click the network icon at the bottom right of the window > Open Network & Internet Settings > Change Adapter Options > Right-click adapter > Properties > Double-click Internet Protocol Version 4 (TCP/IPv4). Change the Preferred DNS server to 192.168.10.7. In Command Prompt, run ipconfig /all to verify that DNS Servers is set to 192.168.10.7. Now navigate back to Computer Name/Domain Changes (this window should still be open), and select "OK". Login with the administrator credentials and restart the machine.</li>
   </ul>
   <ul>
      <li>When you go to login, select "Other User", and verify the domain the login is pointing to is "DEMODOMAIN". Login using the credentials of a user you created earlier under the IT or HR organizational unit. 
         <br/>
         <img src="https://i.imgur.com/xLBPzXn.png" height="40%" width="40%" alt="script"/>
         <br/>
      </li>
   </ul>
   <ul>
      <li>You will want to install tools for user and system management by running the following bash script:
         <br/>
         <img src="https://i.imgur.com/eVUHNRy.png" height="40%" width="40%" alt="script"/>
         <br/>
      </li>
   </ul>
</ol>
<h2>Step 4: Kali Linux Brute Force Attack on the Target Machine</h2>
<ol>
   <li>Configure Network</li>
   <ul>
      <li>Power up the Kali Linux VM and log in. From the desktop, right-click the connections icon at the top right of the screen > Edit Connections > Wired connection 1 > Edit selected connection > IPv4 Settings > set Method to "Manual" > Add > Address: 192.168.10.250 > Netmask: 24 > Gateway: 192.168.10.1 > DNS servers: 8.8.8.8 > Save. Disconnect from the network, then rejoin. Open the terminal and run ip a and you should see the changes be reflected here. You can verify connectivity by running pinggoogle.com and/or 192.168.10.10. Now run sudo apt-get update && sudo apt-get upgrade -y. Continue once finished installing.</li>
   </ul>
   <li>Setting up the Attack:</li>
      <ul>
      <li>Run mkdir ad-project and sudo apt-get install -y crowbar. Now navigate with cd /usr/share/wordlists. Verify "rockyou.txt.gz" is present by running ls, and unzip it using sudo gunzip rockyou.txt.gz. Now copy this file into the directory we made earlier using cp rockyou.txt ~/Desktop/ad-project. Now run cd ~/Desktop/ad-project. Now we will take the first 20 lines of this file and output it into a new file called passwords.txt using head -n 20 rockyou.txt > passwords.txt</li>
   </ul>
   <li>Enable Remote Desktop</li>
      <ul>
      <li>On our target machine, search "This PC" > Properties > Advanced System Settings > Remote > check Allow remote connections to this computer > Select users > Add > Enter the usernames we created earlier such as "jsmith" and "tsmith" > Apply.</li>
   </ul>
    <li>Attack</li>
       <ul>
      <li>Navigate back to the Linux machine. crowbar -h to get more information about the Crowbar tool. Run crowbar -b rdp -u tsmith -C passwords.txt -s 192.168.10.100/32. If a password listed in passwords.txt matches a password assigned to the user-specified, you will get a success message with the password attributed to the specified user.
</li>
   </ul>
   <li>Analyze Attack Using Splunk</li>
       <ul>
      <li>We can view all activity of this endpoint using index=endpoint, and if we know something happened to Terry's account we can also add tsmith. We will notice when we scroll down and select EventCode there are 3 events, and one stands out. Value 4625 stands out because it occurred 20 times. A quick Google search (https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4625) verifies that this is 60 counts of an account failing to log on. When investigating further, it can be noted that all of the counts are occurring at approximately the same time which is an indication of brute force activity. We also notice an event code 4624 which when is looked into (https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4624) we know is an indication of a successful logon. We can expand this event by clicking "Show all x lines", and we can then see the soruce of this logon.</li>
   </ul>
   <li>Run Tests on the Target Machine using Atomic Red Team (ART)</li>
       <ul>
      <li> Open Powershell as administrator. Run Set-ExecutionPolicy Bypass CurrentUser > Y. Click the up arrow located at the bottom right of your window. Windows Security > Virus & threat protection > Manage Settings > Add or Remove Exclusions > Add an exclusion > Folder > This PC > Local Disk (C:). Now navigate back to PowerShell and run IEX ( IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing); followed by Install-AtomicRedTeam -getAtomics and Y.</li>
   </ul>
   <ul>
      <li>Now navigate to the (C:) drive > AtomicRedTeam > Atomics. We can also navigate to https://attack.mitre.org/ in order to view adversary attacks and techniques, as well as use it as a key for the "T" values. Run Invoke-AtomicTest T1136.001 to T1136 is a persistence, create local account test. After running this test and checking Splunk, we can see that no events popped up with the NewLocalUser that was created. This is excellent because we have just identified a gap in our protection. We can continue this with as many tests as we please. This makes Atomic Red Team very valuable for an organization.</li>
   </ul>
   </li></ul>
</ol>

   <h2>Conclusion</h2>
You have reached the end of the walk-through! With the conclusion of the last part of this step-by-step walk-through, you should have a successful setup and communication between 4 Virtual Machines: Windows 10 Target Machine, Kali Linux Attacker Machine, Splunk, and Windows Servers. You should be able to view events with Splunk, test Splunk functionality and defense using Crowbar brute force password cracker, and more.     
</ol>

