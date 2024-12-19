# JobPhishing

## Introduction
The job market for cybersecurity professionals can feel like navigating a labyrinth, especially with sky-high expectations for entry-level roles. In a landscape where frustration with endless applications is common, it’s easy to fall prey to cleverly crafted phishing schemes that exploit the hopes of job seekers. This documentation highlights my experience with a suspicious email claiming to offer a job interview, the red flags I discovered, and the steps I took to determine that it was part of a phishing campaign.

## Discovery and Investigation
On Wednesday, 12/18/2024, at 5:26 PM, I received an email from Connor Redden about my Security Analyst application at Navagis Inc. The email states that they are excited to learn more about me and my qualifications, and would like to interview me for the position. They asked me to acknowledge this invitation to interview tomorrow (12/19/2024) at 10:00 AM.

Initially, I thought my luck had finally caught up, and I would be able to interview for a position, so I replied and said yes. After sending the reply at 7:58 PM, I proceeded to prepare for the interview by trying to find my original application. I searched through my inbox and found no other emails about Navagis or this Security Analyst position except for the one email from Connor. At this point, skepticism started to kick in, and it became my job to figure out if this was a real email or not.

First, I checked everywhere to make sure that I even applied to this position since the name sounded familiar. I looked at my Job App Excel sheet to see if I ever put down Navagis, and there was no presence of it. I followed up by looking at my applied jobs on LinkedIn, Dice, Indeed, and ZipRecruiter, aaaaaannddddddd zilcho. Nada. Absolutely nothing was found.

I then took note of the person who emailed me, Connor Redden. I decided to try to find a matching user on LinkedIn, and it came back with four results:

- Connor Redden | Airline Pilot | Canada 
- Connor Redden | Political Science Student | United States 
- Connor Redden | Time Management Analyst | United Kingdom 
- Connor Redden | Replenishment Associate | United Kingdom

