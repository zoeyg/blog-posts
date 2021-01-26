# How to CPTC

A few weeks ago I had the opportunity to compete in the [Global Collegiate Penetration Testing Competition](https://globalcptc.org/) with a team of classmates
from City College of San Francisco. We had high hopes going into it, as we had [won the western regionals](https://twitter.com/nationalcptc/status/1320524078768377857) a couple months prior, beating out the previous year's national gold and bronze medal winners. But alas, we weren't able to pull off a top 3 placement. We were, however, told we were in the top third out of 15 teams, and went home(well, I guess technically none of us ever actually left home, COVID, ugh) with the ["Most Professional" award](https://twitter.com/nationalcptc/status/1348376842332479488)(not sure why most professional is in quotes though). Not bad for a community college that doesn't have the benefit of 4 years in which the more experienced team members are able to compete many times, and pass on their wisdom to the newer team members. After the competition, in an attempt to help close the gap, I drew up some documentation in an attempt to pass on some of the knowledge and wisdom that we had gained to future team members. Then I decided there may be others who might benefit from it, and thus, the document was adapted into this blog post.

# Preparing

This time last year, I had a lot of previous experience as a software engineer, but had never hacked a box in my life. Classes at CCSF will provide a great foundation in cybersecurity, but aren't going to allow you to compete with the big girls. Unless you're already an experienced pentester, how well you do is going to come down to how much time you're willing to put into preparation and practice.

As soon as you can, find a couple times during the week the team can practice, then be diligent about getting together when you're able. Something we didn't quite do as a team, that may have helped, is divide and conquer when it comes to practicing. It might be smart to split up studying in different areas and have people specialize(windows, linux, networking, web applications, privilege escalation, forensics, etc...), with some overlap for backup. Then again, the whole team practicing together on various boxes was a lot of fun, improved the team dynamic, and allowed for people to find their individual strengths and where they fit within the team. So maybe do some of both.

## What to Practice/Learn

As for what you should practice, the following information is an attempt at a brain dump encompassing all of the tools I've used and lessons I've learned that came in handy during the competition. It is by no means exhaustive. I also know a good deal more about linux than windows, and more about web applications than privilege escalation and binary exploitation, so this list may be skewed in those directions. Feel free to [let me know](https://twitter.com/53c0nd_2473) if there's something critical I've forgotten.

At some point it might be interesting to take all of the data from this and previous years, drop it into splunk, and use it to find the most commonly used tools. Maybe that's an option for a future project/blog post.

In addition to what's in this document, there will be new exploits that come out over time. Do your best to keep track of them and learn them. One of the best ways to do that is likely via twitter. Get an account if you don't already have one, and start following people in cybersecurity.

I've done my best to organize the tools into various categories, but there's probably quite a few tools that could be in multiple places.

### Remote Scanning/Enumeration

You'll need to know a lot of tools in order to discover services running on open ports, and then how to extract and enumerate information from them.  On windows you'll want to be able to enumerate smb shares, usernames via kerberos, information in ldap, and be able to search for specific, named vulnerabilities like BlueKeep, EternalBlue, Zerologon, etc…  In my experience, there has been less to enumerate for linux boxes, at least in terms of the number of services, as they're commonly running only SSH and a web server, though you might also encounter stuff like reverse proxies and database servers.

#### Tools/Resources

*   **[nmap](https://nmap.org/book/)** - Know nmap inside and out.  Which scans are fast, which are slow, and what ports are involved. Learn which vuln scripts nmap makes available and how to run them.
*   **[autorecon](https://github.com/Tib3rius/AutoRecon)** - Autorecon will automatically run a lot of scans for you depending on what it finds. You can also add your own scans to it.
*   **[gobuster](https://github.com/OJ/gobuster)** - There are alternatives here, but you'll want to be able to do content discovery on web servers/applications.  This could also fall under the brute-force category.
*   **enum4linux** - This can provide a lot of useful information on windows boxes and domain controllers if null sessions are enabled on the box.
*   **smbmap/smbclient** - Used for mapping windows samba shares and retrieving files from them
*   **ldapsearch** - For searching ldap on windows boxes
*   **[metasploit](https://www.metasploit.com/)** - There's a lot more than just scanning here, but metasploit has a lot scanners for specific vulnerabilities, particularly for unpatched windows boxes
*   **curl/wget** - These are command line tools for sending individual requests to web servers.  You'll be able to view headers which can aid in identifying the software that's running server side, as well as client side source code which can contain valuable information
*   **chrome/firefox devtools** - Another way to view http requests and the client side source code.  You can also use the network tab to [generate curl commands](https://developers.google.com/web/updates/2015/05/replay-a-network-request-in-curl)
*   **[burp suite](https://portswigger.net/burp)** - Web server proxy that records all requests and makes replaying requests easy, has a number of other nice tools
*   **[nikto](https://github.com/sullo/nikto)** - Runs lots of automated tests against web servers, searching for known vulnerabilities
*   **dig** - For querying dns, trying zone transfers
*   **davtest** - Check upload ability on IIS servers
*   **[wpscan](https://github.com/wpscanteam/wpscan)** - Scanner for wordpress installations

### Remote Exploitation

Once you've identified the available services and pulled as much information as you can, you're going to want to find and run remote exploits.  If you've got the name and version of the software, start looking for existing exploits/CVEs, check for default credentials, check for lockouts and start trying to brute-force(see other section). Google is your friend.

#### Tools/Resources

*   **[cvedetails.com](http://www.cvedetails.com)** - Database of CVEs organized by product and version, with details and links to exploits or detailed explanations
*   **[exploitdb](http://exploitdb.com)** - Database of various exploits
*   **[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)** - Great aggregation of payloads and cheat sheets for various types of application security exploits
*   **ftp** - Know how to do anonymous login and list and download files
*   **[impacket](https://github.com/SecureAuthCorp/impacket)** - This is a whole suite of tools used for various things against windows, but has some specific tools that can help with gaining a foothold
    *   **GetNPUsers.py** - Impacket script for asrep roasting
*   **mysql** - Command line tool for accessing mariadb/mysql databases
*   **redis-cli** - For interacting with redis, though netcat can also work as well
*   **[sqlmap](http://sqlmap.org/)** - Automatic sql injection and mapping
*   **[metasploit](https://www.metasploit.com/)** - Usually comes preinstalled on pentesting dists, has lots of exploits for known vulnerabilities, and has facilities for setting up listeners for remote shells
    *   **msfvenom** - Part of metasploit, used to create all types of payloads.  Particularly good for creating reverse shell binaries for windows
*   **[reverse shell cheat sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)** - Good set of reverse shells for use with RCE payloads
*   **nc(netcat)** - Commonly used to receive reverse shell connections
*   **socat** - Netcat on steroids, allows for port forwarding and proxying, can also fork new threads/processes for each incoming connection
*   **[reverse shell upgrade](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/)** - how to upgrade your reverse shell so it's not so frustrating to use

### Offline Enumeration/Reverse Engineering

In certain cases you may run across custom software that the CPTC team has written specifically for the competition. For instance, the Human Machine Interface GUI for the SCADA system was written in C# and decompiling the source revealed how it interacted with the web application backend, exposing vulnerable routes that weren’t otherwise obvious.  Looking at network captures can be another good, non-invasive way to determine what an application is doing.  Sam’s practical malware analysis course has a bunch of good [projects](https://bowneconsultingcontent.com/pub/PMA/pma221/pma221.htm) pertaining to windows tools.

#### Tools/Resources

*   **[wireshark](https://www.wireshark.org/)** - Tool for capturing and analyzing network communications
*   **[dotpeek](https://www.jetbrains.com/decompiler/)** - .Net decompiler and assembly browser.  Allows viewing the decompiled source of .net programs
*   **[ghidra](https://ghidra-sre.org/)** - The NSA’s disassembler, and has a debugger in early development
*   **strings** - dumps strings from a binary
*   **file** - looks up file types by magic numbers
*   **bintext** - windows GUI version of strings
*   **process monitor** - logs events like file and network access for windows processes
*   **process explorer** - basically a better version of task manager

### Brute-forcing/Cracking

You'll want to know how to brute-force a number of network protocols, as well as crack hashes when you run across them, or extract them from a database.  A lot of the time you'll also be trying to enumerate some kind of resource like usernames, or web server paths, and not just brute-forcing logins.  Possibly the most important protocols to know how to brute-force: kerberos, samba, windows RPC, windows remote management, ssh, remote desktop, and http(s) protocol web logins.  Various database protocols might be important as well.

#### Tools/Resources

*   **[hydra](https://github.com/vanhauser-thc/thc-hydra)** - Hydra will allow you to brute-force logins on all kinds of protocols.  Has lots of functionality and options but the documentation kinda sucks
*   **[nmap](https://nmap.org/)** - Nmap has some brute-forcing scripts available
*   **[hashcat](https://hashcat.net/wiki/)** - GPU cracking, try using this first, the docs are good
*   **[john](https://www.openwall.com/john/)** - john the ripper, sometimes hashcat can't crack some odd formats, like zip files or something
*   **wordlists** - most pentesting distributions have rockyou(/usr/share/wordlists) builtin, and [SecLists](https://github.com/danielmiessler/SecLists) has all kinds of good stuff.  However, for the sake of brevity during CPTC most passwords that will be cracked should be pretty simple. Also try transformations of known passwords, like password1 -> password2
*   **[kerbrute](https://github.com/ropnop/kerbrute)** - A tool to quickly bruteforce and enumerate valid Active Directory accounts through Kerberos Pre-Authentication
*   **[cewl](https://github.com/digininja/CeWL)** - custom wordlist generator, point it at the company's website or similar

### Local Enumeration/Privilege Escalation

Once you've got a lower privileged shell on a box, you'll want to enumerate the box and try and gain additional privileges.  There are various scripts available that will check some of the most common avenues.  They spit out a lot of information though, so getting used to using them and recognizing what's important can take some practice.  For windows you'll need to learn about various privileges and what they allow, as well as group membership in active directory.  Bloodhound can help find non-obvious paths to sets of permissions that allow privilege escalation.  For linux, suid/guid binaries are pretty important to know, as well as capabilities and how sudo works. You'll also probably wanna be familiar with the major linux vulnerabilities that allow privilege escalation.

#### Tools/Resources

*   **[bloodhound](https://github.com/BloodHoundAD/BloodHound)** - Combination of a GUI and a binary that runs on windows boxes to show non-obvious privilege escalation paths
*   **[winpeas and linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)** - Scripts/Binaries that run on linux and windows and search for common privilege escalation paths, as well as interesting files, file contents, services, etc…
*   **[hacktricks.xyz windows](https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation)** - checklist of things to check for windows privilege escalation, some of which winpeas searches for
*   **[hacktricks.xyz linux](https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist)** - checklist of things to check for linux privilege escalation, some of which linpeas searches for
*   **exfil/tools/http server** - You'll need a way to get files on and off the box and over http can be a good way if you've got a reverse shell or similar.  Python has a built-in [simple http server](https://docs.python.org/3/library/http.server.html#:~:text=python%20-m%20http.server%208000), but we [created our own in javascript](https://github.com/nationalcptc-teamtools/City-College-of-San-Francisco/blob/main/exfil-tools-server.js).  The readme in that repo [has some examples](https://github.com/nationalcptc-teamtools/City-College-of-San-Francisco) for how to use it.  If you set the server up with the [setup script](https://github.com/nationalcptc-teamtools/City-College-of-San-Francisco/blob/main/setup-kali.sh) it'll add a bunch of useful tools to the server directory.
*   **[gtfobins](https://gtfobins.github.io/)** - If you find a suid/guid/sudo binary, this is a cheat sheet for how to take advantage of it
*   **[pspy](https://github.com/DominicBreuker/pspy)** - takes advantage of the /proc filesystem to watch what's being run on the box.  Just use the statically linked 64 bit binary
*   **[PayloadsAllTheThings Linux PrivEsc](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)** - Cheat sheet for linux privesc
*   **[PayloadsAllTheThings Windows PrivEsc](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)** - Cheat sheet for windows privesc
*   **[static binaries](https://github.com/andrew-d/static-binaries)** - Sometimes the linux system tools you want are not on the remote box.  Most of the tools have a bunch of dependencies as well.  This is why you want statically compiled versions with all the dependencies in a single binary.
*   **[secretsdump.py](https://medium.com/@benichmt1/secretsdump-demystified-bfd0f933dd9b)** - For dumping various hashes that can be cracked or used with pass the hash, part of impacket
*   **[dsacls](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc771151(v=ws.11))** - "Displays and changes permissions (access control entries) in the access control list (ACL) of objects in Active Directory Domain Services"

### Remote Administration/Pivoting

In a lot of cases you’re not gonna have access to the desktop, only a terminal.  Once you have a foothold on a box you'll want to pivot from it, utilizing socks proxies and port forwarding, in order to find parts of the network that weren't previously accessible to you.

#### Tools/Resources

*   **[PayloadsAllTheThings cheat sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Network%20Pivoting%20Techniques.md)** - Good cheat sheet of pivoting options
*   **[chisel](https://github.com/jpillora/chisel)** - Tcp tunnel and socks proxy, self contained binaries for both linux and windows
*   **[proxychains](https://github.com/haad/proxychains)** - Allows you to use commands on your attack box and forward the connections via a socks proxy through a compromised box
*   **[socat](https://book.hacktricks.xyz/tunneling-and-port-forwarding#socat)** - Good for forwarding single ports
*   **[ssh](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Network%20Pivoting%20Techniques.md#ssh)** - If you have ssh access you can setup a socks proxy, local port forwarding, and remote port forwarding
*   **[rpcclient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)** - Tool for executing client side MS-RPC functions
*   **[evil-winrm](https://github.com/Hackplayers/evil-winrm)** - Good client for interfacing with windows remote management.  You’ll need credentials or a hash to connect. Easy uploading and downloading of files, as well as the ability to automatically make powershell scripts and binaries available.

### Windows

My knowledge here is admittedly lacking, but you should have a basic understanding of the windows directory structure, access control in active directory, and how to navigate the system via the command line in powershell.

#### Commands

*   type - windows version of cat
*   dir /adh - list hidden files
*   redirects > and | piping, tee
*   find some cheat sheets
*   whoami /priv or /group
*   ipconfig /all

#### Important Files/Directories

*   C:\Users\username\ - user home directories
*   C:\Users\username\AppData - user application data
*   C:\Windows\System32\Config\ - contains various files with registry data
*   [https://book.hacktricks.xyz/pentesting-web/file-inclusion#windows](https://book.hacktricks.xyz/pentesting-web/file-inclusion#windows) - list of files to check when you've got a local file inclusion exploit

### Linux

You should have a basic understanding of the linux directory structure, permissions, and how to navigate the system via the command line.

#### Commands

*   pipes |, &lt;, >, and >> redirection, stdin, stdout, stderr
*   cat
*   base64 -d - decode base64 on the command line
*   pwd - get current directory
*   `` `command` `` $(substitution) - also useful for command injection at times
*   export ENV_VARS=value
*   difference between ' and "
*   ifconfig - network interface information
*   uname -a - os/kernel info
*   lsb_release -a - dist info
*   ss -pant - list processes with open ports
*   ip a - show current ip addresses
*   id/whoami - find out which account you're using
*   find - find files
*   grep - search inside files
*   sed - replace contents in files
*   tmux - allows you to easily turn one terminal window into multiple, as well as detach from a session and reconnect
*   tee - both redirect to file and stdout
*   sudo

#### Important Files/Directories

*   /etc/passwd - list of all users
*   /etc/shadow - hashed passwords
*   /etc/group - group memberships
*   /etc/crontab - cron jobs
*   /etc/sudoers - sudo configuration
*   /opt - user installed software
*   /var - logs, packages and databases, mail, print queues, some temp files
*   /var/www/html - common root directory for web servers
*   /etc - various configs for services like apache or nginx
*   /proc/self/cmdline - good for use with LFI to figure out what the server is running
*   /home/user - home directories for users, not always properly locked down
*   ~/.bash_history - command history
*   ~/.ssh/id_rsa - private key, may allow ssh access to other machines
*   [https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi-linux-list](https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi-linux-list) - list of files to check when you've got a local file inclusion exploit

### Best Practices/Hardening

Learn best practices and hardening for a corporate network and windows active directory setup.  Separation of concerns into subnets, separated by firewalls, using encrypted transports, certificate authorities and signing, up to date hashing and encryption algorithms, multi-factor authentication, enforced password strength policy, incremental lockout to prevent brute-forcing, logging/monitoring/splunk, IDS/IPS, etc...

### Coding

Knowing how to code in at least one language will be immensely helpful. Python would likely be a good choice as you'll need to be able to understand and run exploits you find, and they're commonly in python. Virtual environments and a familiarity with pip would likely be rather helpful as well. Bash and powershell scripting can be useful. Knowing some basic php and javascript can be helpful for understanding web applications as well.

### Industry Specific Regulation

CPTC generally has a theme/industry, and you're going to want to research what industry specific regulations the company is going to care about. As an example, this year the competition was pentesting a power company. We spent a fair amount of time researching the NERC CIP regulations and how certain common issues might violate certain regulations. Having this information in your report will really drive home the business impact. Looking up what businesses have been fined for violations might be helpful as well when it comes to explaining business impact.

## How to Practice

HackTheBox, Vulnhub, and TryHackMe are some great options. [CTFs](https://ctftime.org/) can be good for learning how to exploit web applications.  We predominantly used HackTheBox for practice, but early on did some TryHackMe as it has more introductory content, though that seems to be changing. Checkout the tracks on HackTheBox as they divide up some of the boxes into topics for learning. The Active Directory track on HTB was rather helpful in regards to learning some windows pentesting.  The Dante Pro Lab was helpful as well, and is a good approximation of CPTC, though is actually probably harder in a number of respects since I don't think it's designed to be finished in one or two 8 hour sessions.  If you can get through the entirety of that Pro Lab you're probably gonna be pretty well prepared. If you ever get stuck on boxes, there are options for hints. For a lot of the retired boxes, there are various writeups available.  For active boxes, checkout the HackTheBox discord, and be prepared to list what you've already tried while you ask nicely for a nudge.

As you're going through different boxes, **make sure to keep notes as you go through them**.  If you're unable to document and explain how you were able to compromise something it won't really matter that you were able to get in.  It's obviously not required for HackTheBox, but it's also good to think about steps to mitigate the vulnerability.  What could be changed to prevent it and harden/secure the application/service/box. You can also draw from your notes later to create cheat sheets for different tools. As an aside, you could also turn these writeups into blog posts and post them to your personal blog/site. Potential employers will then know you have experience with certain things 😉.

## Have a Plan

No plan survives contact with the enemy, but at least know what you’re going to do for the first half hour or so.  Setup your jump boxes, run [install scripts](https://github.com/nationalcptc-teamtools/City-College-of-San-Francisco/) or grab other tools you need. You'll also want to have a cheat sheet of scans that you're going to run initially.

## OSINT

There’s usually a public website for the company, LinkedIn profiles, Github, etc…  Make sure to extract as much information from them as you possibly can.  Generate some wordlists from their site with cewl. Learn about what's important to them. Generate a potential user list based on employees' names.  Follow employees or repositories on sites like github to get notifications if anything changes.

# The Competition

## Pentesting

Make sure to have an initial plan of how to proceed. You're probably going to want to install some things on the boxes they provide for you. Docker is pretty useful in that you don't have to worry about dependencies or configuration. There's going to be a set of scans you'll want to run regardless of the setup so have those ready to go.

Periodically during the competition you're probably going to want to take a step back and discuss current progress as a team and what everyone is working on/has found. It probably can't hurt to have the team lead or person with the most experience decide which rabbit holes are worth going down. Play to your individual strengths and communicate about who is doing what. Take short breaks to eat and clear your head, particularly if you've been working for a while and not making much progress.

When it comes to CPTC, it's easier to ask permission than forgiveness. If you're worried you might break something that's out of scope, cause a natural disaster, lock someone out of an account, or reset someone's password, be sure to message the company's team and ask if you can do it first. You'll get points off if you don't. If you do break something, own up to it as quickly as possible.  If you're asked if you broke something, definitely don't lie about it, and if you're not sure, say so and that you need a bit of time to converse with the team and look into details.  If you find something that's of critical importance and should be fixed immediately, this is another good opportunity to message the company rather than wait and put it in the findings.

Keep detailed notes as you go and document as much as possible with screenshots or saved text files.  You'll need it for the report, and the more detailed and ready your notes are to drop into the final document, the more time you'll have to optimize/improve the report. Make sure the screenshots are clear and uncluttered. Having a proof of concept where you show RCE is better than just an nmap scan that says it should be possible.  We used google docs during the competition as that's what was provided, but something like running an instance of [CTFNote](https://github.com/TFNS/CTFNote) on one of the jump boxes and having everyone access it might be a good idea. Maybe try it out during practice. Dockerizing it might be the best way to do it. There may be other alternatives as well. Be sure to keep track of even low impact smaller things like an http server not having X-Frame-Options or a Content Security Policy, as they are worth something.  Having something pre-written for these kinds of low priority findings might be free points.

## Injects

Be sure to do all these and that you send them where they're supposed to go. Designate a couple people to do the inject. This will mostly require doing some research and then synthesizing it into something during the time allotted. This may be different when in person though, we were all stuck at home during quarantine so it was all written reports.

## The Report

There are [some examples from 2019](https://github.com/nationalcptc/report_examples), and the 2020 examples will likely be released soon enough, but looking at  previous reports and replicating their format is likely a good option. The organizers also wrote a [letter to competitors](https://github.com/nationalcptc/report_examples/blob/master/2019/letter%20to%20competitors.pdf) in 2019 with a list of dos and don’ts for report writing.  Having a template ready to go that you can drop your content into will save you a lot of time.

Our kill chain diagram is something that [was really well received](http://lockboxx.blogspot.com/2021/01/cptc-2020-finals-review.html), and wasn't done by any other team. It's basically a graphical flow chart that summarizes the pentest and the techniques that were used to jump from box to box. Given the blog post, this might show up in other teams' reports in the future.

Make sure the reproduction steps are detailed enough for an engineer or security employee to easily replicate the finding.  Be sure to include even low impact findings that might show up in scans, as they're worth points, but prioritize getting more critical findings into the report if you've got limited time.

## Presentation

Everyone has to speak, and you've gotta fit the presentation into basically 7 minutes as you need to _be sure to leave 30% of your time for questions_.  Had we been to nationals before, we might have known this, but had no idea this was a requirement until the debrief, after everything was over, when they told us we lost points for it. You basically want to leave time for the board _to get in at least two questions_.

Don't get too overly technical, but do your best to tie in your findings to an overall, bigger picture for the execs. Be sure to talk about _overall security posture_ and specifics about _how to move forward_. Cater to the audience; learn about the specific industry and what they're worried about or should be worried about.

A couple general points made during our debrief that were given as positives, but don't have an effect on the score directly:

*   Have an initial summary slide so if people end up having to leave in the middle of your presentation they can
*   Have a conclusion slide so you can skip forward if you're running short on time

# After/Between Competitions

If you manage to make it to globals, you’ll have a decent idea about what’s going to be in the environment and can focus your practice toward learning about those specific things. If there was any open source software in use, you can actually take time to audit it in between competitions. Maybe you’ll get lucky and find a 0day. If you were unable to exploit something during the competition, take some additional time to lookup potential exploits, misconfigurations, and possible mitigations. Check hackerone and other bug bounty sites for recent reports on software that you know is a part of their system. Do OSINT again to check and see if anything has changed.

# Have Fun and Good Luck!

Most of all, have a good time and learn something. That’s what it’s all about anyways, and be sure to share your hard earned wisdom with the teams that will come after you.

_January 2020_