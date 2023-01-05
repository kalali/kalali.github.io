# Proactive vs Reactive Working in Software Development


## Intro
I was a guest at the [The Exchange Nordics Podcast](https://evolution-nordics.com/category/podcast/) a while back around this subject and decided to write a bit more detailed blog post with my thoughts more organized. I recommend listening to the podcast as the other guests had views on the subject from different angles which enriches the understanding.
## What are Reactive and Proactive working?

Looking at the Oxford dictionary for the adjective's definition I find the brief but clear description as follows

{{< admonition type=quote title="Reactive" open=true >}}
showing a reaction or response
{{< /admonition >}}

{{< admonition type=quote title="Proactive" open=true >}}
controlling a situation by making things happen rather than waiting for things to happen and then reacting to them
{{< /admonition >}}

The description is clear on what the approaches are, but there are some characteristics that form each one of them:
### Proactive leaning organization 
Any organization more focused on a proactive approach has a higher understanding and readiness for the future and what may come to pass. To enumerate, proactive mindset and leadership summerise in:

- Anticipating and preventing problems
- Taking initiative and anticipating future needs
- Planning and organizing work effectively
- Seeking out opportunities for improvement and innovation
- Focusing on long-term goals

Proactive leaning organizations often have this characteristic at every level of organization, from the strategy down to implementation, being a software system or human resource strategies and governance. That being said, in every organization, there are crises and crisis management which require a reactive mindset as nobody has predicted such an event. But the learning from the event will shape how the organization prepares for possible future occurrences. 

### Reactive leaning organization 
A more reactive-leaning organization may consider short-term goals to be more important than long-term ones. To enumerate, reactive mindset and leadership summerise in:

- Responding to problems and issues as they arise
- Waiting for instructions and direction from others
- Less planning and organization
- Less focus on improvement and innovation
- Focusing on short-term goals
- Waiting for things to happen rather than actively seeking out opportunities


### Mesh of Reactive and Proactive in an organization
There aren't that many businesses that would only thrive on a single approach but rather a mix of the two approaches at play in different situations. From the individual up to the strategic directions a mix of approaches are at play:

 - *Individual level*:* At the individual level the reactive approach to a server being down is to bring it up. A proactive component is to look into why the server was down and how to either prevent the server from being down or restart it automatically when it is down and send a notification about it.
- *Organization level:* At the organization level the reactive approach is most often the change in the business landscape. For example, if offering a particular service and getting no traction for it, the reactive response might be sunsetting the service. The proactive approach is doing extensive market research on the possible service usage and adoption before developing the next service.

Different organizations budget different amounts of time by default for the reactive work that needs to be done, I have seen from 10-90 down to 50-50 split between the time that is allocated for reactive and proactive work by the individuals and teams. Depending on the time in an organization's life the ratio of proactive to reactive work evolves. For example, big enterprises with established business models fall more into the category of proactive organizations. A startup may be learning toward reacting to changes in the business landscape much more rapidly to adjust vision and strategies.

##  Proactive or Reactive software architecture
A quick look at how the proactive and reactive mindset sips into the software design and architecture resonates with the usual best practices that exist in designing software systems.

### Proactive Architecture
A proactive software architecture is designed to anticipate and address potential problems or challenges that may arise in the future, a more upfront investment to ease future progress. This can involve designing the software to be scalable, flexible, and adaptable so that it can easily accommodate changing requirements or new features. Proactive software architecture also involves designing the software in a way that is easy to maintain and update, so that it can be continuously improved over time.

Some specific characteristics of a proactive software architecture might include:

- *Modularity:* The software is broken down into smaller, independent components that can be easily modified or replaced without affecting the rest of the system.
- *Loose coupling:* The Components are designed to be as independent as possible in both temporal and static space. Aiming to isolate changes to as few components as possible. 
- *Reasonable Abstraction:* Hiding complexities behind clear, well-defined interfaces, making it easier to understand and modify.
- *Extensibility:* The Architecture allows you to easily add new features or functionality without requiring major rework of the existing code.
- *Operability:*: The Architecture includes the necessary rules and principals and defaults to ensure easy operation, which in turn improves agility.

Overall, proactive software architecture is designed to be evolvable, flexible, and scalable, so that it can easily respond to changing requirements or challenges that may arise over time.

### Reactive Architecture

A reactive architecture responds to changes as they occur, rather than anticipating and possibly making room for them. Some characteristics of such architecture might be

- *Rigid:* Harder to adjust for the new requirements.
- *Tightly coupled components:*  Different components have direct dependencies both in temporal and static spaces.
- *Unflexible:* The lack of separation of concerns and less well-defined interfaces makes it harder to adapt the architecture to new requirements or evolving business needs. 
- *Lack of agility*: Given the above, it might be more prone to becoming unwieldy or inefficient as the system grows. It may require more frequent updates or overhauls in order to keep up with new developments. This will reduce the cadence of delivery value and increases the maintenance investment. 


## Summary

Both approaches have a time and a place and no organization can be fully proactive or reactive in a complex environment. Of course in an alternate universe where time is not linear and everything can be predicated and accounted for a proactive approach would rule. And in a completely chaotic environment without the possibility of anticipating changes and events a purely reactive approach may be for the win. Since most do not live in any of those two, and rather live in space in between, a combination of proactive and reactive approaches is what every organization counts on.
