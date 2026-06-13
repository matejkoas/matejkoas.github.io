---
layout: case
title: "Eliminating Operational Bottlenecks Through Automated Campaign Control"
date: 2026-06-12
categories: [Analytics Engineering, Automation, System Design, Python]
description: Designed and implemented an automated campaign monitoring system that eliminated manual operational bottlenecks, increased the number of active campaigns from 5–7 to 45–50, expanded platform coverage from 3–4 to 10–12 sources, and reduced manual effort by approximately 80%.
---
# Eliminating Operational Bottlenecks Through Automated Campaign Control

*Designed and implemented an automated campaign monitoring system that eliminated manual operational bottlenecks, increased the number of active campaigns from 5–7 to 45–50, expanded platform coverage from 3–4 to 10–12 sources, and reduced manual effort by approximately 80%.*

## Executive Summary

The organization managed large-scale information distribution campaigns across multiple external platforms. Campaign performance was measured against strict daily KPIs, including impression targets and engagement metrics. However, the existing operational model relied heavily on manual monitoring, spreadsheet-based calculations, and repeated interventions throughout the day. As the number of campaigns increased, this approach became increasingly difficult to sustain.

A key business objective was to expand the number of simultaneously running campaigns in order to improve traffic acquisition quality and increase audience reach. However, operational limitations prevented such scaling. Monitoring campaign performance, controlling pacing, and reacting to deviations required significant manual effort, creating a bottleneck that constrained further growth and increased the risk of overspending.

To address this challenge, I designed and implemented an automated campaign control system that continuously monitored campaign statistics across multiple platforms, evaluated them against predefined thresholds, and automatically adjusted campaign states when required. The solution operated on a scheduled basis, significantly reducing the need for manual oversight while preserving operational flexibility.

As a result, the number of simultaneously active campaigns increased from 5–7 to 45–50, while the number of integrated platforms expanded from 3–4 to 10–12. Manual effort associated with campaign monitoring and operational adjustments was reduced by approximately 80%, allowing the team to focus on higher-value analytical and optimization activities. Improved pacing control also enabled more efficient utilization of available budgets and contributed to greater operational stability throughout the reporting period.

**Key outcomes**:

- Increased the number of simultaneously active campaigns from 5–7 to 45–50
- Expanded platform coverage from 3–4 to 10–12 sources
- Reduced manual operational effort by approximately 80%
- Improved consistency of campaign pacing against predefined impression targets
- Enabled the team to allocate more time to analytical and optimization tasks
- Established a scalable operational foundation for future automation initiatives

## Business Context

The team was responsible for managing large-scale information distribution campaigns across multiple external advertising platforms. In addition to launching and maintaining campaigns, the team prepared daily performance reports and delivered statistics to the client through Google Sheets.

Operational performance was measured against predefined KPIs. The most critical metrics included achieving a target number of daily impressions and maintaining a minimum engagement rate across all active campaigns. Meeting these objectives required continuous monitoring of campaign performance and frequent operational adjustments throughout the day.

At the time, the operational process relied heavily on manual work. Campaign statistics were collected twice a day using a combination of different methods depending on the capabilities of each platform. Some platforms provided export functionality, while others required data to be copied directly from web interfaces. The collected information was then standardized manually within Google Sheets using a set of established procedures documented in internal instructions.

Following the preparation of daily reports, performance indicators were calculated using spreadsheet formulas. These calculations were used to identify campaigns that were underperforming or exceeding their expected pacing relative to predefined impression targets. Based on these observations, team members manually adjusted campaign settings, including campaign activation status and bidding parameters.

Although approximately 20–30 minutes were required to complete a single reporting cycle, monitoring activities were not limited to scheduled reporting windows. When campaign performance deviated significantly from expected pacing, additional checks were performed during the day to prevent overspending and maintain alignment with operational targets.

At the time of implementation, the team managed campaigns across 2–3 external platforms, with only 5–7 campaigns running simultaneously. Despite a larger pool of available campaigns, only a small proportion could be launched due to operational constraints and the increasing difficulty of controlling campaign performance as scale grew.


## Identifying the Bottleneck

One of the strategic objectives set by management was to increase the number of simultaneously active campaigns. Running a larger number of campaigns would improve traffic acquisition quality by allowing the team to diversify traffic sources and distribute volume across a broader set of campaign assets.

However, it quickly became apparent that the existing operational model could not support this level of growth. Each additional campaign introduced new monitoring requirements and increased the number of decisions that had to be made throughout the day. Since campaign pacing was controlled manually, scaling campaign volume resulted in a proportional increase in operational complexity.

