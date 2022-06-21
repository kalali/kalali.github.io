# How to plan the architectural attributes?




Last week I was talking to a former colleague, he is running a SaaS startup now, and our conversation went around SRE, production, and reliability. We talked a lot about architectural attributes that relate or impact the incidents, from detection to recovery, and on to prevention. I thought to put some of my thoughts around architecture into a blog post since that was the core of our conversation. The next post I will cover more details on architecture for fault tolerance. 

## Architectural attributes, the -ities

I generally put different architectural attributes into two categories:

- **Internal**: for example operability, testability, supportability, upgradability, extensibility, etc.
- **External**: for example scalability, responsiveness, security, compliance, usability, availability, etc. )

The architectural attributes can be sliced and diced in different ways, I will probably write another slicing and decision making in another post.

At the end of the day, any attribute and quality of a software system are going to impact the users. But some attributes are more concerns of customers and users of the system while another group is more concerning those building and maintaining it (developers, support, QA, etc.). 

There is always a push and pull between different stakeholders on the importance and investment that should go into these two categories. For example, usability, availability, and responsiveness might be the product owner's focus while a technical stakeholder would also think, and advocate about investment in extensibility, supportability, and so on. Each stakeholder advocates for the attributes they better relate to.

These pushes and pulls get more interesting when other stakeholders are advocating for attributes, not that clearly understood by others. For example compliance and legal matters (I generally put this into the external category). Keeping the balance 
 

Deciding on which attributes from different categories should be the core is very much context-dependent. For example, deciding between yield and harvest would result in deciding whether reliability or availability should have a higher degree of importance. I approach deciding about architectural attributes is based on a few practices:

- I Talk to all stakeholders to form a clear understanding of what the software system is going to do. Why is it being built, or rebuilt? How is it going to be used and who is going to be the user/s.
- I think about three attributes as the heart of a system based on the above. Any other attributes form around these over time, without diminishing them 
- I associate a degree of importance with attributes contributing to architecture. These will be considered when the architecture is evolving or when trade-offs are being made for adding a new feature, etc. 
- Iterate over these as an understanding of the system is building up and evolving the system.

Deciding on the core attributes is like setting a strategy, decision being made around how the architecture could evolve is easier when you have some core attributes that cannot be diminished or ignored.

Next to the core attributes, there are some considerations that deferring or ignoring has the highest cost of reconsideration. For example, testability and operateability are important enough to be next to the core attributes. If a software system is not testable and operatable it is going to rot over time and a re-write will ensue.


Some examples to consider:

- For a regulated business attributes contributing to compliance are the top concerns. For example data integrity, audit-ability, access control, etc. Building anything, no matter how good, that is not compliant is unlikely to see the light of production.
- For a web-scale service the first items to consider are scalability, availability, and reliability, the rest of the attributes would form around these.
- Some systems may need very little attention to scalability but an extreme emphasis on safe operation, reliability, and upgradability. A medical device, a CNC machine, and so on.


Deciding on any architectural attributes should be deferred as long as it is not causing indecision. When the same subject comes up multiple times without a conclusive way forward it means there is a lack of strategy, being for the product or the organization. I would put having a decision about the architectural attributes of a product to be part of the product and in a larger scope, the organization's strategy.



