# SOC Automation Lab

## Objective

To design, implement, and demonstrate an automated Security Operations Center workflow using Shuffle SOAR for orchestration, VirusTotal for threat intelligence enrichment, and TheHive for case management—aimed at improving incident response efficiency, reducing workload, and streamlining threat detection and triage processes.

### Skills Learned

- Hands-on experience configuring and using Shuffle SOAR platform.
- Designing and deploying automation workflows for incident response.
- Utilizing VirusTotal API for automated file and URL reputation lookups.
- Parsing and interpreting threat intel data for enrichment in investigations.
- Creating, managing, and updating cases in TheHive.
- Automating the creation of alerts, tasks, and case escalation.

### Tools Used

- Shuffle SOAR for security workflow automation.
- TheHive for case management.
- Virustotal for threat intelligence enrichment.

## Steps
This project picks up where my Detection Lab left off, I will be using alerts from Wazuh to trigger the workflow in shuffle. I will first use the rull I created for Mimikatz to trigger the alerts, then I will create a second agent on Wazuh that will trigger a workflow that denies IP's who are attempting to connect to the machine via SSH.

The first thing I needed to do was create another VM running Ubuntu 22.04. I will then install TheHive on this machine.

<img width="759" height="361" alt="Screenshot 2025-07-28 212829" src="https://github.com/user-attachments/assets/bc877db6-11a0-47da-a8e0-01703ef6fe96" />

Once created, I then needed to add it to a firewall rule set that limited access to the machine, in this case, I used the same farewall as my Wazuh manager machine.

<img width="2516" height="976" alt="Screenshot 2025-08-12 142657copy" src="https://github.com/user-attachments/assets/6f2d46ba-0a3f-4ab6-a56b-057c8037fa5c" />

Now it was time to SSH into this machine and install the necessary programs to get TheHive up and running. There were three prerequisites I needed to install before installing TheHive: Java, Cassandra, and ElasticSearch. Once those were complete, I then installed TheHive. The next step was to configure these services.

The first service I will configure is Cassandra by editing the cassandra.yaml file. I start by changing the listen_address and rpc_address to the address of the TheHive machine. I also needed to change th seed address to TheHive machine on port 7000.

<img width="277" height="92" alt="Screenshot 2025-08-07 161125" src="https://github.com/user-attachments/assets/87002641-d09a-4d3f-bf80-bfa0f19e6045" /><tr>

<img width="256" height="130" alt="Screenshot 2025-08-07 161154" src="https://github.com/user-attachments/assets/398e1f38-272d-4672-85a4-0828603addd2" /><tr>

<img width="329" height="110" alt="Screenshot 2025-08-07 161247" src="https://github.com/user-attachments/assets/dba9e564-0213-4ae7-802a-f5f29614a94c" />

Next, I needed to configure ElasticSearch, Which involves editing the elasticsearch.yml file. First I needed to change the cluster.name to the same name in the cassandra.yaml, then I needed to change the network host to the the address of TheHive machine on port 9000.

<img width="262" height="178" alt="Screenshot 2025-08-07 161411" src="https://github.com/user-attachments/assets/c802ed7c-39e7-4be3-8dc6-580791ab910d" />

Now It was time to configure TheHive, The first thing I needed to do was change the permissions of the thp directory so that thehive user and group have access to it.

<img width="467" height="110" alt="Screenshot 2025-08-07 161557" src="https://github.com/user-attachments/assets/b6c58d25-5d5c-4206-85c7-4e38bf220fdd" />

Now we can go to the TheHive application.conf file and configure that. first I need to change the hostname to the public IP of TheHive machine. Then I need to the cluster.name to the name i had given it in cassandra.yaml. we also need to change the application.baseUrl to include the public IP of TheHive machine.

<img width="683" height="443" alt="Screenshot 2025-08-07 161709" src="https://github.com/user-attachments/assets/4f974080-f141-4173-b0bf-d113cf868e3d" /><tr>

<img width="246" height="124" alt="Screenshot 2025-08-07 161728" src="https://github.com/user-attachments/assets/de3e35ce-c344-4b6f-8989-fb00a75f62e8" /><tr>

<img width="336" height="102" alt="Screenshot 2025-08-07 161818" src="https://github.com/user-attachments/assets/b7abf890-d430-4773-a83d-5b0f00fecd08" />

Ok, now I have TheHive up and running!

<img width="1510" height="781" alt="Screenshot 2025-08-13 143308" src="https://github.com/user-attachments/assets/d0adb5bf-c93e-444c-aa38-26b3d0bb9c55" />

Now that I have TheHive working, I am going to create a new organization and create two users in this organization, one normal account with analyst privelages, and one service account, for the privelages on this one, normally we would want to create a custom profile with "least privilage" in mind, but for the sake of this just being a demo, I chose analyst. For the service account, I create an API key to use in Shuffle.

<img width="973" height="390" alt="Screenshot 2025-08-07 161907" src="https://github.com/user-attachments/assets/6178c65b-466c-4fec-a650-4d62337c4a0b" /><tr>

<img width="3146" height="522" alt="Screenshot 2025-08-13 144830" src="https://github.com/user-attachments/assets/b46fad2b-d4ad-43b1-881d-95bafa593e84" />

Now I started a new workflow on Shuffle and added a webhook. the next thing I need to do is get the webhook URI and integrate it in the Wazuh manager ossec.conf file. (A note for the second picture below: I took these screenshots after I had finished this lab. At this point I would want the <level> tag to be replaced with <rule_id> and the rule to be 100002, as this is the rule I created for Mimikatz detection. Later in this lab, I will change it to <level> though.)

