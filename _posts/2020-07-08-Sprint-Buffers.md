---
layout: single
title: Buffer your Sprints
categories: [software,agile]
tags: [software,agile,scrum]
---

My team has been successfully delivering software to our stakeholders using the
Scrum framework. The fixed duration sprints and detectable/measurable velocity
allows us to accurately forecast what we can deliverable each time.... mostly.  

> Plans are worthless, but planning is everything.

What we cannot account for during our Sprint Planning sessions is unplanned
work, or, as the Agile Manifesto might word it, "Responding to change".  
Things break, hotfixes need to be deployed, one-pointer stories grow legs, shit
happens.

Accepting new tasks into the sprint is known as Scope Change. The planned scope
has been modified. If you look at your Burndown Chart, this is a vertical spike
correlating to the number of points assigned.

Typically, the nature of such additions is that they are pretty important and urgent. Eisenhower's Matrix
for decision making tells us that if something is both urgent and important,
then we must do it now.  
This, however, pushes the remaining tasks a little further away, and potentially to
the point of not being completed in the Sprint.

A mechanism that we have been using to help with such pain points, is including
an explicit buffer in our Sprints.

## Sprint Buffers
A sprint buffer is a very simple, but effective means of planning for unplanned
work. It allocates a single task, storypointed to an average of the historical
unplanned tasks (scope change in Jira lingo), which will then be used to
bankroll unplanned work up to the buffer. Once the buffer has been reached, then
you will need to make a call on how best to proceed.  

In our case, I looked back over our last five sprints, and found that on
average, the team accepted two points of unplanned work into each sprint. In
those cases, we did not deliver all forecasted tasks.

In the next planning session, we added a task named "Buffer" into the sprint.
The task wast estimated at two points. We started the sprint as usual and left
the buffer task in the TODO state until some scope change was encountered.  

> We've just found a spark in production and would like to extinguish it before it becomes a fire

Hmmmmm

> Ok, if the estimate does not exceed the buffer, then track it and play it

A simple, straightforward, and transparent mechanism to budget for unplanned
effort whilst achieving the forecasted deliverables of the sprint.
