# Characteristics of a Good Observability Platform



## Introduction

Observability is a critical pillar of operability in software systems. And the more complex the mesh of contributing components and infrastructure, the more important to have clear observability. The market for observability tooling is getting more mature with the explosion of the adoption of microservices and the standards for the interfaces are emerging with different vendors, adapting them at different levels.

Plenty of SaaS and on-premise products are around to buy and start using and almost all of them come with a 5 minutes demo to show how easy they are to use! No matter which platform you pick, the product needs to work with an existing culture and workflow to some extent. This integration results in some characteristics, which are a combination of how the product works, and how the functionalities and features offered by the product are integrated into the existing system of work.

An observability platform is a prerequisite for operability and is an essential component of a healthy software system. platform that provides the means to collect, store, and assess the data and react to them using pre-defined rules and heuristics. The more the relevant components are enrolled into the observability platform, the higher the chance of end-to-end visibility. The organization needs to commit to and embrace rolling out an observability solution for it to work. It is not enough if applications are enrolled, or just databases or it is only the network switches that are providing visibility. It is a collaboration between all layers of abstraction that eventually provides operational visibility 

The observability platform must be able to answer questions related to what, how, and why behavior has occurred. Different teams or functions may require a different level or type of observability. A holistic view and overall health might be something that an operation center is interested in, while a detailed diagnostic view might be the realm of application developers, and reliability and performance might be the focus of SRE. 

What Constitutes as a Good Observability Platform
From high above, some attributes that must be considered when building a good observability platform are listed below. These are features and functions of a product purchased mixed with integrating with the current automation, tools, frameworks, etc.

- **Availability and reliability:** The platform must be highly available and reliable. During any incident, the investigation starts with whatever observability platforms can provide some insight into what is happening now or what has happened before and then the details of how.
- **Accessibility and Correlation:** All observability verticals are readily available to analyze. Logs, metrics, traces, alerts, and easy correlation among them.
- **Tracing and Coverage:** A good platform covers as much of the request journeys as possible, from the client down to the application and the database or message queue, network switches, operating systems, etc.
- **Dynamic resolution:** Information is available at high resolution but does not need to be consumed at high resolution. Zoom in and zoom out both using aggregation and statistical analysis methods. 
- **Automated Onboarding:** Onboarding to the platform requires almost no effort from application developers, especially if this is a green field application development platform. Just add a dependency to the obs library and it glues everything together 
- **Uniformity And Cohision:** Dimensions are standard throughout similar observed components. E.g. hostname stored using the same attributes in logs, metrics, and traces for all microservices, vms, etc.
- **Standard friendly:** OpenTelemetry has come far and any platform not supporting it must have some serious other advantages to be considered. 
- **Developer friendly:** Integrated with automation and pipeline
- **Intelligence and Insights:** An evolving attribute of observability platforms to provide predictive analysis. This may include deducing trend lines, analyzing past events and predicting possible future events, or better explaining current events. 
- **Integration:** Integrated with the existing tools and workflows. For example, a JIRA/ServiceNow/Teams/OnCall system, etc.
- **Multi vertical analytics:** Logs, distributed traces, and metrics on their own are helpful, but visualizing or detecting statistical patterns over a combination of these can give significantly better visibility. 

## Good Application Observability
A healthy organization forms activities around reasonable customer satisfaction. Having the best observability platform from the top vendor does nothing if developers have not properly understood observability and instrumented the applications accordingly. The Absence of operational excellence does not disappear overnight by introducing an expensive observability solution. Some attributes of good application observability are:

- Includes proper metrics, traces, and structured logs
- Focuses start from the customer to the application and then the resource
- Meaningful signals and metrics to cover those important signals. 
- Visibility into the health and diagnostic attributes
- Actionable alerts/feedback with a very low noise/signal ratio
- Easily accessible to everyone involved
- The observability artifacts (alerts, metric definition, dashboards, etc.) as code
- Includes application-specific metrics on top of the (for example Spring Boot and Micrometer) out-of-the-box diagnostic metrics.


## Observability for CI/CD
Software delivery automation has grown beyond just delivering a binary to a deployment destination. The CI/CD process should not only cover the delivery aspect but also consider observability attributes and actively benefit from good application observability.

- **Observability artifact and pipelines:** To ensure effective observability, it is best to consider incorporating observability artifacts (such as dashboards, rules, and routing) deployment into the deployment pipeline as a first-class stage. These artifacts should be versioned and treated in the same way as the rest of the code.
- **Observability-aware pipeline:** An observability-aware pipeline goes beyond just deploying observability artifacts. It also uses the application's health indicators to perform other tasks, for example, automated rollbacks if needed. This ensures that issues are addressed quickly and effectively in important environments.
 
## Observability Platform Pitfalls

- **Silos:** Siloed observability platforms within the same organization can make it difficult to gain a holistic view of operations. When each application or application platform, such as A, B, C, and D, has its own separate observability system, it's challenging to have a unified view. A single pane of glass with integrated observability is significantly easier to manage than a siloed one.

- **Observability as an afterthought:** Operability is a prerequisite for observability. Achieving operational excellence requires more than just a cultural shift, it also requires building observability into the system from the enterprise architecture down to the instrumented application operations. It should not be treated as an afterthought.

- **Non-unified views:** Consistency in labeling and vocabulary is crucial to the success of observability. Disjointed and free-form naming reduces reusability, correlation, and analysis success. It is important to have a unified and consistent naming system for better observability.




