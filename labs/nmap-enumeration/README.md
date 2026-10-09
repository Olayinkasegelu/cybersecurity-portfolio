Lab 1 – Network Enumeration with Nmap
Objective
Perform network discovery and service enumeration against a deliberately vulnerable Linux
target (Metasploitable2) to identify open ports, running services and version information —
the reconnaissance phase of a penetration test.
Environment
Attacker: Kali Linux (VirtualBox, host-only network)
Target: Metasploitable2 at 192.168.56.101 (host-only network)
Date: 2026-10-09
All testing performed on systems under my own control, within the law.
Tools Used
Nmap
Kali Linux
Metasploitable2 VM
Steps
Verify target reachability:
   ping -c 4 192.168.56.101
   Result: 4/4 replies, 0% packet loss — target is up.
Run service/version scan with default scripts:
   nmap -sV -sC 192.168.56.101
   Result: 1 host up, scanned in ~73 seconds.
Findings
19 open TCP ports observed (output partially scrolled). Key results:
Port
Service
Detail
53
domain
ISC BIND 9.4.2
80
http
Apache httpd 2.2.8 (Ubuntu) — title confirms "Metasploitable2 - Linux"
111
rpcbind
RPC services enumerated (nfs, mountd, nlockmgr)
139 / 445
netbios-ssn
Samba smbd 3.0.20-Debian
512–514
exec / login / shell
Legacy netkit-rsh services (cleartext)
1099
java-rmi
GNU Classpath grmiregistry
1524
bindshell
"Metasploitable root shell" — a root shell on the network
2049
nfs
NFS 2–4
2121
ftp
ProFTPD 1.3.1
3306
mysql
MySQL 5.0.51a-3ubuntu5
5432
postgresql
PostgreSQL 8.3.0–8.3.7
5900
vnc
VNC protocol 3.3
6667
irc
UnrealIRCd
8009
ajp13
Apache Jserv Protocol v1.3
8180
http
Apache Tomcat 5.5 (Coyote JSP engine 1.1)
Notable script output:
SMB security mode: message signing disabled ("dangerous, but default")
OS identification: Unix/Linux — metasploitable.localdomain
Lessons Learned
-sV (version detection) turns a bare port list into actionable intelligence — version
  numbers map directly to known CVEs.
-sC (default scripts) enriches the picture: certificate details, SMB security posture,
  database banners.
The single most critical finding is often not the most ports but the worst service:
  a root bindshell on 1524/tcp is game over on its own.
Attack surface thinking: every open port is a potential entry point; this host has
  far too many.
Recommendations / Mitigation
The 1524 bindshell must never exist on a real system — remove immediately.
Close or firewall every port that is not business-required.
Remove legacy cleartext services (rsh, rexec, rlogin); use SSH.
Upgrade end-of-life software: Samba 3.0.20, Apache 2.2.8, MySQL 5.0, PostgreSQL 8.3,
  Tomcat 5.5, UnrealIRCd.
Enable SMB message signing; enforce VNC authentication; segment the network.
Screenshots
screenshots/nmap-scan.png — full nmap -sV -sC output (to be added)
