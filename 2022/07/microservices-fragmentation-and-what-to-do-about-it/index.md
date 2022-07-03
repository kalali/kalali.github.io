# Microservices Runtime Fragmentation


## Fragmentation of Java runtimes in a microservices-based architecture

There are many many differences between the monolithic architecture and microservices-based architecture. One of them is the number of independently deployed bits of software, with a monolith there are usually many replicas of the same distribution running on many machines, virtualized or not. With the Microservices the number of independently deployed software systems grows as the different contexts raise... or an already existing context boundary grows enough to be split. So many independent teams and deployments will likely results in different versions of libraries, charts, runtimes, and so on being present in the production environment. Below is a snapshot of what we had in production at one point, it shows what percentage of microservices are running on what Java distribution and version.

{{< echarts >}}

title:
  text: Java versions in use
  subtext: Percentage of each JRE version in production
  left: center
legend:
  orient: vertical
  left: left
backgroundColor: '#0000'
tooltip: 
    trigger: 'item'
    formatter: '{b} : ({d}%)'
series:
  - namename: JRE fragmentation
    type: pie
    radius:
      - 40%
      - 70%
    avoidLabelOverlap: true
    label:
      show: false
      position: center
    emphasis:
      label:
        show: false
        fontSize: 14
        fontWeight: bold
    labelLine:
      show: true
    data:
      - name: 'Eclipse Adoptium,18.0.1+10'
        value: 74
      - name: 'Eclipse Adoptium,17+35'
        value: 19
      - name: 'Eclipse Adoptium,17.0.3+7'
        value: 216
      - name: 'Eclipse Foundation,11.0.12+7'
        value: 5
      - name: 'AdoptOpenJDK,11.0.10+9'
        value: 1
      - name: 'Red Hat, Inc.,11.0.12+7-LTS'
        value: 12
      - name: 'Amazon.com Inc.,18.0.1+10-FR'
        value: 2

{{< /echarts >}}


As I mentioned above, it may look fragmented, but this level of fragmentation is accepted for us and if we manage to keep it at this level over the lifetime of the service we within the accepted range. Almost 70% of the workload is running on Java 17 (current LTS) less than 5% running the previous LTS (Java 11) and some services are running the latest release (Non-LTS) which we accept in the ecosystem.

### What causes fragmentation to begin with?
Sometimes fragmentations are positive, like a technical debt that can be a good debt. The same goes for fragmentation. If the different segments are having a purpose, for example, a small early adaptor group, a majority of steady progressors, and a small group on the previous version; it can be considered ok. Most often there is freshness for previous, current, and next versions of software systems. And having the majority of your workload on the current TLS of any platform is a sign of good management.

Bad fragmentation can be the result of a bigger organizational issue or a smaller team/service owner neglect. Some causes of fragmentations are:

- No strategic direction by the organization for dependency hygiene
- Missing architectural design and implementation for easy dependency management for different dependency layers
- No automation for detecting an update and/or creating a pull request for it
	- The pull request usually means some tests are executed and there is a reasonable level of certainty that everything works fine with this new version
- Lack of prioritization by the team to accept such PRs due to other usually urgent! work
- Missing (sense of) clear ownership for services
- etc.


We are mainly using a combination of the following to encourage the service owners adopting to the latest JDK versions:

1. Use Renovate Bot to scan repositories and submit pull requests 
2. Use Prometheus to gather version information from every running pod and if any service lagging too far behind generate alerts
3. Tie the above metrics to our DORA interpretation and generate dashboard and alerts based on that
4. Gamification of deploying to production and uptaking renovate PRs to encourage staying up to date
5. Announcement when there is somethign urgent and teams must accept the PRs 


Some description for each of the above elements follows:
### Renovate Bot
The [Renovate bot](https://github.com/renovatebot/renovate) can scan a variety of dependencies descriptors and submit a pull request if an update is available. The bot scans all the repositories, that are not labeled to opt-out,  and submits an update for the JRE version. If the build is green and the service owner sees it fit they will merge the changes. There are plenty of examples on how to do dependency managemnt using renovete for different platforms and also how to extend the rules for anything that is not already supported.

### Prometheus
As the platform team, we encourage everyone to use a metrics-related library that we built. Among other things the library exposes some versions and builds information as a metric that is scraped by [Prometheus](prometheus.io). The metrics go and form a dashboard for visualization and exploration as well as providing possibilities to derive other analytics and alerts. Each framework has a way to expose this. For example for Spring boot it is described in this [spring boot github issue](https://github.com/spring-projects/spring-boot/issues/12348). The idea is there, few classes need to be implemented and contribute to a metric.



### DORA metrics
We have an interpretation of [DORA Metric](https://cloud.google.com/blog/products/devops-sre/using-the-four-keys-to-measure-your-devops-performance) and some custom tooling that does the measurement and publish the metrics about teams. We have more work to do about DORA metrics as measuring an accurate *Change Failure Rate* and *Time to Restore Services* by automation is more involved. Measuring some of the metrics can be done using the CI/CD metadata, the change tracking systems (for example ServiceNow or similar solutions), the package registry, source versioning solutions (if you have a specific workflow that can provide the information), the ApplicationInfoMetrics as described above and so on. But generally whatever source is being picked must be the absolute source of truth that rest of the organization would use (so audit trails, and other numbers match at the end of the day)

### Gamification
We developed an in-house system that gather metrics from different sources (change management, Prometheus, etc.) and publishes some statistics about teams' achievement and who has more points depending on the type of achievements, etc. That is an encouraging way to keep things fun and yet relevant to our overall goal of staying up to date with dependencies and preventing stale, abandoned services in production.

### Announcements
There is an announcement channel that everyone is tuned to. If there is something urgent that must be taken care of an announcement is sent out and teams are supposed to act. For example, a config issue in a sidecar, a bug in a library, etc. We are working to automatically creating incidents for these type of issues that needs a short reaction turnaround.
 
