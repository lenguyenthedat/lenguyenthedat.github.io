---
layout: post
title: 'Data team workflows'
date: '2017-02-07T20:50:00.001-07:00'
author: Dat Le
tags: thoughts, engineering, management, teamwork, data
modified_time: '2017-02-07T20:50:00.001-07:00'
comments: true
---

<center><img src="https://cdn-images-1.medium.com/max/2000/1*mkRF5wuUs9DWF0wyb3_PPw.jpeg"></center>

## Introduction

Choosing the right tools and workflows that fit well with your team is a critical task but oftentimes get overlooked. This article demonstrates our approach in order to improve engineering efficiency for the data team at honestbee. Within this regard, we hope it can help you improve your team workflow or set it up from scratch.
<br><br>
Do note that how the workflows actually look like isn’t that important, what more important is to have *something that actually works for you*, as well as to be transparent within the organization.

## Atlassian Confluence

Confluence is the first tool that we chose to have, and arguably the *most important step* when setting up a new team.
<br><br>
We decided very early on that it will be used as the one and only platform for organizing all of our documentations, from team managements, data infrastructure designs, dashboards, internal company logics, meeting notes, guides, project documentations, and so on.
<br><br>
Confluence pages are also created for quickly on-boarding new members, as well as communicating within the organization effectively.
<br><br>

<center><img src="https://cdn-images-1.medium.com/max/800/1*Wfs1QpM-XQnzt3QzXc2rgw.png?style=centerme"></center>
<center>Data home page</center>

## Atlassian JIRA

We started off using a standard *JIRA engineering workflow scheme* and JIRA team setup, but quickly simplify ours as much as we can, so that it will be *easily understandable and followed*.
<br><br>
As such, there are only 3 types of issues we used:

- *Bugs* (technical task that is directly related to a known bug or error from the current system)
- *Stories* (technical task that has a relatively short timespan of less than a week. If the story created might last longer than a week, it is required to be broken down into smaller stories that can be linked together)
- *Epics* (group of stories that form a single business idea, product feature or simply a significant larger task that consist of stories)

We also have internal rules, such as:

- All *Bugs* and *Stories* need to tie to one and only one *Epic*
- All *Bugs* and *Stories* need to be assigned to a person
- All *Epics* will need to be communicated to senior managers and stakeholders before creation to ensure commitment from both side

Our workflow:

- Everything starts at *Inbox* (a.k.a backlog)
- Anything from *Inbox* can be progressed further to *Ready For Dev*, *In Progress*, *Review*, and *Done*
- Anything from *Inbox* can also be moved to *Cancelled* if it is not relevant, rejected, or not needed anymore

<center><img src="https://cdn-images-1.medium.com/max/1000/1*IGvdPyijujypMQ4EQ1QzYw.png?style=centerme"></center>
<center>JIRA Workflow</center>
<br><br>
*Kanban Board* simply reflects the workflow above, where everyone can have a bird-view on how all the tickets are progressing. The engineers and project managers will take responsibility for updating the tickets accordingly.

<center><img src="https://cdn-images-1.medium.com/max/800/1*zdBeUxmyIphxwiR9Zucl2g.png?style=centerme"></center>
<center>Kanban board</center>

## Github

*Labels* are used to indicate the progress of any *Pull Request*.

<center><img src="https://cdn-images-1.medium.com/max/800/1*MSJHofnwbRd7KCRRweC0nA.png?style=centerme"></center>
<center>Pull Request Labels used in Github repositories</center>
<br><br>
Every Pull Request should contain:

- link to *JIRA Ticket(s)*,
- *WIP Checklist*,
- the *Why, How, and What*, as well as
- having *Reviewers* assigned once ready.

A Pull Request *can only be merged when at least one reviewer approved the change*. A Pull Request should not stay *In Review* for more than one week.

<center><img src="https://cdn-images-1.medium.com/max/1000/1*4Riw-t4a2CGdCXtbKTo82w.png?style=centerme"></center>
<center>A Pull Request in Data Team</center>
<br><br>
We don’t use *Github Issue* or *Github Project* since it should more or less be covered by *JIRA*.

## Continuous Integration and Deployment — with Github, Travis, Marathon, and Slack

We release our changes typically once a day.
<br><br>
A release can be done by any engineer in the team simply by using *Github’s Release* interface:

<center><img src="https://cdn-images-1.medium.com/max/1000/1*rKMF1meeBOvtD5zUCiuHEg.png?style=centerme"></center>
<center>Github’s Release interface</center>
<br><br>
*Marathon* dashboard was setup to keep track of *deployment process* and sometimes, for *applications health check*

<center><img src="https://cdn-images-1.medium.com/max/800/1*CtiOK6hTNL12treTeEiyDA.png?style=centerme"></center>
<center>Marathon dashboard</center>
<br><br>
We also *use Slack to automatically notify* the team that a new deployment was successfully done, as new changes are now live on production:

<center><img src="https://cdn-images-1.medium.com/max/800/1*ABwBLjC7FrvvKLpgo54XmQ.png?style=centerme"></center>
<center>baymax is our Data team’s bot 😅</center>
<br><br>
In general, software release is usually a complicated, multi-step process, that is quite prone to errors sometimes. We believe it should be automated with minimal human supervision, thus we spent a good amount of engineering time to polish the process in such a way that it can be *as transparent, and easily done as possible*, by every engineer in the team.
<br><br>
If you are more interested in this topic, the whole process looks like this:

<center><img src="https://cdn-images-1.medium.com/max/800/1*bGokx6Dy-2EXqUIvUdlmTg.png?style=centerme"></center>
<center>Continuous integration and deployment (for more info: https://mesosphere.com/blog/2015/04/02/continuous-deployment-with-mesos-marathon-docker/)</center>

## Meetings

- We rarely do meetings, especially for data engineers and scientists.
- We have a weekly casual sync-up, that is used mostly for *team updates* and *technical sharing* among the members.
- We also have weekly *1–1 meetings* between team manager and each member, which usually happens during coffee breaks.

## Final thoughts

- Every process and workflow is as good as how well people follow it. The more complicated it is, the harder it will get adopted and properly followed as the team grows bigger.
- It would be wise to invest more in time and communication as early as the team was formed. Make decisions as a team, and follow through with every question and scenario as much as you can think of. Because ultimately, it will help the team move faster with less communication overhead later on.
- At the same time, flexibility is important as the team grows, the business evolves, and sometimes, due to unforeseen circumstances. Decision should be carefully made when the current process does not work out well anymore. There should always be a balance between extending legacy tools and implementing new tools for new requirements.

*PS: special thanks to Tran Anh Cuong, Maksim Golub, and Liva Paudere for helping me with this article.*
