## Unit testing in general

When unit testing any blocking function like the Sendgrid controller.   Simply put -- given a `unit under test` what are all the ways it can be changed and what are expected outcomes when tose changes occur?


<ul>
<p/><li><strong>What is the `unit" under test?   This is usually a single work unit -- a method or a tightly dependent, small set of method.</strong></li>
<p/><li><strong>What are the `inputs` to the unit?</strong></li>
  <li>the input parameters that the unit is called with</li>
  <li>any external input that the unit depends on</li>
<p/><li><strong>What are the `outputs` from the unit?</strong></li>
</ul>



![](../../../.gitbook/assets/sendgrid-personal-page-6-1-.png)