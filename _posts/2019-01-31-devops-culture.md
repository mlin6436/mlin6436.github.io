---
layout: post
title: "Is DevOps Destroying Our Productivity?"
tags: ["devops", "culture", "continuous delivery"]
---

<div class="message">
Development and Operations used to be two different disciplines, but it is coming closer given the rise of the DevOps culture. While some still face challenges like business is still lingering on the illusion that they are in control by not giving up Ops permissions to development teams, some might already feel tired of time being consumed by Ops work. I think both are genuine problems, and can be and should be addressed by looking into why are we fostering a DevOps culture and how can we be better at it.
</div>

# DevOps is a Culture

I often heard people talking they THE DevOps person in the team, or companies recruiting for DevOps roles. This puzzles me a lot, and I think it's worth putting this out upfront:

__DevOps is a culture, not a role!!!__

# So what is DevOps Culture?

DevOps wasn't just a thing being created out of blue. The likelihood it draws so much attention was people from all parts of business often get frustrated by the slow-moving process, specifically to do with releases, making infrastructure changes or experimenting ideas. Therefore, instead of being a trendy move in the industry, it's more about __giving the right group of people, the right tooling with right amount of access__. The purpose of this arrangement is to shorten feedback loop, the cycle time from idea being recorded as tickets to productionise the idea.

If you are wondering why this is not common sense still, I can only say lucky you and don't easily give up that hard-fought privilege. But if you are searching for means to bring fire to Dev world by convincing the business this is the way forward, I hope I can offer you some humble opinions and observations to challenge the status quo.

# What do we gain?

- The team has control and can tailor its own integration and release pipelines
- The team needs to think about performance impacts of the code
- The team needs to think about how to make scalable software
- The team is free to experiment and innovate
- The team is responsible for the cost implications and reducing costs
- The team can continuously deliver ideas with faster pace

# What might we lose?

- The team needs to do release by themselves
- The team's velocity will suffer from Ops work
- The team needs to stay on top of security patches
- The team is responsible for the first line support of the application
- The team is responsible for any data breach risks

# Doing it right

Despite the benefits, you as a team needs to decide how confident you are in coping with the risks and the culture change. As a person coming from a DevOps heavy team, I think some of my experience and the tooling is worth sharing before anyone/team considers embarking on this journey. Take it with a grain of salt, and hopefully you are already in a better place and laughing at my naivety.

- Have you reach a good test coverage of code from unit, integration to end-to-end level
- What is the level of automation of end-to-end tests, such as contract tests for APIs, journey or behaviour tests for websites on different devices
- How much integration and deployment pipelines are automated and source controlled, using tools like Jenkinsfile
- How reproducible are infrastructure stacks, using tools like puppet, AWS CloudFormation or Docker
- How easy it is to trace logs/trouble shoot application, using log aggregator like Sumologic or Kibana
- What is the level of monitoring for application and infrastructure, with tools like AWS CloudWatch Alarm
- What is the rollback strategy