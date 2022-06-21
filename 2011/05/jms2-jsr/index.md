# Brief overview of JSR 343: JavaTM Message Service 2.0

 Well, as many of us already know Oracle submitted the JSR for Java EE 7 which is sort of an umbrella JSR for many update in already existing specifications, new versions of some JSRs and some completely new JSRs which will be part of the grand [Java EE 7 - JSR 342](http://jcp.org/en/jsr/detail?id=342). One of these JSRs is the [JSR 343](http://jcp.org/en/jsr/summary?id=343) which introduces a new version of JMS into the platform as an evolution of its previous version, JSR-914, and will unify the JMS usage with what added to the platform in the past 8 years. The following represent some very simple usecases of JMS in the enterprise while complex multiphase transactional usecases are not unusual when MDBs and XA data sources are involved.

*   ![](http://download.oracle.com/javaee/5/tutorial/doc/figures/jms-programmingModel.gif "JMS API architecture")JMS itself is for asynchronous communication and widely used to communicate some execution instructions from one node or point to another or a set of other points. For example long running queries can be queued using a JMS queue to get processed by another point in the system while the query client is not blocked for the query result.

*   Or it can be used to communicate a common set of instructions to many interested parties which may or may not be around the communication happens, durable subscriptions and persisted topics. For example when clients need to get an ordered set of update messages to update a localcache when they get online after some times. Each client will get its own copy of messages it should receive when getting online.

JMS API provides enough functionalities to realize most of our design out of the specification and the minor features and functionalities not included in the JSR while required by some designs are covered by the vendor specific pack of enhancement and tweaks provided in the broker level and through the vendor specific API. You may ask if the current JMS API provides all we need, why a new JSR should be put on the table, the answer mainly relies on the theme for Java EE 7 which is making Java EE more cloud friendly and sort of cloud enabled by nature rather than by product. The details of JMS 2.0 spec goals are listed at the JSR [homepage](http://www.jcp.org/en/jsr/detail?id=343 "homepage") but a brief list can be seen as follow:

*   Community requested features and enhancements.
*   Make the JSR more cloud friendly based on how Java EE 7 will define "to be cloud friendly"
*   Cleanup of some ambiguity in the relation of JMS with other Java EE specs.
*   Make the API easier to use, more annotations and more generics will be involved for the least of the things or maybe reducing number of boxes and lines in the aove figure  could help many to start with the API faster.
*   Make necessary changes to benefit from the JSR-299 or Contexts and Dependency Injection to easier and more unified use of API.

In the follow up posts I will iterate over each one of these bullet points in more details. I am member of the JMS 2.0 expert group but this post or any other post in my personal blog does not reflect the expert group opinion or the opinion of my employer on the subject unless you could not see this paragraph at the end of the post :-).

