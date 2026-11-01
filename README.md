# s3 bucket pricing: What 1TB Actually Costs on AWS and Flat-Rate S3 Storage, and How to Dodge the Request and Egress Fee Traps

The short answer: a 1TB S3 bucket on AWS runs about **$23.55/month for storage alone** in the US East region — and that's before request fees, egress charges, and the class of storage you pick. On flat-rate S3-compatible providers like Sharktech, the same 1TB costs **$4.90/month with 1TB of transfer included**, and the invoice has exactly two line items.

This guide breaks down how S3 bucket pricing actually works, where the hidden costs live, and which pricing model fits which workload.

> **Quick pick:** If you're storing backups, archives, media libraries, or CI/CD artifacts and don't need AWS-ecosystem integration, Sharktech's S3 Object Storage at $4.90/TB/month (1TB transfer included) is the simplest math you'll do all day. [👉 Check Sharktech's current S3 storage plans](https://bit.ly/SharKtech)

## How S3 bucket pricing actually works

S3 pricing is per-usage with no minimum charge, but it's split across more dimensions than most people expect. On AWS, your bucket bill can include up to eight separate components: storage, requests and retrievals, data transfer, management and analytics features, replication, transform and query features, plus the newer S3 Vectors and S3 Tables services.

The three that hit almost everyone:

1. **Storage** — billed per GB per month, and the rate depends on the storage class you choose.
2. **Requests** — every API call costs money. PUT, GET, LIST, COPY, lifecycle transitions, all of it. Even browsing your buckets in the AWS console generates GET and LIST charges at the same rates as API calls.
3. **Data transfer out** — data leaving AWS costs $0.09/GB in the US East region. Data coming in is free, and the first 100GB/month of outbound transfer is free across all AWS services.

Requests aren't symmetric, either. In S3 Standard, writes cost **$0.005 per 1,000** while reads cost **$0.0004 per 1,000** — writes are 12.5 times more expensive. LIST requests on any storage class are billed at the Standard PUT rate ($0.005 per 1,000), and DELETE and CANCEL requests are free.

## AWS S3 storage classes and their prices

AWS offers eight storage classes, and the price spread from cheapest to most expensive is about 23x. These are the US East (N. Virginia) rates; other regions run 10–30% higher, and São Paulo costs 76% more per GB than N. Virginia.

| Storage class | Storage /GB-month | Retrieval /GB | Minimum duration | Best for |
| --- | --- | --- | --- | --- |
| S3 Standard | $0.023 | None | None | Frequently read files |
| S3 Intelligent-Tiering | $0.023–$0.00099 | None | None | Unpredictable access patterns |
| S3 Standard-IA | $0.0125 | $0.01 | 30 days | Files read about monthly |
| S3 One Zone-IA | $0.01 | $0.01 | 30 days | Recreatable files, one AZ |
| Glacier Instant Retrieval | $0.004 | $0.03 | 90 days | Quarterly-access archives |
| Glacier Flexible Retrieval | $0.0036 | $0.01 standard | 90 days | Yearly-access archives |
| Glacier Deep Archive | $0.00099 | $0.02 standard | 180 days | Sub-yearly-access archives |
| S3 Express One Zone | $0.11 | $0.0006 | None | Single-digit ms access |

At $0.023/GB, a 1TB bucket in S3 Standard costs about $23.55/month, 10TB runs $235.50, and 100TB lands around $2,305 — storage only, before requests and transfer.

One thing worth knowing if you're a new AWS customer: the old free tier (5GB free for 12 months) ended on July 15, 2025. New accounts now get a $200 credit valid for 6 months, non-renewing. Accounts created before that date keep the original terms.

## Sharktech S3 Object Storage: the full plan and pricing table

Sharktech prices its S3-compatible object storage at a flat **$4.90/TB per month** with no tiers, no volume discounts, and no step-ups as you grow. Every plan includes 1TB of outbound transfer per month; beyond that, bandwidth costs $0.90/TB. Billing is monthly, and there are no long-term contract requirements.

Here's everything currently shown on Sharktech's pricing page:

| Plan | Storage | Included transfer | Monthly price | Order |
| --- | --- | --- | --- | --- |
| S3 Starter | 1 TB | 1 TB | $4.90 | [ Order the 1TB plan](https://bit.ly/SharKtech) |
| S3 10TB | 10 TB | 1 TB | $49.00 | [ Order the 10TB plan](https://bit.ly/SharKtech) |
| S3 100TB | 100 TB | 1 TB | $490.00 | [ Order the 100TB plan](https://bit.ly/SharKtech) |
| Custom | 100TB+ storage or 300TB+ transfer | Scalable | Volume pricing | [ Request a custom quote](https://bit.ly/SharKtech) |

The flat rate is the whole point. Many providers structure pricing so the headline rate only applies to the first tier, with a step-up once you cross into the next bracket. Here, $4.90/TB holds whether you're storing 1TB or 100TB.

Under the hood, Sharktech uses high-capacity HDD storage with an NVMe cache layer on the active data path, exposed through the standard S3-compatible API. The storage clusters run with triple redundancy in Sharktech's own data centers, behind full DDoS protection, with 40G connections in and out.

Feature-wise, you get what you'd expect from an S3-compatible platform: bucket and object ACLs, bucket policies, object versioning, Object Locking with retention policies, lifecycle rules for object expiration, presigned URLs for time-limited sharing, multipart uploads with integrity checks, CORS, bucket notifications, object tagging, server access logs, and static website hosting. It works with Rclone, Cyberduck, Nextcloud, S3 Browser, or direct API integration — so existing backup tools and scripts that already speak S3 don't need reworking.

> If your workload is mostly write-once-read-rarely — backups, archives, cold media — you'll likely never touch the 1TB bandwidth ceiling, which makes the $4.90/TB rate effectively all-in. [👉 See how the plans scale at 10TB and above](https://bit.ly/SharKtech)

## Real total cost: the same workload across providers

Sticker rates hide the real difference. Here's a concrete scenario — store 10TB, pull 1TB out per month — worked through the providers using their published rates:

- **AWS S3 Standard (us-east-1):** 10TB storage at $0.023/GB is roughly $235.50, plus about $92 for 1TB of egress at $0.09/GB, plus request fees. Call it **~$328/month**.
- **Sharktech:** 10TB plan at $49, and the 1TB egress falls inside the included transfer. **$49/month.**
- **Wasabi:** $7.99/TB puts 10TB at **$79.90/month** — no egress fees, but with a fair-use cap (outbound transfer can't exceed stored volume) and a 90-day minimum storage duration.
- **Backblaze B2:** since May 2026, pay-as-you-go storage is **$6.95/TB**, so 10TB is about **$69.50/month**, with free egress up to 3x stored volume and API transaction fees eliminated.

The gap comes down to billing philosophy. AWS charges per component; the flat-rate providers bundle. For a backup-and-archive workload, the component model mostly means paying for things you barely use.

One caveat on the flat-rate side: Sharktech's pricing page lists the 1TB bandwidth allowance and a $0.90/TB overage rate, but doesn't publish a per-request (GET/PUT) fee schedule. If your workload involves millions of tiny objects — think 1KB log files — that's exactly where per-request fees tend to appear, so it's worth confirming with their team before committing. For backups, media, and archives, it's a non-issue.

## The three traps inside AWS's cheaper storage classes

If you're comparing s3 bucket pricing to decide whether a cheaper storage class makes sense, three rules trip people up constantly — and they're buried in AWS's footnotes.

**Minimum billable object size.** Standard-IA, One Zone-IA, and Glacier Instant Retrieval bill every object as at least 128KB. A concrete example: 1 million 10KB thumbnails (10GB total) costs $0.23/month in S3 Standard, but in Standard-IA they're billed as 128GB × $0.0125 = **$1.60/month**. The "cheaper" class costs about seven times more for that workload.

**Minimum storage duration.** Standard-IA and One Zone-IA enforce 30 days, Glacier classes enforce 90, Deep Archive 180. Delete or overwrite early and you pay for the remaining days. Example: 100GB of backups uploaded to Glacier Instant Retrieval and replaced after 10 days still bills for the full 90 days — $1.20. The same data in Standard for 10 days costs $0.08. If you rotate backups every few days, Standard is genuinely cheaper.

**Archive metadata overhead.** Objects in Glacier Flexible Retrieval and Deep Archive carry an extra 40KB of billable metadata per object (8KB at the Standard rate, 32KB at the Glacier rate). With billions of small objects, that adds up to real money on "free" storage.

There's also the region effect: the same Standard storage costs $0.023/GB in N. Virginia, $0.0245 in Frankfurt, $0.025 in Singapore, and $0.0405 in São Paulo. Where you put the bucket matters as much as which class you pick.

## Where Sharktech fits — and where it doesn't

Sharktech's own docs are clear about the boundaries. Its S3 Object Storage is capacity-first object storage for backups, archives, media libraries, logs, and application data. It is explicitly the wrong tool if your application needs a boot disk, filesystem semantics, or consistently low-latency block storage — the company says as much on the product page and will route you to a different platform instead.

Also worth knowing before you sign up:

- **No published durability number.** AWS, Azure, and Google all quote "11 nines" (99.999999999%) durability. Sharktech describes high-redundancy clusters that protect against loss, corruption, and hardware failure, but doesn't publish a numeric durability target. The triple-redundancy design and DDoS-protected infrastructure are documented; the exact figure isn't.
- **Storage class configuration and cross-region replication** need to be arranged with their team rather than self-served.
- **Bandwidth scales oddly at the top.** Every plan includes the same 1TB of monthly transfer, so the 100TB plan's bandwidth allowance is proportionally much smaller than the 1TB plan's. Heavy-read workloads should price the $0.90/TB overage into the comparison.
- **Scale ceiling:** beyond 100TB of storage or 300TB of monthly transfer, you're into custom-quote territory through their sales team.

If any of those are dealbreakers, the hyperscalers remain the right answer — especially if you're already deep in the AWS ecosystem, need S3's newer features (S3 Vectors for AI embeddings, S3 Tables for Iceberg analytics, S3 Files for shared filesystem access on buckets), or want the deepest storage-class menu for tiering data yourself. [👉 View Sharktech's S3 plans and current pricing](https://bit.ly/SharKtech)

## FAQ

**Does AWS charge for empty S3 buckets?**
No. With no minimum charge, an empty bucket costs nothing on AWS. You pay only for stored data, requests, and transfer.

**Does Sharktech charge API request fees on its S3 storage?**
No per-request fee schedule is published on its pricing page, and the billing model is described as two line items — stored capacity and data transfer. For heavy small-object workloads, confirm request handling with their team before signing up.

**What's the difference between a bucket and an object?**
A bucket is the container — think of a folder on your computer. Objects are the actual files inside it: images, videos, database backups, log files.

**Is cheap S3-compatible storage safe for backups?**
It depends on the provider's redundancy model. Sharktech runs triple-redundancy clusters in its own data centers with DDoS protection, but doesn't publish a numeric durability spec. Whatever you choose, the standard practice applies regardless of provider: keep encrypted copies in more than one place.

## Picking a plan before you order

Match the plan to the workload rather than to the biggest discount:

- **Personal backups or a small media archive** → the 1TB plan at $4.90/month. There's no cheaper entry point, and the rate never gets worse as you grow.
- **Team backups, photo/video libraries, or CI/CD artifacts around 10TB** → the 10TB plan at $49/month. The same workload on AWS would run roughly $328.
- **Compliance archives or multi-team data at scale** → the 100TB plan at $490/month, or a custom quote beyond that.
- **Frequently-read data, AWS-native apps, or boot-disk needs** → skip flat-rate S3 entirely; you want the hyperscaler ecosystem.

Two things to confirm at checkout: the listed prices exclude the outbound transfer allowance (transfer is billed separately per the $0.90/TB overage), and billing is monthly with no contract lock-in — so you can re-evaluate after a real invoice or two. [👉 Start with the 1TB plan at $4.90/month](https://bit.ly/SharKtech)
