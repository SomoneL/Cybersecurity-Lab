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
      <li>Navigate to the VirtualBox Downloads Page.</li>
   </ul>
   <ul>
      <li>Navigate to https://www.virtualbox.org/, download the compatible version, and install it with dependencies.</li>
   </ul>
   <li>Download and Install Windows 10 to your VM</li>
   <ul>
      <li>Visit https://www.microsoft.com/en-ca/software-download/windows10, get "Create Windows 10 installation media", click "Download tool now", choose "Create installation media (USB flash drive, DVD, or ISO file) for another PC", then select "ISO file" and save it. In Oracle VM VirtualBox Manager, click "Add" to create a new VM, name it, choose the previously downloaded Windows 10 ISO, select 4096MB RAM, 1 CPU, 50GB virtual disk, and finish. Start the VM, follow the installation prompts, select "Custom: Install Windows only (advanced)", and let Windows 10 install.</li>
   </ul>
   <li>Download and Install Kali Linux to your VM</li>
   <ul>
      <li>Get Kali Linux from https://www.kali.org/, download the VM version, and also download/install 7-zip from https://www.7-zip.org/. Extract Kali Linux via 7-zip, import it into Oracle VM VirtualBox Manager, and start the VM.</li>
      </ul>
   <li>Download and Install Windows Server 2022</li>
   <ul>
      <li> Download Windows Server 2022 ISO from https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022, fill out the form, download "64-bit edition", create a new VM in Oracle VM VirtualBox Manager with the ISO, 4096MB RAM, 1 CPU, 50GB virtual disk, and finish. Start the VM, select "Install now", choose "Windows Server 2022 Standard Evaluation (Desktop Experience)", customize settings, create a password, and finish..</li>
   </ul>
     <li>Download and Install Ubuntu Server</li>
   <ul>
      <li>Navigate to https://ubuntu.com/server. In products, Ubuntu Server, download Ubuntu Server, download Ubuntu Server. This lab uses version 22.04.4 LTS. Create a new VM in Oracle VM VirtualBox Manager with the ISO, 8192MB RAM, 2 CPUs, 100GB virtual disk, and finish. Start the VM, select "Try or Install Ubuntu Server", continue through a series of "Done" and "Continue", then fill out the form before continuing the installation. Finally, reboot. Error messages are expected. After rebooting, login and run sudo apt-get update && sudo apt-get upgrade -y. After this completes, hit "Enter".</li>
     
   </ul>
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
   















    
<h2>Step 2: Prepare Your Environment </h2>
<ol>
   <li>Configure your VM Settings and Mount your Linux OS</li>
   <ul>
      <li>Allocate at least 2 CPUs, 4GB RAM, and 20GB storage for the VM.</li>
   </ul>
   <ul>
      <li>Choose the version that matches your host operating system (Windows, macOS, or Linux).</li>
   </ul>
   <li>Install Essential Tools:</li>
   <ul>
      <li>Once your VM has finished setting up, you will want to prepare it for use.</li>
   </ul>
   <ul>
      <li>You will want to update and upgrade packages by running the following bash script: 
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
<h2>Step 3: Automate User Account Creation </h2>
<ol>
   <li>Write a Bash Script</li>
   <ul>
      <li>Create a script to automate adding users and assigning them to groups. The script name we will be using is (user_management.sh):.</li>
   </ul>
   <br/>
   <img src="https://i.imgur.com/7V1Ieb7.png" height="40%" width="40%" alt="script"/>
   <br/>
   <li>Make the Script Executable:</li>
   <img src="https://i.imgur.com/HsDzzg6.png" height="40%" width="40%" alt="script"/>
   <br/>
   <li>Run the Script:</li>
   <img src="https://i.imgur.com/y0pv2di.png" height="40%" width="40%" alt="script" "/>
   <br/>
   </li></ul>
