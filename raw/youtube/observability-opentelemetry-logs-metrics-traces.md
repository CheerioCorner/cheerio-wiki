---
title: "System Observability: Logs, Metrics, Traces & OpenTelemetry"
type: raw-transcript
created: 2026-08-30
video_id: umm-MyCl3Q4
url: https://www.youtube.com/watch?v=umm-MyCl3Q4
duration: 00:35:51
language: en
auto_generated: true
source_api: youtube_transcript_api
segments: 764
timestamp_format: "[MM:SS]"
---

# System Observability: Logs, Metrics, Traces & OpenTelemetry

> Duration: 00:35:51 | Segments: 764 | Language: en | Source: API (auto-generated)

## Transcript

[00:00] By the end of this video, you will understand exactly how to see inside a running system, what logs, metrics, and traces each really tell you, why they fall apart the moment one service becomes many, how OpenTelemetry ties them back together, and how to know your system is healthy before your users do.
[00:26] It is the full picture most engineers only have understand.
[00:31] Our app is a ride-share back end, and today it is a single service.
[00:37] When you hook a ride, that one service takes the request, finds a drivers, prices the trip, charges the card, and sends the confirmation.
[00:49] Watch one booking move through it.
[00:52] It flows through matching, then pricing, then payment, and then the confirmation step stales and it quietly fails. The rider is left waiting on a message that never arrives.
[01:08] Every dashboard stays green.
[01:10] CPU is fine, memory is fine, the request count looks normal, and this booking is still broken.
[01:19] You are staring at healthy charts and a real failure at the same time, and nothing on screen tells you why it happens. The dashboards you built are monitoring.
[01:33] Monitoring collects and displays data you decided to watch ahead of time.
[01:39] So, every chart answers a question you already thought to ask.
[01:45] That works beautifully when you predict the failure, and it leaves you blind when you did not.
[01:52] The useful way to think about it is known unknowns versus unknown unknowns.
[01:59] Monitoring handles the known ones. The failures you set a threshold for in advance. The broken booking is an unknown unknown. A state nobody built a chart for and being able to answer it anyway has a name.
[02:17] The name is observability.
[02:19] Observability means you can understand the system from the outside and ask new questions about how it's behaving without shipping new code first. If you can pull up the raw events for that one booking and ask why it failed right now with no deploy, you have it.
[02:41] If you would have to add a new dashboard and wait for data, you were only ever monitoring. The first raw signal is the log.
[02:50] A log is a timestamped message a service writes when something happens one line per event.
[02:59] Plain text doesn't scale though.
[03:02] One busy service is writing thousands of lines. There is no clean way to ask a question without fighting regular expressions.
[03:12] Structured logging fixes that.
[03:14] Each event becomes a set of key value fields usually JSON.
[03:20] So every field is something you can query directly.
[03:24] The booking ID, the status, the duration, they are all real fields now.
[03:30] Instead of words buried inside a sentence. So you go back to that failed booking and filter straight to it.
[03:39] One query on status equal failed and there is exact event for that request.
[03:45] You can finally see what happened to it.
[03:48] Although a single event still cannot tell you how often this happens or how slow things are running overall.
[03:58] That how often and how fast is exactly what metrics answer.
[04:03] A metric is a number aggregated over time.
[04:07] A rolled up total rather than one event and that's what makes it the cheapest signal you have. Three types cover almost everything.
[04:17] A counter only goes up like an odometer and it holds a lifetime total like bookings served.
[04:26] You almost never graph that raw number.
[04:30] What you want is how fast it's clamping.
[04:33] So you query its rate over a window and that one query sits behind every traffic panel and error rate alert in this video.
[04:44] Counters also resets to zero on restarts and the rate function expects that.
[04:51] Trying to make a counter goes down is the classic mistake and that's what a gauge is for.
[04:58] A gauge moves up and down for a current level like bookings in flight right now or queue depth or memory in use.
[05:08] The third one is the histogram and it is the one people get wrong.
[05:13] A histogram drops each measurement into a bucket so you can add the buckets up across every instance and read one P95 for the whole fleet. That P95 is estimated from the buckets.
[05:30] So it's only as honest as the boundaries you pick it. It is cosine the summary computes percentile on each machine and those can never be combined it because you cannot average percentiles.
[05:45] So for latency across a fleet, you want histograms. You can record any number.
[05:51] So, the real question is which ones matter. Google's answer is the four golden signals. Latency, traffic, errors, and saturation. If you could only watch four things on a user-facing system, those are the four. Two short hands make that practical. Red from Tom Wilkie is the request view.
[06:15] Rate, errors, and duration. One shape you put on every service.
[06:21] Use from Brendan Gregg is the resource view. For each resource like CPU or disk, you check utilization, saturation, and errors. They are not reveals. They are two angles on the same system. So, you run both.
[06:40] One warning on duration, track it as a distribution, never as a single average, because the average quietly hides your slow tail. The average booking can look perfectly fine while your P95 is the one waking people up. And that one slow booking is exactly what a trace can follow. A trace follow that one slow booking. Metrics and logs look at everything in aggregate or one event at a time.
[07:13] But, a trace is a request scoped, so it records the path a single request takes as it moves through your code. Picture the whole trip that one booking made, it drawn out from start to finish.
[07:29] Each step on that trip is a span.
[07:33] A span is one unit of work with a name, a start and end time, some attributes, and a status, a its own span ID.
[07:44] Stack the spans up and you get the trace.
[07:48] A tree of spans that all share one trace ID.
[07:52] With a root span at the top marking where the request begin.
[07:58] Every other span records the ID of the span that called it. And that parent link is what gives the tree its shape.
[08:07] Now, the failure is obvious. The confirmation span is the long red bar sitting right there in the waterfall.
[08:16] Telling you it ran far too long and then error it.
[08:20] It only turns red because the code recorded the error on that span. Had it coded the exception and it quietly returned it a default, you would be looking at a slow green bar instead. And that's the version of this bug that hides for weeks inside a single process, this is easy to draw.
[08:42] And the interesting question is what happens the moment that request leaves the process to call another service. So, we grow up.
[08:52] The monolith splits into real services.
[08:55] Rides, matching, pricing, payments, and notification.
[09:00] Each running on its own.
[09:03] Now that one booking it crosses the network from service to service. And each service only ever sees its own little piece of the trip.
[09:14] For the trace to survive that, the services have to pass context along.
[09:20] It is called context propagation.
[09:23] The caller writes the trace context into the outgoing request and the next service reads it back out and make its span a child of the callers.
[09:36] The agreed is W3C Trace Context and it is a couple of HTTP headers that every language and vendor understands.
[09:47] The main header is called it a trace parent. It carries the trace ID which stays the same on every hop plus the caller's span ID and a flag for whether this trace is being kept.
[10:03] You won't store every trace at real traffic and that flag rides along so every service makes the same keep or drop call rather than saving half a trace.
[10:16] Drop the pattern on even one hop and the trail snaps in two with the downstream spans floating loose and no parent to attach to.
[10:26] Pass it correctly and all those per service spans stitch back into one clean trace across the whole system.
[10:34] Open Telemetry does this for you by default using those same standards headers.
[10:41] There is one shape those headers don't cover.
[10:44] Notifications doesn't get called over HTTP.
[10:48] It gets a message on a queue and payments move on without waiting for it.
[10:55] By the time a worker picks that message up the span that queued it has already ended.
[11:01] So the new span cannot be its child. So Open Telemetry marks the two sides as a producer span and a consumer span and joins them with a span link instead.
[11:15] That link says both pieces of work belong to the same booking which is how the confirmation you never got still lands on the trace.
[11:25] The catch is that you now have three signals scattered across a lot of services and the real trick is using them together.
[11:35] You have probably heard logs, metrics, and the traces called the three pillars.
[11:42] When that idea first showed up, it was drawn as a three overlapping circles, not three separate towers.
[11:51] And the pillars label came along later.
[11:54] The value was never in having all three.
[11:58] It is in being able to move between them for the same request. Watch it plays out on our booking. The error log for that request carries its a trace ID.
[12:10] So, from that one log line, you jump straight to the full trace across all five services.
[12:17] That ID gets there because your logger is wired into the tracing context.
[12:24] Whatever span is active when you log, its IDs get copied onto the record.
[12:30] Wire that up once and every log line identify itself.
[12:35] Skip it and this jump simply doesn't exist, which is the most common way people find their correlation quietly missing.
[12:44] From the metric side, a spike on the latency chart carries an exemplar, a small pointer to one real request that landed in that bucket.
[12:55] And it takes you to the very same trace.
[12:59] There is a tempting shortcut worth avoiding. You may think to just tag the metric with a trace ID, but that would explode the number of series into the millions and wreck your metrics bill.
[13:13] The clean link is the exemplar, so your metrics stay cheap and still point straight at real traces.
[13:21] This is the whole idea. Observability is correlation.
[13:26] One request's trace ID ties its log, its metric, and its trace into a single story you can walk end-to-end.
[13:36] Doing that by hand across 30 services would be impossible.
[13:41] And that's the problem OpenTelemetry was built to solve. Before OpenTelemetry, every service was wired straight into one specific vendor's tools.
[13:52] Switch vendors or add a service in a new language and you are re-instrumenting the whole thing by hand. OpenTelemetry replaces that with one vendor natural standard for generating and shipping all three signals, traces, metrics, and logs the same way everywhere. It is a project under the CNCF, born from merging two earlier efforts into a single standard. That history is why it feels settled instead of experimental, and why just about every tool speak it now. OpenTelemetry is not a back end, and that's the single
[14:34] biggest thing people get wrong about it.
[14:37] It doesn't store your data or draw your dashboard. It generates and ships telemetry, and something else, like Prometheus, Grafana, or a paid vendor, stores it and shows it.
[14:51] Because it's natural, you instrument once and can point that same data at a different back end later without touching your services.
[15:02] Open up that pipe and there are a few pieces inside.
[15:06] In your app, the API is what you write your code against, and the SDK is the implementation that actually does the work. Leave the SDK out and the API just does nothing. So, no telemetry comes out at all.
[15:24] The data travels on a standard wire format called it OTLP over HTTP or gRPC.
[15:33] It usually lands in the collector, a separate process that receives your telemetry, runs it through processors, and then exports it.
[15:43] That is where you patch things for efficiency, stripe a sensitive field like a phone number, and send the same data out to more than one back end.
[15:54] The plumbing works now.
[15:56] And two more pieces decide whether the data is actually usable once it lands.
[16:02] Semantic conventions are agreed names for your attributes. So, something like the HTTP method is labeled the same across every tool.
[16:13] Attribute describes the operation, and a second set called it resource attributes describe whoever emitted it, which is where service name lives.
[16:24] Service name is the one field OpenTelemetry insist you set, and leaving it out is why telemetry shows up as unknown service, and a service map renders as one unlabelled blob.
[16:40] And telemetry reaches you two ways.
[16:43] Auto instrumentations cover the libraries you already use like HTTP and database calls with no code changes, while manual instrumentation is you adding spans for your own business logic that auto cannot see.
[17:01] Use both, and once it's all flowing, the next headache is that keeping every trace starts to get expensive.
[17:09] At real traffic, storing every trace is just too expensive. So, you sample, keeping a representative slice instead of all of it. The cheap way is head sampling. You decide to keep or drop right at the very start before anything has happened.
[17:28] Head sampling has one real weakness.
[17:31] Because it decides up front, it cannot know a trace is about to error. So, the exact one you wanted can get thrown away.
[17:41] Tail sampling fixes that by deciding at the end.
[17:45] It waits for the whole trace to finish, then keeps the ones that errored or ran slow.
[17:51] That power isn't free.
[17:53] To judge a whole trace, every span of it has to reach the same collector and wait in a buffer until the trace finishes.
[18:02] You get that by routing spans by trace ID. So, every span of one booking lands on the same collector, which holds the buffer and makes the calls.
[18:14] That still costs memory and delay storage by a few seconds.
[18:18] Although, it's a standard setup rather than a reason to avoid tail sampling.
[18:24] So, it is a straight trade. Head is cheap, but can miss the interesting request.
[18:31] Tail catches it, but you pay to hold everything. And the next volume problem is over on the metric side. On the metric side, the thing that bites you is cardinality, which is just how many distinct value a field can have.
[18:49] Status code has a handful of values.
[18:53] Region has a few dozens, but a field like a user ID has millions, and that gap changes everything.
[19:01] High cardinality is actually the point of observability. It is what lets you slice all the way down to a single user or a single booking out millions and answer, "Why did this one fail?" Instead of, "How's the average doing?" That ability to filter on the messy, unique fields is the whole game.
[19:24] The trap is putting one of those fields on a metric.
[19:28] Every unique combination of labels is its own time series. So, the moment you add user ID as a label, one tidy series explodes into millions and your bill goes straight along with it.
[19:43] It is the same reason you never tag a metric with a trace ID.
[19:48] So, the messy, unique fields go on the traces and events. Your metric labels stay small and exemplars link the two.
[19:59] Labels aren't the only place money leaks. The honest way to run this is a retention window per signal instead of keeping everything forever.
[20:09] Metrics are cheap enough to keep for a year if you rule older data into bigger buckets.
[20:16] Traces are already sampled, so a couple of weeks usually covers it.
[20:21] Logs surprises people because they are often the biggest bill of the three and the fix is to keep every error while sampling the routine success and the dropping the noisy ones at the collector.
[20:36] Which raises the obvious question, "What does healthy even means?" Healthy needs a definition and that's the job of these three.
[20:47] An SLI is the thing you measure, like the percentage of bookings that succeeded. An SLO is the target you set on it, say 99.90%.
[20:59] An SLA is that same target written into a contract with the real consequences.
[21:06] And the quick test is simple.
[21:09] If breaking it costs you money or credits, it's an SLA.
[21:14] Once you have a target, you get an error budget for free. Your error budget is 100% minus your SLO. So, it is the amount of failure you are allowed before users start to hurt. At 99.9% that thin silver at the top of the graph is your whole budget for the month.
[21:38] And that budget is a real countable number. Over a 30-day month, 99% lets you be down for 7.2 hours.
[21:49] 99.9 gives you 43 minutes.
[21:53] 99.99 gives you barely four.
[21:58] And that the drop is exactly why every extra nine costs so much.
[22:04] The budget also settles an argument.
[22:07] While there is a budget left, you keep shipping features. When it runs out, releases slow down or stop until you have earned it back.
[22:17] And because that rule is agreed ahead of time, it's a policy instead of a fight at 2:00 in the morning.
[22:25] The trap with alerting is drowning in it. When every small thing pages you, CPU at 81%, a poll at 90, a disk half full, you stop reading them and you miss the one that mattered. The fix is to page on effects rather than causes.
[22:44] In a busy kitchen, a burner running hot is a cause and orders coming out late is what diners actually feel.
[22:53] The burner can run hot all evening with every plate still on time, which is why paging on it wakes people for nothing.
[23:03] So, page when users are in real pain, like booking failing, and leave CPU and memory for debugging once you are awake.
[23:13] If the right reaction to a page is a shrug, it should never have fired.
[23:19] Tie the page to the error budget through burn rate, which is how fast you are spending it. At one times, a 30-day budget lasts 30 days.
[23:29] 40.4 is the number people code, and it's not arbitrary. Burning that fast for 1 hour spends 2% of a 30-day budget, which is worth waking someone for.
[23:44] Slower burns still matters, so a second tier pages on six times over six hours, and a gentler one over three days files a ticket instead.
[23:56] The trick is to make a long window and a short window both agree.
[24:01] So, you only fire on something real and sustain it.
[24:06] And because the short window recovers quickly, the alert clears a few minutes after the errors do, instead of nagging you for an hour.
[24:16] The page tell you something is wrong, but not which service it started in, and that's where the map comes in.
[24:24] That map is built straight from your traces.
[24:28] The old way is a hand drawn architecture diagram, and it is always a little out of date, wrong the day after you drew it.
[24:38] So, you stop drawing it by hand and let the traces draw it for you.
[24:43] You already have the data for it.
[24:45] Every call between services is a pair of spans, one on the caller and one on the callee, and each carries with a service name, so you know exactly who called Feed those in and the map build itself.
[25:01] Edges sticking with real traffic and turning red wherever calls are failing.
[25:08] The simplest way to get it is a collector feature e that turns your spans into per edge metrics with no custom code.
[25:17] Now, you can see the system as it actually runs. Which make the next problem obvious. Instrumenting all 30 of these by hand is a lot of work.
[25:28] Instrumenting 30 services by hand is exactly the wall we just hit. This is where eBPF comes in.
[25:37] eBPF lets you run tiny sandboxed programs inside the operating system kernel.
[25:45] So, you can watch what every service is doing without changing a single line of its code.
[25:52] There are guardrails on it.
[25:55] Each program has to pass a kernel verifier first. Which rejects anything unsafe and loading it needs alleviated privilege on the machine. With that in place, tools like a Grafana Bella and Odigos hand you request metrics and the traces in open telemetry format.
[26:16] Automatically with no code changes.
[26:20] There is a real limit, though.
[26:22] eBPF sees the raw system calls. A connect here, a write of 200 bytes there. But, it has no idea that meant it charged the rider $18.
[26:35] For that meaning, you still add a manual span or two. So, the honest pattern is a hybrid. eBPF for broader coverage, handwritten spans for the parts that carry business context. So, eBPF gets you coverage almost for free.
[26:55] What it still cannot tell you is which line of code inside a service is actually burning the time, and that's completely a different signal. Metrics told you pricing was a slow. The trace narrowed it down to the pricing span, but neither one tells you which line of code is actually eating the CPU. And that's what continuous profiling does.
[27:21] It samples the running program and shows you performance right down to the source line. The output is a flame graph. So, a hot loop or a slow distance calculation jumps straight out.
[27:36] And it is cheap enough to leave running in production.
[27:40] Usually, a couple percent of overhead on all the time.
[27:45] Grafana Pyroscope and Parca are the common open source options. It sits right alongside your other signals, too.
[27:54] So, you can jump from a slow trace straight into the profile for that exact code.
[28:00] Some people have started calling profiling the fourth pillar of observability. Although, that's more of a depth than a settled fact. Either way, your whole back end is visible now, which leave one question. Is the user actually happy?
[28:18] Your back end can be entirely green.
[28:21] Every SLO met.
[28:23] And the person on the other end still has a bad time.
[28:27] Their experience happens in a browser, on a real device, and a real network, far from your servers. Real user monitoring, or RUM, measures exactly that. The actual experience of actual users.
[28:45] It's built around three core web vitals.
[28:48] LCP is how fast the main content loads.
[28:52] INP is how quickly the page responds when you tap it. And CLS is whether the layout jumps around while it settles.
[29:03] Good means LCP under 2 and 1/2 seconds.
[29:08] INP under 200 milliseconds, and SLS under 0.1.
[29:14] All measured at a 75 percentile of real page load. On a slow phone, INP is often the one in the red while your servers looks perfect. A scripted test from a fast data center will happily tell you everything is fine.
[29:34] Which is the trap. Only real user data shows the truth.
[29:39] So, you run both. Synthetic as a safety net, and rum as the grounded truth.
[29:46] And it ties back to everything else.
[29:49] Because the browser can start the trace, it opens the first span for the page load and passes the trace context with its API call. So, your back-end the spans become children of the user's real click.
[30:05] Now, you can see the whole path. And there is one moment where all of it matters at once.
[30:12] 3:00 in the morning. It's 3:00 in the morning and your phone goes off. If the page isn't CPU is high, it is booking success is dropping and the error budget is burning fast. A real symptom your users are failing right now. The on-call tool routed to whoever's holding the pager.
[30:34] And the clock on how fast you detect and resolve this start ticking. You open the runbook for that alert, a short repeatable checklist so nobody is improvising half asleep. Step one says confirm the burn is real. So you pull up the same rate query from earlier and it agrees.
[30:58] Step two, ask what changed because most incidents follow a deploy and the deploy markers sit right on the same graph. So you can see a release landed exactly where the rate fell. Then you localize it. From the burning metric you click on the exemplar which drops you straight into the offending trace and from the failing span you jump to its error log and the shared trace ID. And there is the cause sitting right in the log. A bad deploy to the notification service.
[31:34] You roll it back, the success rate climbs and the budget stop draining.
[31:39] If this had been bigger, you would pull in defined roles, an incident commander running the response while other people handle the fix and the updates. So a large incident stays coordinated instead of chaotic. Afterwards comes the review and the important word there is plainless.
[32:02] You write down the timeline and the contributing causes without hunting for someone to pin it on.
[32:10] Assuming everyone did their best with what they know at that time.
[32:15] The output isn't a name, it's a list of fixes so the same thing cannot break the same way twice.
[32:23] You just ran an incident end-to-end on the stack. So let's talk about how you actually build that stack. Here is the whole thing wired together, all open source. The open telemetry collector is the pipeline in the middle and it feeds three stores.
[32:43] Tempo for traces, Loki for logs, and the Prometheus for metrics.
[32:49] Two of those arrows are pushed and the third one runs backward.
[32:54] Prometheus scraps, so it reached out on a schedule and pulls metrics from an endpoint the collector exposes.
[33:04] Everything you have seen in this video flows through exactly that shape.
[33:10] On top sits a Grafana, one window over all three stores, and it is what lets you jump from a trace to its logs to its metric without leaving the screen.
[33:22] That is the correlation from earlier made it real.
[33:27] Grafana's own agent, Alloy, is a packaged build of Open Telemetry Collector, which tells you how central that collector is.
[33:38] You don't have to run all of this yourself, either.
[33:42] Self-hosting at scale is real work.
[33:45] Prometheus need extra pieces to grow, and running the rest takes people. So, a small team or a very large fleet often finds managed cheaper than the engineering hours.
[33:59] And because you instrumented with Open Telemetry, moving to Grafana Cloud or Datadog or Honeycomb is just pointing the collector somewhere else with no app change, and that's the real payoff of betting on the standard. Let's pull it together. We took one ride-share app from a single service to six of them talking to each other, and at every step we added observability only when a real failure forces us to.
[34:29] Logs, then metrics, and the methods, then traces, then the correlation that ties them together, then open telemetry to carry it all, and finally the production realities, sampling, budgets, alerts, and deeper signals.
[34:47] And that very first booking, the one that failed invisibly back when we had nothing, is in view now. You can follow it across every service and hop between its logs, its metrics, and its traces in a seconds. Once you have watched it work this way, you start seeing the same shape in every system you touch, whether it's yours or a tool you use every day.
[35:12] If you keep one thing from all of this, keep this.
[35:16] Observability isn't three separate pillars. It's the ability to correlate them for a single request, and that's the full picture we promised you at the start. That's the whole crash course, from a blind spot in one service to full visibility across a system. If this made observability click for you, the best thing you can do is subscribe, so the next deep dive finds you. Share it with whoever on your team keeps insisting the dashboards are green, and I will see you in the next one.
