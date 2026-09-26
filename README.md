# DevHub

The first thing I did was conduct an nmap scan using the command below.
```
nmap -sCV -T4 10.129.245.216
```

<pre>
Nmap scan report for devhub.htb (10.129.103.240)
Host is up (0.18s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35782e790d8713052f538ee73c55b64c (ECDSA)
|_  256 dd568ebcdab8383e9acd0b74ee5385f8 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: DevHub - Internal Development Platform
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 28.42 seconds
</pre>

Which gave me port 22, and 80 open. Meaning the next step would be to conduct web enumeration. However, doing so gave me no results.
Including subdomain enumeraiton. So, the next step was the check the webpage manually, after adding ``10.129.245.216    devhub.htb`` to /etc/hosts.

![Image Alt](https://github.com/mfahdk/DevHub/tree/main/Screenshot/1.png)

Opening the webpage gives us some information, on the left-side it mentions an MCP Inspetcor instance is running port 6264. We will investigate that first.

![Image Alt](https://github.com/mfahdk/DevHub/tree/main/Screenshot/2.png)

Doing so, takes us to MCPJam v1.4.2 instance. Further reconnaisance shows us that this version is vulnerable to RCE, particularly the CVE is ``CVE-2026-23744``.

### CVE-2026-23744

CVE-2026-23744 is a critical remote code execution vulnerability affecting MCPJam Inspector versions 1.4.2 and earlier.

The /api/mcp/connect endpoint accepts a serverConfig containing command and args without authentication or adequate security checks. Since MCPJam Inspector binds to 0.0.0.0 by default, the endpoint can be remotely reached and a crafted HTTP request can trigger arbitrary command execution.

The vulnerability requires no authentication or user interaction and is classified as CWE-306 (Missing Authentication for Critical Function). The vulnerability was fixed in version 1.4.3.
