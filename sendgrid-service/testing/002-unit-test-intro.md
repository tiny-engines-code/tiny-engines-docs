
The SendGrid mailer API is pretty basic.  Let's first focus on unit testing those basic functions.   One approach to testing a "unit under test"  is to break out the input and outputs of the unit itself and any dependencies it has.  A well-designed function (or a very simple one like this) should have clear inputs outputs and dependencies.

![](../../.gitbook/assets/sendgrid-personal-page-6-1-.png)

For any module we should ask:
* [x] What does our function under test do?
* [x] What are the **possible inputs** to the function, and it's dependencies?
* [x] What are the **possible outputs**?
