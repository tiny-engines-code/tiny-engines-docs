---
description: >-
  A boneyard of rants while documenting .. maybe articles or maybe they'll look
  terrible tomorrow
---

# Tiny rants



Test Driven Development \(move somewhere else\)

* A good idea if you have a very solid low-level design
* In a new feature I've seen engineers struggle to "guess" what they are going to create, only to have their original set of units test become throw away as designs changed
* TDD might be a better candidate for functional tests - where the requirement is not going to change
* TDD is great in a case where we are adding a smaller feature 

Code Review \(move somewhere else\)

* PR reviews are for small changes
* Walkthroughs and design review for new features
* Absolutely vital but problematic
* People assume they are free, and don't plan for the long long time it takes to get through them properly
* We look at a change
* We make assumptions about all of the connected dependencies
* We are too busy to remember all of that so we focus on just the change itself 
* We hope that getting these little things right - will keep the big thing right
* See a lot of reviews focus on code-craft 
* Maybe focus more on the integration and unit testing of the change - how can we make sure it is not breaking?
* Let's just admit that they are a pain in the .. and plan accordingly
* Engineers who have to review want small code reviews -- but the reasons for that are not that it make sense from a build point of view - it's that we don't have time to look through a lot of files -- and that right - we don't -- but that's solving the wrong problem.
* A new feature of any size ids going to require a lot of rework and verification - that's why we have developer branches.   So that kind of R&D can happen.
* Once the kinks are worked out - you have a bigger 

