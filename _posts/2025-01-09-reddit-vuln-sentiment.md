---
title: "Analyzing the Buzz Around Cybersecurity Vulnerabilities"
date: 2025-01-09 18:00:00 +0200
categories: [Cybersecurity, Vulnerabilities]
tags: [CVE, vulnerability analysis, cybersecurity trends, Reddit, sentiment analysis]
description: Analyzing the most-discussed cybersecurity vulnerabilities on Reddit, with a focus on community engagement, sentiment analysis, and the factors that drive conversations around these vulnerabilities.

---

# Analyzing the Buzz Around Cybersecurity Vulnerabilities: What Gets the Community Talking?

Every day, dozens—sometimes hundreds—of new cybersecurity vulnerabilities are discovered. Yet, only a handful manage to capture the attention of the cybersecurity community. What makes some vulnerabilities stand out? Is it their severity, the impact on critical systems, or perhaps just the sheer absurdity of their exploit mechanisms? Out of curiosity (and a pinch of research enthusiasm), I decided to dive into the data to uncover which vulnerabilities have historically sparked the most discussion.

## The Search for Meaningful Data

Although there are paid services and established platforms that effectively track vulnerability trends, I was curious to see what insights I could uncover on my own. Platforms like Twitter (now X) or LinkedIn were obvious choices, but many community-driven projects using their APIs have struggled due to changes in free tiers or rising costs. Reddit, however, offers a viable alternative, with its API remaining free (for now), albeit with rate limitations. Bless them 🙏

To kick things off, I focused on vulnerabilities listed in the [CISA Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog), This catalog is maintained by the Cybersecurity and Infrastructure Security Agency (CISA) and includes vulnerabilities that are actively being exploited in the wild. It serves as a reference for critical vulnerabilities that have been identified as high priority due to their potential impact on systems and infrastructure. This seemed a logical starting point—if any vulnerabilities were going to ignite discussion, these would be it.

