# Building a development platform

# Intro
Building a new development platform, for example a new microservices oriented platform to replace an existing monolith software system, is a massive endeavour. I have been working on a development platform, the whole ecosystem from ways of working to pipeline in the past 3 years and the experience might help others. So I thought to write down some of the observation and experience before they fully turn into intrinsic/implicit knowledge and hard to pen. 

# Platform?

If we divide the software development within an organization into two very broad categories, there is platform development, and platform products in one layer and and application development in another, which is usually consumes the platform.

The applications are usually customer/end-user facing or directly facilitating a particular business use-case, for example a login routine or a batch job that runs every night to process schedule payments. Applications most often have a clear business goal and  defining OKRs is easier and ROI can be observed within a shorter period of time.

On the other hand, platform development which can be anything from shared components for different applications to release pipelines, development tools, or combination of many things together is not as straight forward. Platforms and platform teams are there to not only save initial cost of starting a new project or product but to save on the cost of maintenance and owning such product but introducing familiar concepts and ways of working across the vertical and horizontal integration points.

Sometimes a platform/team is hard to justify, not easy to have any meaningful OKRs and usually have no immediate ROI. For example, If the platform team develops a pipeline and related libraries, the adoption of such pipeline takes time. After a reasonable number of applications have started using the said pipeline, product owner would slowly see an indirect ROI from:

- Better compliance baked into the pipeline
- Automation in release practices rather than throw over the wall and hand overs
- Uniform quality gates as pipeline would enforce some level conde analysis and apply some gates
- Faster time to production as it reduces the number of manual steps and communication costs
- Less code sediment as continues or even frequent deployment reduces the code that is sitting in the codebase without being exercised
- Less regressions because of frequent small releases
- etc.

That is when the justification for existence of a platform become easier and the platform teams and products will become necessities.

# Why Platforms?
{{< image src="post-img/why-to-have-a-development-platform.jpg"  >}}

Simply put platforms help with the following:
- Time saving as they prevent inventing the same thing again and again; e.g. a pipeline.
- Time saving as, when done right, they introduce a consistent way of working across diferent layers. Being applications, infrastructure or tooling. That will in turn makes transfer of knowledge and understanding easier
- Time saving as teams with expertise build re-usable components that other team can incorporate rather than every time having to acquire such expertise
- Governance, and compliance and other orthogonal aspects can be addressed via automation backing variety of self services. 

Let's say that there an enterprise with the very well established process for software development, delivery, operation and support. This has been the case for the past couple of decades and individuals, teams, organisational units, and organisation as a whole developed a culture around the development process (for good or bad). The process can be something along the line of the following for simple features/functionalities. Imagine a handover between most steps.

1. Long contemplations and many meetings of the domain architect/s 
2. Retirement gathering and certifying it with stakeholders
3. Doing any risk assessment, compliance check in relation to data being processed and/or collected
4. Planning (usually includes capacity planning and schedule as well as development timeline)
5. Development and figuring out networking and storage changes that are required
6. Deployment to variety of environment for variety of integrations to be tested
7. Testing including acceptance, performance, etc
8. Scheduling the feature for release, usually using a feature toggle and getting approval of multiple stakeholders
9. Releasing the feature
10. Plan the bugfix release or deploy hot-fixes!

Now with such a list of steps for delivering a functionality to end user it can take sometimes 3 quarters to deliver a feature to production and the enterprise is falling behind the competition because of whole combination of reasons:

- No timely innovation to attract new customers
- Bug ridden, slow, barebone user functionalities failing to retain current customers
- Legacy development culture failing to attract or retain technology taskforce 

The organisation decides to go with a new development platform that addresses the trade-offs and divergence made in the previous platform (and make a whole new collection of them while doing that). The new platform is supposed to ease the feature and functionalities delivery to reduce the gap with the competition.

The following list is a few aspects of a new development platform's responsibilities. Some may already exist, some might be completely new, and some might be a revamp of existing criteria.

- A set of guidelines and principals on what constitute an application on the platform
- A set of common libraries for common functions, e.g. logging, key-management, http-client, build-info (helm chart, platform libraries, sidecars, etc.)
- A set of technical architectural decisions that must be followed. E.g. kubernetes namespace separation, database versioning, different application types and their responsibilities (e.g. worker services, end user services, and core services to name few)
- A set of well defined way of working; e.g. code reviews, squash merge, semantic versioning, conventional commits and so on.
- A set of expectations, for example the application on the platform must define and monitor SLO and SLA using prometheus rules, etc.
- Automation in the areas like observability, high availability over multiple data center and so on.
- Automation in digital certificate management; this is the most poorly understood and managed in any organisation I have had the experience of working.
- Automation in managing feature flaggs, api-keys and some QoS attributes 
- A collection of KPI and OKRs for the platform and the platform consumers are measured by

### Setting up the expectations

Being a product owner or an advocate for a platform requires more conversation and investigation about pain points existing in the organization as a whole. Being the application/program development teams or the relationship between compliance/security/process divisions with the application development heirarcy.

Setting clear expectations for external customers and goals for the platform team itself is an important task that the product owner, in combination with engineering teams (and management) should set. This expectation setting will make it clear to management on what timeline and outcome to expect for given organisational support, runway and budget.

At the same time, a platform development team must make it clear to the management that having a platform is a tradeoff. For example if the current way of working is that each team entirely choose their guidelines and architectural patterns, a development platform may enforce certain principles and guidelines using automation as much as possible, so no exception and exemptions. An example can be use of certain libraries for logging, or certain pipeline for deployment, certain number of active engineers per application and so on.

# Platform adoption
{{< image src="post-img/properly-position-for-adoption.jpg"  >}}

A development platform like any other product will need adoption and use to survive the next budget definition. Let's say that the platform teams understood the painpoints and the platform is developed to not only remedy the painpoints but also ensure the viability of the application ecosystem over the next few decades. There is still the work on convincing the individuals, teams and the organisation to switch to this unknown and largely unproven platform. 

There are some general approach in spreading the adoption of the platform, similar rules applies to anything to some extent.

#### Platform team owning applications
One approach that I have employed, and seems to work for adoption of the platform, is ensuring the platform team owns some programs built on top of the platform. This service can be the example to showcase how platform works and how does it help with the pain-points that it exists to remedy and how does it propel the organisation into the new ways of working and tech landscape.

Of course, platform owners developing applications has some drawbacks:
- The application/s may turn into a an unintended blueprint and others may follow something that is not fully baked yet. 
- The platform team too far get into habit of optimising the platform for their own use/level of competence. 
- The platform team get too far distracted by the application to pay attention to the platform itself. This may result in mental overload for the team and far too many context switching.

#### Platform team helping an application team convert

Another approach is locating a team that struggling the most and is vocal the most about the painpoints, of course within reason, and help them convert their service development to use the new platform. This would require multiple criteria:

- The service in question does not have too many dependencies that can impact the conversion
- The service team is willing to take the steps. The steps that the platform is advocating for. For example code review, use of pipeline, test automation and so on that is a service provided by the platform.
- The platform team has some understanding of the domain that the service team is driving.

#### Combination of the above two
One thing to consider in the combination or even in the second approach is to identify the players and influencers and get buy-in from them. In every organisation there are people who are sitting far behind the scene without any title with more influence on what different teams may adopt that any line managers. Convincing them will multiply the rate of adoption.

# Next
In the next instalments of this series I will write more about each aspect of the development platform mentioned here.

