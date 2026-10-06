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





