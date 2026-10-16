---
layout: product
title: Aiglo Services
permalink: /products/aiglo-services/
accent: "#004A99"
accent_ink: "#1A1A1A"
accent_soft: "#ffffff"
font: "Roboto, Arial, sans-serif"
mark: /assets/img/aiglo-services/logo.png
mark_alt: "Aiglo Services eagle logo"
---

*Commercial supplies for businesses*

## Why it exists

My sister runs a commercial supply business. Her customers needed a simple way to see product options and get a clear quote before committing to quantities, so I built the e-commerce site for her.

## What it is

A bilingual (English and French) e-commerce and quote site for Aiglo Services, my sister's commercial supply business serving Vaudreuil and the surrounding area.

## Who it's for

Businesses in Vaudreuil and the surrounding area that buy commercial supplies and want to compare options and request a quote.

## Key features

- **Bilingual French and English on every page.** French is the default, and a one-click FR/EN toggle switches the whole interface: navigation, category pages, product cards and the quote list. The site remembers the visitor's choice across pages and visits.
- **Quote-first purchasing, built for B2B.** Products show no price and there is no checkout. Visitors build a quote list and send it, and Aiglo replies to confirm availability, quantities, pricing and delivery.
- **A catalog of more than 200 products in eight commercial categories:** cleaning products, food packaging, cups and lids, bags, trash bags, plates and utensils, paper products and gloves.
- **Product pages built for repeat buyers.** Each product has a bilingual name, pack size and an Aiglo product code that buyers can quote back. Every product card and product page has an Add to Quote button.
- **Site-wide product search** in the header of the homepage, category pages and product pages.
- **Guest-friendly quote list.** No account is needed. A visitor's quote list stays with them as they move between categories and product pages.
- **A partner section for Art Wood.** Aiglo Services is the Canadian representative for Art Wood, a maker of church furniture and architectural woodwork. This section has a portfolio gallery, a capabilities overview and an inquiry path for churches, mosques and institutions.
- **A St. Anthony Monastery support section.** It has its own themed catalog of monastery products and gift-shop items. It uses the same quote list, and it states clearly that payment goes directly to the monastery and never through Aiglo.

## How it works

A purchasing manager, restaurant owner or facilities lead arrives on the homepage. The site opens in French, and they can switch to English with one click. The homepage introduces Aiglo as a local supplier and shows the eight product categories. It also explains the quote process in three steps: choose a category, review product details and request pricing.

The visitor opens a category, such as Gloves, and browses product cards. They can also use the search bar to jump straight to an item. Each product page shows the bilingual product name, the pack size and the Aiglo product code, so the buyer knows exactly what they are asking for. A click on Add to Quote puts the item on a quote list without leaving the page. The visitor keeps browsing and adds items from other categories as needed.

When they are ready, they open the quote list, adjust quantities and fill in a short form with their name, company, email, phone and comments. Then they send the request and land on a confirmation page. Aiglo Services reviews the request and contacts the customer directly to confirm availability, quantities, pricing and next steps.

Visitors who came for Art Wood woodwork or St. Anthony Monastery items follow the same pattern in their own themed sections: browse, collect, send a request and get a personal reply.

## Architecture

### Overview

I built the site on a managed commerce platform rather than a custom application, because a small business owner has to run it day to day without a developer. WordPress and WooCommerce hold the product catalog. Elementor Pro handles page layout and templates. A request-a-quote plugin replaces the usual cart and checkout. Hostinger provides hosting and edge caching, and LiteSpeed provides page caching. On top of this base I added a small number of focused front-end modules: a bilingual language layer, consistent page shells for headers and footers, enhanced product cards and product pages, header search, and a fix that keeps guest quote lists intact. I designed each module first as a standalone HTML prototype, compared design options side by side, and only then moved the chosen version into Elementor. The result is a site the owner can maintain herself, with custom code limited to what the platform could not do well.

### Stack by layer

<div class="table-wrap">
<table>
  <thead>
    <tr><th>Layer</th><th>What is used</th></tr>
  </thead>
  <tbody>
    <tr>
      <td data-label="Layer">Front end</td>
      <td data-label="What is used">Elementor and Elementor Pro on the Hello Biz theme. Custom HTML, CSS and JavaScript modules provide the bilingual layer, page shells, product cards, product page layout and search.</td>
    </tr>
    <tr>
      <td data-label="Layer">Hosting / CDN</td>
      <td data-label="What is used">Hostinger managed WordPress hosting with Hostinger's CDN in front. LiteSpeed server with the LiteSpeed Cache plugin for full-page caching.</td>
    </tr>
    <tr>
      <td data-label="Layer">Back end</td>
      <td data-label="What is used">WordPress (PHP) with WooCommerce for products, categories and product pages.</td>
    </tr>
    <tr>
      <td data-label="Layer">Data</td>
      <td data-label="What is used">The standard WordPress and WooCommerce database, managed by the host. Language choice and the guest quote-list identifier are stored in the visitor's browser.</td>
    </tr>
    <tr>
      <td data-label="Layer">Email / forms</td>
      <td data-label="What is used">ELEX Request a Quote plugin for the quote list, the request form and the confirmation page. Art Wood inquiries go by direct email or phone. Follow-up with customers is done by people, not automated.</td>
    </tr>
    <tr>
      <td data-label="Layer">Admin</td>
      <td data-label="What is used">The standard WordPress, WooCommerce and Elementor editors, used by the business owner to manage products and content.</td>
    </tr>
    <tr>
      <td data-label="Layer">Deploy</td>
      <td data-label="What is used">Manual. I design in standalone HTML prototypes, then publish through Elementor templates, HTML widgets and site-wide custom code. Prototypes, snippets and design assets are version-controlled in a private Git repository. There is no CI/CD pipeline.</td>
    </tr>
    <tr>
      <td data-label="Layer">Observability</td>
      <td data-label="What is used">Google Analytics through Google Site Kit, and Google Search Console for indexing with the Rank Math sitemap.</td>
    </tr>
    <tr>
      <td data-label="Layer">SEO</td>
      <td data-label="What is used">Rank Math for metadata and XML sitemaps.</td>
    </tr>
    <tr>
      <td data-label="Layer">Payments</td>
      <td data-label="What is used">Not used. There is no online checkout. Pricing and payment are arranged after the quote.</td>
    </tr>
  </tbody>
