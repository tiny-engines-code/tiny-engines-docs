## Unit testing 

Effective unit testing starts with modular code design.  Of course that's much easier in a textbook example like this, but the really key functions should be able to define a simple set of inputs and the expected outputs for those inputs.  

For each "function" under test (This is usually a single method)

![](../../.gitbook/assets/sendgrid-personal-page-6-1-.png)

<p/><strong>What are the inputs to the function?</strong>

* List the input parameters that the unit can be called with
* List external inputs that the function depends on


<p/><strong>What are the expected results?</strong>

* For each set of inputs, what is the expected response?