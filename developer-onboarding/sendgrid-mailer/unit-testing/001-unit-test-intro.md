## Premise

The prerequisite here is understanding of and experience with unit testing in general

The focus of the unit test is to put small component of code \(the unit\) under test.  The value is that each unit of code has its own contract with the rest of the code base.

Over time the definition if unit testing in business settings has not changed, but its usage and value has blurred a bit.  Originally the  idea was that we would build highly reusable components that could be re-usable anywhere in the code.  And unit testing was the key instrument that could guarantee those min-contracts

Honestly in many business environments the time and organization to make that happen has never really been a top priority.  It takes a lot of time and effort and constraint to gain the level of maturity required to do it consistently, and in most business settings the value-versus-cost just isn't there.

Unit testing is still vitally important but

* we'll sometimes see the lines between unit testing and other testing \(integration, functional\) begin to blur a bit.  You'll hear some developers say that integration and functional testing is "more important" and It's easy to see what they would think that way
* Unit testing should not be a silo, where we write unit tests for our code without thinking through those tests as a part of an overall plan.  We do not want to be in the position where the engineer creating a functional test assumes that some scenario "should have" been covered in unit testing

So then, practically speaking, what is unit testing?  It's still our only change to verify that each unit under test can predictably handle whatever input we throw at it.   At this point we should not make assumptions about the business scenario.  I've heard some engineers say "we don't need to test that because it will never happen".   That's logical in a business context, but code changes over time and unless we can show that we have guardrails in place to guarantee that at some future point in time the scenario that will never happen we should not make that assumption.

Unit tests are

* not relatively expensive to create
* part of what separates a hacker from a professional
* part of what separates a good solution from a bad solution
* first class citizens - a decent set of unit tests can take longer than it took to write the component under test.  Plan for them and don't rush through them during build or in PR review

