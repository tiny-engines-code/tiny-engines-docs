
At least once every few months some well-meaning individual who has some experience with coding will say to me:  

> I just really want to understand why xyz is taking 4 weeks - I remember coding something similar, and it did not take that long.  

And they are right -- because it's not coding logic that soaks time ... it's everything else  ... and becoming efficient at that "everything else" is where a team can gain efficiency and quality.

This is a continual work in progress that I've used more than I ever expected to, to illustrate one concept or another.  Its meant to be a refresher of some simple issues that engineers who have to both develop and maintain code need to wrestle with - and also a reminder for those of us who have forgotten: 

Other than "whiles" and "ifs" and "loops", we also need to realize:

  - **Observability - how we trace and monitor the solution**
    - monitoring - New Relic, SignalFX
    - metering - dropwizard, ...
    - tracing solution - like Zipkin
  - **Microservice ambassadors**
    - discovery
    - circuit breaker
  - **Logging solution**
    - logging tools like log4j are well known
    - need ways to turn massive volumes of logs into a central repository that can be queried and alerted
      - Splunk, Elasticsearch, Lucene
      - Log packaging Flume, Logstash
  - **Error handing standards**
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



