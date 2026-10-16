---
layout: product
title: ScrollScripture
permalink: /products/scrollscripture/
accent: "#091311"
accent_ink: "#091311"
accent_soft: "#091311"
font: "Inter, ui-sans-serif, system-ui, sans-serif"
font_class: uses-serif
mark: /assets/img/scrollscripture/symbol.png
mark_alt: "ScrollScripture logo"
mark_tile: true
---

*Read. Reflect. Grow.*

## Why it exists

Most of us scroll every day, while opening the Bible often gets pushed aside. I wanted some of that scrolling to point back to God's Word. ScrollScripture is a ministry project first and a product second.

## What it is

A web app and iOS app, with Android planned, that present Scripture in a simple, familiar scrolling format: daily Scripture-based posts and reflections, guided Bible Journeys, and Christian teachings, in English, Arabic and French.

## Who it's for

Anyone who wants to spend a few minutes with God's Word each day: in the morning, on a break or before bed. It's for English, Arabic and French readers, and for families who want to share Scripture with each other.

## Key features

- **AI-generated reflections, human-reviewed: every reflection is drafted with AI and reviewed by a person before it's published**
- Daily Scripture-based posts and reflections
- Bible Journeys that walk through books of the Bible step by step
- Christian teachings and foundational topics
- English, Arabic and French
- Audio narration
- Save posts to revisit, and share them with family and friends
- Distraction-free reading experience with reflection generation and AI generation at a fingertip

## How it works

ScrollScripture is a short daily feed that pairs Bible passages with reflections, in English, Arabic and French, on the web and on iOS. Android is planned. AI helps pick passages and write commentary, but it never supplies the Scripture text. That always comes from a canonical Bible source, and by default a human editor approves every post.

### Tech stack

- **Clients:** Next.js 16 static site (React 19, TypeScript, Tailwind) and native SwiftUI on iOS. Android is planned, using Kotlin with Jetpack Compose.
- **Cloud:** AWS CDK, with Lambda (Node.js 24), API Gateway, DynamoDB, S3, CloudFront, Cognito, Step Functions, SQS, EventBridge, SNS, Polly, CloudWatch and X-Ray.
- **AI:** OpenAI Responses API with schema-checked structured output.

### Behind the feed

- **Edge:** CloudFront serves the site and media from private S3 and sends API requests to API Gateway and a Lambda backend.
- **Auth:** anyone can read without an account. Likes and saves need a Cognito sign-in, and the admin tools also require membership in a Cognito admins group.
- **Data:** DynamoDB stores verses, posts, interactions, generation jobs and live settings.
- **Bible text:** English uses the NKJV via API.Bible. Arabic uses Van Dyck and French uses Louis Segond 1910, both imported and validated. A public-domain KJV is used only as private grounding for the AI.

### From verse to reader

1. **Schedule.** EventBridge runs daily lanes: the Balanced feed, book journeys, topic series and a Coptic daily Gospel.
2. **Queue.** A planner Lambda puts jobs on SQS, which has a dead-letter queue.
3. **Generate.** Step Functions loads the passage. The writer model drafts commentary in all three languages, and code confirms the Scripture matches the source exactly.
4. **Review.** A second model checks theology and clarity and can ask for one revision. OpenAI moderation screens the text, and a separate model paints a background with no text on it.
5. **Publish.** Posts land in Needs Review. Editors publish them or schedule them, and a Lambda publishes due posts every five minutes. Auto-publish is off by default.
6. **Deliver.** A DynamoDB stream triggers Polly to narrate the English version. Arabic and French audio is created when a reader taps Listen. A second stream groups new posts into one push notification sent through SNS to APNs. Android and FCM are planned.

### Deploys and observability

- **Blue/green web:** each build is exported twice. Blue is a test copy that shares the backend, and green is the live site. Every deploy names its slot.
- **Monitoring:** CloudWatch alarms cover API, pipeline and planner errors, dead-letter queues and the OpenAI quota. X-Ray traces the content and notification workflows.
- **Analytics:** GA4 on the web, Firebase Analytics in the iOS app.

<figure class="diagram">
  <img src="{{ '/assets/img/scrollscripture/flow.svg' | relative_url }}" alt="Journey from a scheduled passage through canonical Scripture, AI commentary, human review and publishing. Android delivery is marked planned.">
  <figcaption>From a scheduled passage to a published reflection. Reject and regenerate stay with the editor. Android and FCM are planned.</figcaption>
</figure>

## Architecture

ScrollScripture serves a short, finite set of passage-and-reflection cards through a Next.js web app and a native iOS app, in English, Arabic and French. Android is planned. AI helps choose passages and draft commentary, but it never writes the Scripture shown to readers, and by default a human editor approves every post before it goes live. Everything runs serverless on AWS, defined in the AWS CDK.

### From verse to published reflection

1. **Source text.** English uses the licensed NKJV through API.Bible, cached in DynamoDB. Arabic uses Van Dyck and French uses Louis Segond 1910, both imported and validated. A public-domain KJV serves only as private context for the AI.
2. **Planning.** EventBridge schedules run four lanes: a Balanced feed, book journeys, topic series and a Coptic daily Gospel. A planner Lambda picks passages, checks them against recent posts and queues jobs on SQS.
3. **Generation.** Step Functions loads the canonical passage, and the writer model drafts commentary in all three languages as schema-checked output.
4. **Safety and quality checks.** Code confirms every displayed verse matches the canonical source exactly. A separate reviewer model grades theology and clarity and can ask for one revision. OpenAI moderation screens the text. Posts that pass get a decorative AI background with no text.
5. **Human gate.** Posts wait in a Needs Review queue. An editor approves, rejects, or regenerates the post or a single field.
6. **Publish.** Editors publish right away or schedule a time. A publisher Lambda releases due posts every five minutes.
7. **Audio.** When a post is published, a DynamoDB stream triggers Amazon Polly to narrate the English version. Arabic and French narration is created on a reader's first Listen, then stored.
8. **Delivery.** Readers on the web and iOS get the feed through CloudFront. A second stream batches new posts into one push notification sent through SNS to APNs. Android and FCM are planned.
9. **Feedback.** GA4 on the web and Firebase Analytics on iOS capture usage, which the admin dashboard reports through the GA4 Data API.