The challenge was not limited to scheduled reporting activities. Although formal statistics collection took place twice a day, campaign performance often deviated significantly from expected pacing between reporting windows. To avoid overspending or underdelivery against predefined impression targets, team members were required to perform additional ad hoc checks directly within platform interfaces and manually adjust campaign settings when necessary.

This created a situation where the team spent a substantial portion of its time on repetitive operational tasks rather than analytical and optimization activities. As campaign volume increased, so did the probability of human error, delayed reactions, and inefficient resource allocation. The process became increasingly dependent on continuous human oversight.

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case12-1.png" alt="Identifying the Bottleneck" class="img-fluid w-50">
    </div>

During my onboarding period, I intentionally immersed myself in the existing workflow by following the same written and video instructions used by the rest of the team. By performing every step manually, I gained a detailed understanding of the operational process and experienced firsthand the limitations imposed by the current approach.

No specific incident triggered the initiative for change. Instead, the bottleneck became evident through day-to-day operations. The existing workflow was functional but inherently difficult to scale. It became clear that sustainable growth would require a fundamental shift away from manual control toward a more automated operating model.

At this stage, the key question was no longer how to optimize individual tasks, but how to redesign the process so that campaign growth would not automatically translate into increased operational workload.


## Designing the Solution

The first opportunity for improvement emerged from the daily reporting process itself. Collecting statistics from multiple platforms was repetitive, time-consuming, and heavily dependent on manual actions. As an initial step, I developed a local Python utility that automated the retrieval and consolidation of campaign statistics from the available platforms. Although some manual work remained, the time required for a single reporting cycle decreased from approximately 20–30 minutes to around 10 minutes.

While this improvement addressed part of the operational burden, it did not solve the underlying scalability problem. The real bottleneck lay in the need for continuous monitoring and manual intervention throughout the day. Campaign performance had to be checked repeatedly, and decisions regarding campaign activation, pausing, and bidding adjustments depended on the availability and attention of team members.

After analyzing the workflow, I concluded that the decision-making process itself could be automated. Since campaign groups operated against predefined impression targets, it became possible to formalize the logic that team members were already applying manually. The central idea was straightforward: campaigns should automatically stop once their corresponding impression thresholds had been reached and resume when conditions allowed.

Implementing such a mechanism required much more frequent access to performance data than the existing reporting process could provide. A locally executed script was no longer sufficient. The solution needed to operate independently of individual workstations, run continuously throughout the day, and evaluate campaign performance at regular intervals without human involvement.

To achieve this, I designed a server-side monitoring service that periodically collected campaign statistics from multiple external platforms, aggregated them according to predefined campaign groups, compared the current performance against operational thresholds, and automatically adjusted campaign states when required.

The proposed approach received organizational support, and the necessary infrastructure resources were made available. Existing server environments maintained by the company's infrastructure teams enabled the deployment of the monitoring service as a continuously running component within the broader operational ecosystem.

The objective was not simply to automate isolated tasks. The goal was to redesign the operating model itself so that growth in campaign volume would no longer result in a proportional increase in manual effort. This shift from reactive manual control to proactive automated management became the foundation for subsequent operational scaling initiatives.


## System Architecture

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case12-2.png" alt="Identifying the Bottleneck" class="img-fluid w-75">
    </div>

The monitoring system was designed as a lightweight server-side service operating on a fixed schedule. Its primary responsibility was to continuously evaluate campaign performance against predefined operational thresholds and execute the appropriate control actions without requiring manual intervention.

Every five minutes, the service initiated a monitoring cycle consisting of several sequential stages.

First, campaign statistics were retrieved from multiple external platforms through their respective APIs. Since each platform exposed data differently, dedicated integration modules were developed to standardize the collection process and convert the retrieved information into a unified internal structure.

The collected statistics were then consolidated into a single dataset containing current-day performance metrics for all tracked campaigns. Campaigns belonging to the same operational direction were grouped together regardless of the platform on which they were running. This allowed performance to be evaluated at the business level rather than at the individual campaign level.

Target impression thresholds and campaign tracking rules were maintained externally in Google Sheets. One worksheet stored daily impression limits for each operational direction, while another defined campaign membership and tracking status. This approach enabled non-technical team members to adjust operational parameters without requiring changes to the application code.

