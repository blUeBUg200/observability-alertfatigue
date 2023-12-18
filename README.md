# Observability-driven Solutions to Alleviate Analyst Alert Fatigue in Cybersecurity

## Audience
Whether you're a seasoned observability professional, a curious newcomer exploring the world of detection engineering, someone looking to operationalize MITRE ATT&CK, or even if you've accidentally stumbled upon this page, this is the place for you. This post will be helpful for below audience and definitely beyond that 😄

🔍 **Observability Enthusiasts**  
👨‍💻 **Security Analyst**  
🛠️ **Detection Engineering Aficionado**  
🤖 **MITRE ATT&CK Devotees**  
🤔 **Curious Learner**  
😜 **Link Wanderers**

## Usecase Briefing
In this blog, we will explore how the incorporation of an observability platform, coupled with a resilient detection engineering approach (utilizing sigma and the MITRE CTID SMAP framework), can enhance organizational work quality and alleviate the significant operational burden of analyst alert fatigue. The methodology elucidated in this article is derived from my personal experience, a comprehensive understanding of prevailing challenges, and the analysis of publicly available statistics concerning current Security Operations Center (SOC) challenges. It is important to note that this proposed approach is not the sole method for addressing the discussed problem; rather, its implementation can vary based on individual experiences, taking on diverse forms and structures.

## From Analysts Shoes
Forbes featured a survey (links for which are provided at the end of this post) conducted by Panther Labs on the state of SIEM, presenting statistics on the challenges encountered in Security Operations Centers (SOCs) with legacy SIEM systems. The 2021 and 2022 reports shed light on the noteworthy challenges faced by analysts, which are succinctly outlined below for your swift reference

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/f316df65-61cf-4d64-904f-802fe7933c8f)
> Credits : Pantherlabs Survey 2021

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/aa8268da-4476-44e2-b3f7-5e461f26f6b5)
> Credits : Pantherlabs Survey 2022

After examining the survey results and drawing insights from my interactions with analysts in my network, it is evident that the primary challenges highlighted by analysts include high false positives, an overwhelming alert volume, and insufficient context. As frontline responders in any SOC, analysts should be provided with high-quality alerts enriched with significant security context. This ensures their ability to swiftly identify malicious activities, eliminating the need for prolonged, fruitless searches.

Alright, now that we have insights into what needs fixing, let's explore where and by whom this problem can be addressed. Before delving into that, let's discuss the origin of the alerts. You may have already guessed it. Hooray! It's none other than **DATA**.

## The Source
Before delving further into the discussion about alert tuning, let's focus on data. In general, data manifests in three primary forms, categorized as logs, metrics, and traces (a combination of events). A visual representation of these forms is provided in the image below. Credits for this simplified explanation go to Cribl, an observability product company that will be discussed later in this post.

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/76ff400c-a3fa-4476-9ba4-ef836dc2a0fb)
> Credits : Cribl

So, what does the future hold for data? How might its expansion unfold as the number of internet users increases day by day? A brief exploration of this subject directed me to the Arcserve Data Attack Surface Report, offering insights into the potential volume of data we may encounter by 2030. A visual depiction of data growth from Arcserve is presented below for your quick reference

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/b4198b6c-4d94-494b-8553-c15e607ef238)
> Credits : Arcserve Data Attack Surface Report

Things are getting rather unsettling now, aren't they? We face significant challenges as analysts strive to identify true positives amidst hundreds of false positives. The projection of data growth underscores the necessity for greater control over alert quality and context enhancement. So, whom can you depend on for this, and where should you begin?

Perhaps you find yourself in a state of confusion at this point, don't you?

Search for Detection Engineers within your office; they are the individuals entrusted with shouldering this challenge. Don't have one? That's okay. Read through this blog and consider devising a plan to establish one.

## From A Detection Engineer's Kitchen

*For those who are new:* Detection Engineering, a formidable team at the forefront of cybersecurity, is charged with crafting and refining sophisticated detection mechanisms. Tasked with identifying and neutralizing threats, they meticulously design, implement, and optimize detection rules and protocols. Their expertise lies in translating threat intelligence into actionable defense strategies, ensuring organizations stay resilient in the face of evolving cyber threats.

Let's break down the Detection Engineering process by incorporating the SANS Detection Engineering Lifecycle.

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/d43b680d-c20f-42c9-82e6-acd461759add)
> Credits : SANS

Upon closer examination and a more profound comprehension of the framework, I've delineated the lifecycle into three primary segments: Intelligence, Operations, and Data Pipeline. A visual representation of this breakdown is provided below for your reference

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/de840a2b-b93c-45b3-bffc-612f1a6de4c9)

Examining the intricate diagram above reveals that the data pipeline contributes significantly, constituting 50% of the efficiency in running a detection engineering lifecycle.

Yet, statistical insights from Panther Labs underscore that current SIEM capabilities fall short in aiding Detection Engineers and SOCs to execute tasks integral to their daily routines. Key challenges include the "complexity of adding new data", "intricate solution structures", "a deficit in features and functionality", "limited product usability", "a lack of customization options", and "cost concerns (which will be delved into later in this post)". 

````
From the survery (Links at the bottom of this post):
Q: When it comes to your SIEM plans for the upcoming 12 - 24 months, what is most accurate?
Answers:
1. We are happy with our current vendor  
2. We are unhappy with our current vendor and evaluating  
3. We are unhappy with our current vendor and haven’t started evaluating.
````

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/384e7e1c-a58a-4d1d-8e52-60f9b44fff66)
> Credits : Pantherlabs Survey 2022 - Happy with exisiting SIEM

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/ee677aec-9db6-4a1c-bba2-0eec42403991)
> Credits : Pantherlabs Survey 2022 - Unhappy with exisiting SIEM

![image](https://github.com/blUeBUg200/observability-alertfatigue/assets/86832373/b9ce6caf-b919-40b2-9d19-b77262776fad)
> Credits : Pantherlabs Survey 2021

This leads us to contemplate the implementation of an independent data layer preceding the final destination tool, such as a SIEM. By adopting a distinct data pipeline, we can achieve streamlined data management, facilitating the enrichment process for detection engineers to effortlessly leverage high-quality data in the creation of use cases.










## For More Details
[Cribl](https://cribl.io/blog/logs-events-metrics-and-traces-oh-my/)  
[Sigma](https://sigmahq.io/)  
[Forbes](https://www.forbes.com/sites/forbestechcouncil/2022/12/07/five-predictions-for-the-future-of-threat-detection/?sh=ba7173b57896)  
[Arcserve Report](https://cybersecurityventures.com/wp-content/uploads/2020/12/ArcserveDataReport2020.pdf)  
[Panther Report 2021](https://panther.com/wp-content/uploads/2023/01/State-of-SIEM-2021.pdf)  
[Panther Report 2022](https://panther.com/wp-content/uploads/2023/01/Panther_SOSR_22.pdf)  
[CTID SMAP Framework](https://github.com/center-for-threat-informed-defense/sensor-mappings-to-attack)  
[Pollfish Polling Method](https://www.pollfish.com/methodology/)  
[SANS Detection Engineering](https://www.sans.org/blog/purple-teaming-threat-informed-detection-engineering/)  
[Orca Report on Alert Fatigue](https://orca.security/lp/2022-cloud-security-alert-fatigue-report/)  
[Cloud Transformation Report by Splunk](https://www.splunk.com/pdfs/analyst-reports/the-state-of-cloud-driven-transformation-hbr.pdf)  