### Stack by layer

<div class="table-wrap">
<table>
  <thead>
    <tr><th>Layer</th><th>Technology</th></tr>
  </thead>
  <tbody>
    <tr>
      <td data-label="Layer">Clients</td>
      <td data-label="Technology">Next.js 16 (React, TypeScript, Tailwind); SwiftUI on iOS; Android planned (Kotlin with Jetpack Compose)</td>
    </tr>
    <tr>
      <td data-label="Layer">Edge / API</td>
      <td data-label="Technology">CloudFront, API Gateway HTTP API</td>
    </tr>
    <tr>
      <td data-label="Layer">Compute</td>
      <td data-label="Technology">Lambda on Node.js 24 (ARM64), Step Functions, SQS with dead-letter queues</td>
    </tr>
    <tr>
      <td data-label="Layer">Data</td>
      <td data-label="Technology">DynamoDB, S3</td>
    </tr>
    <tr>
      <td data-label="Layer">Identity</td>
      <td data-label="Technology">Cognito user pool with an admins group</td>
    </tr>
    <tr>
      <td data-label="Layer">AI</td>
      <td data-label="Technology">OpenAI: gpt-5.6-luna (planner and writer), gpt-5.6-terra (reviewer), gpt-image-2 (art), omni-moderation</td>
    </tr>
    <tr>
      <td data-label="Layer">Media</td>
      <td data-label="Technology">Amazon Polly, S3 behind CloudFront</td>
    </tr>
    <tr>
      <td data-label="Layer">Automation</td>
      <td data-label="Technology">EventBridge Scheduler and rules</td>
    </tr>
    <tr>
      <td data-label="Layer">Observability</td>
      <td data-label="Technology">CloudWatch alarms and dashboard, X-Ray</td>
    </tr>
    <tr>
      <td data-label="Layer">Delivery</td>
      <td data-label="Technology">SNS to APNs; FCM planned with Android. Blue/green web slots on S3 and CloudFront</td>
    </tr>
  </tbody>
</table>
</div>

### Design decisions

- **Canonical Scripture is never AI output.** Models return references and commentary. Code fills in verse text from the Bible source. This rules out invented Scripture, and it makes the pipeline stricter.
- **Human in the loop.** Auto-publish is off by default. Trust comes before throughput, so publishing depends on editors keeping up with the queue.
- **Blue/green web deploys.** Each build ships as live green and a blue candidate, and every deploy names its slot. Rollback is redeploying a known commit to green. Both slots share one backend, so API changes must stay backward compatible.
- **Audio on demand for secondary languages.** Only English is narrated at publish. Arabic and French audio is made on first Listen and reused, so narration is created only when someone plays it. The first listener waits a little longer.
- **Bounded automation.** At most three generation workflows run at once, under admin-set daily caps and schedules. This protects Lambda concurrency, and output arrives more slowly.
- **Tracing and one secret store.** X-Ray traces the pipeline and notification workflows and stays off the busy API. Third-party keys live in one Secrets Manager secret per environment, with dual-read fallback during cutover.
- **Guardrails for the codebase itself.** A test checks every web API call against the routes declared in the infrastructure, and a durable handoff ledger lets coding tools take over from each other safely.

<figure class="diagram">
  <img src="{{ '/assets/img/scrollscripture/architecture.svg' | relative_url }}" alt="Layered architecture: clients, then edge, then API, then pipeline, then data and AI services. The Android app and FCM are dashed planned nodes.">
  <figcaption>Clients, then edge, then API, then the publishing pipeline, then data and AI services. Dashed nodes are planned.</figcaption>
</figure>

## Status and links

- Status: live on the web and iOS
- Web: [scrollscripture.life](https://scrollscripture.life)
- iOS: "ScrollScripture: Bible Study" on the [App Store](https://apps.apple.com/us/app/scrollscripture-bible-study/id6811592927) (free)

<p><a class="btn" href="https://scrollscripture.life">Visit scrollscripture.life</a></p>

## Get involved

Read today's post and share one with someone who needs it. If you have feedback or ideas for a Bible Journey, message me on [LinkedIn](https://www.linkedin.com/in/ramybassem).

## Source code

Code lives in a private repository: [github.com/{{ site.github_user }}/scrollscripture](https://github.com/{{ site.github_user }}/scrollscripture) (private, not publicly viewable).

<p class="note">This link shows a 404 page to visitors. That's by design: the repository is private, and this portfolio repo holds no product code.</p>

## Brand

<div class="brand-lockup">
  <span class="mark-tile"><img src="{{ '/assets/img/scrollscripture/symbol.png' | relative_url }}" width="72" height="72" alt="ScrollScripture logo"></span>
</div>

<ul class="swatches">
  <li><span class="chip" style="background:#091311" aria-hidden="true"></span> #091311 theme</li>
  <li><span class="chip" style="background:#e9c46a" aria-hidden="true"></span> #e9c46a gold</li>
  <li><span class="chip" style="background:#f8f3e8" aria-hidden="true"></span> #f8f3e8 parchment</li>
  <li><span class="chip" style="background:#c49845" aria-hidden="true"></span> #c49845 deep gold</li>
  <li><span class="chip" style="background:#9fc3a9" aria-hidden="true"></span> #9fc3a9 sage</li>
</ul>
