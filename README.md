<h1>SOC Analyst Lab</h1>

 ### [So You Want To Be A SOC Analyst](https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-intro)

<h2>Description</h2>
This project consists of getting hands-on experience through the life cycle of a security incident. This demonstration starts with creating both an attack Ubuntu Server and a victim Windows 11 system through VMWare, then continues through the creating, infecting the Windows VM, and deciphering the noise of an infected machine in LimaCharlie EDR. <br/>I then create Detection and Response rules based off of the malware, and tune the rules to prevent false positives.
<br/>Finally I demonstrate the use of YARA rules to both manually, and automatically, scan an endpoint for the malware.
<br />


<h2>Utilities Used</h2>
 
- <b>PowerShell</b>
- <b>Sliver</b>
- <b>LimaCharlie</b>

<h2>Environments Used </h2>

- <b>Ubuntu Server</b>
- <b>Windows 11</b> (22H2)

<h2>Project walk-through:</h2>

<p align="center">
SSH into attacker VM, download Sliver, an open source red team emulation framework software: <br/>
<img src="https://i.postimg.cc/6qcY4vhP/1-First-SSH-into-Attack.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/y6yfbMzT/2-Download-Silver-C2.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Installing Sliver, generating a payload, sending payload to Windows VM via temp web server:  <br/>
<img src="https://i.postimg.cc/cCDYRMtV/1-Sliver-install.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/T2QrvGNC/2-Generate-payload.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/FsR09q2m/3-Temp-web-server-payload.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/bNBkdghn/4-Malware-staged.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Confirmed Sliver could see session from Windows VM, poking around victim computer from attacker computer using commands like "whoami", "getprivs", "netstat", etc: <br/>
<img src="https://i.postimg.cc/7hLJZGtX/5-Sliver-HTTP-callback.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/nh97mmFP/6-Sliver-session-control-Info.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/63cGKLcy/7-whoami-getprivs.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
 <br /> Sliver highlights attacker activity within netstat as green, and any security connections in red
<img src="https://i.postimg.cc/9f39YfzF/8-pwd-netstat.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/VNjCGcb9/9-ps-T.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
The Endpoint Detetction and Response software used is LimaCharlie, here I can see the connections to the endpoint that are signed, and unsigned:  <br/>
<img src="https://i.postimg.cc/xCnbBFKw/10-Lima-Charlie-signed-and-unsigned-processes.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
I am able to look at the unsigned connection's network information, and check for all events relating to that IP:  <br/>
<img src="https://i.postimg.cc/WzfFFNQV/11-Malware-network-connections.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
 <img src="https://i.postimg.cc/RVNnrtHZ/12-Lima-Charlie-Network.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
 <img src="https://i.postimg.cc/RZt6hzGr/13-Lima-Charlie-Network-IP.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
