# Personal Forensics Toolkit

## Introduction

This document should be used as a Digital Forensics Toolkit quick reference sheet. Each tool includes the following:

- Source URL (each tool's title is hyperlinked)
- Description
- Review
- Usage

**Unless otherwise stated, all tools are installed on Debian GNU/Linux*

[TOC]



[TOC]
## 1. Host and Storage Tools
### [1.1 FTK Imager](https://accessdata.com/product-download/ftk-imager-version-4.2.0)

- FTK Imager is a commercial forensic imaging software distributed by AccessData. The program creates images from hard drives and other types of storage devices. FTK can create images in four different file formats: .E01, SMART, AFF, and Raw. These images can be one file or be split into segments that can be constructed later on. When the file is split into segments, the files can be moved and stored in several locations.

  **[Source](https://www.champlain.edu/Documents/LCDI/Tool_Comparison_(1).pdf)*
- Review

  - I really like using FTK Imager, it is very easy to use and runs well off a USB drive. For faster speeds, I recommend running it on a USB flash drive rather than an external hard disk,

- Usage

  - FTK Imager comes with a GUI that is very straight forward and easy-to-use. Simply choose what you want to name your disk images and follow the prompts. 

  CLI: 
  ```bash
  ftkimager <source drive> <destination> --case-number <X> --evidence-number <X> --examiner <X> --outpass <encryption password>
  ```
  
### [1.2 Autopsy](<https://www.sleuthkit.org/autopsy/>)

- Description

  - Autopsy® is a digital forensics platform and graphical interface to The Sleuth Kit® and other digital forensics tools. It is used by law enforcement, military, and corporate examiners to investigate what happened on a computer. You can even use it to recover photos from your camera’s memory card.
    *[Source](https://www.sleuthkit.org/autopsy)*

- Review

  - I prefer almost every tool in a Linux environment, apart from Autopsy. The user interface in Linux Autopsy is nigh impossible to use. Do yourself a favor and just use the Windows 10 Version, it really is better. 

- Usage

  - Simply use the GUI to navigate to various sections of the disk. I like to go in this order:

    1. If you an idea of what you are looking for, initialize a keyword search. (I like to do this first, it can take some time).

    2. Check the security logs. 

       1. On Linux, check at

          ```/var/log/auth and /var/log/secure```

       2. On Windows 10, check the Window's Defender Logs at  

          ```/ProgramData/Microsoft/Windows Defender/Scans/History```

    3. Check user temp files

       - On Linux, check at 

         ``\temp``

       - On Windows 10, check 

         ```Users/username/AppData/Local/Temp```

    4. Check Registry 

    5. Check Web Search, History, and Artifacts

       !['Autopsy Web History'](pics/web-history.PNG)

    6. Check emails

    7. Check Peripheral and Aux Devices for USB Rubby Duckys and similar devices

       !['USB Rubber Ducky'](pics/rubber-ducky-confirm.PNG)

    8. Check Multimedia Files

       ![](pics/mm-10-1.PNG)

  **can be install via apt on Debian-based Linux distributions*

  ```bash
  sudo apt install autopsy
  ```

### [1.3 dd](https://linux.die.net/man/1/dd)

- Description

  - Convert and copy a file, write disk headers, boot records, create a boot floppy. dd can make an exact clone of an (unmounted) disk, this will include all blank space so the output destination must be at least as large as the input.

    **Source](<https://ss64.com/bash/dd.html>)*

- Review

  - I use dd all the time. Once you've used it a couple times it very easy to use. All you need to know is that disks are considered files and your "if" is the source disk, and the "of" is where you want the the disk file to go. Additionally, using the "status" lets you know the progress of the copy. I like setting my read/write block size to 4 megabytes.

- Usage


  ```bash
  sudo dd if=/dev/sdX of=<destination> bs=4M status=progress
  ```
Things start to get really spicy when you use dd over SSH. I like to do this to clone my Raspberry Pis.
  ```bash
  ssh root@<remote ip> "dd if=/<source disk>" | dd of=<destination file> status=progress bs=4M
  ```

!['dd over ssh'](pics/dd-over-ssh.PNG)

  ** dd comes default on all Debian-based Linux distributions*




## 2. Network Tools

### [2.1 Wireshark](http://www.wireshark.org/)

- Description

  - **Wireshark** is a GUI network protocol analyzer. It lets you interactively browse packet data from a live network or from a previously saved capture file. **Wireshark**'s native capture file format is **pcap** format, which is also the format used by **tcpdump** and various other tools.
  - **[Source](<https://www.wireshark.org/docs/man-pages/wireshark.html>)*

- Review

  - I am not a huge fan of Wireshark. Wireshark is *very* memory consumptive, also I have found that most of the time Wireshark gives way too much information. With that said, Wireshark is extremely useful when you want to really dig into some network data.

- Usage

  To pull up the GUI and perform a live packet capture:


  ```bash
  wireshark
  ```
  - Then choose the interface you want to sniff on

  !['Wireshark example'](wireshark.PNG)

  To use the GUI to browser through an existing pcap file:

  ```bash
  wireshark <name of pcap>
  ```
  **can be install via apt on Debian-based Linux distributions*

  ```bash
  sudo apt install wireshark
  ```

### [2.2 Zeek AKA Bro](https://www.zeek.org/)

- Description

  - Bro is primarily a security monitor that inspects all traffic on a link in depth for signs of suspicious activity. More generally, however, Bro supports a wide range of traffic analysis tasks even outside of the security domain,  including performance measurements and helping with trouble-shooting.
  - Bro comes with built-in functionality for a range of analysis and detection tasks, including detecting malware by interfacing to external registries, reporting vulnerable versions of software seen on the network, identifying popular web applications, detecting SSHbrute-forcing, validating SSL certificate chains, among others.
    **[Source](http://manpages.ubuntu.com/manpages/xenial/man8/bro.8.html)*

- Review

  - Zeek, previously known as Bro, is my absolute favorite network monitoring tool. Bro has its own scripting language that you can use to tune your network sniffing to an extreme. Bro is great because it gives you a high-level view of what is going on in your network, whereas Wireshark gives you everything, down to the version of each network protocol of each packet(Bro is capable of this as well). 
  - I typically use Bro as a live network monitoring appliance, but in terms of Digital Forensics, Bro works better at parsing through pcaps and creates logs based on the traffic within the pcap.

- Usage

  ```bash
  bro -r -C capture.pcap
  ```
  **can be install via apt on Debian-based Linux distributions*
  ```bash
  sudo apt install bro broctl
  ```

  
### [2.3 tcpdump](http://www.tcpdump.org/)

- Description

  - Dumps traffic on a network. Tcpdump  prints  out a description of the contents of packets on a network interface that match the boolean expression.

    *[Source](https://www.tcpdump.org/manpages/tcpdump.1.html)*

- Review

  - tcpdump is my go to tool when I want to know what is going on in my network RIGHT NOW. tcpdump can be used in the same way as Bro and Wireshark to parse through pcaps, but I have found that I prefer Bro for that task.

- Usage

  ```bash
  tcpdump -i <interface> -vvv |grep -e '<regex pattern>'
  ```

  ![tcpdump google.com example](pics/tcpdump.PNG)

  **can be install via apt on Debian-based Linux distributions*   
    ```bash
  sudo apt install tcpdump
    ```

  
### [2.4 Snort](http://snort.org)

- Description

  - Snort is an open source network intrusion detection system,  capable  of  performing real-time traffic analysis and packet logging on IP networks.  It can perform protocol analysis, content searching/matching and can be used  to  detect  a  variety  of attacks  and  probes, such as buffer overflows, stealth port scans, CGI attacks, SMB probes, OS fingerprinting attempts, and much more.  Snort uses a flexible rules language  to  describe  traffic  that it should collect or pass, as well as a detection engine that utilizes a modular plugin architecture.  Snort also has a modular  real-time  alerting  capability, incorporating alerting and logging plugins for syslog, a ASCII text files, UNIX sockets or XML.

  - Snort has three primary uses.  It can be used as a straight packet sniffer like tcpdump(1),  a  packet logger (useful for network traffic debugging, etc), or as a full blown network intrusion detection system.
    **[Source](<https://www.manpagez.com/man/8/snort/>)*

- Review

  - I like snort a lot. Not as much as Bro, but it's still pretty dang good. I like Snort because it is a intrusion detection system, Snort looks at network traffic specifically for attacks and network-based malware. The community around snort has contributed many custom Snort rules that can be saved at ```/etc/snort/rules/``` .

- Usage

  ```bash
  sudo snort -r <pcap file> -c <config file location>
  ```
  ​	After that, snort will print its results to ```/var/log/snort/alert``` .

  **can be install via apt on Debian-based Linux distributions*

  ```bash
  sudo apt install snort
  ```

  
## 3. Memory Analysis Tools

### [3.1 Volatility](https://www.volatilityfoundation.org/)

- Description

  - The Volatility Framework is a completely open collection of tools forthe extraction of digital artifacts from volatile memory (RAM) samples. It is useful in forensics analysis. The extraction techniques areperformed completely independent of the system being investigated but offer unprecedented visibility into the runtime state of the system.

    **[Source](http://manpages.ubuntu.com/manpages/cosmic/en/man1/volatility.1.html)*

- Review

  - 

- Usage

  ```bash
  volatility -f memdump.mem imageinfo
  volatility -f memdump.mem --profile=Win10x64_10568 netscan 
  volatility -f memdump.mem --profile=Win10x64_10586 pslist
  volatility -f memdump.mem --profile=Win10x64_10586 psscan
  volatility -f memdump.mem --profile=Win10x64_10586 pstree`
  volatility -f memdump.mem --profile=Win10x64_10586 psxview
  volatility -f memdump.mem --profile=Win10x64_10586 apihooks
  volatility -f memdump.mem --profile=Win10x64_10586 netscan
  ```
  **can be install via apt on Debian-based Linux distributions*
  ```bash
  sudo apt install volatility
  ```

  
### [3.2 LiME](https://github.com/504ensicsLabs/LiME)

- Description

  

  **[Source]()*

- Review

  - 

- Usage

  ```bash
  apt-get install build-essential linux-headers-4.9.0-8-amd64 openssh-server
  #After that, we made a copy of the “LiME” memory capture repository posted on Github.com with the following command:
  git clone https://github.com/504ensicsLabs/LiME
  #After we obtained a copy of the repository, we started the process of acquiring a memory capture via network. Here are the steps we took:
  sudo su
  cd /root/LiME/src
  make
  insmod /root/LiMe/src/lime-3.2.0-24-generic-pae.ko "path=tcp:4444 format=lime timeout=0"
  nc <target ip addreess> 444 > mem-cap-evidence2.1.2.mem
  ```
```
**can be install via apt on Debian-based Linux distributions*
  ```bash
  
```



## 4. Documentation and Other Tools

### [4.1 hashdeep](https://github.com/jessek/hashdeep)

- Description

  

  **[Source]()*

- Review

  - 

- Usage

  ```bash
  
  ```
  **can be install via apt on Debian-based Linux distributions*
  ```bash
  
  ```

  
### [4.2 vim](https://github.com/vim/vim)

- Description

  

  **[Source]()*

- Review

  - 

- Usage

  ```bash
  
  ```
  **can be install via apt on Debian-based Linux distributions*
  ```bash
  
  ```

  
### [4.3 FIR (Fast Incident Response](<https://github.com/certsocietegenerale/FIR>)

- Description

  

  **[Source]()*

- Review

  - 

- Usage

  ```bash
  
  ```
  **can be install via apt on Debian-based Linux distributions*
  ```bash
  
  ```

  
### [4.4 FTK](https://accessdata.com/products-services/forensic-toolkit-ftk)

- Description

  

  **[Source]()*

- Review

  - 

- Usage

  ```bash
  
  ```
  **can be install via apt on Debian-based Linux distributions*
  ```bash
  
  ```

  
### []()

- Description

  

  **Source]()*

- Review

  - 

- Usage

  ```bash
  
  ```

  **can be install via apt on Debian-based Linux distributions*

  ```bash
  
  ```

  


FIR (fast incident response)
FTK
Wireshark
ELK (Security information and event management)
Volatility
Netcat
Portminer?
EnCase
Snort
tcpdump
Autopsy
WinPrefetch View
LiME
DD
forensics-all
lime-forensics-dkms
snort
suricata
tcpdump
vim
volatility
wireshark
tcpdump 
#
wireshark
#
ngrep
#Search packets http://ngrep.sourceforge.net/
flowgrep
#Search for expressions in packet stream http://monkey.org/~jose/software/flowgrep/
hexdump
#http://sourceforge.net/projects/hexdump/
snort
#
suricata
#http://www.openinfosecfoundation.org/index.php/downloads
tcpdstat
#http://staff.washington.edu/dittrich/talks/core02/tools/tools.html
fir
#https://github.com/certsocietegenerale/FIR
vim
#
forensics-all
lime-forensics-dkms
volatility