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

Ok, now we have TheHive up and running!

<img width="1510" height="781" alt="Screenshot 2025-08-13 143308" src="https://github.com/user-attachments/assets/d0adb5bf-c93e-444c-aa38-26b3d0bb9c55" />