During each monitoring cycle, the system compared the aggregated number of impressions for every campaign group against its corresponding threshold value. If the predefined limit had been reached or exceeded, all campaigns associated with that group were automatically paused. Otherwise, the campaigns remained active or were resumed if previously stopped.

An important design decision was to avoid maintaining complex state information within the application. Rather than tracking historical campaign states internally, the service recalculated the required action during every monitoring cycle based on the latest available data. This simplified the implementation, reduced the risk of state inconsistencies, and improved the maintainability of the solution.

The resulting architecture transformed campaign management from a manually supervised process into an automated feedback loop capable of operating continuously throughout the day. By separating operational rules from implementation details and relying on periodic evaluation instead of persistent state management, the system remained both flexible and resilient as new platforms and campaigns were introduced.

**High-level workflow:**

1. Retrieve campaign statistics from external platforms
2. Standardize data into a common format
3. Aggregate metrics by operational campaign groups
4. Load impression thresholds and tracking rules from Google Sheets
5. Compare current performance against predefined limits
6. Automatically pause or resume campaigns when necessary
7. Repeat the monitoring cycle every five minutes


## Technical Challenges

Implementing the monitoring system required addressing several technical challenges related to data acquisition, platform heterogeneity, reliability, and operational safety.

### Integrating Multiple APIs

One of the primary challenges was the lack of consistency across external platforms. Each platform exposed its functionality differently, requiring dedicated integration logic.

Some platforms allowed campaign statistics to be retrieved directly through a single request, while others required a multi-step process involving campaign discovery followed by separate statistics requests. Authentication mechanisms also varied considerably, ranging from static credentials to short-lived access tokens obtained through dedicated authorization endpoints.

As a result, a unified abstraction layer had to be developed to standardize data retrieval despite differences in API design.

### Platform-Specific Limitations

Not all platforms were technically compatible with the proposed monitoring model.

Certain providers imposed restrictions on reporting frequency, allowing campaign statistics to be generated only once per hour. Since the monitoring system relied on five-minute evaluation intervals, these limitations prevented such platforms from being incorporated into the automated control loop.

In practice, this meant that technical feasibility became an important factor when selecting platforms suitable for operational scaling.

### Reliability of External Data Sources

The monitoring logic depended on receiving accurate and timely statistics from multiple independent systems. However, API failures, temporary service interruptions, and delayed report generation occasionally resulted in incomplete datasets.

To mitigate these risks, retry mechanisms were introduced within data retrieval procedures. In cases where statistics could not be obtained successfully after multiple attempts, the monitoring cycle recorded the failure and proceeded to the next iteration.

Although these situations occasionally led to temporary discrepancies in campaign control decisions, the recurring execution model ensured that subsequent successful iterations corrected the system state automatically.

### Safe Handling of Control Actions

Another challenge involved executing campaign state transitions safely.

External platforms sometimes returned errors when attempting to activate campaigns that were already running or pause campaigns that had already been stopped. While these situations did not represent true failures from a business perspective, they initially generated unnecessary operational noise.

The control logic was therefore refined to treat such scenarios as expected conditions rather than exceptional events. This approach improved the robustness of the system and significantly reduced false-positive error reporting.

### Architectural Trade-offs

An important architectural decision involved determining whether the application should maintain persistent knowledge of campaign states.

The initial design considered storing and tracking state information internally in order to execute only the strictly necessary actions during each monitoring cycle. However, this approach introduced substantial complexity and increased the likelihood of synchronization issues between the monitoring service and external platforms.

After evaluating the trade-offs, a simpler stateless strategy was adopted. Instead of relying on historical state information, the system recalculated the required action during every execution cycle based solely on current operational data.

This decision reduced implementation complexity, improved maintainability, and accelerated delivery without compromising the effectiveness of the overall solution.

### Future Reliability Enhancements

While the monitoring service proved effective in production, several opportunities for further improvement were identified.

One enhancement currently under consideration involves storing validated impression counts within a database and updating them only when newly retrieved values exceed previously recorded totals. Such an approach would introduce an additional safety layer capable of mitigating the impact of temporary reporting inconsistencies originating from external platforms.

These observations reinforced an important engineering principle: in distributed systems dependent on third-party services, resilience is often achieved not by eliminating failures entirely, but by designing mechanisms that tolerate and recover from them gracefully.


## Results and Business Impact

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case12-3.png" alt="Identifying the Bottleneck" class="img-fluid w-75">
    </div>

The implementation of the automated monitoring and control system fundamentally changed the way campaign operations were managed. By removing the dependency on continuous manual supervision, the team was able to shift its focus from repetitive operational tasks toward analytical and optimization activities.

