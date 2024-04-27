# iLO 5 Password Recovery
![maxresdefault](https://github.com/fayas1290/iLO-5-Password-Recovery/assets/157561213/27e63f69-0bf9-48c6-9ca9-e57a21650532)



## Issue

Customer needs to change iLO 5 login passwords for 4 different servers via scripting method without rebooting the OS

## Scenario
The customer does not have access to the server physically.
The customer only has remote access to the OS (RHEL, terminal) on the server

Assets in question: HPE Proliant DL360 Gen10 Servers.

<br>

> 💡 What is iLO 5? (Integrated Lights Out 5)
>  
> iLO 5 is a remote server management processor embedded on the system boards of HPE ProLiant servers and Synergy compute modules. iLO enables the monitoring and controlling of servers from remote locations. It is the proprietary BMC (Baseboard Management Controller) for HPE servers.

<br>

![iLO SS](https://github.com/fayas1290/iLO-5-Password-Recovery/assets/157561213/430be0c5-7802-41c8-8197-6c9e73a1c428 )
Screenshot of iLO5 Web GUI Power & Thermal tab which can be accessed with a previously set IP address. 

<br>


## Troubleshooting

Since the customer does not have physical access to the server, they cannot access the BIOS settings (HPE name: System Utilities) to recover the iLO 5 password, we can create a temporary user with the HPONCFG utility within the OS and log in to iLO 5 GUI to edit the existing users’ login credentials

<br>

>  💡 What is HPONCFG Utility?
>
> The HPONCFG utility is an online configuration tool used to setup and
reconfigure iLO from within the Windows and Linux operating
systems without requiring a reboot of the server operating system. The utility
runs in a command line mode, and must be executed from an operating system
command line.

<br>

## Procedure 

1. Download the HPONCFG Utility for Linux from [https://support.hpe.com/connect/s/softwaredetails?language=en_US&collectionId=MTX-db808d6aa2e24648&tab=Installation+Instructions](https://internal.support.hpe.com/connect/s/softwaredetails?language=en_US&collectionId=MTX-db808d6aa2e24648&tab=Installation+Instructions)
2. Transfer the .RPM installation file to the target server with the help of an FTP client like FileZilla and navigate to its location via terminal
3. Type in the following command in the terminal to install HPONCFG with dependency check,
    
    Note: Login as root
    
    ```bash
    rpm -ivh hponcfg-x.x.x.x86_64.rpm
    ```
    
4. After installation, navigate to /sbin to find the executable for HPONCFG utility
5. At this directory type in following command to pull the current configuration XML file
    
    ```bash
    hponcfg -w filename.xml
    ```
    
6. Edit the file to add new user, and create new XML file with the following text: using a text editor like Vim
    
    ```xml
    <RIBCL VERSION="2.1"> 
    <LOGIN USER_LOGIN="Administrator" PASSWORD="password">
    <USER_INFO MODE="write">
    <ADD_USER //Adds a new user with user login as "Test" and password as "test123test123"
    USER_NAME="Test"
    USER_LOGIN="testuser"
    PASSWORD="test123test123">
    <ADMIN_PRIV value ="Y"/>
    <REMOTE_CONS_PRIV value ="Y"/>
    <RESET_SERVER_PRIV value ="Y"/>
    <VIRTUAL_MEDIA_PRIV value ="Y"/>
    <CONFIG_ILO_PRIV value="Y"/>
    </ADD_USER>
    </USER_INFO>
    </LOGIN>
    </RIBCL>
    ```
    
7. Pushed the new xml file to HPONCFG by command:
    
    ```bash
    hponcfg -f newfile.xml
    ```
    
8. Now, we can login to iLO 5 Web GUI with username as testuser and password as test123test123 and change existing users’ credentials from iLO 5>Administration tab

## Conclusion
In summary, the task was to change iLO 5 login passwords for four servers remotely, without rebooting the OS. Challenges arose due to the lack of physical server access, which rendered BIOS settings inaccessible. To overcome this, we created a temporary user using the HPONCFG utility within the OS. This tool allowed us to modify iLO settings without rebooting. Patience while communicating with the customer played a crucial role as we navigated through the technical intricacies and ensured clear understanding of the steps involved. By following a streamlined procedure, including downloading the utility, installing it, and editing the configuration XML file, we successfully updated the passwords and managed server access remotely.

