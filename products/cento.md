---
layout: product
title: Cento
permalink: /products/cento/
accent: "#0F3D3E"
accent_ink: "#0F3D3E"
accent_soft: "#F7F5F0"
font: "Source Sans 3, ui-sans-serif, system-ui, sans-serif"
mark: /assets/img/cento/app-icon.png
mark_alt: "Cento logo"
---

*Every cent, in plain sight.*

## Why it exists

Wellness Compass gave me one private place for my health data, and a way for an AI assistant to read it. My money was still stuck inside bank apps. I wanted the same kind of system for personal finance: my own transactions and balances, in a place I control, with a coach that can see them.

## What it is

Cento is my personal-finance coach, the sibling of Wellness Compass. It follows the same architecture pattern, with money in place of health. It connects bank accounts read-only through Plaid, syncs transactions and balances to a private AWS backend, and shows them in a web portal. A secure MCP server lets AI assistants coach on that data.

On the site the name is Cento Coach. The line on the page is "Every cent, in plain sight."

## Who it's for

Me, and people I invite. Cento v1 is live at [cento.coach](https://cento.coach). Accounts are invite-only.

## Key features

- **Spending by category, on the dates you choose.** The portal shows where the money went, including a custom date range. The home page carries the line "Every cent, in plain sight."
- **Bills matched to transactions and placed against paychecks.** Bills line up with the payments that cover them and with the pay periods on the pay schedule.
- **Income tracking.** The pay schedule holds income, and the portal shows what came in.
- **Accounts overview.** Connected accounts sit in one place. Bank access is read-only.
- **Read-only bank linking through Plaid.** Plaid Link does the bank sign-in. Cento never handles a bank password. Plaid access tokens are kept only in AWS Secrets Manager.
- **A coach that can read, and cannot change your data.** The site's own line for this is: let an AI assistant or coach read your Cento data through MCP. Each connection is for one AI client, has its own access and expiry, and every call it makes is logged. Connections can never change anything, and never see bank logins or tokens.
- **Sign-in with optional MFA.** Amazon Cognito is the user pool. Invite email is sent through Amazon SES from cento.coach.
- **Sharing by invite.** You can invite someone to a profile. A view-only invite covers spending, bills, categories and rules. A wider invite can include managing them. Sharing can be stopped.

## How it works

Open [cento.coach](https://cento.coach). The www address redirects to that apex. Sign in with Cognito. If MFA is turned on for the account, confirm with the code from the authenticator app.

To add a bank, use Plaid Link and sign in at the bank there. Cento receives a read-only connection. The access token is stored in Secrets Manager. It is not kept in the browser.

Sync Lambdas, started by an EventBridge schedule and an SQS queue, pull transactions and balances from Plaid into DynamoDB. The tables are encrypted with KMS.

In the portal, review spending by category for the range you care about, bills matched to transactions and set against paychecks, income, and accounts. The pages are Home, Bills, Transactions, Categories, Rules and Recurring. Settings covers banks, the pay schedule, sharing and AI connections.

To use a coach, connect an AI assistant such as Claude, ChatGPT, Grok or Cursor to the MCP endpoint at `/mcp`. After authentication, the assistant can read the money data. It cannot change it, and it never receives bank logins or tokens.

## Architecture

### Overview

Cento v1 is a serverless personal-finance app on AWS, in the same shape as Wellness Compass: a static portal, an API, a scheduled sync, and a read-only MCP server. The browser talks to CloudFront at cento.coach, and www redirects to that apex. CloudFront serves the static portal from S3 and sends API calls to API Gateway. Cognito is the user pool, with optional MFA, and invite email goes out through Amazon SES from cento.coach. API Lambdas read and write DynamoDB tables encrypted with KMS. Bank data comes from Plaid, read-only. Sync Lambdas, triggered by an EventBridge schedule and an SQS queue, pull transactions and balances. Plaid access tokens live only in Secrets Manager. AI assistants reach an authenticated MCP endpoint at `/mcp`. CloudWatch holds the logs.

### Stack by layer

<div class="table-wrap">
<table>
  <thead>
    <tr><th>Layer</th><th>What it uses</th></tr>
  </thead>
  <tbody>
    <tr>
      <td data-label="Layer">Clients</td>
      <td data-label="What it uses">The web portal in the browser at cento.coach. AI assistants: Claude, ChatGPT, Grok and Cursor, through MCP.</td>
    </tr>
    <tr>
      <td data-label="Layer">Edge</td>
      <td data-label="What it uses">Amazon CloudFront. www redirects to the apex. S3 holds the static portal. API Gateway fronts the API.</td>
    </tr>
    <tr>
      <td data-label="Layer">Identity and email</td>
      <td data-label="What it uses">Amazon Cognito user pool for sign-in and optional MFA. Amazon SES sends invite email from cento.coach. Accounts are invite-only.</td>
    </tr>
    <tr>
      <td data-label="Layer">Compute</td>
      <td data-label="What it uses">API Lambdas for the portal. Sync Lambdas for Plaid. An MCP endpoint at /mcp, behind authentication.</td>
    </tr>
    <tr>
      <td data-label="Layer">Sync</td>
      <td data-label="What it uses">EventBridge on a schedule and an SQS queue start the sync Lambdas. Plaid Link is the read-only bank connection.</td>
    </tr>
    <tr>
      <td data-label="Layer">Data and secrets</td>
      <td data-label="What it uses">DynamoDB tables encrypted with KMS. Plaid access tokens are kept only in AWS Secrets Manager.</td>
    </tr>
    <tr>
      <td data-label="Layer">Observability</td>
      <td data-label="What it uses">CloudWatch logs.</td>
    </tr>
  </tbody>
</table>
</div>

### Design decisions

1. **Read-only bank access, and no bank passwords.** Linking happens in Plaid Link. Cento never collects a bank password. The connection can read transactions and balances. It is not a path for moving money.

2. **Plaid tokens stay in Secrets Manager.** Access tokens are stored only there, not in the portal, the browser or the repository. A sync Lambda reads the token when it needs to call Plaid. The MCP connections never see bank logins or tokens.

3. **Money data is encrypted with KMS.** Transactions, balances and the rest of the stored money data sit in DynamoDB tables encrypted with KMS.

4. **The MCP server is behind auth and cannot write.** Each AI connection is for one client, with its own access and expiry, and every call is logged. The assistants that can connect are Claude, ChatGPT, Grok and Cursor. They reach `/mcp` only after authentication. They can coach on the user's own data. They cannot change it.

5. **Invite-only sign-in, with optional MFA.** Cognito is the user pool. There is no open sign-up. Invite email is sent through SES from cento.coach. MFA is optional, using an authenticator app when it is turned on.

6. **Sharing is by invite.** The owner invites someone to a profile and chooses what they can do. A view-only invite is limited to spending, bills, categories and rules. Sharing can be stopped, and a stopped share ends that person's access.

7. **One public hostname.** Visitors use cento.coach. www redirects to that apex. CloudFront sits in front of the S3 portal and API Gateway, so the browser has a single front door.

<figure class="diagram">
  <img src="{{ '/assets/img/cento/architecture.svg' | relative_url }}" alt="Cento layers: the browser and AI assistants, CloudFront with www redirecting to the apex, S3 and API Gateway, Cognito and SES, API and sync Lambdas and the MCP endpoint, EventBridge, SQS and Plaid, then DynamoDB, Secrets Manager and CloudWatch.">
  <figcaption>The browser reaches CloudFront at cento.coach. Sync pulls from Plaid into KMS-encrypted tables. AI assistants use the read-only MCP endpoint.</figcaption>
</figure>

<figure class="diagram">
  <img src="{{ '/assets/img/cento/flow.svg' | relative_url }}" alt="Journey from opening cento.coach, through Cognito sign-in, read-only Plaid Link, EventBridge and SQS sync, KMS-encrypted DynamoDB, the portal, and a read-only AI coach on /mcp.">
  <figcaption>Sign in, connect a bank read-only, and sync into encrypted tables. The portal and an AI coach both read that data.</figcaption>
</figure>

## Status and links

- Status: v1 · invite-only
- Released: October 23, 2026
- Web: [cento.coach](https://cento.coach)

<p><a class="btn" href="https://cento.coach">Visit cento.coach</a></p>

## Get involved

Cento v1 is invite-only. If you'd like an invite, message me on [LinkedIn](https://www.linkedin.com/in/ramybassem).

## Source code

Code lives in a private repository: [github.com/{{ site.github_user }}/cento](https://github.com/{{ site.github_user }}/cento) (private, not publicly viewable).

<p class="note">This link shows a 404 page to visitors. That's by design: the repository is private, and this portfolio repo holds no product code.</p>

## Brand

<div class="brand-lockup">
  <img src="{{ '/assets/img/cento/app-icon.png' | relative_url }}" width="72" height="72" alt="Cento logo">
</div>

<img src="{{ '/assets/img/cento/lockup.png' | relative_url }}" width="360" alt="Cento Coach. Every cent, in plain sight.">

<ul class="swatches">
  <li><span class="chip" style="background:#0F3D3E" aria-hidden="true"></span> #0F3D3E ink</li>
  <li><span class="chip" style="background:#E0A458" aria-hidden="true"></span> #E0A458 gold</li>
  <li><span class="chip" style="background:#F7F5F0" aria-hidden="true"></span> #F7F5F0 canvas</li>
  <li><span class="chip" style="background:#BDC8C4" aria-hidden="true"></span> #BDC8C4 tint</li>
  <li><span class="chip" style="background:#5A7A78" aria-hidden="true"></span> #5A7A78 secondary</li>
  <li><span class="chip" style="background:#FFFFFF" aria-hidden="true"></span> #FFFFFF surface</li>
</ul>