</table>
</div>

### Design decisions

1. **Quote-only commerce on top of WooCommerce.** WooCommerce is the catalog engine. Prices stay hidden, and every purchase goes through a request-a-quote flow instead of a cart and checkout. B2B supply pricing depends on quantity, delivery location and stock. A quote fits how the business actually sells, and WooCommerce still provides structured products, categories and product pages. Customers cannot buy instantly, and every request needs a manual reply.

2. **One bilingual page per URL with a browser-side language toggle.** Each page carries both French and English content. A small script shows one language, defaults to French, and remembers the visitor's choice in the browser. The same layer also translates the text that plugins generate, such as the quote list, buttons and pagination. The business keeps one catalog and one set of pages. French comes first for the Quebec market. Because the language is chosen in the browser and not on the server, cached pages work for both languages. There are no separate French and English URLs, so search engines see one mixed-language page. The switch depends on JavaScript.

3. **Managed platform first, custom code only where needed.** WordPress, WooCommerce, Elementor and existing plugins handle what they handle well. Custom code is only for the gaps: the bilingual layer, page shells, product cards, search and the quote fix. The owner can add products and edit content without a developer. The site depends on third-party plugins, so a plugin update can require small adjustments.

4. **Reliable guest quote lists.** A small module makes sure every guest has one stable quote-list identifier on catalog and product pages, with a browser-side backup, and keeps the quote buttons labeled in the active language. Buyers don't need an account, and they must not lose their list when they move between pages. It works around the plugin's behavior, so it is tied to how the plugin identifies guests.

5. **Prototype first, then build in Elementor.** I design the homepage, product cards, Art Wood page and churches page as standalone HTML prototypes, often with several options side by side, before building the chosen version in WordPress. Design choices can be compared with the business owner without touching the live site. The prototype and the live version are separate copies and have to be kept in step by hand.

6. **Partner sections on the same platform, each with its own look.** The Art Wood representative section and the St. Anthony Monastery section live on the same site, each with its own page shell and branding. Both reuse the catalog and the quote list. This is one system to maintain and one quote process. The St. Anthony section states plainly that payment goes directly to the monastery. One site serves several audiences, so navigation and styling need care.

7. **Caching at the edge and on the server, without tuning by hand.** Hostinger's CDN works together with LiteSpeed full-page caching. Personalization such as language and the quote list happens in the browser, so pages stay cache-friendly. Pages load quickly on a small hosting plan. After content edits, a cache purge may be needed before changes show up.

<figure class="diagram">
  <img src="{{ '/assets/img/aiglo-services/architecture.svg' | relative_url }}" alt="Aiglo Services layers: the visitor browser and language toggle, Hostinger and LiteSpeed, WordPress with Elementor, WooCommerce and the quote plugin, then analytics and the Aiglo team.">
  <figcaption>A visitor request moves through Hostinger and LiteSpeed to WordPress, then to a person at Aiglo.</figcaption>
</figure>

<figure class="diagram">
  <img src="{{ '/assets/img/aiglo-services/flow.svg' | relative_url }}" alt="Customer journey from the French homepage through browsing, adding products to a quote, and receiving a personal reply.">
  <figcaption>From the homepage to a personal reply. There is no checkout.</figcaption>
</figure>

## Status and links

- Status: live
- Web: [aigloservices.ca](https://aigloservices.ca)

<p><a class="btn" href="https://aigloservices.ca">Visit aigloservices.ca</a></p>

## Get involved

If your business is in the Vaudreuil area and needs commercial supplies, browse the catalog and request a quote at [aigloservices.ca](https://aigloservices.ca).

## Source code

Code lives in a private repository: [github.com/{{ site.github_user }}/aigloservices](https://github.com/{{ site.github_user }}/aigloservices) (private, not publicly viewable).

<p class="note">This link shows a 404 page to visitors. That's by design: the repository is private, and this portfolio repo holds no product code.</p>

## Brand

<div class="brand-lockup">
  <img src="{{ '/assets/img/aiglo-services/logo.png' | relative_url }}" width="72" height="72" alt="Aiglo Services eagle logo">
</div>

<ul class="swatches">
  <li><span class="chip" style="background:#004A99" aria-hidden="true"></span> #004A99 primary</li>
  <li><span class="chip" style="background:#007BFF" aria-hidden="true"></span> #007BFF accent</li>
  <li><span class="chip" style="background:#1A1A1A" aria-hidden="true"></span> #1A1A1A ink</li>
  <li><span class="chip" style="background:#333333" aria-hidden="true"></span> #333333 secondary</li>
</ul>
