[Personal Website Home](https://chrislomeli.gatsbyjs.io/)

---

# Table of contents

* [Tiny Engines](README.md)

[Personal Website Home](https://chrislomeli.gatsbyjs.io/)

## Onboarding Project <a id="sandbox"></a>

---

* [motivation](developer-onboarding/onboarding-intro.md)
* [the project](developer-onboarding/the-project.md)
#### The mailer service
  * [intro](sendgrid-service/code/sendgrid-mailer-intro.md)
    * [setup](sendgrid-service/code/sendgrid-setup.md)
    * [walk-through](sendgrid-service/code/code-review.md)
    * [unit testing](sendgrid-service/testing/002-unit-test-intro.md)
    * [testing the controller](sendgrid-service/testing/unit-test-controller.md)
    * [testing the handler](sendgrid-service/testing/unit-test-handler.md)
    * [testing the mailer](sendgrid-service/testing/unit-test-mailer.md)
    * [integration testing](sendgrid-service/testing/003-integration-testing-overview.md)
    * [testing our service](sendgrid-service/testing/integration-testing.md)
    * [acceptance testing](sendgrid-service/testing/acceptance-testing-intro.md)
    * [an acceptance test](sendgrid-service/testing/acceptance-testing.md)
    * [documenting the API](sendgrid-service/documents/swagger.md)
    * [test coverage](sendgrid-service/testing/jacoco.md)
    * [performance testing](common/under_contstruction.md)
    * [mutation testing](common/under_contstruction.md)
    * [grammar checking](common/under_contstruction.md)

#### The event webhook
  * [intro](developer-onboarding/event-activity/001-event-activity-intro.md)
  * [design](developer-onboarding/event-activity/event_activity-design.md)
  * [setup](developer-onboarding/event-activity/event-activity-setup.md)
  * [walk-through](developer-onboarding/event-activity/event-activity-code.md)
  * [testing](common/under_contstruction.md)
  * [the kafka connector](developer-onboarding)



---

### Walk-throughs

* [spark streaming hld](walkthroughs/ses-transform/01-motivation.md)
  * [background](walkthroughs/ses-transform/02-background.md)
  * [architecture](walkthroughs/ses-transform/03-architecture.md)
  * [threat](walkthroughs/ses-transform/04-threat-model.md)
  * [project](walkthroughs/ses-transform/04-project.md)
  * [transform-design](walkthroughs/ses-transform/05-ses-transform-poc.md)
  * [transform-poc](walkthroughs/ses-transform/06-ses-transform-code.md)
  * [query-poc](walkthroughs/ses-transform/07-database_records.md)
* [kafka walkthroughs](kafka-walkthroughs/001-kafka-intro.md)
* [java futures](walkthroughs/java-futures/goals.md)
  * [async servers](walkthroughs/java-futures/async-server.md)
  * [async clients](walkthroughs/java-futures/simple-futures-client.md)
  * [async streams](walkthroughs/java-futures/async-stream.md)