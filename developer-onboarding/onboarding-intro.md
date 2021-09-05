---
description: >-
  Software developers come from varied backgrounds. The purpose of this project
  is to create a baseline of the set of tools needed -- not just to write code, but to deliver it to a production environment
---

# Onboarding

Each of us has followed a different path in building our coding skills. Generally most java developers I have worked with have an understanding of the language itself.   They understand the while, if, switch statements and how classes, streams and functions work.
The purpose of this on-boarding is to walk-through the other things you need to know to deliver code into a production environment.  

In a production environment we are not just delivering software, but also deploying and running it.  That requires more than just understanding a language such as Java.  It requires understanding of the set of tools need to deliver that software.

**This is not a Java tutorial.** and it's not a tutorial for Junit, Mockito, or any other specific skill.
<p/>

If you are in an environment were you can just develop code and hand that code off to a QA engineer and then a Dev-ops engineer -- that can be wonderful for a developer -- but more and more companies are building and maintaining thier own software and some of those companies are coming to the conclusion that strict boundaries between design, coding, QA and dev-ops are less and less efficient, and the separation between those roles is becoming more blurred.   Even if they weren't, a good working understanding of those other disciplines while designing and developing makes for much better solutions.

The assumption is that the reader is already familiar with 
* Java programming
* Agile development

Today we are concerned with "other" skills that come into play while delivering Java software.
<p/>When designing a solution we need to be aware of


  - **Observability - how will we trace and monitor the solution**
    - monitoring - New Relic, SignalFX
    - metering - dropwizard, ...
    - a tracing solution - like Zipkin
  - **Microservice ambassadors**
    - discovery
    - circuit breaker
  - **Logging solution**
    - logging tools like log4j are well known
    - need a way to turn massive volume of logs into a central repository that can be queried and alerted
      - Splunk, Elasticsearch, Lucene
      - Log packaging Flume, Logstash
  - **Error handing standard**
  - **Configuration and secrets management**
  - **Quality control**
    - unit testing
    - integration testing
    - acceptance testing
    - performance testing
    - code quality such as lint, Jacoco, Sonarqube
  - **Continuous integration and delivery**
    - build tools such as Gitlab, Jenkins, Jenkins X
    - deployment tools such as Terraform, Packer
    - Production platforms such as AWS, GKE, or Azure
    - Soft platforms such as Kubernetes