![p1](https://github.com/user-attachments/assets/fcb32e07-2c86-4086-a801-89ed375e4c46)

Clearly, none of these Connors work with Navagis, nor do any of them have any experience in Tech / Technical Recruitment / Recruitment. No SHRM-CP certification here. To further validate these findings, I went to Navagis Inc.'s LinkedIn page and headed to the People section to see if Connor would come up, and he did not.

![p2](https://github.com/user-attachments/assets/63e21937-046d-4c45-93b2-3718b89b3761)

Since I was already on LinkedIn, I decided to check out the Jobs section on Navagis' page, and there were no jobs listed, which was another big red flag.

![p3](https://github.com/user-attachments/assets/e8e5d403-40e2-4e67-97f6-e2ef889b9291)

After seeing that our Connor doesn't exist on LinkedIn, I decided to check out Navagis Inc.'s official website to see what was listed under Careers to find out if the email used was fake or not. Lo and behold, the email shown on the site (contact@navagis.com) was a different domain than the one used in Connor's email (connorredden@navagiscareers.com).

![p4](https://github.com/user-attachments/assets/31945613-020b-4e39-9ce5-008b464855a6)

Not only that, when looking at their Careers section, the only positions available were in the Philippines and Japan.

![p5](https://github.com/user-attachments/assets/b22ee302-328c-47a0-ab27-13331bbf44bc)

After doing some basic OSINT to find these big discrepancies in the email, I decided to analyze the email header to further evaluate if this email was legitimate or not. Since I was using Outlook, I clicked the three dots, then selected View Source. From there, I copied and pasted the email header into Notepad for easier viewing.

First, I checked out the Return-Path to see if it matched the From Field.

- Return-Path: connorredden@navagiscareers.com
- From: Connor Redden <connorredden@navagiscareers.com>

After comparing the two, they both matched, showing some consistency.

In the email header, we can see that the sender's IP is 209.85.166.67.

![p4senderip](https://github.com/user-attachments/assets/144f22d8-1761-410b-bd7c-a2fe6b7076e0)

Knowing this, we can use a site like ip-lookup to find some DNS info about our sender.

![piplok](https://github.com/user-attachments/assets/004d95f3-b200-4677-bbcf-daa5ca55bdda)

As we can see, the A and MX records show that the host is google.com, confirming that the sender is using Gmail or Google SMTP servers. This matches the From address in the email header, which shows some consistency. All the name records are Generic Top Level Domains (GTLD), which can be an indicator of potential phishing.

Next, I decided to look at DKIM, SPF, and DMARC. These are authentication results that ensure the email is authentic.

- SPF (Sender Policy Framework): Ensures that the email came from an authorized server for the domain.
- DKIM (DomainKeys Identified Mail): Ensures the message integrity has not been tampered with.
- DMARC (Domain-based Message Authentication, Reporting, and Conformance): Combines SPF and DKIM to enforce sender policies.
  
By using Ctrl + F on Notepad, we can type in SPF, DKIM, and DMARC to easily find these authentication results to better determine the authenticity of the email.

![ar](https://github.com/user-attachments/assets/792500f7-0696-4424-9309-e0384dd58c34)

As we can see, the email had passed DKIM but had none for SPF and DMARC, which is extremely suspicious.

To confirm my phishing suspicions, I put the two domains into Who.is to check out the registration info.

![p9](https://github.com/user-attachments/assets/8bd836cd-5ca7-404e-a5d0-b062b0cbb443)
![p9 2](https://github.com/user-attachments/assets/3e31e8be-3ebe-441a-998f-98a58f5cc636)

After comparing the two WHOIS outputs, we can see that the legitimate navagis.com was registered on 07/25/2008 and is through FastDomain. However, our other domain, navagiscareers.com, was registered on 12/15/2024 and through NameSilo. This is big since we can easily see that the domain used in the email was made just three days ago. This is a huge red flag and a serious indicator of phishing.

Now, I know I mentioned GTLD servers before, and yes, they are both using GTLD servers. However, the long registration time with FastDomain suggests legitimate use.

Both of these domains had their registrar data under privacy protection services, both based in the US, with navagiscareers using PrivacyGuardian.org (Phoenix, AZ) and navagis using Perfect Privacy, LLC (Jacksonville, FL).

![p9 1](https://github.com/user-attachments/assets/8612482c-9b58-48fd-b22d-7620e3e4afbc)
![p9 3](https://github.com/user-attachments/assets/c3d6acee-9f57-4a3a-a2e3-3acdc6d10926)

As we saw before with our Whois results, and can confirm with running nslookup on both domains, we have two different nameservers.

![p7](https://github.com/user-attachments/assets/04aea75a-dd28-4d3e-ad10-37528ec5e190)
![p7 legitdnslookup](https://github.com/user-attachments/assets/27293ac8-af3f-46e5-bd61-c4b506c4d8ee)

By using IPLookup, we can take the IP of the nameserver for Navagis, and receive an IP based in the United States.

![piploookup](https://github.com/user-attachments/assets/c82d94e9-f4e3-46ff-aa17-b78da2c5763e)

However, if we take the IP received from navagiscareers, we get an IP based in Germany. This further layer of confusion only adds to our suspicion of this email.

![piplookupger](https://github.com/user-attachments/assets/4f73f47a-2575-4148-a57b-88d60bc7e829)

After performing our analysis, I can confidently claim this email as phishing. The best course of action is to not respond to this email any further, block the email connorredden@navagiscareers.com, and report this email as phishing. On Outlook, this can be done through the following:

- For blocking an email: Select the three dots, hover on block, then press 'Block Connor Redden'.

![p10block](https://github.com/user-attachments/assets/b39de67c-3c59-4dcd-9240-76399431b16c)

- To report an email as phishing: Select the three dots, hover over Report, then press 'Report Phishing'.

![p10phish](https://github.com/user-attachments/assets/55acbe81-85f1-4996-94e8-057c379d229a)

## Recommendation
Word of advice for anyone else falling victim to these emails:
- Remove your resume from anywhere that is public, such as job hunting sites like LinkedIn, ZipRecruiter, Dice, etc.
- If you receive an email after 5 PM about a job interview the next day at 10 AM, it’s probably suspicious.
- Don’t stress too much about the job hunt; build up your portfolio, refine your resume, and keep applying. :D!

## Conclusion
This investigation underscores the importance of staying vigilant during the job application process, especially in a competitive field like cybersecurity. Phishing emails prey on the desperation and urgency felt by job seekers, but by using basic OSINT techniques, cross-referencing information, and analyzing email headers, these scams can be exposed. As a cybersecurity professional, every incident is an opportunity to refine your skills and protect others from similar threats. Remember to remain cautious, educate yourself about phishing tactics, and never let frustration compromise your judgment. Keep applying, keep learning, and stay resilient!
