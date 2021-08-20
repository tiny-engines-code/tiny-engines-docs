## Unit testing in general

When unit testing any blocking function like the Sendgrid controller.   Simply put -- given a `unit under test` what are all the ways it can be changed and what are expected outcomes when tose changes occur?



<p/><strong>For each "function" under test</strong>

* Usually a single method


<p/><strong>What are the inputs to the function?</strong>

* List the input parameters that the unit can be called with
* List external inputs that the function depends on


<p/><strong>What are the expected results?</strong>

* For each set of inputs, what is the expected response?



![](../../../.gitbook/assets/sendgrid-personal-page-6-1-.png)