</ol>
<h2>Step 4: Implement Access Control Policies </h2>
<ol>
   <li>Define User Roles: Assign specific roles to users based on their function in the lab.</li>
   <ul>
      <li>Administrators: Full access to all VMs, Active Directory management, and Splunk configurations.</li>
   </ul>
   <ul>
      <li>Standard Users: Limited access to specific Windows or Linux machines for testing and learning purposes.</li>
   </ul>
   <ul>
      <li>Guests: Minimal access, primarily for observing system activity without making changes.</li>
   </ul>
   <ul>
      <br></br>
      <li>Create groups for specific roles (e.g., admin, user, guest) by running the following code:</li>
   </ul>
   <img src="https://i.imgur.com/RXI5kjZ.png" height="30%" width="30%" alt="script"/>
   <br/>
   <li>Assign Permissions to Groups</li>
   </ul>
   <ul>
      <li>Use the chmod and chown commands to set directory permissions.</li>
   </ul>
   <img src="https://i.imgur.com/9c335UK.png" height="30%" width="30%" alt="script"/>
   <li>Enforce Access Control</li>
   <ul>
      <li>Verify permissions by switching to different users and testing to see if you can access the created directories.</li>
   </ul>
   <img src="https://i.imgur.com/pY3M8ON.png" height="30%" width="30%" alt="script"/>
</ol>
<h2>Step 5: Automate Password Policies </h2>
<ol>
   <li>Password Policy: Configure strong password requirements on all systems</li>
   <ul>
      <li>Minimum length: 12 characters.</li>
   </ul>
   <ul>
      <li>Must include uppercase, lowercase, numbers, and special characters.</li>
   </ul>
   <ul>
      <li>Prevent the reuse of the last five passwords.</li>
   </ul>
   <ul>
      <li>Implement password expiration policies: Set passwords to expire every 60 days on all machines (via Group Policy for Windows and /etc/login.defs for Linux).</li>
   </ul>
   <li>Password Requirements</li>
   <ul>
      <li>Modify /etc/login.defs for system-wide policies by running the following:</li>
      <img src="https://i.imgur.com/u0ywtmF.png" height="40%" width="40%" alt="script"/>
      <br/>
   </ul>
   <ul>
      <li>Set these parameters:
         <br/>
      </li>
      <ul>
         <li>PASS_MAX_DAYS: Sets the maximum number of days a user can use their current password before being required to change it.</li>
      </ul>
      <ul>
         <li>PASS_MIN_DAYS: Defines the minimum number of days a user must wait before they can change their password again after setting it.</li>
      </ul>
      <ul>
         <li>PASS_MIN_LEN: Sets the minimum length for user passwords to 12 characters.</li>
      </ul>
      <ul>
         <li>PASS_WARN_AGE: Sends a warning to the user 7 days before their password is set to expire.</li>
      </ul>
      <img src="https://i.imgur.com/lhys6XV.png" height="25%" width="25%" alt="script"/>
   </ul>
   <li>Implement Account Lockout:</li>
   <ul>
      <li>Edit /etc/pam.d/common-auth to lock accounts after three failed login attempts by running the following:</li>
      <img src="https://i.imgur.com/mdQCxCn.png" height="25%" width="25%" alt="script"/>
      <br/>
      <ul>
         <li>Add the following line of code:</li>
         <img src="https://i.imgur.com/nsauMaI.png" height="40%" width="40%" alt="script"/>
         <br/>
         <li>auth required pam_tally2.so: Specifies the use of the pam_tally2 module, which keeps a tally of failed login attempts for user accounts.
         <li>deny = 3: Sets a limit of 3 failed login attempts before the account is locked.</li>
         <li>unlock_time=600: Configures the lockout period to 600 seconds (10 minutes). After this time, the account is automatically unlocked.</li>
         <li>onerr=fail: Ensures that if there’s an error in the PAM module, access is denied by default.</li>
         <li>audit: Enables logging of authentication attempts, including both successful and failed logins.</li>
         </li>
      </ul>
   </ul>
</ol>

<h2>Step 6: Monitor User Activity </h2>
<ol>
   <li>Enable Audit Logging</li>
   <ul>
      <li>Install auditd:</li>
   </ul>
   <br/>
   <img src="https://i.imgur.com/tBsG67J.png" height="40%" width="40%" alt="script"/>
   <br/>
   <li>Start and enable the service:</li>
   <img src="https://i.imgur.com/TWlGzOR.png" height="40%" width="40%" alt="script"/>
   <br/>
   <li>Define Audit Rules</li>
   <img src="https://i.imgur.com/y0pv2di.png" height="40%" width="40%" alt="script" "/>
   <br/>
   </li></ul>
   <h2>Step 7: Conclusion</h2>
 In this project, I developed an automated user management solution for Linux, covering onboarding, access control, and offboarding. I created a Bash script to automate account creation with secure default settings, restricted SSH access based on roles, and implemented group-based access controls. The project also includes login monitoring for proactive security and regular user access reviews, ensuring that only authorized users have access. This demonstrates my skills in Linux administration, automation, and security, showcasing essential competencies for a System Administrator role.     
</ol>

