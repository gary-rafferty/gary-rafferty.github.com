---
layout: single
title: Jira Dashboards for an Agile Project
categories: [software,agile]
tags: [software,agile,jira,scrum]
---

Jira dashboards are a useful way to take a quick peak into a project to get
a feel for their state and health. Think of them as an at-a-glance healthcheck.

There are a few useful gadgets/widgets that I use to answer a few important
quetions. My most recent projects are using Scrum so this will be tailored for
that flavour, but by tweaking a few charts and queries, you can apply them to
any agile project.

The dashboard is a useful tool for gathering insights, but is not a replacement for standups or checkins.

## Sprint health
A really insightful little widget that provides you with the current status of
the sprint.  
![sprint health](/assets/images/jira/health.png)

It includes
1. The progress of tasks (not yet started, in progress, completed)
2. Any scope change
3. Any blockers or flagged issues
4. Remaining effort to complete the sprint
5. Time remaining in the sprint
6. Assignees


## Large storypoints
I like to flag tasks that have been assigned a high number of storypoints. High
estimates can be an early sign for tasks that i) can grow more than
expected or ii) may not be well enough defined.  
To keep an eye on these types of tasks, I create a JQL filter that
queries on storypoints, and then I use the Filter Results widget to display them.

Create a JQL filter that includes a query on point count.
```sql
sprint in openSprints() AND "Story Points" >= n
```

Adjust `n` as you see fit

Next, add the __Filter Results__ widget and choose your filter. This will run
the underlying query and pass the results to the widget.

![high point issues](/assets/images/jira/points.png)

## Assignees with a lot of issues
Depending on your workflow, this may or may not be helpful, but this next widget
is helpful to see if someone on the team has a lot of assigned tasks, which
could lead to a bottleneck if they became ill or needed to be redeployed to a
different project.

Create a filter for active sprint issues and pass that to the __Issue
Statistics__ widget. Sort by descending.

Here's an extreme example, but it highlights that Peter Griffin has the bulk of
the workfload assigned to him.

![assignees with issues](/assets/images/jira/assignees.png)


## Created vs Resolved
Is your backlog growing faster than you're shipping?  
The __Created vs Resolved__ chart is a quick way to answer that question.

![creates vs resolved](/assets/images/jira/createdresolved.png)

## Sprint burndown
The burndown chart is a quick visualisation of work remaining in the sprint and
a way of forecasting the likelhood of achieving the goal.
It is based on points and time remaining, taking into account non working days.

Here's one I pinched from Atlassian.

![burndown](/assets/images/jira/burndown.png)

1. The total number of points is shown on the y axis
2. The points remaining to be completed.
3. The guideline (think of a glideslope for an aircraft)

Fairly self explanatory but it's one that I like to have close to hand.

## Other useful widgets
Keep your dashboards neat and minimal, but here's some other widgets that can be
helpful too.
- Activity Stream
- Resolution Time
- Issue Statistics by Type

