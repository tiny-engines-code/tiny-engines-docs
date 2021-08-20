## Unit testing in general

When unit testing any blocking function like the Sendgrid controller.   Simply put -- given a `unit under test` what are all the ways it can be changed and what are expected outcomes when tose changes occur?



<p/><li><strong>What is the "unit" under test?</strong></li>
<div style="padding-left: 20px">

<li><em>Usually a single method</em></li>

</div>

<p/><li><strong>What are the `inputs` to the unit?</strong></li>

<div style="padding-left: 20px">
<ul>
<li><em>Identify the input parameters that the unit is called with</em></li>
<li><em>Identify any external input that the unit depends on</em></li></ul>
</div>

<p/><li><strong>What are the `outputs` from the unit?</strong></li>
<div style="padding-left: 20px">
<ul>
<li><em>Assert that we returned the appropriate response</em></li>
</ul>
</div>



![](../../../.gitbook/assets/sendgrid-personal-page-6-1-.png)