<img width="350" height="271" alt="Screenshot 2025-08-07 190948copy" src="https://github.com/user-attachments/assets/8ac8508c-d31c-4a1f-be9d-d76295bdfae2" /><tr>

<img width="993" height="646" alt="Screenshot 2025-07-28 212552copy" src="https://github.com/user-attachments/assets/b221e048-a8ff-4da7-9dbe-699184a8100d" />

Now that I have the webhook configured, the Mimikats alert is sent from Wazuh to shuffle. Now I want to extract the SHA256 hash from the Mimikatz file, to do this I need to parse out the hash value using a Regex capture group. I used ChatGPT to create a Regex script to parse SHA256 from the value "hashes" provided in the alert data.

<img width="333" height="582" alt="Screenshot 2025-08-13 160714" src="https://github.com/user-attachments/assets/cac52dd8-84e4-47ac-b5c1-712fbdb23cd0" />

Now that I have parsed the SHA256, I am going to use Virustotal to analyze this hash and gather information on the file. In order to do this I needed to use an API key from Virustotal. Once I got that, I added the Virustotal app to my workflow and configured it to get a hash report, and for the has i used the output from the Regex parse from earlier.

<img width="339" height="468" alt="Screenshot 2025-08-13 162258" src="https://github.com/user-attachments/assets/dd41dffe-7524-4c12-8f58-1b46a5e32fd0" />

And we get results, I won't expand it just to save space on this page.

<img width="882" height="550" alt="Screenshot 2025-08-13 163740" src="https://github.com/user-attachments/assets/ca325d09-55fc-4f86-8a5a-7a00d5e545ba" />

Now my next task was to set up the TheHive app to to send the alert TheHive and create a new case. This was actually more of a challange than I was expecting because some of the fields that I wanted to include weren't in TheHive app, so I had to manually create the JSON script. In hindsight, it wasn't that challanging, but it was the first for me, so there was a bit of trial and error. I used the API key that I generated earlier to authenticate, then chose the action to create alert. Here is the general configuration as well as the JSON script that i came up with.

<img width="334" height="370" alt="Screenshot 2025-08-13 165331" src="https://github.com/user-attachments/assets/319d66e2-7059-4e0e-adcc-b8331445bd68" /><tr>

<img width="1551" height="676" alt="Screenshot 2025-08-07 191110" src="https://github.com/user-attachments/assets/d75d3825-45c1-4f27-a967-e9244889b2e5" />

I ran the workflow and the alert came through.

<img width="1861" height="229" alt="Screenshot 2025-08-13 165754" src="https://github.com/user-attachments/assets/689ec396-c527-4d25-869c-d617c6526004" />

My last task for this part of the project was to automate an email to notify an analyst of the alert, so I added an email app, and linked it to Virustotal so that it would send an email with information about the alert. There is certainly more information that could be added to the email for context, but in this example, I kept it short just for an example of the process.

Here is the input.
<img width="324" height="633" alt="Screenshot 2025-08-13 170859" src="https://github.com/user-attachments/assets/1a9f244d-b9a0-4fb0-88fb-f04211d149b8" />

And the output.

<img width="978" height="184" alt="Screenshot 2025-08-07 191215" src="https://github.com/user-attachments/assets/91649d5f-4788-43f8-a75d-b34bd522ee16" />


Now that I had this working, I wanted to try and apply this to real world situations. Thus far, I have been using an alert that I was generating locally. My plan is to create another Ubuntu VM, add it as a Wazuh agent, but use a different firewall that allows all incoming traffic on port 22 (SSH). So I created the new machine and named it Ubuntu agent, installed the Wazuh agent software and added it to the list of agents connected to Wazuh.

<img width="954" height="287" alt="Screenshot 2025-08-07 155644" src="https://github.com/user-attachments/assets/a6dd10d5-4741-43bf-a73e-4debaef7b87c" />

Next, I went back to shuffle, and I changed the workflow configuration, this is what it looks like complete, but I will walk through the steps. 

<img width="1049" height="800" alt="Screenshot 2025-08-07 194959" src="https://github.com/user-attachments/assets/9bc5b399-b895-4ad9-b705-593153aa451f" />

The first thing I did was replace the regex parse with the HTTP application, changed the name to Get-API and changed the action to curl. I am using this to utilize Wazuh's API capability but in order to do this I need to obtain a JWT (JSON web token). I also need to change my firewall to allow access to port 55000. The statement I used was <curl -u USER:PASSWORD -k -X Get "https://<WAZUH-IP>:55000/security/user/authenticate?raw=true"> and filled it in with the information from Wazuh API.

next I changed the Virustotal action t Get an IP address report and used and changed the IP field to use the IP.

<img width="330" height="772" alt="Screenshot 2025-08-07 195041" src="https://github.com/user-attachments/assets/0fdf08c5-1bbc-4320-b837-470f0e1badcb" />

Now I added the Wazuh application and set the action to run command and for the API key, I selected the Get-API exec.

<img width="327" height="765" alt="Screenshot 2025-08-07 195134" src="https://github.com/user-attachments/assets/3d4e89df-fbff-434b-a518-c84af72502b4" />

Next I needed to change a few things in the Wazuh manager's ossec.conf file. One of them was to change the <event_id> to <level> in the integration block, then change the level to 5 in order to obtain events that include denial
