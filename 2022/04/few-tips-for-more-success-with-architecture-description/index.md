# Few tips for more success with architecture description

## Introduction
During the past 20 years I have seen trends come and go; one of the thing that has stayed around in one or another form is the term architecture and the  architect role. 
Of course, there are plenty of overloads for the term and plenty of architect archetype like domain architect, enterprise architects, IT architect, network architect, software architect and you name it.

Wikipedia has a good [definition of software architecture](https://en.wikipedia.org/wiki/Software_architecture) that I quote below:

>*Software architecture* refers to the fundamental structures of a software system and the discipline of creating such structures and systems. Each structure comprises software elements, relations among them, and properties of both elements and relations. The _architecture_ of a software system is a metaphor, analogous to the architecture of a building. It functions as a blueprint for the system and the developing project, laying out the tasks necessary to be executed by the design teams.

Software, data, IT, domain, and enterprise are going to have an architecture no matter if it is intentional, developed and nurtured and well documented. Or something that is grown out of what everyone involved in the system has done to arrive to without a record of why the _architecture_ is what it is. Below I write about how and why documenting software architecture is not as successful as it should be and how can it possibly be improved.
{{< admonition type=note title="AD and ADR" open=true >}}
During the blog I refer to Architecture description as AD and Architecture Decision Record as ADR. I see adding the ADR as an ongoing effort while the AD is the overall architectural view of the system. The AD has higher level of abstraction compared to the ADRs which are focused for a particular decision.
{{< /admonition >}}

### Software architecture description infamy

The primary reasons for the infamy of software architecture description, as far as I can say, is the failure in attracting different stakeholders to read or to develop architecture descriptions. There are plenty of archetypes of architectural descriptions targeting different groups of stakeholders. Here I will put the focus on the software architecture with the target audience being software engineers. 

Some reasons for architecture description usually is not the favorite topic of conversation are the followings:

- **The architecture description is too generic** and is not created for software developers consumption. The vocabulary is wrong, the addressed concerns may not be relevant and so on.
- **It has too much irrelevant information** that the software developers may not need. So the noise to signal ratio is too high for the document to be considered.
- **The architecture description is not easy to access**, and if it is; it is not easily readable because of the tooling that is used to create it.
- **No uniform theme is used.** Different architecture description within the same organisation are not following the same theme. 
- **The description is not up to date**

From the list above "keep it up to date" might be hard to imagine and the "theme" item may not be clear. So I will go into a bit of details for each.

#### Keep architecture description up to date
Keeping documentation up to date is hard, we all know it. And there are many advice on how to approach this. Some of these advice are applicable to any documentation.

- **Keep the documentation short:** Write as little and as targeted as possible. Avoid fluff, avoid write one document for all stakeholders approach.
- **Have a responsible person:** Each ADR and the AD itself should be owned by someone who is the sole responsible to keep track of and update them. If everyone is responsible nobody is.
- **Make updating easy and streamlined:** Keep the AD and ADR in the code repo, where the devs like to spend their time in.
- **Make it easy to contribute to the AD/R:** Anyone in the team/s should be able to open a PR or contribute to clarifying the AD/R


#### Establish a uniform theme
I know almost no software developer without a very carefully selected theme for the editors, terminal and IDE. So I'd expect the same emphasis of look and feel would apply on the documentation as well. When I say theme I am referring to the following aspects of the architecture documentation:

- **Terminology**: Refer to any one concept/artefact/etc. with one term throughout the org
- **Framework**: Use the same architecture model framework, if it is [C4 Model](https://c4model.com) or [4+1 Model](https://en.wikipedia.org/wiki/4%2B1_architectural_view_model) or anything else, stay with the same model everywhere
- **Tooling**: There are 100s of diagraming tools, pick one and stay with it. The lighter it is the better chance of it being used. Stay away from heavy tools for day to day work. Choose a tool that works on all operating systems used in the organisation
- **Common icons/glyphs**: Develop common icons and glyphs for internal architectural concepts and artefacts; use standard icons, e.g. cloud vendor provided icons, or framework/tools provided icons and glyphs
- **Use a standarar template**: At application server organisation we had a architecture committee (AsArch) which had a [OnePager](#The-OnePager-as-a-story) template for architectural decisions/changes that needed to review. Everyone in the committee or any attendees knew what to expect to see in the architecture description.

For each of the above line items I can write down a long blog post. But let me talk a little bit about the template as the container for rest of the items (a glyph, a diagram, a term or expression wont be used without it appearing on a page)

### Software architecture descriptor template
Templates have poor reputation as anyone in larger enterprises associate them with a long document with unfamiliar terminology and vague questions. The architecture description OnePager template should not endup being vague or filled with questions. It is a description not a checklist, not a questionnaire. 

#### What about [ADRs](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)?
One thing to mention before I go far with describing a template, I can say that template is not a farfetched concept when using ADRs. The ADRs follow a template, for example it can contain headers for context, assumptions, decision, consequences and status and the content for each header. I see ADR mostly suitable to document the on-going decisions. Such decisions, if they impact the bigger picture must result in an update in the one page. Think about the OnePager as a summary of all the ADRs.

{{< admonition type=info title="Usual heading in ADRs" open=true >}}
Some of the most common headings in an ADR are the following:
- **Tittle**: What is it that this decision talksa about
- **Date**: When was this decision made
- **Context/Summary**: Problem definition and solution context. This is setting the scene for rest of the description/decision to come
- **Decision**: What is decided in relation to the issue/context/question
- **Status**: proposed | rejected | accepted | deprecated |...
- **Consequences**: What changes in the system (Performance, testability, cohesion, isolation, etc.), what turn more complicated or easier. 
{{< /admonition >}}
You may also see the following headings being mentioned:
{{< admonition type=info title="More heading in ADRs" open=true >}}
- **Deciders**
- **Assumptions**
- **Constraint**
- **Related Decisions**
{{< /admonition >}}

But generally speaking, the template is a set of common sense headings encapsulating the discussion that has happened over a coffee, over an email, in a meeting, etc. in an easy to follow structure.

#### The OnePager as a story
Now, down to a OnePager. What I appreciated about the OnePager that we had was the clear headings and subheadings. There was no confusion about what is needed to be mentioned and in what order. Of course not all headers were necessary to have any content or to be present, but their presense in the OnePager was a guide so that the authors do not forget about adding some details. For example, if there was something to mention in relation to performance impact it would go under "Performance impacts" header. If there was an impact on the system security it would go under the security headring and so on.

Reading one pager should feel like reading a story, same as reading a well written code imho, starting with an author and an overview, what is involved in the system and what is not (the scope) and down to details. It must be open to everyone in the organisation, anyone should be able to easily find it, read it, comment on it and could reach out to the author/s for any clarification.

The OnePager, at any point in time, must reflect the current state of the approach taken to build the system. Depending on the scope and size of the system the architecture description may have a single a very high level representation of the system components or a more detailed approach. For a significant enough s system there wont be more than Context diagrams (if we assume [C4 Model](https://c4model.com)). For example mentioning the presence of a pipeline to deliver the code to a target deployment environment can be a component in the context and described in a single paragraph. Later on each of these high level components of the architecture will have their own OnePager going into the details.

## Conclusion

- Write less
- Write where it can be accessed and changed. 
- Stay consistent through the organisation
- Dont write a single document for all the role; write targeted documents for different roles
- Have clear ownership for the description



