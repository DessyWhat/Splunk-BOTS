# Splunk-BOTS
Completion and Documentation of Splunk BOTS and Other Splunk based Challenges

# Boss of The SOC Version 1

## Scenario 1 Background:
I am Alice a recent new hire for the Security Operations Center of Wayne Corporations and I have just received my first task. Wayne Corporations received a memo from Gotham City Police Department (GCPD). Apparently GCPD has found evidence online (http://pastebin.com/Gw6dWjS9) that the website www.imreallynotbatman.com hosted on Wayne Enterprises' IP address space has been compromised. The group has multiple objectives... but a key aspect of their modus operandi is to deface websites in order to embarrass their victim. Lucius has asked Alice to determine if www.imreallynotbatman.com. (the personal blog of Wayne Corporations CEO) was really compromised.

## Resources to Use
` Splunk server: ` https://gettingstarted.splunk.show  

``Credentials: `` Username: user001-splk Password: Splunk.5  

`Alice's Journal:` https://assets.ctfassets.net/v6zg15is9ny1/61mwyKjugG9TCfJFFu42iV/b207b79bcd52c33ae6067ad51022b2c4/Alice-Journal.html.pdf  

`Splunk Quick Reference Guide:` https://www.splunk.com/en_us/resources/splunk-quick-reference-guide.html  

`GCPD Poison Ivy Memo:` https://assets.ctfassets.net/v6zg15is9ny1/4obuohLDEd3IESciwYn6ZO/ab70d17cc52e7f98a1518452943cdeac/GCPD-PoisonIvy-Memo.html.pdf  

`Mission Document:`  https://assets.ctfassets.net/v6zg15is9ny1/1Rr1oPQ0JHwBsNFKqNNUPJ/560deb3f9b6fe236bc977a34de0d8c73/mission_document.html.pdf  

## Questions 
### Question 101: What is the likely IPv4 address of someone from the Po1s0n1vy group scanning imreallynotbatman.com for web application vulnerabilities?
1. First I ran the following query to get into the index of where the data for the simualtion was created and then the source was HTTP as it was a website defacement.
   ```
   index=botsv1 sourcetype="stream:http"
   ```
2. I then checked out the top 10 most popular IP's and the IP 40.80.148.42 was at the top of the list.
   <img width="1910" height="918" alt="image" src="https://github.com/user-attachments/assets/1158c7bd-0569-4dea-a9ae-c913db7a6ad0" />

3. To verify I then created query to check out a specific entry from the source IP that shows in the headers its from a web vulnerability scanner from Acunetix which is the next question.
 ```
index=botsv1 sourcetype="stream:http" src_ip="40.80.148.42"
```
   <img width="1903" height="882" alt="image" src="https://github.com/user-attachments/assets/a9b32450-27c4-438f-bc36-2941db69433f" />


### Question 102: What company created the web vulnerability scanner used by Po1s0n1vy? Type the company name.
 In the Previous question i ran the following query to verify the IP found was used for scanning purposes and the scanner was the free version of the Acunetix Web Vulnerability Scanner
```
index=botsv1 sourcetype="stream:http" src_ip="40.80.148.42"
```
<img width="1903" height="882" alt="image" src="https://github.com/user-attachments/assets/a9b32450-27c4-438f-bc36-2941db69433f" />  

### Question 103: What content management system is imreallynotbatman.com likely using?
With the previous query if we look at the headers we will see the vulnerabilty scanner being used is from Acunetix.
```
index=botsv1 sourcetype="stream:http" src_ip="40.80.148.42"
```
<img width="1903" height="882" alt="image" src="https://github.com/user-attachments/assets/a9b32450-27c4-438f-bc36-2941db69433f" />  

### Question 104: What is the name of the file that defaced the imreallynotbatman.com website? Please submit only the name of the file with extension?  
1. I ran the following query to see where the web server with the IP address of "192.168.250.70" was getting the file that defaced the website.
```
index="botsv1" sourcetype="stream:http" src_ip="192.168.250.70" http_method=GET
```
2. It requests the jpeg which is the file used to deface the website.
<img width="1912" height="884" alt="image" src="https://github.com/user-attachments/assets/55a66b50-bf1d-42c6-b501-9035893c4099" />  

### Question 105: This attack used dynamic DNS to resolve to the malicious IP. What fully qualified domain name (FQDN) is associated with this attack?  
In the previous question the file was being downloaded from the website prankglassinebracket.jumpingcrab.com on port 1337 which is the FQDN of the site the malicious actors are using to host their files and C2 command site.
<img width="1912" height="884" alt="image" src="https://github.com/user-attachments/assets/bb2c2fc7-64bb-4e39-b78c-90abc4812cdf" />  

### Question 106: What IPv4 address has Po1s0n1vy tied to domains that are pre-staged to attack Wayne Enterprises?
The IP is the dest_ip 23.22.63.114 for when the file is being requested from the website prankglassinebracket.jumpingcrab.com
<img width="1912" height="884" alt="image" src="https://github.com/user-attachments/assets/bb2c2fc7-64bb-4e39-b78c-90abc4812cdf" />    

### Question 108: What IPv4 address is likely attempting a brute force password attack against imreallynotbatman.com?
1. I ran the following query to find the IP that was posting authentications requests under form data to the server 192.168.250.70 and the only IP's present were the web scanner IP and 23.22.63.114.
```
index="botsv1" sourcetype="stream:http" http_method=POST dest="192.168.250.70"
```
<img width="1907" height="873" alt="image" src="https://github.com/user-attachments/assets/783cd3eb-0839-42f6-9b91-e99353bfcfcd" />  

### Question 109: What is the name of the executable uploaded by Po1s0n1vy? 
1. Ran the query to search the Fortinet Index for an exe downloaded in the network.
 ```
index=botsv1 sourcetype="fgt_utm" srcip=40.80.148.42 *.exe
```
3. Found the exe "3791.exe" downloaded from the srcip 40.80.148.42
<img width="1907" height="919" alt="image" src="https://github.com/user-attachments/assets/bc3faea1-e5eb-47e6-82b5-1009aee0500e" />

### Question 110: 
1.Search the file being run via this query to find the cmd line running the malware exe.
```
index="botsv1" sourcetype="xmlwineventlog:microsoft-windows-sysmon/operational" "3791.exe" CommandLine-"3791.exe"
```
<img width="1903" height="914" alt="image" src="https://github.com/user-attachments/assets/01013df0-c3b2-4fb6-a1e6-f1874267b92b" />  

### Question 111: GCPD reported that common TTPs (Tactics, Techniques, Procedures) for the Po1s0n1vy APT group, if initial compromise fails, is to send a spear phishing email with custom malware attached to their intended target. This malware is usually connected to Po1s0n1vys initial attack infrastructure. Using research techniques, provide the SHA256 hash of this malware.
1. You search up the IP 23.22.63.114 and search it on VirusTotal to see what other files are associated with it and their hashes.
<img width="1916" height="923" alt="image" src="https://github.com/user-attachments/assets/ff83c5ac-8b9b-410a-a555-238f22b9c6af" />

### Question 112: What special hex code is associated with the customized malware discussed in question 111?
Hex code is found in community notes of file in VirusTotal
<img width="1916" height="909" alt="image" src="https://github.com/user-attachments/assets/f32ef53d-a46a-478f-9a32-3eb60fa5fecc" />  

### Question 114: What was the first brute force password used?
1. Ran the query below to get everytime the IP tried to login to the web server and then went to the first event created.
```
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" src_ip="23.22.63.114" http_method=POST uri=/joomla/Administrator/index.php
```
<img width="1917" height="963" alt="image" src="https://github.com/user-attachments/assets/ac0ab167-22f5-4e9d-90e2-ef7ac46a1686" />  

### Question 115: One of the passwords in the brute force attack is James Brodsky's favorite Coldplay song. We are looking for a six character word on this one. Which is it?
Used AI to create a regex expression to extract 6 letter words to add to the below query to find 6 character passwords then I cross-examined with coldplay songs and the found song named yellow.
```
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" src_ip="23.22.63.114" http_method=POST uri=/joomla/Administrator/index.php | rex field=form_data "passwd=(?<extracted_password>[A-Za-z]{6})(?:\b|&|\s|$)"
| where isnotnull(extracted_password)
| table _time, extracted_password
```
<img width="1909" height="925" alt="image" src="https://github.com/user-attachments/assets/9725bad0-dccd-486c-bf79-a029a7795192" />  

### Question 116: What was the correct password for admin access to the content management system running "imreallynotbatman.com"?
1. Using this query I found all the authentication events for the web server and looked over the most recent ones to find the password that got them in.
```
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" src_ip="23.22.63.114" http_method=POST uri=/joomla/Administrator/index.php
```
<img width="1917" height="915" alt="image" src="https://github.com/user-attachments/assets/e0a7e744-2be2-4e94-be95-0de7c5ee8b96" />  

### Question 117: What was the average password length used in the password brute forcing attempt?
Ran the updated query from Question 116 to query the average length of the passwords.

```
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" src_ip="23.22.63.114" http_method=POST uri=/joomla/Administrator/index.php
| rex field=form_data "passwd=(?<string>\w+)"
| eval stringlength=len(passwd)
| stats avg(stringlength) as average_length
```
<img width="1917" height="915" alt="image" src="https://github.com/chan2git/splunk-bots/blob/main/botsv1/images/ss25.png" />

### Question 118: How many seconds elapsed between the time the brute force password scan identified the correct password and the compromised login? (Round to 2 decimal places)
I used the following query to see the difference between the first use and second use of the password of the web server which is batman.
```
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*passwd*batman*
| rex field=form_data "passwd=(?<string>\w+)"
| transaction string
| table duration
```
<img width="1917" height="831" alt="image" src="https://github.com/user-attachments/assets/e5ac81d6-3207-4841-8d55-13069bb1b223" />

### Question 119: How many unique passwords were attempted in the brute force attempt? 
We can try the first query where all the passwords where put as a string and see how many events there were which was 412.
```
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" src_ip="23.22.63.114" http_method=POST uri=/joomla/Administrator/index.php
```

<img width="1917" height="963" alt="image" src="https://github.com/user-attachments/assets/ac0ab167-22f5-4e9d-90e2-ef7ac46a1686" />  









