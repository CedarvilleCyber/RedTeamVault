Reporting is the final and most important part of any penetration test. This is the product you are giving your customer, NOT the test itself. Unless you convey the critical information here, your test will be worthless. Some tips to help:
1. Tell a story with your findings. This is the information you were paid to gather, so do your best to make it interesting. 
2. Report as you go. Have team members document findings directly into the final report documents and then spend time later revising instead of writing. Additionally, this allows you to re-exploit to confirm or get data you failed to get on the way in, which is always easier. 
3. Template early. The earlier you have a workable report template, the less time you have to spend in the allotted reporting time. 

CPTC does not allow the information to be taken off-premises, but in another environment or for certification attempts, [[SysReptor]] could be of help.

# Titling
For CPTC, the report must bear the name and logo of the organization, and it must not bear the name or logo of Cedarville or any related moniker. It should be titled with team number only. Otherwise, use the name and branding of your penetration testing firm. 

# Table of Contents
Word will generate this for you if you do it right, but sometimes it takes some tweaking. This is a must-have, but is pretty easy to implement usually. 

# Usage Statement and Legal Points
Add a statement on appropriate viewing and usage, and the limits of who may see or use the report. Use the Traffic Light protocol described here: https://www.cisa.gov/news-events/news/traffic-light-protocol-tlp-definitions-and-usage to control dissemination in an industry-standard fashion. Legal disclaimers about the responsibilities of your team and the protections associated with it

# Executive Summary
At the beginning, put a 1-2 paragraph explanation to the C-suite of why their infrastructure needs fixing. This should comunicate a monetary cost to not fixing the infrastructure and explain how to do so from their office. Wide-reaching policy issues and financial decisions need to be here in a fashion a business exec can appreciate and understand. 

# Introduction
Explain the objectives, dates, and SOW of the test. This should communicate when you were there, what you where there to do, and what you were not there to do. RoEs and IP Scope can be included in brief form here, definitely not more verbose than the original scoping document. That document in actuality would appear alongside this document so referencing it should be sufficient. 

# Findings
A good rule of thumb is one finding per page, a table denoting what the vuln is, how serious it is, and where it is. No more than one finding should appear per page, but findings may take up more than one page in emergencies. Remediation steps should be in appendices, along with all full walkthroughs and demonstrations. A location or link should be provided in the Findings page. Screenshots should be abundant, and sufficient to reproduce the entire attack, but a maximum of one should appear on the Findings page for the vulnerability. 

# Appendices
This is the section heading to the organization's IT personnel for remediations. Appendices should contain the long-form explanations of each attack and attack path, methodologies, ATT&CK mapping (if applicable and time allows), OWASP number (if applicable and time allows), etc. All of the nitty gritty details should live here, and keep the main sections of the report clean and tidy. 

# Screenshots
Make all details legible, and add supporting notation as necessary. [[Greenshot]] is an excellent tool for this. 
- Do not leak information in screenshots, whether account details or your desktop. Redact PII and turn off the transparency. 
- Save your target company ink if possible. Use a light background for screenshots or invert if necessary. 
- Highlight important information so your eye is drawn to it instantly, and document ever step. A screenshot of a tool's output is not useful, especially since the tool is likely cheaper than you are. A little effort in screenshots goes a long way. 

> [!important] Report as you go! This makes it much easier to get screenshots you forgot about...
