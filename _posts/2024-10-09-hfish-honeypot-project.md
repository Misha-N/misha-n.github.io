---
title: "HFish: 2 Weeks of Cloud Honeypot"
date: 2024-10-09 10:00:00 +0200
categories: [Cybersecurity, Honeypots]
tags: [honeypot, hfish, cloud security, cybersecurity, threat analysis]  # TAG names should always be lowercase
description: A two-week experiment using the HFish platform to analyze external network threats and attacker behavior through a variety of cloud-based honeypots.
---

# HFish: 2 Weeks of Cloud Honeypot 🕵️‍♂️

Over the last two weeks, I’ve been running a honeypot platform in the cloud (specifically, a small VM hosted in the US) to conduct small research on external network threats. A honeypot is a security tool designed to lure and detect attackers by mimicking a real, vulnerable system, allowing cybersecurity teams to monitor and analyze malicious activity without putting actual systems at risk. Since the platform captured quite a lot of statistics in two weeks, I decided to summarize them in a short article.

So let’s dive into the numbers and see what was detected! 

## The Honeypot Lineup 🎯

To maximize the chances of catching attackers in the act, I set up honeypot platform [HFish](https://github.com/hacklcx/HFish) with **20 honeypots** simulating commonly targeted services, such as:

- **High Interaction SSH**, **Telnet**, and **MySQL** Honeypots
- Standard **SSH**, **MySQL**, **Redis**, **FTP**, and more
- Niche systems like **ESXi**, **Tomcat**, **Hikvision Cameras**, and **Modbus**
- Even some **WordPress** and **phpMyAdmin** honeypots for those web-based attackers

Each honeypot was designed to look like a real, vulnerable system—perfect for baiting scans, attacks, and attempted exploits. And the attackers definitely didn’t disappoint.

## Key Statistics

### Overall Activity 💥

| **Metric**                   | **Count**       |
|------------------------------|-----------------|
| Unique IP addresses detected  | 25 000+         |
| Total service scans           | 430 000+         |
| Total attacks on services     | 185 000+        |

The numbers alone tell us that services on the internet don’t stay lonely for long.

### Attack Origins 🌍

One important aspect is that the honeypots were hosted on a **US-based cloud VM**, which likely influenced the data. Still, attackers from all over the world joined the fun.

| **Country**    | **Attacks**   |
|----------------|---------------|
| 🇮🇳 India      | 124,133       |
| 🇺🇸 USA        | 17,711        |
| 🇻🇳 Vietnam    | 7,404         |
| 🇷🇺 Russia     | 2,123         |
| 🇵🇰 Pakistan   | 2,031         |

India topped the list by a huge margin, contributing more than half of the detected attacks. The US and Vietnam were distant runners-up, but still showed a strong presence. India likely ranked first in attacks due to its large internet user base and the economic factors.

### Most Popular Honeypots 🎯

Some honeypots got more interest than others. The top 3 most interacted-with services were:

| **Honeypot Type**                    | **Number of attacks** |
|--------------------------------------|-----------------------|
| TCP Port Listening Honeypot          | 🔥 141353               |
| High Interaction SSH Honeypot        | 🔥 17994               |
| High Interaction Telnet Honeypot     | 🔥 8086               |

It’s no surprise that **SSH** and **Telnet** honeypots were heavily targeted—these services are prime candidates for brute force attacks and other exploit attempts. The **TCP Port Listening Honeypot** primarily simulated vulnerable services on ports such as TCP/135 (used for RPC services), TCP/139 (NetBIOS Session Service), TCP/445 (Microsoft-DS Active Directory and SMB), TCP/1433 (Microsoft SQL Server), and TCP/3389 (Remote Desktop Protocol), making it also an attractive target for attackers seeking to exploit known vulnerabilities associated with these commonly used protocols.

## Login Attempts: Attackers’ Favorite Credentials 🚪

In addition to service scans and attacks, the honeypots captured a whopping **12,000 login attempts**. Attackers tried a mix of generic usernames and passwords, with some rather predictable patterns:

### Top 10 Usernames 📛

| **Username**       | **Attempts** |
|--------------------|--------------|
| `root`             | 4,315        |
| `345gs5662d34`     | 888          |
| `ubuntu`           | 729          |
| `test`             | 699          |
| `user`             | 491          |
| `admin`            | 422          |
| `sysadmin`         | 214          |
| `steam`            | 204          |
| `postgres`         | 199          |
| `support`          | 195          |

### Top 10 Passwords 🔐

| **Password**       | **Attempts** |
|--------------------|--------------|
| `123456`           | 4,315        |
| `2233`             | 902          |
| `3245gs5662d34`    | 888          |
| `888`              | 700          |
| `123`              | 287          |
| `password`         | 263          |
| `admin`            | 194          |
| `P@ssw0rd`         | 186          |
| `root`             | 164          |
| `test`             | 157          |

Let’s just say attackers are sticking with the *classics*—**“root”** and **“123456”** still reign supreme in their playbook. If you’re using any of these credentials… it’s probably time for a change. What also caught my eye is username and password with values **“3245gs5662d34”** and **“345gs5662d34”**. Quick Google search suggests it might be the foreign equivalent of a phrase like "my password" entered on a non-English keyboard, or it could be a default password for certain devices.

## Sample Collection: What Did Attackers Upload? 💻

Along with login attempts and service scans, the honeypots captured over **1,000 files** uploaded by attackers trying to exploit various simulated vulnerabilities. Most of these files were **SSH keys** used by attackers to maintain remote access to SSH service and **shell scripts** designed (based on Virus Total and other CTI platforms) to do everything from crypto mining or establishing remote access. 

## Wrapping It Up 🎁

In conclusion, my experience with HFish has been a funny side project that provided valuable insights into the nature of external threats and some interesting data. 
While honeypots can usually be easily identified by experienced attackers, they can still serve as an excellent early warning system when an attacker doesn’t expect them. For example, deploying honeypots within an organization's internal network can significantly enhance its defenses—assuming the organization has some level of security maturity. If an attacker discovers a vulnerable honeypot during their reconnaissance, there's a high likelihood they will attempt to exploit it. This gives security teams a chance to detect the activity and react promptly.
