---
layout: single
title: Successful Handovers
categories: [software]
tags: [software handovers]
---

Notes on taking over a software project from an existing team.

I lead an engineering team that was recently tasked with taking
over an existing software project from another team, in a different country.
The transition period lasted approximately one month. Here are some notes on how
we successfully achieved the handover, and some things to think about if you are
going to be doing the same.

### Understand the reason(s)
First you need to understand why the project is being transferred. This will
influence how to prepare and give you some insight into what to expect during
the transition.   
In my most recent case, this was a strategic business decision, but in some scenarios there
could be other reasons such as
- The team are underperforming or delivering below par
- The team may be in the process of being wound down or terminated
- The project is a result of an acquisition / acquihire

Some possibilities

| Reason                           | Expectation |
| -------------------------------- |------------ |
| Underperformance / Underdelivery | Inefficient ways of working and/or inefficient team |
| Team being wound down            | Morale, and willingness to collaborate **could** be low |
| Team already wound down\*        | Expect to spend a lot of time reverse engineering codebases and pipelines |
| Acquired (external)              | Softwate probably won't be built with the same tooling, patterns, languages etc |

\*_This article mostly focuses on taking over from an existing team_

The above are merely notes and pointers, and of course YMMV - for example, I
once took over a project from a high-performing team that were being made
redundant for reasons upstream, yet they spent the last month documenting everything
from top to bottom.  

Nonetheless, understanding the context will help you to develop a plan that is specific to
the situation.

### Fix the duration
This will depend on the size of the project and the number of people involved,
but it is important to have fixed start and end dates. Defining these will not only
give you a deadline to work against, but it also makes it easier to quantify
progress at intervals.  
Be realistic here. Engineers may not be familiar with the architecture or design
patterns. If the applications are in production, then incidents can happen and
all hands may be required to down tools to resolve. Make sure to build in enough
buffer to handle the unforeseens.

### Know the participants
You need to have a clear understanding of who will be participating in
the handover, and what their roles are. Roles may not have a
one to one mapping between the teams, and the experience levels may vary. 
Moreover, this should include the full team and not just engineering effort.
Don't forget about product, operations, management et al.   
Ideally, all participants will be present for the entire duration. If this however is a
gradual wind-down, then factor that into your timeline and estimates.

### Get familiar with the current ecosystem
An unordered list of things to think about to get a lie of the land.
#### Languages
If the project was developed inhouse, then it's possible that there will
be a selection of recommended programming languages, but this may not always be
the case. Engineers will feel more comfortable exploring the codebase if they
are familiar with the language. This familiarity leaves more brain-power for
grokking the domain and data structures used. Give the engineers as much of a
heads-up as possible to allow them to get acquainted with a new language so that
they can hit the ground running.
#### Frameworks
Similarly with frameworks, of which there tends to be a lot more variety, the
less brain effort that needs to be exerted on understanding what is happening
under the hood, the more that can be harnessed directly into the domain and
business logic.
#### Architecture and design patterns
Every design pattern and architectural decision comes with advantages and tradeoffs. 
You need to understand exactly why something was built the way it was. 
Often, the best way to explore is to simply ask for an architecture diagram (or
draw it up on a whiteboard) and then to start asking questions.  
This is a learning exercise and definitely not an opportunity to bash technologies or patterns. 
You might feel passionate about monoliths over microservices but this is not the time to voice those passions.
We want to use this to explore the reasoning behind design decisions and to get
an idea of the requirements the original engineers were given.

#### Postmortems
This can be an interesting avenue to learn about incidents to date. Services
fall over, databases get choked up, APIs stop responding. Learning about the
cause of incidents to date, and how they were resolved, can shed a lot of
interesting light on dependencies, missing fallback strategies, and any areas of
concern.  
If there is no central source documenting incidents, then allocate some time to
getting this information directly from the engineers. Just because incidents aren't
documented, it doesn't mean that they didn't happen. 
- When was the last time production went down?
- What happens if we're consuming an API that stops responding?
- Have we implemented a MultiAZ strategy?
- Does deploying work everytime?

