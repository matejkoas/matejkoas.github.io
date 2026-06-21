---
layout: case
title: "From Spreadsheets to a Data Platform"
date: 2026-06-21
categories: [Analytics Engineering, Automation, System Design, Python]
description: Designed and implemented an automated campaign monitoring system that eliminated manual operational bottlenecks, increased the number of active campaigns from 5–7 to 45–50, expanded platform coverage from 3–4 to 10–12 sources, and reduced manual effort by approximately 80%.
---

# From Spreadsheets to a Data Platform

Google Sheets are amazing.

They're flexible, familiar, and incredibly useful. Given enough patience, you can build dashboards, reports, calculations, and even small applications inside them.

Like many internal tools, our reporting system started there.

And for quite a while, it worked remarkably well.

Then the project grew.

More advertising networks appeared. More campaigns were launched. More data had to be collected every day. More people started working with the same spreadsheets.

One spreadsheet became several.

Several became dozens.

At some point, the spreadsheets were doing everything except making coffee.

This article is not about why Google Sheets are bad.

They aren't.

It's about what happens when a solution that was originally designed to solve one problem gradually becomes responsible for solving ten.

In my [previous case study](#case12-eliminating-operational-bottlenecks), I described how I automated campaign monitoring and bid management for a large number of advertising campaigns. That project significantly reduced manual work and gave the team much better operational control.

Once that system was running reliably, a new question naturally appeared:

**What if the biggest bottleneck was no longer campaign management, but the reporting infrastructure itself?**

That question became the starting point for the next stage of the project.

Not another automation.

A complete redesign of how data was collected, stored, validated, and analyzed.


## The System That Almost Worked


{% include case-image.html
file="assets\images\case13-1.png"
alt="Old reporting architecture"
width="75"
%}

One of the easiest mistakes to make in engineering is to look at an old system and ask:

*"Who built this?"*

A much better question is:

*"Why did it make perfect sense when it was built?"*

Our reporting process was actually a good example of a system that evolved naturally.

Statistics from multiple advertising networks were collected twice a day and stored in Google Sheets. Over time, the spreadsheets became the central source of information for the entire team. Management dashboards were built there, colleagues worked with the data daily, and the customer also received reports in the same format.

There was nothing fundamentally wrong with this approach.

In fact, it served us well for a long time.

The problems only appeared as the project continued to grow.

Every new advertising network added another stream of data.

Every new campaign increased the reporting volume.

Every new feature made the spreadsheets a little larger, a little slower, and a little more difficult to maintain.

Eventually, collecting statistics became a routine task performed twice every day—including weekends.

I had already reduced much of this manual work by developing a local application that automated most of the collection process for our team. It saved a considerable amount of time, but it didn't change the underlying architecture.

The system was still built around spreadsheets.

At first glance, the obvious solution seemed to be full automation.

Simply collect the data automatically and push everything into Google Sheets.

Problem solved.

Except it wasn't.

The more I thought about it, the more it became clear that automating the existing workflow would mostly automate its weaknesses.

The bottleneck wasn't the people anymore.

It was the architecture.


## Why We Decided Not to Automate Google Sheets

The obvious solution seemed simple.

Automate the reporting process completely.

Collect statistics from every advertising network on a schedule, write everything into Google Sheets automatically, and remove the remaining manual work.

From a distance, it looked like the logical next step.

The more we discussed it, however, the less attractive the idea became.

> **Automating the wrong architecture only makes the wrong architecture faster.**

The challenge wasn't collecting the data.

The challenge was trusting it.

Our reporting process had one important detail.

Every evening, new statistics were appended to the reports.

The following morning, those same records had to be updated with finalized numbers.

That meant the pipeline had to distinguish between inserting new records and updating existing ones. A small mistake could easily create duplicate rows or overwrite valid data.

Technically, all of these problems could be solved.

But there was another issue.

Several people worked with the same spreadsheets every day.

New formulas appeared.

Existing formulas were modified.

Additional columns were added whenever someone needed a quick calculation.

Google Sheets were no longer just storage.

They had quietly become both the database and the application.

The more business logic moved into spreadsheets, the more fragile the entire reporting process became.

Even routine maintenance started carrying unnecessary risk.

At that point, we realized something important.

The goal wasn't to automate Google Sheets.

The goal was to move Google Sheets out of the critical path.

Instead of making spreadsheets smarter, we needed to build a system where spreadsheets were simply another consumer of reliable data—not the place where that data lived.



## Choosing a New Architecture

Once we agreed that Google Sheets should no longer be the center of the reporting system, the next question was obvious.

Where should the data live instead?

Interestingly, the idea didn't come out of nowhere.

Another engineering team in our company had already gone through a similar journey.

They had started with MySQL, but as their datasets continued to grow, the database eventually became a bottleneck. The team later migrated to ClickHouse to handle analytical workloads more efficiently.

Our department processed several orders of magnitude less data, but their experience demonstrated something important:

A dedicated database wasn't an unnecessary complication.

It was the natural next step in the evolution of a growing data platform.

Management supported the idea almost immediately and offered to provision a MySQL database for our team.

At first, we pushed back.

My colleague and I believed PostgreSQL would be a better fit. It offers excellent analytical capabilities, flexible data types, and it's often the first choice for modern data engineering projects.

Technically, our arguments were reasonable.

Operationally, they weren't.

The response from management was refreshingly pragmatic.

MySQL was already the company's standard.

Infrastructure was already in place.

Database administrators knew how to support it.

Backups, monitoring, maintenance, and deployment were already part of existing processes.

"If you choose MySQL," they told us, "the database can be ready almost immediately. If something goes wrong, the entire infrastructure team already knows how to help."

It was difficult to argue with that.

We weren't building a database for a résumé.

We were building a production system that other people would have to support.

Looking back, I still think PostgreSQL would have been an excellent technical choice.

But choosing MySQL was the better engineering decision.

Sometimes the best technology isn't the one with the longest feature list.

It's the one your entire organization is already prepared to operate.

Within a short time, the new database was provisioned, and we finally had a clean foundation on which the new reporting pipeline could be built.



## Building the Pipeline

Once the database was ready, the next challenge was designing the data pipeline itself.

The objective was straightforward:

Collect data from multiple advertising networks, transform it into a unified format, store it reliably, and make it immediately available for analysis.

Simple on paper.

Much less simple in production.

Rather than building one large application responsible for everything, I decided to split the pipeline into several independent stages.

Each stage had a single responsibility.

The first stage communicated with advertising network APIs and collected raw statistics.

The second transformed the incoming data into a common structure. Different networks exposed different fields, naming conventions, and reporting formats, so normalization became an essential part of the pipeline.

The third stage stored the processed data in MySQL.

Keeping the raw collection separate from the transformation logic made the entire system significantly easier to maintain. When one network changed its API, only the corresponding integration needed to be updated instead of rewriting the entire pipeline.

At this point, Google Sheets didn't disappear.

Instead, they became one of the outputs.

This was an intentional decision.

The existing reporting workflow continued to operate exactly as before, allowing both systems to run in parallel while the new architecture was being tested.

This approach dramatically reduced migration risk.

Another important design decision was to avoid unnecessary API requests.

Previously, the local reporting application communicated directly with advertising networks every time statistics were needed.

Once the database became the central storage layer, that workflow changed.

The server collected the data once.

Everything else—including local tools, reports, and dashboards—worked with the database instead.

This reduced API traffic, improved response times, and established a single source of truth for the entire reporting system.

The architecture was still evolving, but the foundation was finally in place.



## DataLens Wasn't the Goal

At this stage, we finally had something we had never had before:

A centralized, structured, and continuously updated dataset.

Only then did it make sense to think about visualization.

DataLens wasn't the reason we built the new architecture.

It was a consequence of having reliable data.

The first version of the dashboard was intentionally conservative.

It reproduced almost everything our existing Google Sheets dashboard already displayed.

The objective wasn't to introduce new visualizations.

It was to prove that the new data platform produced exactly the same business metrics as the existing reporting process.

Once that confidence was established, the real improvements began.

Since all data was now stored in a relational database, creating new reports became dramatically easier.

Instead of adding more formulas to already overloaded spreadsheets, we could build dashboards directly on top of normalized data.

Over time, the dashboard evolved far beyond its original purpose.

We introduced breakdowns by advertising network.

Campaign-level analytics.

Traffic type segmentation.

Performance metrics grouped by internal indexes.

And perhaps the most valuable addition: hourly impression reports for every campaign.

That report quickly became one of the team's primary operational tools.

Instead of simply checking whether campaigns reached their daily impression targets, we could now see **when** they reached them.

This seemingly small detail turned out to be extremely useful.

If a campaign exhausted its daily budget by noon, while still meeting its impression target, it often meant we were bidding more aggressively than necessary.

Lowering the bid allowed the campaign to distribute impressions more evenly throughout the day while reaching the same target at a lower cost.

The automated campaign monitoring system described in my previous case study handled impression limits automatically.

The new dashboard provided the missing context needed to optimize bidding strategy.

Together, these two systems complemented each other remarkably well.

One executed operational decisions automatically.

The other helped us make better decisions in the first place.

Looking back, that's probably the biggest lesson from this stage of the project.

Dashboards don't create insights.

Well-structured data does.

The dashboard simply makes those insights visible.



## The Unexpected Discovery

By this point, we had no intention of replacing the existing reporting process overnight.

Instead, we allowed both systems to run side by side.

The original workflow continued producing reports exactly as before.

The new pipeline collected the same data independently, processed it automatically, and stored it in the database.

The plan was simple.

If both systems produced identical results over time, we could confidently move forward with the migration.

Then something unexpected happened.

The numbers didn't always match.

Not often.

But often enough to make us uncomfortable.

At first, it was tempting to assume that one of the systems was simply "wrong."

The reality turned out to be much more interesting.

Some discrepancies came from configuration mistakes.

For example, after connecting a new advertising network, one of its parameters hadn't been added to the transformation stage. The raw data was successfully collected, but part of it never reached the final reporting tables.

Other differences exposed bugs inside the pipeline itself.

And in a few cases, the discrepancies originated from the existing reporting workflow rather than the new one.

Without running both systems in parallel, we probably wouldn't have discovered any of these issues.

Ironically, the migration process became one of the best testing tools we could have built.

Every mismatch was treated as a signal rather than a failure.

Instead of asking,

*"Which system is correct?"*

we started asking,

*"Why are they different?"*

That small change in perspective transformed the migration into an ongoing validation process.

Each discrepancy improved the quality of the pipeline.

And every resolved inconsistency increased our confidence in the data.

Looking back, running both systems in parallel wasn't just a safer migration strategy.

It became one of the most valuable engineering decisions in the entire project.



## Validation Became Part of the Architecture

The parallel run revealed something we hadn't anticipated.

The pipeline didn't just need to collect, transform, and store data.

It also needed to continuously prove that the data could be trusted.

Validation was no longer a debugging tool.

It became part of the architecture.

Every interaction with an advertising network already returned more than just data.

Each request also produced a status describing the result of the operation.

- **Success** — the request completed successfully and the data was considered valid.
- **Warning** — the request completed successfully, but no statistics were returned. This wasn't necessarily an error; sometimes there were simply no active campaigns in a particular network.
- **Error** — the request failed and required investigation.

Instead of hiding these states inside application logs, we decided to expose them.

The pipeline started writing execution information into dedicated log tables.

DataLens, in turn, displayed the current health of the data collection process in real time.

For the first time, we could answer two completely different questions independently:

- **What does the business data say?**
- **Can we trust the business data?**

Those are not the same question.

A dashboard can display beautiful charts while quietly relying on incomplete or outdated information.

By making validation visible, we gained confidence not only in the numbers themselves, but also in the process that produced them.

The validation layer is still evolving.

Today it focuses primarily on API responses and data collection.

The next step is to extend the same level of monitoring to every stage of the pipeline—from data transformation to loading and downstream processing.

Reliable data pipelines are not built by eliminating failures.

They are built by detecting failures early, making them visible, and ensuring they cannot go unnoticed.


## Where the Project Is Today

The migration is still in progress.

That may sound surprising after everything described above, but large engineering projects rarely move from one architecture to another overnight.

Today, both workflows continue to operate in parallel.

The existing Google Sheets-based reporting process remains the official workflow used to deliver statistics to the customer.

At the same time, the new data platform is already being used internally.

The server collects statistics automatically.

The database acts as the central storage layer.

DataLens provides operational dashboards.

Local tools now retrieve prepared data from the database instead of repeatedly requesting it from advertising network APIs.

The final step is replacing the customer-facing reporting workflow.

To make that possible, I developed a REST API using FastAPI, allowing external systems to retrieve reporting data directly from the new platform.

The API has already been completed and handed over to our infrastructure team for deployment.

Once it becomes part of the production environment, the remaining dependency on Google Sheets can finally be removed.

Until then, the parallel architecture serves another purpose.

It allows us to continue improving the platform without disrupting the existing reporting process.

New reports can be added.

Validation rules can be expanded.

The pipeline can continue evolving.

And every improvement can be verified before becoming part of the official workflow.

Looking back, I no longer think of this project as "moving data from Google Sheets into a database."

It became something much bigger.

It was the gradual transition from a collection of useful tools into a reliable data platform.

The technology mattered.

The dashboards mattered.

The automation mattered.

But the most valuable outcome was something less visible.

A system that the team could trust.
















