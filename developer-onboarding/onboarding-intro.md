---
description: >-
  Software developers come from varied backgrounds. The purpose of this project
  is to create a baseline of the set of tools needed -- not just to write code, but to deliver it to a production environment
---

##Onboarding

In many 'business' environments we deliver software, but we also deploy, scale and maintain it.  That requires more than just understanding of any given language such as Java.  It requires understanding of the set of tools need to deliver that software.  In those environments an effective team needs a common understanding of supporting disciplines while designing and developing makes for much better solutions.

**This is not a Java tutorial.** and it's not a tutorial for Junit, Mockito, or any other specific skill.  This is not about `if` or `while` or switch statements, or `parallel` or `concurrent` programming techniques.  Over the next few days we'll touch briefly -- not on the coding essentials -- but on the supporting technologies that are critical to deploying and maintaining code.

### Motivation
At east once a month some well-meaning stakeholder who has some experience with coding will say to me:  

> I just really want to understand why it's taking 4 weeks - I remember coding something similar in college, and it did not take that long.  

And they are often right -- because often it's not coding logic that soaks up the time ... it's everything else  ... and becoming efficient at that "everything else" is where a team can often gain efficiency and quality.

For many of you this should be common knowledge or just a review.  But it's still "ok" to take a little time to sync with one another on how we approach subjects such as: 

  - **Observability - how we trace and monitor the solution**
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
    - mutation testing
    - chaos 
  - **Continuous integration and delivery**
    - build tools such as Gitlab, Jenkins, Jenkins X
    - deployment tools such as Terraform, Packer
    - Production platforms such as AWS, GKE, or Azure
    - Soft platforms such as Kubernetes