#### SLOs
On a similar thread
- What are the SLOs (service level objectives) on the project?
- How do we know if we are achieving them?
- Is there sufficient monitoring and alerting in place?
- Are dashboards and reports available to see?

Perhaps SLOs have not yet been defined, but having sufficient monitoring in
will make things easier as the project moves toward production-readiness.

### Define the plan
The following methods have worked for me over a number of projects.
#### Maintain current WoWs (initially)
By maintaining the current ways of working, you will get an insight into how the
team currently works. This will keep things moving, but also may shed some light
on areas of improvement or things that are working well.  
The new team is already in a state of change so by keeping things consistent for
the existing team, at least one of two can keep momentum, wherease diving
straight in with new WoWs puts both teams in a state of flux.

Continue the daily standups, the retros, the demos etc. Use these to gauge
the pulse or rhythm of the team. Use the same tools for planning, source code,
deployment and monitoring. Maintain the status-quo initially.

#### Pair Programming FTW
Over the years, I've worked in many engineering roles where pairing was part of
the job. If you're new to the concept of pair programming, then I would
encourage you to
[read](https://martinfowler.com/articles/on-pair-programming.html) up on it, but
essentially it is two engineers working together on a single task. One is the driver
(actively writing the code) and the other is the navigator (I used to use the
term passenger but this can have negative connotations). The navigator is
reviewing the code and keeping an eye on the bigger picture.  

In the knowledge process, pairing has worked incredibly well for me. It allows
the engineer to deep dive into the codebase with a dedicated resource for asking
questions and for direction. Moreover, it is a natural way to discover the
reasoning behind design patterns, and also to uncover smells (an engineer
groaning at having to visit a particular area of the code).

#### Taking over
Gradually, you will want the new team to drive. This can be a tricky one if the
intended changes are substantial.
In my most recent handover, it was more of a big bang transition that maintained
the old mechanisms right up until the end of the handover period, and then
started with new ones thereafter.  
There are pros and cons to doing it this way, but in our case, we were transitioning from
one flavour of development to another, and changing many processes and plays within.
Moreover, the new team were keen to define their own WoWs and track their own
velocity and performance together, as opposed to beginning with, and supporting, an inflated
team size.  

A gradual transition would perhaps be easier if the processes are remaining the
same but the drivers are rotating i.e new scrum-master.

### Success criteria
What defines a successful handover? Much like you would expect engineers to
define exit criteria for a task, it is important to determine what success looks
like. I won't provide an exhaustive list here as every project will be different
but some things to think about

- Do the team fully understand the architecture?
- Do the team fully understand the design patterns?
- Have they deployed successfully to all environments?
- Are the engineers comfortable adding and releasing new functionality?
- Have all applications been migrated to new infrastructure (if necessary)?
- Can all components by located and accessed by engineers?
- Is the backlog or roadmap accessible and understood?
- Are there any areas of concern (performance, reliability, scaleability)?
- Have all super powers been transferred to the new actors?

Clearly not a template or checklist, but these are the sort of questions that
will help to determine if a handover is completed or not.   

At the core of it, if the old team fell off the face of the earth tomorrow, can
the project continue without disruption? If so, then you're probably done.

### Comms
A team and its projects are generally part of a bigger picture. There will be
numerous external stakeholders and dependencies in the mix. Some of which may
not be encountered on a daily or frequent basis, and for this reason, it is
important that communications are clear, concise, and pushed out in a timely
manner.  
It is important that any and all interested parties know who the new team is,
the location of communication channels, how to contact them, and WoW going
forward.

Having a team or project doc site (MKDocs, Jekyll, Google Sites etc) helps a lot
here. Ensure that all information is updated and correct before pushing it out.  
In some cases you might want to mark old repositories as read-only and point to
the fork or new location etc.

### Support period
Another thing to consider here is if there is an available support period after
the handover is complete. This should only be used for mission critical problems
that the team did not encounter during the transition and that they cannot
easily solve. This is akin to a safety net and can be used to minimise
disruption. There may be some political issues on this one because it can be
expensive to have engineers sitting around on-call, but a buffer of a week or
two can give reassurance to the team and the business.