For data mining, I used [aPRAW](https://apraw.readthedocs.io/en/latest/), a Python wrapper library for Reddit's API, which allowed me to collect data on posts, comments, reactions, and other engagement metrics for specific CVEs. Admittedly, this wasn’t groundbreaking work, but it did yield some interesting insights.

### Results and Insights: The Most Discussed Vulnerabilities

When we look at the number of submissions across Reddit, the vulnerability with the most mentions was **CVE-2021-44228**, which appeared in **234 posts**. This vulnerability is better known as **Log4Shell** and it made headlines worldwide for its remote code execution (RCE) flaw in the widely-used Log4j logging library, which affected millions of devices.

Here's a brief overview of the top 5 most-discussed vulnerabilities by total submissions:

| **CVE**           | **Total Submissions** | **Description**                                                                                                                                           |
|-------------------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CVE-2021-44228** | 234                   | RCE vulnerability in Apache Log4j2, which allows attackers to remotely execute arbitrary code.  |
| **CVE-2020-2021** | 233                   | A PAN-OS vulnerability allows unauthenticated access to protected resources.                     |
| **CVE-2023-23397** | 119                   | Vulnerability in the Windows Microsoft Outlook client exploited by sending a specially crafted email.             |
| **CVE-2020-1472** | 117                   | Vulnerability that allows unauthenticated attackers to gain domain administrator access via a vulnerable Netlogon secure channel connection.                      |
| **CVE-2021-3156** | 115                   | A vulnerability in Unix Sudo feature that allows privilege escalation to root through a buffer overflow.                           |

When we shift our focus to the number of **reactions in the form of comments**, the ranking changes slightly. The vulnerability that generates the most engagement in comments is already mentioned **CVE-2020-2021** (PAN-OD authentication bypass), with a total of **3008 comments**. This suggests that while **CVE-2021-44228** (Log4Shell) had more submissions overall (by just one), **CVE-2020-2021** sparked deeper discussions among users.

Here’s how the list of top vulnerabilities looks when considering the total number of comments:

| **CVE**           | **Total Submissions** | **Total Comments** | **Description**                                                                                                                                           |
|-------------------|-----------------------|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CVE-2020-2021** | 233                   | 3008               | A PAN-OS vulnerability allows unauthenticated access to protected resources.                                       |
| **CVE-2021-44228** | 234                   | 2652               | RCE vulnerability in Apache Log4j2, which allows attackers to remotely execute arbitrary code.                           |
| **CVE-2023-23397** | 119                   | 1637               | Vulnerability in the Windows Microsoft Outlook client exploited by sending a specially crafted email.      |
| **CVE-2021-34527** | 80                    | 1611               | A vulnerability in the Windows Print Spooler service that allows remote code execution with SYSTEM privileges.                           |
| **CVE-2024-3400** | 99                    | 1508               | A command injection vulnerability in PAN-OS that allow remote code execution with root privileges.                         |

We can see new entrants in the top 5, CVE-2021-34527 and CVE-2024-3400, which had fewer total posts but generated notable discussions. This indicates a strong community interest in exploring and addressing these vulnerabilities.


## Beyond the Numbers: Analyzing How People Talk About Vulnerabilities

Crunching numbers is informative, but it’s also, let’s be honest, a little dull. To add depth, I wanted to analyze how people discuss vulnerabilities. Are they joking about them? Angry? Fearful? One way to tackle this question is through sentiment analysis, a process that evaluates the emotional tone of digital text.

For this, I turned to [VADER](https://github.com/cjhutto/vaderSentiment), not our friend from Star Wars series, but Valence Aware Dictionary and sEntiment Reasoner - a sentiment analysis tool designed for social media and short-form text. It calculates sentiment scores—positive, neutral, and negative—and produces a compound score that captures overall sentiment. However, cybersecurity discussions often involve specialized jargon, so I fine-tuned VADER’s default lexicon to better reflect the domain. For instance, terms like "RCE" or "exploit" were assigned more negative weight, while "patch" or "workaround" got a positive nudge.

## Results and Insights: Sentiment analysis

In addition to examining the number of posts and comments for each vulnerability, I also analyzed the average sentiment in posts and first level of comments. For each vulnerability, I computed the overall average sentiment, considering both the post and the comments.

For vulnerabilities that had at least 20 posts, the vulnerabilities with the most positive and negative sentiment were as follows:


#### Most Positive Sentiment

| CVE               | Average Post Sentiment | Average Comment Sentiment | Average Overall Sentiment | Description                                                                                   |
|-------------------|------------------------|---------------------------|---------------------------|-----------------------------------------------------------------------------------------------|
| **CVE-2013-3900**  | 0.33                   | 0.26                      | 0.30                      | A vulnerability in the WinVerifyTrust function that allows attackers to execute remote code by modifying signed executable files without invalidating the signature.           |
| **CVE-2021-36934** | 0.16                   | 0.29                      | 0.22                      | A vulnerability in overly permissive Access Control Lists (ACLs) on system files that allows attackers to execute code with SYSTEM privileges.      |
| **CVE-2019-1367**  | 0.26                   | 0.05                      | 0.15                      | A vulnerability in the Internet Explorer scripting engine that allows remote code execution by exploiting memory corruption when handling objects in memory. |

#### Most Negative Sentiment

| CVE               | Average Post Sentiment | Average Comment Sentiment | Average Overall Sentiment | Description                                                                                   |
|-------------------|------------------------|---------------------------|---------------------------|-----------------------------------------------------------------------------------------------|
| **CVE-2015-4852**  | -0.44                  | -0.31                     | -0.38                     | A vulnerability in Oracle WebLogic Server that allows remote command execution via crafted serialized Java objects in T3 protocol traffic. |
| **CVE-2017-0199**  | -0.42                  | -0.02                     | -0.22                     | A vulnerability in Microsoft Office and Windows that allows remote code execution through crafted documents.                  |
| **CVE-2017-11882** | -0.28                  | -0.12                     | -0.20                     | A vulnerability in Microsoft Office allows attackers to execute arbitrary code by exploiting improper memory handling. |

These sentiment scores provide a deeper understanding of how the community reacts to these vulnerabilities, with some vulnerabilities generating more positive discussions, particularly when solutions or workarounds were proposed. Meanwhile, others, like **CVE-2015-4852** and **CVE-2017-0199**, elicited more negative reactions, possibly due to their perceived severity or the challenges involved in mitigating them.

## Summary

While these findings are interesting, it’s worth mentioning the limitations. VADER, even with a fine-tuned lexicon, doesn’t perfectly capture the nuances of cybersecurity language or the community’s unique humor. Nonetheless, it provides a solid foundation for understanding how vulnerabilities are perceived and discussed. Additionally, only the first level of comments was analyzed, which may not fully capture the depth of community discussions. Expanding to additional data sources would provide a more comprehensive view, but as a starting point, Reddit offers a solid and insightful foundation.

For those interested, I’ve published the full statistics, including detailed sentiment scores and post analysis, at [this page](https://961203.xyz/stats/cve@reddit.html). I plan to update this dataset weekly, so stay tuned for fresh insights.

![Analysis data page](/assets/img/sentiment/sentiment.png){: w="700" h="400" }
_Webpage with full dataset_