The most significant outcome was the ability to scale campaign operations without a corresponding increase in operational workload. Prior to automation, only 5–7 campaigns could be managed simultaneously while maintaining acceptable control over pacing and spending. Following implementation, the number of concurrently active campaigns increased to approximately 45–50.

The monitoring framework also enabled the integration of additional external platforms into the operational ecosystem. As the monitoring logic proved stable, new platform integrations were gradually introduced, increasing the number of actively utilized sources from 3–4 to 10–12. This diversification improved operational flexibility and expanded the available inventory pool.

Improved pacing control produced measurable efficiency gains. Because campaign performance could now be monitored continuously throughout the day, the team was able to identify situations where campaigns consistently reached their impression targets too early. In such cases, bidding strategies could be adjusted to distribute delivery more evenly over time, allowing similar impression volumes to be achieved with more efficient budget utilization.

Automation also generated substantial productivity improvements. The combination of automated reporting procedures and server-side campaign control reduced the amount of manual operational work associated with this process by approximately 80%. Time previously dedicated to monitoring activities, repeated status checks, and manual interventions became available for other analytical initiatives and cross-functional projects.

Beyond quantitative improvements, the solution introduced a more scalable operating model. Growth in campaign volume no longer translated directly into increased operational burden. Instead, campaign expansion became primarily a technical challenge addressed through platform integrations and rule configuration rather than through additional manual oversight.

**Key outcomes:**

* Increased the number of simultaneously active campaigns from **5–7 to 45–50**
* Expanded the number of integrated external platforms from **3–4 to 10–12**
* Reduced manual operational effort by approximately **80%**
* Improved campaign pacing consistency against predefined impression targets
* Enabled more efficient budget utilization through data-driven bid adjustments
* Freed up team capacity for analytical and optimization activities
* Established an operational framework capable of supporting future growth without proportional increases in workload

The project demonstrated that relatively simple automation principles, when applied to well-understood operational constraints, can produce substantial business impact. Rather than optimizing isolated tasks, the solution addressed the underlying process bottleneck, enabling sustainable scaling of campaign operations.

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case12-4.png" alt="Identifying the Bottleneck" class="img-fluid w-100">
    </div>

## Future Improvements and Lessons Learned

Although the monitoring system successfully addressed the primary operational bottleneck and continues to support daily activities, the implementation process revealed several opportunities for future enhancement.

One area identified for improvement involves strengthening the resilience of the monitoring logic against inconsistencies originating from external platforms. The current implementation relies on the most recently retrieved statistics during each execution cycle. While the recurring five-minute schedule helps correct temporary discrepancies automatically, an additional persistence layer could provide further protection against incomplete or delayed reporting.

A potential enhancement would involve storing validated impression counts within a database and updating those values only when newly retrieved statistics exceed previously recorded totals. Using persisted values as the reference point for threshold evaluation would reduce the likelihood of undesirable control actions caused by temporary API failures or partial responses.

Another direction for future development is the expansion of automated decision-making capabilities beyond campaign activation status. The monitoring framework has already demonstrated that operational rules can be translated into reliable automated processes. Extending this approach to campaign bidding strategies and budget management represents a natural next step toward a more sophisticated optimization system.

Some of these capabilities have already been partially implemented within specific platform integrations, validating the feasibility of a broader rollout in the future.

Additional opportunities also exist in the area of operational interfaces. During the evolution of the system, the idea emerged to provide business users with a dedicated web application for managing campaign states and operational parameters. Although this initiative has not yet been prioritized due to overlapping development efforts within the organization, it remains a promising direction for improving accessibility and reducing dependence on spreadsheet-based workflows.

Beyond the technical outcomes, the project reinforced several important engineering principles.

First, meaningful automation begins with a deep understanding of existing processes. The effectiveness of the final solution depended not only on technical implementation but also on firsthand experience with the operational challenges faced by the team.

Second, the simplest solution is often the most sustainable. The decision to adopt a stateless control model significantly reduced implementation complexity while maintaining the reliability required for production use.

Finally, this experience demonstrated that the greatest value of automation frequently lies not in accelerating individual tasks, but in removing systemic constraints that limit organizational growth. By redesigning the operating model rather than optimizing isolated activities, it became possible to scale campaign operations without increasing the corresponding manual workload.

The system continues to evolve, but its core objective remains unchanged: enabling teams to focus their attention on analysis, decision-making, and strategic improvements instead of repetitive operational execution.




