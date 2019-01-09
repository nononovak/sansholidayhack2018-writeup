# Objectives

## 1) Orientation Challenge

Difficulty: 1/5

What phrase is revealed when you answer all of the [questions](https://www.holidayhackchallenge.com/2018/challenges/osint_challenge_windows.html) at the KringleCon Holiday Hack History kiosk inside the castle? For hints on achieving this objective, please visit Bushy Evergreen and help him with the Essential Editor Skills Cranberry Pi terminal challenge.

### Solution

The answers to each of these questions can be found by referencing the [landing pages for past challenges](https://www.holidayhackchallenge.com/past-challenges/).

**Question 1**: In 2015, the Dosis siblings asked for help understanding what piece of their "Gnome in Your Home" toy?

Answers: **firmware**, clothing, wireless adapter, flux capacitor

**Question 2**: In 2015, the Dosis siblings disassembled the conspiracy dreamt up by which corporation?

Answers: Elgnirk, **ATNAS**, GIYH, Savvy Inc

**Question 3**: In 2016, participants were sent off on a problem-solving quest based on what artifact that Santa left?

Answers: Tom Tom Drums, DNA on a mug of milk, Cookie crumbs, **Business Card**

**Question 4**: In 2016, Linux terminals at the North Pole could be accessed with what kind of computer?

Answers: Snozberry Pi, Blueberry Pi, **Cranberry Pi**, Elderberry Pi

**Question 5**: In 2017, the North Pole was being bombarded by giant objects. What were they?

Answers: TCP Packets, **Snowballs**, Misfit toys, Candy canes

**Question 6**: In 2017, Sam the snowman needed help reassembling pages torn from what?

Answers: The bash man page, Scrooges Payroll ledger, System swap space, **The Great Book**

## 2) Directory Browsing

Difficulty: 1/5

Who submitted (First Last) the rejected talk titled Data Loss for Rainbow Teams: A Path in the Darkness? [Please analyze the CFP site to find out](https://cfp.kringlecastle.com/). For hints on achieving this objective, please visit Minty Candycane and help her with the The Name Game Cranberry Pi terminal challenge.

### Solution

Navigating around the site will eventually get you to the `/cfp/cfp.html` page. Removing the trailing page, and navigating to the [https://cfp.kringlecastle.com/cfp/rejected-talks.csv](https://cfp.kringlecastle.com/cfp/rejected-talks.csv) will give you the answer.

## 3) de Bruijn Sequences

Difficulty: 1/5

When you break into the speaker unpreparedness room, what does Morcel Nougat say? For hints on achieving this objective, please visit Tangle Coalbox and help him with Lethal ForensicELFication Cranberry Pi terminal challenge.

### Solution

The door passcode can be found [here at https://doorpasscoden.kringlecastle.com/](https://doorpasscoden.kringlecastle.com/). As the title hints, we can create a de Bruijn sequence for k=4, n=4 at [http://www.hakank.org/comb/debruijn.cgi?k=4&n=4&submit=Ok](http://www.hakank.org/comb/debruijn.cgi?k=4&n=4&submit=Ok). By starting to type this into the door passcode we eventually find the answer triangle-square-circle-triangle.

## 4) Data Repo Analysis

Difficulty: 2/5

Retrieve the encrypted ZIP file from the [North Pole Git repository](https://git.kringlecastle.com/Upatree/santas_castle_automation). What is the password to open this file? For hints on achieving this objective, please visit Wunorse Openslae and help him with Stall Mucking Report Cranberry Pi terminal challenge.

### Solution

Cloning the git repo linked to in the challenge shows that this is a similar challenge to the terminal one. I like to use the graphical `gitk` program so I opened that up and scrolled down until I found the commit with the password. Using that password to decrypt `schematics/ventilation_diagram.zip` gives two images for the vents found in the Kringlecon arena.

![gitk](./images/gitrepo_gitk.png)

```
$ 7z x schematics/ventilation_diagram.zip

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=utf8,Utf16=on,HugeFiles=on,64 bits,12 CPUs x64)

Scanning the drive for archives:
1 file, 740409 bytes (724 KiB)

Extracting archive: schematics/ventilation_diagram.zip
--
Path = schematics/ventilation_diagram.zip
Type = zip
Physical Size = 740409

    
Enter password (will not be echoed):
Everything is Ok                                       

Folders: 1
Files: 2
Size:       837190
Compressed: 740409
$ ll ventilation_diagram/
total 1720
-rw-r--r--  1 jnovak  staff  421604 Dec  7 13:37 ventilation_diagram_1F.jpg
-rw-r--r--  1 jnovak  staff  415586 Dec  7 13:37 ventilation_diagram_2F.jpg
```

## 5) AD Privilege Discovery

Difficulty: 3/5

Using the data set contained in this [SANS Slingshot Linux image](https://download.holidayhackchallenge.com/HHC2018-DomainHack_2018-12-19.ova), find a reliable path from a Kerberoastable user to the Domain Admins group. What’s the user’s logon name? Remember to avoid RDP as a control path as it depends on separate local privilege escalation flaws. For hints on achieving this objective, please visit Holly Evergreen and help her with the CURLing Master Cranberry Pi terminal challenge.

### Solution

I wasn't really familiar with bloodhound, but this challenge was fairly straightforward. Downloading the VM, opening it up and running bloodhound we're exposed to several features in the GUI. Eventually navigating the "Queries" tab and selecting "Shortest Paths to Domain Admins from Kerberoastable Users" runs a query that should hold our answer (given the verbose question above). Looking at the graphical output, we see exactly one path that doesn't include RDP shown below.

![Bloodhound Output](./images/adprivilege_bloodhound.png)

## 6) Badge Manipulation

Difficulty: 3/5

Bypass the authentication mechanism associated with the room near Pepper Minstix. [A sample employee badge is available](https://www.holidayhackchallenge.com/2018/challenges/alabaster_badge.jpg). What is the access control number revealed by the door authentication panel? For hints on achieving this objective, please visit Pepper Minstix and help her with the Yule Log Analysis Cranberry Pi terminal challenge.













## 7) HR Incident Response

Difficulty: 4/5

Santa uses an Elf Resources website to look for talented information security professionals. [Gain access to the website](https://careers.kringlecastle.com/) and fetch the document C:\candidate_evaluation.docx. Which terrorist organization is secretly supported by the job applicant whose name begins with "K." For hints on achieving this objective, please visit Sparkle Redberry and help her with the Dev Ops Fail Cranberry Pi terminal challenge.











## 8) Network Traffic Forensics

Difficulty: 4/5

Santa has introduced a web-based packet capture and analysis tool at https://packalyzer.kringlecastle.com to support the elves and their information security work. Using the system, access and decrypt HTTP/2 network activity. What is the name of the song described in the document sent from Holly Evergreen to Alabaster Snowball? For hints on achieving this objective, please visit SugarPlum Mary and help her with the Python Escape from LA Cranberry Pi terminal challenge.












## 9) Ransomware Recovery

Alabaster Snowball is in dire need of your help. Santa's file server has been hit with malware. Help Alabaster Snowball deal with the malware on Santa's server by completing several tasks. For hints on achieving this objective, please visit Shinny Upatree and help him with the Sleigh Bell Lottery Cranberry Pi terminal challenge.

### Catch the Malware

Difficulty: 3/5

Assist Alabaster by building a Snort filter to identify the malware plaguing Santa's Castle.

### Identify the Domain

Difficulty: 5/5

Using the Word docm file, identify the domain name that the malware communicates with.

### Stop the Malware

Difficulty: 3/5

Identify a way to stop the malware in its tracks!

### Recover Alabaster's Password

Difficulty: 5/5

Recover Alabaster's password as found in the the encrypted password vault.

















## 10) Who Is Behind It All?

Difficulty: 1/5

Who was the mastermind behind the whole KringleCon plan? And, in your [emailed answers](SANSHolidayHackChallenge@counterhack.com) please explain that plan.















