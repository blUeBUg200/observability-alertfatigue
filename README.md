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

Alright, now that we have insights into what needs fixing, let's explore where and by whom this problem can be addressed. Before delving into that, let's discuss the origin of the alerts. You may have already guessed it. Hooray! It's none other than DATA.

## The Source

**Usecase:**
An analyst working in SOC operations, monitoring multiple customers from decentralized console trying to understand the group of customer names having technology X

**Setup:**
- Install neo4j browser or desktop version.
  I spinned up a docker instance of neo4j browser with host directory volumes(for data persistence). Docker compose file will look like below
  
````
version: '3.8'
services:
  neo4j:
   image: neo4j:latest
   hostname: neo4j
   ports:
      - '7474:7474'
      - '7687:7687'
    volumes:
      - /home/testuser/neo4j/data:/data
      - /home/testuser/neo4j/logs:/logs
      - /home/testuser/neo4j/import:/var/lib/neo4j/import
      - /home/testuser/neo4j/plugins:/plugins
    environment:
      - NEO4J_AUTH=neo4j/Snipper58
````
Web UI Login to neo4j console on port 7474 with username=neo4j ; password=Snipper58

- Build your technology vendor data, customer matrix as CSV. Pretty much you can retrive this data from any SIEM. My test data looks like below,


![data](https://user-images.githubusercontent.com/86832373/175833192-207253b3-401e-42f8-b695-549588b190c6.PNG)

Place this CSV file under "import" folder. In our case it will be "/home/testuser/neo4j/import"

- Build relationship between the nodes "Customer" and "Technology"

````
// Build Query From CSV. You can load the CSV from remote sites as well.

LOAD CSV WITH HEADERS FROM 'file:///testdata.csv' AS row with row where row.Customer is not null
MERGE (n:Technology {Name: row.Technology})
MERGE (m:Customer {Name: row.Customer})
MERGE (m) -[:CONTAINS]-> (n)

````
- The node relationship will be shown like below,

![relation_data - 3](https://user-images.githubusercontent.com/86832373/175832768-9af129d7-3f92-44fc-9b99-98b99ffa7cb0.PNG)

- Query the graph as required. In our case we have queried the DB to identify the list of customers having Juniper device.

````
// Query graph DB to match partial match criteria "juniper" anywhere in the text and return values
MATCH (c:Customer)-[:CONTAINS]->(t:Technology) 
WHERE t.Name =~ '(?i).*juniper.*'
RETURN c,t
````
![Juniper Customers - 2](https://user-images.githubusercontent.com/86832373/175832778-aebb90e2-9826-425f-8dad-e0950ea1d7ae.PNG)
