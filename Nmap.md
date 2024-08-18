# $${\color{red}Nmap}$$

![Pasted image 20240424134353](https://github.com/lm3nitro/Projects/assets/55665256/b3eb7242-99a3-4e22-a98b-3171e1c3b191)

Nmap is short for Network Mapper. It is an open-source Linux command-line tool that is used to scan IP addresses and ports in a network and to detect installed applications. Nmap allows network admins to find which devices are running on their network, discover open ports and services, and detect vulnerabilities. 

### Scope:

I will be installing namp and will use it against a Linux host to verify which ports and services are open. I will also being using vulcssn and namp-vulnerls script.

### Tools and Technology:
Nmap, Linux OS, and Wireshark

## Installing Nmap
To start, we will need to install Nmap. I will also need the nmap-vulners script, so we will also need to install git:

```
sudo apt install nmap
```

![Pasted image 20240423204014](https://github.com/lm3nitro/Projects/assets/55665256/d25eaf2b-533f-4f33-a954-d296bce08893)

```
sudo apt install git
```

![Pasted image 20240423204124](https://github.com/lm3nitro/Projects/assets/55665256/62ef5302-8ffc-4f23-803f-4746d51f44e1)

## Installing the nmap-vulners script

Before we can install the nmap-vulners, we'll first use cd to change into the Nmap scripts directory:

```
cd /usr/share/nmap/scripts/
```

![Pasted image 20240423204323](https://github.com/lm3nitro/Projects/assets/55665256/795318d0-9a32-4ca8-9076-e4ed62c81447)

We can now clone the nmap-vulners GitHub repository by using the command below. Once map-vulners is installed, there's no configuration required.

```
git clone https://github.com/vulnersCom/nmap-vulners.git
```

## Install vulscan

We'll also need to clone the GitHub repository into the Nmap scripts directory.   

```
git clone https://github.com/scipag/vulscan.git
```

![Pasted image 20240423205431](https://github.com/lm3nitro/Projects/assets/55665256/75510a87-0aa7-45aa-9ea3-7293c73b654d)

Vulscan utilizes preconfigured databases that are stored locally on our computer. We can view these databases in the root of the vulscan directory. Run the below ls command to list the available databases:

```
ls vulscan/*.csv
```

![Pasted image 20240423205508](https://github.com/lm3nitro/Projects/assets/55665256/5c220072-cdb2-4ae1-80bb-eaca3a183bdb)

Vulscan also supports a numbered of excellent exploit databases:

+ scipvuldb.csv
+ cve.csv
+ osvdb.csv
+ securityfocus.csv
+ securitytracker.csv
+ xforce.csv
+ expliotdb.csv
+ openvas.csv

>#### Note: To ensure that the databases are fully up to date, we can use the updateFiles.sh script found in the vulscan/utilities/updater/ directory. Change into the updater directory by typing the below command into a terminal.
```
cd vulscan/utilities/updater/
```
![Pasted image 20240423205809](https://github.com/lm3nitro/Projects/assets/55665256/d4848860-754a-48db-afa1-71ff0e9384c0)

Once we change into the directory, we can execute the update script:

```
./updateFiles.sh
```

![Pasted image 20240423210027](https://github.com/lm3nitro/Projects/assets/55665256/331b6d81-ead9-4160-870a-f4598f9d3ec9)

## Scan Using Nmap-Vulners:

The vulners script is in the "external" category because it sends CPE descriptions of discovered services to the vulners.com vulnerability database API and reports known CVEs in those services. It does not attempt to verify or exploit any vulnerabilities, and will not result in extra traffic to the target itself. The degree of confidence is dependent on the accuracy and precision of Nmap's version detection, which in turn may be confused by non-standard service banners or custom/patched builds.

To run the script, we can use the command below:

```
nmap --script nmap-vulners -sV
```
![Pasted image 20240423214801](https://github.com/lm3nitro/Projects/assets/55665256/3a74a217-6160-4ecb-b57c-f03c340f0e91)

>####Note:The -sV is **absolutely** necessary. With -sV, we're telling Nmap to probe the target address for version information. If Nmap doesn't produce version information, nmap-vulners won't have any data to query the Vulners database. Always use -sV when using these NSE scripts.

## Scan Using Vulscan:

The vulscan script is not included with Nmap. It does a similar lookup to the vulners script, but uses an offline copy of VulDB and some other databases. The same caveats apply, except that it does not send CPE information to an external service. To use vaulscan, we can use the command below.

```
nmap --script vulscan -sV
```

![Pasted image 20240423211318](https://github.com/lm3nitro/Projects/assets/55665256/e50a8101-9b00-4ba3-b149-40bfc5dcd3af)

>#### Note: By default, vulscan will query all of the previously mentioned databases at once. I highly recommend querying just one database at a time. We can achieve this by adding the vulscandb argument to our Nmap command and specifying a database as shown in the below examples.

Example:
+ nmap --script vulscan --script-args vulscandb=database_name -sV 
+ nmap --script vulscan --script-args vulscandb=scipvuldb.csv -sV 
+ nmap --script vulscan --script-args vulscandb=exploitdb.csv -sV
+ nmap --script vulscan --script-args vulscandb=securitytracker.csv -sV 

## Testing:

Now that we have everything installed and up do date, lets run nmap with vulscan against our target host and see what is is able to find. I will also have wireshark running the the background while I run the following comamand. This will allow me to view the traffic generated by the command. I will be using the following command:

```
sudo nmap --script vulscan --scripts-arg vulscandb=exploitdb.csv -sV 192.168.91.129
```
![Pasted image 20240423220604](https://github.com/lm3nitro/Projects/assets/55665256/c73dcf50-a71f-4691-8f20-6c8bb7e606ce)

Based on the output, we can see that the target host has some FTP ans SSH related vulnerabilities. Jumping over to Wireshark, lets view what it can tell us. 

FTP traffic :
![Pasted image 20240423220701](https://github.com/lm3nitro/Projects/assets/55665256/a8d71462-4e81-4abb-9368-2d3fb2fe831e)

SSH traffic :
![Pasted image 20240423220745](https://github.com/lm3nitro/Projects/assets/55665256/0695b161-0be2-4206-8dd6-500f0c7e6f46)

We can see the same information in Wireshark as in the CLI. 

## Combo:

Scripts significantly improve Nmap's versatilityand range as a security scanner. To get the most out of Nmap's version scans, we can use both nmap-vulners and vulscan in one command. 

```
nmap --script nmap-vulners,vulscan --script-args vulscandb=scipvuldb.csv -sV
```

![Pasted image 20240423215055](https://github.com/lm3nitro/Projects/assets/55665256/442f84f1-74b5-42e4-bde1-71e5fa47b44f)

Here we are able to see that VNC was detected on our host. 

## Summary:

Inconclusion, namp is a great tool that can be enhanced with other options such as scripts. Administrators and adversaries alike can use nmap for both network discovery and security auditing. Administrators utilize nmap to identify open ports, detect services, and uncover vulnerabilities that can be exploited. On the other hand, the adversary can use nmap to do reconaissance and identify weak points or flaws so that they can exploit it. Using Wireshark also provided another level or visbility and allowed me to undersand how this type of scan can be identified via traffic analysis. 