I am also able to inspect the hash of the file related to the unsigned connection, and compare it to VirusTotal for known malware (since this isn't known malware and was just created by me, VT comes up with no results):  <br/>
<img src="https://i.postimg.cc/FFPJnp16/14-Inspect-hash.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/xThN9dvy/15-Virus-Total-hash-result.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
 <br />
<br />
From the attacker I will run a common process attackers will use to steal system credentials, lsass, then identify the event in the EDR:  <br/>
<img src="https://i.postimg.cc/JnggxgHL/1-Reconnect-C2-run-lsass-dump.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/9QpnKzw1/2-Finding-lsass-event-in-EDR.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Now I will build a Detection and Response rule to address the lsass dump  <br/>
LimaCharlie lets you create rules directly from an event, like so:   <br/>
<img src="https://i.postimg.cc/qBPSCPdc/3-Build-D-R-rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
 <br />
<br />
I will input these rules for detection and the response. The detection rule is defining an operation take place whenever an event ends with the "lsass.exe" in the /TARGET/FILE_PATH path. <br/>
The response is that it will generate a report named LSASS access <br/>
<img src="https://i.postimg.cc/SRMPvrcz/4-Adding-D-R-rules.png" height="80%" width="80%" alt="SOC Analyst Lab"/> <br/>
 <br/>
Under the creation of the rule, I can test that rule against the original event. The EDR will let me know that the event would be caught by the rules I laid out with the green text at the bottom.<br/>If the rules had no effect, they would appear red
 <br/>
<img src="https://i.postimg.cc/KvtV70Z6/5-Testing-rules-against-event.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
  <br />
<br />
Running lsass again from the attacker, I can see it detected from the EDR <br/>
<img src="https://i.postimg.cc/HkHN6bnV/7-Run-lsass-again-it-is-detecected-on-EDR.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
Next I run a very basic version of another well known attack, deleting volume shadow copies, which aids in the implmentation of ransomware  <br/>
Here I see the command run from the attacker, and the detection from the EDR<br/>
<img src="https://i.postimg.cc/63rhFmyR/1-Delete-volume-shadow-copies.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/022CHZxM/2-Detection-in-EDR.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
Within the event, the EDR gives references to why this event was automatically detected, linking to resources describing legitimate and illegitimate Shadow Copy deletion examples<br/>
<img src="https://i.postimg.cc/ry9JJ762/3-EDR-giving-references-on-why-it-is-detected.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
Here I am crafting rules to report and now also deny the process
<img src="https://i.postimg.cc/vTGvytVK/4-EDR-response-rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
This shows the attacker side completing a shadow copy delete and able to run whoami, confirming a connection, before I added the rule.<br/>
Then underneath it I can see running the shadow copy delete command again leads the whoami command to return that the shell exited, aka my connection was killed <br/>
<img src="https://i.postimg.cc/d3njVZRV/5-Volume-shadow-delete-attempt-after-EDR-rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
The next demonstration is in regard to false positives, and how to fine tune the detection rules to lessen or eliminate them  <br/>
Below is a demonstration of a broad rule that is creating an alert from very common event.<br/>
The output in the EDR shows many events that match the criteria, which were just from the reboot of the Windows VM<br/>
<img src="https://i.postimg.cc/59vw7GZ2/1-Purposefuly-broad-detection-rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/7hyTrHpY/2-Demo-of-false-positives-from-broad-rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
Pulling one of these events, I can see the process includes the expected file path for a normal svchost, and expected command line arguments from a normal svchost <br/>
<img src="https://i.postimg.cc/zBhRQb2c/3-Expected-file-path-from-a-normal-svchost.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/nLgDTY2m/4-Expected-command-line-arguments-from-a-normal-svchost.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
When marking an event as a false positive in LimaCharlie, below are the suggested edits it puts forth to help me tune the false positive. <br/>
<img src="https://i.postimg.cc/5NS81z9c/5-Automatic-false-positive-tuning-suggestions-from-EDR.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
However, I want to fine tune even further. The scvhost command will always use the -k argumnet, but the other arguments might change. So by getting rid of the other arguments and just focusing on -k, I can be sure I'm not catching any false positives from normal commands that are slightly different<br/>
I also get rid of the auto-generated hash and hostname detection rules, since something as basic as an OS update could potentially change the hash of svchost, causing further false positives. <br/>
<img src="https://i.postimg.cc/ZKS61HSF/6-Fine-tuned-false-positive-tuning.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
Grabbing a false positive event, I can test the rules to see the rules would have caught that false positive <br/>
<img src="https://i.postimg.cc/GpHDkSFr/7-Testing-rule-against-previous-detection.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
After clearing the detections, then rebooting the VM again, I can see the previously detetced svchosts are no longer being detetced <br/>
<img src="https://i.postimg.cc/sgf7221M/8-Confirm-no-more-false-positives.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<br />
<br />
The final demonstration is creating YARA rules in the EDR and then automating those rules  <br/>
Sliver YARA rules I am using were published by the UK NCSC in a PDF found [here](https://www.ncsc.gov.uk/files/Advisory-further-TTPs-associated-with-SVR-cyber-actors.pdf)
<img src="https://i.postimg.cc/9MxZZ5SW/1-Creating-YARA-rules-for-sliver.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/VvdMFZQX/2-YARA-DR-Rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
After running the payload on the Windows VM again, I can see the detection on the EDR after I manually scan for it via the console <br/>
<img src="https://i.postimg.cc/VvdMFZQX/2-YARA-DR-Rule.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/c4twgzcS/4-YARA-Detection.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
Now to automate it, I will create an automation rule in the EDR. Here I will say that a new document appearing in the downloads folder with an EXE extension will trigger both a report, and my YARA scan <br/>
<img src="https://i.postimg.cc/Vkjt4bTZ/5-Automate-scan-for-new-EXE.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/sgFSpJnZ/6-Automate-scan-for-new-process-from-Downloads.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
Here I will move my malware on the Windows computer out of the downloads folder, into the documents folder, then back into downloads folder. This will trigger one of the rules I just created, then running the program will trigger the other rule <br/>
<img src="https://i.postimg.cc/N0DHvvQ1/7-Moving-malware-out-of-then-back-into-Downloads-to-trigger-detection.png" height="80%" width="80%" alt="SOC Analyst Lab"/><br/><br/>
As you can see, both actions were detected automatically, as opposed to manually running the YARA scan from the previous step <br/>
<img src="https://i.postimg.cc/RCDw0mKd/8-YARA-Detections-in-EDR.png" height="80%" width="80%" alt="SOC Analyst Lab"/>
<img src="https://i.postimg.cc/N0BHnjvs/9-YARA-Detections-from-running-EXE.png" height="80%" width="80%" alt="SOC Analyst Lab"/>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
