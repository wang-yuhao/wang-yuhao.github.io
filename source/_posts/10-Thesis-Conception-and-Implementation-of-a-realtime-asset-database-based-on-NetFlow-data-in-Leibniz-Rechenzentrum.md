---
title: >-
  10-Master-Thesis:Conception-and-Implementation-of-a-realtime-asset-database-based-on-NetFlow-data-in-Leibniz-Rechenzentrum
date: 2020-04-19 15:25:54
tags:
---

(Draft)
<!--more-->
## 1   Introduction
#### 1.1   Motivation

The Leibniz Supercomputing Centre of the Bavarian Academy of Sciences and Humanities (LRZ) is the largest Supercomputing Center in Germany operates the Munich Scientific Network (MWN). In this network, including the Munich universities, many student dormitories, and some non-university facilities such as e.g., Bavarian State Library, museums, and the institutes of Max Planck Society and Fraunhofer Society.  A total of approximately 100,000 devices are connected. The MWN consists of a backbone network to which the networks of the facilities at the various locations are connected via routers and switches. The routers in the backbone area of the Cisco Nexus C7010 type. To connect buildings connected via DSL and smaller X-WiN locations, we operate some devices of the Cisco 1900 series (manufacturer information) and Cisco ISR 1100 (manufacturer information). For broadband connection of the computer clusters in the LRZ, a Leaf & Spine infrastructure (Leaf & Spine Design) based on Cisco Nexus 9364C (manufacturer information) is used. 

Hundred thousand scientific research and service equipment are connected per second in MWN. The efficiency, stability, and security of MWN are closely related to the daily teaching, researching, and work of every facility in the MWN. But the limitation of the update and modification for the massive data limit the competence of network management. On the other hand, the lack of an appropriate flow collector script and intuitive, reliable display web interface also restrict the efficiency of the work of the administrator. As an essential part of the program, the asset database has long been ignored by various flow collectors. Thus, the limitation of the asset database also raises the difficulty of network optimization. 

Botnets and other network attacks have always been a severe problem in the current network environment. The resulting network congestion, waste of network resources, and other network problems have undoubtedly caused substantial economic losses. With the rapid development of current machine learning and deep learning research, trying to use the latest data science technology to analyze the acquired flow data to predict future network usage, optimize the network structure and traffic, and explore the network potential reasonably and efficiently is also a significant challenge.

#### 1.2   Problem Statement 
To improve the efficiency, stability, and security of MWN by managing the massive flow data per second, design an appropriate real-time NetFlow data collection and analysis script and an intuitive, simple, reliable display interface. Traditional NetFlow collectors are designed in many different situations, primarily their complicated functionality, insufficient asset databases, and other unsatisfied requirements of MWN, making them unsuitable for MWN. In this case, an independently designed and developed system integrated with MWN is what is urgently required.

The detection against the Botnets, abnormal network usage, and other malicious cyberattacks in MWN is also possible in the future system automatic protection functionality. Besides, through the latest RNN or other state-of-the-art technology, analyze the steady flow data from MWN automatic generation to optimize the network structure. The utilization of the prediction functionality from the data science model also can improve the elasticity, maintainability, and expandability of the MWN. The administrator's global control and deployment ability of the entire network routing situation can be significantly increased.


#### Plan:
1. compute the network traffic, set a script to automaticly generate network situation data.
1. Comparsion with different data base engine
2. Plot the Trend graph of different data base engine write, search, and sort result when the data base once 1 million record increase.
3. Research use different search algotithm to optimize the efficiency.
4. Use RNN to build a model, train the model with currently data,predict the future network traffic situation.
5. Read more state-of-the-art data science technogy to handel the data.
6. Use RDD, redis


