# managed cloud: Sharktech's $39/mo OpenStack plans compared — overage rates, billing fine print, and which tier actually fits your workload

Searching "managed cloud" usually means one of two things: you want someone else to handle the infrastructure so you don't babysit servers, or you're comparing providers and trying to figure out what you actually get for the monthly fee. Both come down to the same question — what's managed, what isn't, and what does it really cost.

Sharktech's Public Cloud sits in an interesting spot here. The platform layer is managed by their team: you get a shared pool of compute, storage, and network resources on OpenStack, with the hardware, redundancy, and DDoS mitigation handled for you. Plans start at **$39/month** with hourly billing on anything above your included allocation. What you won't get is application-level hand-holding — their terms are explicit about that, and it matters for whether this fits your situation.

Here's the quick version if you just want to decide:

> Most people starting out should take the **Small plan at $39/month**: 4 CPU cores, 8 GB RAM, 300 GB SSD, 20 TB of included bandwidth, and DDoS protection built into the network across 5 data center locations. It scales up to 16 cores and 32 GB RAM within the same plan before you need to upgrade. If your usage swings a lot month to month, the metered overage model is where this platform actually gets interesting. 👉 [configure the Small plan at $39/mo](https://portal.sharktech.net/aff.php?aff=1626&pid=602)

## What "managed cloud" actually means at Sharktech

At Sharktech, the managed part covers the infrastructure layer, and the unmanaged part covers everything running on top of it. Their Public Cloud page describes a shared resource pool "managed by our team," running on OpenStack with hyper-converged, fully redundant infrastructure. You interact with a control panel and API; they keep the platform alive.

The terms of service draw the line more bluntly: all servers are unmanaged at the application level, and the company states it "only ensures that the server is online and is remotely accessible." No support for CGI programming, HTML, or other application-specific issues, and no support for your own customers if you're reselling.

This split is worth understanding before you buy, because "managed cloud" means different things at different providers. Some MCSPs will patch your OS, manage your databases, and take 3 a.m. pages for your app. Sharktech manages the cloud platform itself — the OpenStack layer, the network, the DDoS mitigation, the hardware — and assumes you can handle what's inside your VMs. If you want a full-service operations team, this isn't that. If you're a developer or a small team that wants cloud infrastructure without becoming a part-time sysadmin for the underlying hardware, it's a reasonable middle ground.

## Sharktech Public Cloud plans and pricing

There are five plan tiers on the official pricing page: Small, Medium, Large, Enterprise, and a Custom option built around a resource commitment. Here's the full table with current base prices:

| Plan | Base price | CPU / RAM / SSD | Included bandwidth | Order |
| --- | --- | --- | --- | --- |
| Small | $39/mo | 4 cores / 8 GB / 300 GB | 20,000 GB | [Configure Small](https://portal.sharktech.net/aff.php?aff=1626&pid=602) |
| Medium | $79/mo | 8 cores / 16 GB / 800 GB | 20,000 GB | [Configure Medium](https://portal.sharktech.net/aff.php?aff=1626&pid=603) |
| Large | $249/mo | 32 cores / 64 GB / 1,500 GB | 20,000 GB | [Configure Large](https://portal.sharktech.net/aff.php?aff=1626&pid=604) |
| Enterprise | $499/mo | 64 cores / 128 GB / 5,000 GB | 20,000 GB | [Configure Enterprise](https://portal.sharktech.net/aff.php?aff=1626&pid=605) |
| Custom | from $51.82/mo (commitment-based) | 32 cores / 64 GB / 2,400 GB configurable | 40,000 GB | [View all plans](https://bit.ly/SharKtech) |

Every plan includes 1 public IPv4 (extra IPs are $1.50/month each), hourly billing on usage above the included allocation, and 24/7 support. Data center choices are Los Angeles, Las Vegas, Denver, Chicago, and Amsterdam.

The Enterprise tier gets discounted overage rates across the board — cheaper per-core, per-GB, and per-GB-of-bandwidth pricing than the smaller plans. Custom plans work differently: instead of a fixed included pool, you commit to a resource level and pay rates against that commitment.

One more thing about Small: it's more headroom than the base specs suggest. The plan ceilings (more on those below) let Small stretch to 16 cores and 32 GB RAM before you're forced into Medium, and HostAdvice's reviewer notes you can upgrade to a higher tier at any time without redeploying your environment.

## How Sharktech cloud billing actually works

Billing is based on your cloud's aggregate resource pool, not on individual VM sizes. This is the detail most reviews skip, and it changes how you should think about the plans.

Say you're on Medium. Your base price includes a pool of resources — 8 cores, 16 GB RAM, 800 GB SSD. You can slice that into one VM or eight VMs in any combination. If you go over the pool, the overage is metered on the aggregate, at these published hourly rates:

| Resource | Overage rate | Small/Medium/Large ceiling | Enterprise |
| --- | --- | --- | --- |
| CPU | $0.0025/hr per core | 16–128 cores | Unlimited |
| RAM | $0.0035/hr per GB | 32–256 GB | Unlimited |
| NVMe storage | $0.00009/hr per GB | 1,200–6,000 GB | Unlimited |
| SSD storage | $0.00006/hr per GB | 2,400–12,000 GB | Unlimited |
| HDD storage | $0.00002/hr per GB | 4,800–24,000 GB | Unlimited |
| Bandwidth | $0.002/GB | Unlimited | $0.0015/GB |
| Public IPv4 | $1.50/mo each | 16 | Unlimited |

Three mechanics here that will affect your bill:

**Storage keeps billing when your VM doesn't run.** The cost calculator's fine print says storage is billed for the full month on a 730-hour basis, even after a VM is powered off. Compute stops when the VM stops; disks don't. If you're spinning machines up and down to save money, delete unused volumes rather than leaving them attached.

**Ceilings cap resources, not spending.** This one trips people up, so here's the official wording verbatim:

> "Resource ceilings are not monetary spending caps."

Small, Medium, and Large each have a Max Threshold — a hard limit on how much of each resource you can consume. The ceiling stops your cloud from growing past 16 cores (on Small), but your invoice can still climb with usage up to that point. Enterprise has no preset ceiling on any resource.

**Egress is the metered line to watch.** Bandwidth charges apply at $0.002/GB on the outbound side (Enterprise: $0.0015/GB). Inbound traffic, according to third-party reviews of the platform, isn't charged — the official rate table doesn't list an ingress fee, but treat that as reviewer-reported until you see your first bill. If your workload is download-heavy — file hosting, media serving — the ratio matters a lot.

For a realistic ballpark: the official calculator's default test case (1 VM, 2 vCPU, 4 GB RAM, 100 GB storage) fits entirely inside the Small plan's included allocation, so the estimate comes back to exactly $39 with zero overage. That's the experience most light-to-moderate users will have — the base price is the price.

If you want to model your own configuration before committing, the same calculator lets you add multiple VM groups and set compute hours, transfer, and IPs. 👉 [check current plans and pricing on the official calculator](https://bit.ly/SharKtech)

## Dedicated Cloud: the fixed-price alternative

Public Cloud isn't the only billing model. Sharktech runs a Dedicated Cloud option on the same OpenStack infrastructure, with a different philosophy: you order a fixed resource allocation, pay a fixed monthly price, and there's no metered overage at all. Need more capacity? You upgrade the allocation through the billing platform.

The official guidance on choosing between them is sensible. Public Cloud suits workloads with changing resource demand — you use extra resources at published rates when you need them. Dedicated Cloud suits steady workloads and predictable budgets, since your bill never moves.

There's also a terminology trap worth flagging: Dedicated Cloud is a fixed allocation on the shared Sharktech platform. A Private Cloud is physically isolated infrastructure built for a single organization. They're different products, and if a sales conversation drifts between the terms, ask which one is actually being quoted.

## The fees and fine print most reviews skip

This is the part that deserves your attention before checkout, because Sharktech's policies are less forgiving than the big clouds in one specific way: **all payments are non-refundable**. The terms of service state this flatly, covering setup fees and monthly charges regardless of usage. There's no money-back guarantee window and no free trial. The offset is that hourly metering means your first hours of usage cost cents, not dollars — you can stand up a test VM and see how the platform behaves for pocket change.

Other terms worth knowing:

- **Billing disputes have a 30-day window** from when the disputed charge occurred. If a dispute is resolved in your favor, you receive an account credit, per the HostAdvice review's reading of the policy — a credit, not a refund.
- **Cancellation requires at least 7 days' notice by email**, with full account details included. Don't assume you can cancel on the last day of the month.
- **Late fees stack up**: $25 for overdue payments, service suspension after 4 days overdue, and a $50 reconnection charge if service is cut for non-payment.
- **Chargebacks are expensive.** Disputing a valid charge with your card issuer triggers an administrative fee of $50–$150. If you have a genuine billing problem, the 30-day dispute channel is the cheaper route.
- **Payment options are broad for this market**: credit card, PayPal, wire transfer, Western Union, and Alipay.
- **SPAM is zero-tolerance**, with escalating fees and eventual termination for repeat violations. If you're sending any kind of bulk email, read the acceptable use policy first.

None of this is unusual for the hosting industry, but cloud buyers coming from AWS or Azure often assume trial credits and refunds exist. Here, they don't, and it's better to know now than at dispute time.

## DDoS protection: included, not an add-on

DDoS mitigation is built into Sharktech's network rather than sold as a separate product, and this is where the company's history actually matters for your purchase decision. They've been building their own mitigation systems since 2003 — not reselling a third-party scrubbing service.

According to third-party reviews covering their product line, every hosted service carries a baseline of 60Gbps of mitigation per IP at no extra charge, with an optional upgrade to 100Gbps at $39/month per IP across all five data centers. For context, the volumetric attacks that knock over ordinary hosting setups typically land in the 3–20Gbps range; one gaming customer cited in reviews reports absorbing 3–8Gbps attacks routinely without downtime. Hyperscalers handle DDoS protection very differently — as a separately billed product with its own pricing tier — so if you're comparing total cost of ownership, protection is a line item you're already paying for here and paying extra for elsewhere.

The mitigation runs automatically via BGP, Anycast, and GRE routing across the five locations, filtering common attack types like SYN floods, UDP floods, and amplification attacks without manual intervention.

## Who Sharktech's managed cloud fits — and who should skip it

The strongest fit is a developer or technical team that wants OpenStack-class infrastructure without hyperscaler pricing complexity. The feature set backs that up: full RESTful APIs across compute, storage, networking, and identity; Kubernetes cluster creation; load balancers configurable from the dashboard; scheduled snapshots; virtual networks and subnets; live CPU/RAM/storage scaling; IPv6 on every deployment; and no proprietary lock-in, since OpenStack tooling travels with you.

Bandwidth-heavy workloads are another strong case. HostAdvice's hands-on review measured sustained throughput around 10 Gbps on a public cloud VM, with upload exceeding 20 Gbps and 0.17 ms latency inside the datacenter — noting that many competing providers cap VM networking at 1–5 Gbps unless you buy premium tiers. For CDN edge nodes, file hosting, or streaming, that raw network capacity plus the free-ingress setup is a genuinely differentiating combination. The same review gave the platform an overall score of 9.4, with support tickets answered in under 40 minutes during testing — while also cautioning that advanced tuning questions got generic answers, so you should bring your own expertise for anything deep.

Who should skip it: teams that need actual application-level managed services — OS patching, database administration, someone on call for the app itself. Providers specializing in fully managed operations will cost more, but that's the service you'd be paying for, and Sharktech's support boundaries are explicit about where their responsibility ends. Also worth skipping if you need broad global coverage: five regions (four US, plus Amsterdam) is the review's only listed con, and if your users are concentrated in Asia or South America, latency will suffer.

## FAQ

**Is Sharktech Public Cloud actually managed?**
The platform is, the applications aren't. Sharktech manages the OpenStack infrastructure, network, and DDoS mitigation; you're responsible for everything inside your VMs, and the TOS confirms servers are unmanaged at the application level.

**Is there a free trial or refund?**
No. All payments are non-refundable per the terms of service, and there's no trial program. The mitigating factor is hourly billing — overage resources meter at rates like $0.0025 per core-hour, so short experiments cost very little.

**Is DDoS protection included in the cloud plans?**
Yes. Mitigation is built into the network on every hosted service. Third-party reviews report a 60Gbps baseline per IP with a $39/month upgrade to 100Gbps per IP; the protection is included in the plan price rather than billed as a separate product.

**Can I upgrade plans without rebuilding everything?**
Yes. HostAdvice's review confirms you can move to a higher tier at any time without redeploying, and the Small plan itself has internal scaling room up to 16 cores and 32 GB RAM before an upgrade is even necessary.

**How does the bandwidth billing compare with big clouds?**
Sharktech charges $0.002/GB for egress only. The major hyperscalers also charge separately for egress, though their rate structures vary by tier and region — if egress is a large cost line for you, run the same monthly transfer number through both billing models before deciding.

## Which plan to pick

Small at $39/month covers most individual projects and light production workloads, and its 16-core/32GB ceiling means you won't outgrow it quickly. Medium at $79 makes sense once your baseline pool is genuinely saturated — check your aggregate usage, since the pool model means two half-used VMs might still fit in Small. Large and Enterprise are for serious compute: 32 and 64 cores respectively, with Enterprise adding unlimited ceilings and the discounted overage rates. The Custom plan is only worth exploring if your commitment level clearly exceeds what Enterprise's included pool covers, since that's where its pricing math starts working in your favor.

Two things to confirm before ordering: that the 7-day cancellation notice and non-refundable payment terms work for your planning style, and that your expected monthly egress fits comfortably inside the 20 TB included allocation — beyond that, $0.002/GB adds up faster than the compute overages do. If both of those check out, the platform is easy to try cheaply: 👉 [start with the Small plan and scale as needed](https://portal.sharktech.net/aff.php?aff=1626&pid=602)
