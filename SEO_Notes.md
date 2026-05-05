

# 🔍 SEO — Complete Developer Notes

> **Search Engine Optimization** is the discipline of making your website *discoverable, understandable, and trustworthy* to search engines — so users find you organically, without paid ads.

---

## Table of Contents

1. [What is SEO?](#what-is-seo)
2. [How Search Engines Work](#how-search-engines-work)
3. [The Three Types of SEO](#the-three-types-of-seo)
   - [On-Page SEO](#on-page-seo)
   - [Off-Page SEO](#off-page-seo)
   - [Technical SEO](#technical-seo)
4. [Implementing SEO with Next.js](#implementing-seo-with-nextjs)
   - [Metadata](#1--metadata)
   - [Sitemap](#2--sitemap)
   - [Robots.txt](#3--robotstxt)
5. [Structured Data — JSON-LD](#structured-data--json-ld)
   - [What is JSON-LD?](#what-is-json-ld)
   - [How Google Uses It](#how-google-uses-it)
   - [The @graph Pattern](#the-graph-pattern)
   - [Common Schema Types](#common-schema-types)
   - [Static Page Example (About Page)](#static-page-example--about-page)
   - [JSON-LD in Next.js](#json-ld-in-nextjs)
6. [Dynamic SEO — End to End](#dynamic-seo--end-to-end)
   - [The Mental Model](#the-mental-model)
   - [Step-by-Step: Blog Post Page](#step-by-step-blog-post-page)
   - [Dynamic Metadata](#step-1--generate-dynamic-metadata)
   - [Dynamic JSON-LD](#step-2--generate-dynamic-json-ld)
   - [Dynamic Sitemap](#step-3--dynamic-sitemap)
   - [The Full File Together](#the-full-file-together)
7. [Quick Reference Cheatsheet](#quick-reference-cheatsheet)

---

## What is SEO?

Imagine you open a beautiful shop — but you build it in a hidden alley with no signboard, no address, and no listing anywhere. People simply will not find it, no matter how great your products are.

**That's a website without SEO.**

SEO (Search Engine Optimization) is the process of improving your website so that search engines like Google can:
- **Discover** your pages through crawling
- **Understand** what each page is about
- **Rank** your pages when users search for related topics

Without SEO, your site may exist but only one page (or none at all) shows up in results — and even that, very low. With SEO applied correctly, every page becomes a separate entry point. One page can rank for `"buy shoes"`, another for `"best running shoes"`, another for `"how to clean shoes"` — multiplying your surface area in search results.

> **In short:** SEO transforms your website from invisible to discoverable — from one unclear result to many targeted, ranked pages.

---

## How Search Engines Work

Search engines operate in three sequential stages. Understanding them is fundamental to understanding *why* SEO works.

```
[ Your Website ] ──► Crawling ──► Indexing ──► Ranking ──► [ User Sees You ]
```

### 🕷️ Stage 1 — Crawling

Search engines deploy automated bots (called **spiders** or **crawlers**) that travel across the internet by following links — from page to page, site to site.

- If your pages are not linked (internally or from external sites), bots may never find them
- If your site structure is broken or your `robots.txt` blocks crawlers, pages get skipped entirely

> **Crawling = "Can the bot find your page?"**

### 📚 Stage 2 — Indexing

Once a page is discovered, Google tries to *understand* it. It reads:
- Your content, headings, and keywords
- Image alt texts
- Internal link structure
- Page metadata

All of this gets stored in Google's massive database — the **Index**. Think of it like being catalogued into a digital library. Pages that are unclear, blocked, or poorly structured may not be indexed at all.

> **Indexing = "Does Google understand your page?"**

### 🏆 Stage 3 — Ranking

When a user searches, Google scans its index and returns the most relevant, high-quality pages in order. This is where the real competition happens.

**Key ranking factors include:**

| Factor | What It Means |
|---|---|
| **Content Quality** | Is the content useful, accurate, and complete? |
| **Keyword Relevance** | Does it match what the user is searching for? |
| **Page Speed** | Does the site load quickly? |
| **Mobile Friendliness** | Is it usable on all screen sizes? |
| **Backlinks** | Do trusted websites link to you? |
| **User Experience** | Do users engage and stay, or bounce immediately? |
| **Search Intent Match** | Does the page answer the *real* question behind the search? |

> **Ranking = "How high does your page appear?"**

---

## The Three Types of SEO

```
                    ┌─────────────────────────────────────┐
                    │              SEO                    │
                    └───────────┬────────────┬────────────┘
                                │            │
                    ┌───────────▼─┐  ┌───────▼──────┐  ┌──────────────────┐
                    │  On-Page    │  │  Off-Page    │  │  Technical SEO   │
                    │  SEO        │  │  SEO         │  │                  │
                    └─────────────┘  └──────────────┘  └──────────────────┘
                    Content &        Trust &            Crawlability &
                    structure        authority          infrastructure
```

---

### On-Page SEO

On-page SEO is about optimizing **everything inside a web page** — your content, structure, and HTML elements — so search engines and users can immediately understand what the page is about.

**Core on-page elements:**

| Element | Purpose | Best Practice |
|---|---|---|
| `<title>` tag | Primary ranking signal; shown in browser tab & search results | Include main keyword; keep under 60 chars |
| `<meta name="description">` | Shown below title in search results; affects CTR | Write a compelling summary; 150–160 chars |
| `<h1>` heading | Tells Google the main topic of the page | One per page; include primary keyword |
| `<h2>`, `<h3>` headings | Structure and subtopics | Use naturally; don't keyword stuff |
| URL Slug | Signals page topic | Use short, readable, keyword-rich URLs |
| Image `alt` text | Tells bots what images contain | Descriptive, relevant, not stuffed |
| Internal links | Connects related content; helps crawling | Link to related pages naturally |
| Canonical URL | Tells Google which URL is the "real" one | Prevents duplicate content penalties |

**Classic HTML example (before frameworks):**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Best Running Shoes | Free Delivery</title>
  <meta name="description" content="Buy the best running shoes with comfort and durability at affordable prices.">
  <link rel="canonical" href="https://example.com/running-shoes" />
</head>
<body>
  <h1>Best Running Shoes</h1>
  <p>Find the best running shoes for men and women with high comfort and durability.</p>

  <h2>Top Features</h2>
  <p>Lightweight, breathable material, long-lasting sole.</p>

  <img src="shoes.jpg" alt="Best running shoes for men" />

  <a href="/shoes">Explore more shoes</a>
</body>
</html>
```

> ⚠️ **Keyword stuffing** (forcing too many keywords unnaturally) is penalized by Google. Write for humans first — keywords should flow naturally.

---

### Off-Page SEO

Off-page SEO is about building your website's **reputation and authority** outside your own pages — so search engines conclude that your content is worth trusting.

The most powerful off-page signal is the **backlink** — a link from another website pointing to yours.

**Not all backlinks are equal:**

```
High Authority Site links to you  ──►  Strong positive signal ✅
Random/Spammy Site links to you   ──►  Weak or negative signal ❌
Many low-quality links            ──►  May trigger spam filter 🚨
Few high-quality, relevant links  ──►  Significant ranking boost ✅
```

Think of it like real-world credibility — a recommendation from a respected expert in your field means far more than praise from a stranger.

**Common off-page SEO strategies:**

| Strategy | Description |
|---|---|
| **Guest Blogging** | Write valuable articles on reputable sites in your niche, with a link back to yours |
| **Digital PR** | Get mentioned or linked from news outlets and industry publications |
| **Content Marketing** | Publish genuinely useful content that people naturally want to share and link to |
| **Brand Mentions** | Getting mentioned by name, even without links, adds to trust signals |
| **Social Signals** | Shares and engagement on social platforms build indirect visibility |

> **The underlying principle:** Great content earns genuine backlinks. Low-quality or spammy link-building hurts more than it helps. Search engines are designed to reward trustworthiness — and they're getting better at detecting artificial signals every year.

---

### Technical SEO

Technical SEO ensures that your website's **infrastructure** is solid — that crawlers can access, understand, and efficiently process your pages. No matter how great your content is, poor technical SEO can prevent it from ever being indexed.

**Two foundational files:**

#### `sitemap.xml` — The Tour Guide

A sitemap lists all important URLs on your site, telling search engines exactly where to look.

```
sitemap.xml
├── /                     priority: 1.0, weekly
├── /about                priority: 0.8, monthly
├── /products             priority: 0.9, monthly
├── /blogs                priority: 0.7, weekly
└── /contact              priority: 0.6, yearly
```

Without a sitemap, crawlers have to discover pages on their own — meaning some pages, especially deeply nested ones, may never be found.

#### `robots.txt` — The Gatekeeper

This file tells search engine bots what they're allowed to crawl and what to skip.

```
User-agent: *           ← Applies to all bots
Allow: /                ← Allow everything by default

Disallow: /_next/       ← Skip Next.js internals
Disallow: /api/         ← Skip API routes
Disallow: /admin/       ← Skip admin panels

Sitemap: https://example.com/sitemap.xml   ← Point bots to your sitemap
```

> **Together:** The sitemap tells bots *where to go*, and robots.txt tells them *where not to go*. Misconfiguring either can silently break your SEO without any obvious error.

**Other critical technical SEO factors:**

| Factor | Why It Matters |
|---|---|
| **Core Web Vitals** | Google uses LCP, FID, CLS as direct ranking signals |
| **HTTPS** | Sites without SSL are flagged as insecure — ranking is penalized |
| **Mobile-First Indexing** | Google indexes your mobile version first |
| **Page Speed** | Slow pages lose rankings and users |
| **Structured Data (JSON-LD)** | Helps Google display rich snippets (stars, FAQs, breadcrumbs) |
| **Hreflang tags** | For multilingual sites — tells Google which language to serve to which region |

---

## Implementing SEO with Next.js

Next.js provides first-class SEO support through its Metadata API, built-in file conventions, and server-side rendering. Everything is handled at the framework level — no manual `<head>` tag management needed.

> ⚠️ **Critical rule:** Static metadata only works in **Server Components**. Never export `metadata` from a Client Component (`"use client"`). Next.js will silently ignore it.

---

### 1 — Metadata

The `metadata` export in Next.js automatically generates `<title>`, `<meta>`, Open Graph, and Twitter Card tags. It must be used in Server Components, and each page in Next.js has its own metadata object to represent that particular page.

```ts
// app/page.tsx (Server Component)
import { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Automatorr | Automation, CRM & Technology Solutions — Sydney, Australia',
  description: 'Automatorr is Sydney\'s trusted technology partner. Delivering automation, CRM, managed IT, software engineering and global talent across Australia and APAC. Est. 2006.',
  alternates: {
    canonical: 'https://www.automatorr.com/',
  },
  keywords: [
    "automation services Australia",
    "workflow automation Sydney",
    "CRM implementation Australia",
    "RPA consulting",
    "managed IT services Sydney",
    "technology partner Australia"
  ],
  openGraph: {
    siteName: "Automatorr",
    locale: 'en_AU',
    url: 'https://www.automatorr.com/',
    title: 'Automatorr — Automation & Technology Solutions, Sydney',
    description: 'Automation, CRM, managed IT and global talent for Australian businesses. 50+ clients. Est. 2006.',
    images: [{
      url: 'https://www.automatorr.com/og/homepage.png',
      width: 1200,
      height: 630,
      alt: 'Automatorr — Automation & Technology Solutions, Sydney'
    }],
    type: 'website',
  },
  twitter: {
    site: "@automatorr_nair",
    card: 'summary_large_image',
    title: 'Automatorr — Automation & Technology Solutions, Sydney',
    description: 'Automation, CRM, managed IT and global talent for Australian businesses. 50+ clients. Est. 2006.',
    images: ['https://www.automatorr.com/og/homepage.png'],
  },
};
```

**Full metadata field reference:**

| Field | What It Does | Why It Matters |
|---|---|---|
| `title` | Page title in browser tab and search results | Primary ranking signal; directly affects CTR |
| `description` | Short summary shown under title in search | Improves click-through rate (does not affect ranking directly) |
| `alternates.canonical` | Declares the authoritative URL of the page | Prevents duplicate content from hurting rankings |
| `keywords` | Keyword hints for search engines | Minor signal today, but still useful for context |
| `openGraph.siteName` | Website brand name in social previews | Strengthens branding in shared links |
| `openGraph.locale` | Language and region (e.g. `en_AU`) | Helps target the right geographic audience |
| `openGraph.url` | Canonical URL for social sharing | Ensures correct link is used when shared |
| `openGraph.title` | Title shown in link previews on social platforms | Boosts engagement and clicks on social |
| `openGraph.description` | Description in social link previews | Gives users context before clicking |
| `openGraph.images` | Preview image (with dimensions and alt text) | Makes shared links visually rich and attractive |
| `openGraph.type` | Content type (`website`, `article`, `product`) | Helps platforms understand content format |
| `twitter.site` | Your Twitter handle | Adds credibility and branding |
| `twitter.card` | Card style (`summary_large_image`, `summary`) | Controls visual layout on Twitter/X |
| `twitter.title` | Title in Twitter card | Drives engagement |
| `twitter.description` | Description in Twitter card | Helps users understand content at a glance |
| `twitter.images` | Image in Twitter preview | Significantly increases click-through on Twitter |

---

### 2 — Sitemap

In Next.js, create `app/sitemap.ts` and it automatically generates `/sitemap.xml`. The sitemap lists all the endpoints available in your app.

```ts
// app/sitemap.ts
import { MetadataRoute } from 'next';

export default function sitemap(): MetadataRoute.Sitemap {
  const base = 'https://www.automatorr.com';
  const now = new Date();

  return [
    // Highest priority — homepage
    { url: base,                                      lastModified: now, changeFrequency: 'weekly',  priority: 1.0 },

    // Core sections
    { url: `${base}/about`,                           lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions`,                       lastModified: now, changeFrequency: 'monthly', priority: 0.9 },
    { url: `${base}/services`,                        lastModified: now, changeFrequency: 'monthly', priority: 0.9 },

    // Solutions sub-pages
    { url: `${base}/solutions/coms`,                  lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/ai-bot`,                lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/invoice-automation`,    lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/contract-automation`,   lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/clockit`,               lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/creatio`,               lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/business-central`,      lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/compliance`,            lastModified: now, changeFrequency: 'monthly', priority: 0.8 },

    // Services sub-pages
    { url: `${base}/services/automation-services`,    lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/crm-services`,           lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/msp-services`,           lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/software-engineering`,   lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/global-talent`,          lastModified: now, changeFrequency: 'monthly', priority: 0.8 },

    // Content & engagement pages
    { url: `${base}/case-studies`,                    lastModified: now, changeFrequency: 'weekly',  priority: 0.7 },
    { url: `${base}/blogs`,                           lastModified: now, changeFrequency: 'weekly',  priority: 0.7 },

    // Lowest priority — rarely changes
    { url: `${base}/contact-us`,                      lastModified: now, changeFrequency: 'yearly',  priority: 0.6 },
  ];
}
```

**`changeFrequency` guide:**

| Value | Use When |
|---|---|
| `always` | Content changes with every visit (live dashboards, feeds) |
| `hourly` | News or real-time data pages |
| `daily` | High-volume content sites, e-commerce listings |
| `weekly` | Blogs, case studies, portfolio pages |
| `monthly` | Product or service pages that rarely change |
| `yearly` | Legal pages, contact pages, "about" pages |
| `never` | Archived or historical content |

---

### 3 — Robots.txt

In Next.js, create `app/robots.ts` and it generates `/robots.txt` automatically.

```ts
// app/robots.ts
import { MetadataRoute } from 'next';

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/_next/', '/api/', '/admin/'],
    },
    sitemap: 'https://www.automatorr.com/sitemap.xml',
  };
}
```

Or as a static file at `public/robots.txt`:

```txt
User-agent: *
Allow: /

# Block Next.js internals
Disallow: /_next/

# Block API and private routes
Disallow: /api/
Disallow: /admin/

# Sitemap location
Sitemap: https://www.automatorr.com/sitemap.xml
```

> 💡 **File location rule:**
> - `app/sitemap.ts` → auto-generates `/sitemap.xml`
> - `app/robots.ts` → auto-generates `/robots.txt`
> - `public/sitemap.xml` or `public/robots.txt` → static files served directly
>
> Use `app/` files for dynamic generation, `public/` for simple static config.

---

## Structured Data — JSON-LD

### What is JSON-LD?

So far, everything we've covered tells Google *surface-level* things about your page — the title, description, and which URLs exist. But search engines are becoming increasingly intelligent, and they want to understand the *meaning* behind your content, not just the words.

**JSON-LD (JavaScript Object Notation for Linked Data)** is a structured data format that lets you describe the entities on your page in a way that machines can definitively understand.

Think of it this way: when a human reads your About page, they naturally understand "Oh, this company was founded in 2006, they're based in Sydney, and this person named Rajesh Nair started it." A search engine reading raw HTML struggles to make those same connections reliably. JSON-LD gives you a way to explicitly state those facts in a universal machine-readable format.

```
What metadata does           →   "Here's the title and description"
What JSON-LD does            →   "This is an Organization. Its founder is a Person.
                                  That Person's name is Rajesh Nair. The organization
                                  was founded in 2006. It is located in Sydney."
```

> **In short:** Metadata is for browsers and social previews. JSON-LD is for search engines to deeply understand the *entities* on your page.

### How Google Uses It

When Google reads your JSON-LD correctly, it can unlock **rich results** — enhanced search listings that go far beyond the basic blue link. These visually stand out and dramatically increase click-through rates.

```
Without JSON-LD:
┌────────────────────────────────────────────────────────────────┐
│ Automatorr | Technology Partner Sydney                         │
│ https://www.automatorr.com/about                               │
│ Delivering automation and CRM solutions since 2006.            │
└────────────────────────────────────────────────────────────────┘

With JSON-LD (Article schema on a blog):
┌────────────────────────────────────────────────────────────────┐
│ How to Automate Your Invoice Processing                        │
│ https://www.automatorr.com/blogs/invoice-automation            │
│ ★★★★★  12 reviews · By Rajesh Nair · May 2, 2025             │
│ Learn how to cut invoice processing time by 80% using RPA...   │
└────────────────────────────────────────────────────────────────┘
```

**Types of rich results JSON-LD can unlock:**

| Schema Type | What It Shows in Google |
|---|---|
| `Article` / `BlogPosting` | Author name, publish date, breadcrumbs |
| `Product` | Price, availability, star ratings |
| `FAQPage` | Expandable Q&A directly in search results |
| `Organization` | Knowledge panel with logo, socials, founding date |
| `Person` | Author profile in Google's Knowledge Graph |
| `BreadcrumbList` | Navigation trail shown under the URL |
| `WebSite` | Enables the search box within your Google listing |
| `LocalBusiness` | Map listing, hours, phone number |
| `Event` | Date, location, ticket link in results |
| `Recipe` | Cooking time, ingredients, star rating |

---

### The `@graph` Pattern

You'll notice in the real-world example that JSON-LD uses something called `@graph`. This is important to understand.

A webpage is rarely about just *one* thing. An About page is simultaneously:
- A `WebPage` (a URL that exists on the internet)
- About an `Organization` (the company)
- Written by or featuring a `Person` (the founder)

You could write three separate JSON-LD `<script>` blocks — one for each entity. But the `@graph` pattern lets you describe all of them **in a single script block** and, crucially, **link them together** using `@id` references.

```
Without @graph — three disconnected facts:
  Script 1: "There is a WebPage at /about"
  Script 2: "There is an Organization called Automatorr"
  Script 3: "There is a Person called Rajesh Nair"

With @graph — three connected facts:
  "There is a WebPage at /about
   └─ which is ABOUT the Organization #automatorr-org
   The Organization #automatorr-org
   └─ has a FOUNDER who is Person #rajesh-nair
   The Person #rajesh-nair
   └─ WORKS FOR Organization #automatorr-org"
```

The `@id` field acts like a unique identifier — an internal anchor — so that different entities in the graph can reference each other. Google reads this as a connected knowledge graph, not just scattered data points. This is what allows Google to build rich Knowledge Panels about your brand.

**The pattern in code:**

```json
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "WebPage",
      "@id": "https://www.example.com/about#webpage",
      "name": "About Us",
      "about": {
        "@id": "https://www.example.com/#organization"  // ← points to the org below
      }
    },
    {
      "@type": "Organization",
      "@id": "https://www.example.com/#organization",   // ← referenced above
      "name": "Example Co",
      "founder": {
        "@id": "https://www.example.com/#founder"       // ← points to person below
      }
    },
    {
      "@type": "Person",
      "@id": "https://www.example.com/#founder",        // ← referenced above
      "name": "Jane Doe",
      "worksFor": {
        "@id": "https://www.example.com/#organization"  // ← back to the org
      }
    }
  ]
}
```

> **Rule of thumb for `@id` naming:** Use your domain as the base, then add a `#fragment` that describes the entity type. Keep them consistent across all pages — `#organization` should always refer to the same organization, site-wide.

---

### Common Schema Types

Before looking at complete examples, here is a reference for the fields you'll use most often in each schema type.

#### `Organization`

```json
{
  "@type": "Organization",
  "@id": "https://www.example.com/#organization",
  "name": "Example Co",
  "url": "https://www.example.com",
  "logo": {
    "@type": "ImageObject",
    "url": "https://www.example.com/logo.png",
    "width": 200,
    "height": 60
  },
  "foundingDate": "2006",
  "description": "A technology company based in Sydney.",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Sydney",
    "addressRegion": "NSW",
    "addressCountry": "AU"
  },
  "contactPoint": {
    "@type": "ContactPoint",
    "contactType": "customer support",
    "email": "hello@example.com"
  },
  "sameAs": [
    "https://www.linkedin.com/company/example-co",
    "https://twitter.com/exampleco"
  ]
}
```

#### `WebSite`

```json
{
  "@type": "WebSite",
  "@id": "https://www.example.com/#website",
  "url": "https://www.example.com",
  "name": "Example Co",
  "publisher": {
    "@id": "https://www.example.com/#organization"
  },
  "inLanguage": "en-AU"
}
```

#### `WebPage`

```json
{
  "@type": "WebPage",
  "@id": "https://www.example.com/about#webpage",
  "url": "https://www.example.com/about",
  "name": "About Example Co",
  "description": "Learn about our team and mission.",
  "isPartOf": { "@id": "https://www.example.com/#website" },
  "about": { "@id": "https://www.example.com/#organization" },
  "primaryImageOfPage": {
    "@type": "ImageObject",
    "url": "https://www.example.com/og/about.png",
    "width": 1200,
    "height": 630
  },
  "inLanguage": "en-AU"
}
```

#### `BlogPosting` / `Article`

```json
{
  "@type": "BlogPosting",
  "@id": "https://www.example.com/blogs/my-post#article",
  "headline": "How to Automate Your Invoice Processing",
  "description": "A step-by-step guide to cutting invoice processing time by 80%.",
  "url": "https://www.example.com/blogs/my-post",
  "datePublished": "2025-05-01T09:00:00+10:00",
  "dateModified": "2025-05-10T12:00:00+10:00",
  "author": {
    "@type": "Person",
    "name": "Rajesh Nair"
  },
  "publisher": {
    "@id": "https://www.example.com/#organization"
  },
  "image": {
    "@type": "ImageObject",
    "url": "https://www.example.com/blogs/my-post/cover.jpg",
    "width": 1200,
    "height": 630
  },
  "mainEntityOfPage": {
    "@id": "https://www.example.com/blogs/my-post#webpage"
  },
  "keywords": ["invoice automation", "RPA", "workflow"],
  "inLanguage": "en-AU"
}
```

#### `BreadcrumbList`

```json
{
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Home",
      "item": "https://www.example.com"
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "Blogs",
      "item": "https://www.example.com/blogs"
    },
    {
      "@type": "ListItem",
      "position": 3,
      "name": "How to Automate Your Invoice Processing",
      "item": "https://www.example.com/blogs/my-post"
    }
  ]
}
```

#### `FAQPage`

```json
{
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is RPA?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "RPA (Robotic Process Automation) is software technology that automates repetitive, rule-based digital tasks."
      }
    },
    {
      "@type": "Question",
      "name": "How long does implementation take?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "A typical RPA implementation takes between 4 and 12 weeks depending on complexity."
      }
    }
  ]
}
```

---

### Static Page Example — About Page

Here is the complete, production-ready About page with full JSON-LD, pulled from a real project. Study this carefully — it shows how `WebPage`, `Person`, and `Organization` are linked together in one `@graph`.

```tsx
// app/about/page.tsx

import AboutHero from '@/src/sections/about/hero'
import ComplexHorizontalScroll from '@/src/sections/about/scroll-info'
import TeamScrollMobile from '@/src/sections/about/team-mobile'
import React from 'react'
import { Metadata } from 'next'

// ─────────────────────────────────────────────
// STEP 1: Static Metadata (for <head> tags)
// ─────────────────────────────────────────────
export const metadata: Metadata = {
  title: 'About Automatorr | Technology Partner Sydney — Est. 2006',
  description: 'Automatorr has delivered technology solutions with clarity and care since 2006. Based in Sydney, serving Australia, New Zealand and APAC. Meet the team behind the work.',
  alternates: { canonical: 'https://www.automatorr.com/about' },
  keywords: [
    "technology partner Australia",
    "automation company Sydney",
    "about Automatorr",
    "Rajesh Nair CEO",
    "Australian technology consultancy",
    "APAC technology services"
  ],
  openGraph: {
    siteName: "Automatorr",
    locale: 'en_AU',
    url: 'https://www.automatorr.com/about',
    title: 'About Automatorr — Sydney Technology Partner Since 2006',
    description: 'Delivering automation, CRM and managed IT with clarity and care since 2006. 50+ clients. 83% avg ROI. Sydney, Australia.',
    images: [{
      url: 'https://www.automatorr.com/og/about.png',
      width: 1200,
      height: 630,
      alt: 'About Automatorr — Sydney Technology Partner Since 2006'
    }],
    type: 'website',
  },
  twitter: {
    site: "@automatorr_nair",
    card: 'summary_large_image',
    title: 'About Automatorr — Sydney Technology Partner Since 2006',
    description: 'Delivering automation, CRM and managed IT with clarity and care since 2006. 50+ clients. 83% avg ROI.',
    images: ['https://www.automatorr.com/og/about.png'],
  },
};

// ─────────────────────────────────────────────
// STEP 2: JSON-LD (for Google's Knowledge Graph)
// ─────────────────────────────────────────────
//
// This uses the @graph pattern to describe three connected entities:
//   1. The WebPage itself (the /about URL)
//   2. The Person (Rajesh Nair, the founder)
//
// Notice how they reference each other using @id — Google reads this
// as a connected graph, not isolated data points.
// ─────────────────────────────────────────────
const jsonLd = {
  "@context": "https://schema.org",
  "@graph": [
    {
      // Entity 1: The WebPage
      // Describes the /about page itself as a URL on the internet
      "@type": "WebPage",
      "@id": "https://www.automatorr.com/about#webpage",
      "url": "https://www.automatorr.com/about",
      "name": "About Automatorr | Technology Partner Sydney — Est. 2006",
      "description": "Automatorr has delivered technology solutions with clarity and care since 2006. Based in Sydney, serving Australia, New Zealand and APAC.",
      "isPartOf": {
        // This page belongs to the broader website entity
        "@id": "https://www.automatorr.com/#website"
      },
      "about": {
        // The subject of this page is the organization
        "@id": "https://www.automatorr.com/#organization"
      },
      "primaryImageOfPage": {
        "@type": "ImageObject",
        "url": "https://www.automatorr.com/og/about.png",
        "width": 1200,
        "height": 630,
        "caption": "About Automatorr — Sydney Technology Partner Since 2006"
      },
      "inLanguage": "en-AU"
    },
    {
      // Entity 2: The Person (Founder)
      // Describes Rajesh Nair as a real-world person entity
      "@type": "Person",
      "@id": "https://www.automatorr.com/#founder",
      "name": "Rajesh Nair",
      "jobTitle": "Founder",
      "worksFor": {
        // Links this person back to the organization entity
        "@id": "https://www.automatorr.com/#organization"
      },
      "description": "Founder of Automatorr, a Sydney-based technology partner delivering automation, CRM, and managed IT solutions since 2006."
    }
  ]
};

// ─────────────────────────────────────────────
// STEP 3: Page Component
// ─────────────────────────────────────────────
const page = () => {
  return (
    <>
      {/*
        The JSON-LD is injected as a raw <script> tag in the HTML.
        dangerouslySetInnerHTML is safe here — we own and control the data.
        Google's crawler reads this script tag from the raw HTML.
      */}
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      <AboutHero />
      <ComplexHorizontalScroll />
      <TeamScrollMobile />
    </>
  )
}

export default page
```

**What Google sees after parsing this page:**

```
Knowledge extracted:
  ✅ There is a WebPage at /about
  ✅ That page is about an Organization (defined elsewhere as #organization)
  ✅ There is a Person named Rajesh Nair
  ✅ Rajesh is the Founder and works for that same Organization
  ✅ The page has a primary image at /og/about.png
  ✅ The content is in Australian English
```

> 💡 Notice that `#organization` and `#website` are referenced with `@id` but not *defined* on this page. They are defined on the **homepage** (`/`) where it makes most sense — the About page simply *links to* them. This is the correct pattern: define global entities once (on your root or layout), reference them everywhere else.

---

### JSON-LD in Next.js

There are two ways to add JSON-LD in Next.js:

#### Method 1 — Inline in the Page Component (recommended for page-specific data)

```tsx
const jsonLd = { /* your schema */ };

export default function Page() {
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      {/* rest of page */}
    </>
  );
}
```

#### Method 2 — In the Root Layout (for site-wide entities)

Your `Organization` and `WebSite` schemas don't change per page — they describe your entire brand. Define them once in your root layout so they appear on every page.

```tsx
// app/layout.tsx
const siteJsonLd = {
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "Organization",
      "@id": "https://www.automatorr.com/#organization",
      "name": "Automatorr",
      "url": "https://www.automatorr.com",
      "logo": {
        "@type": "ImageObject",
        "url": "https://www.automatorr.com/logo.png",
        "width": 200,
        "height": 60
      },
      "foundingDate": "2006",
      "address": {
        "@type": "PostalAddress",
        "addressLocality": "Sydney",
        "addressRegion": "NSW",
        "addressCountry": "AU"
      },
      "sameAs": [
        "https://www.linkedin.com/company/automatorr",
        "https://twitter.com/automatorr_nair"
      ]
    },
    {
      "@type": "WebSite",
      "@id": "https://www.automatorr.com/#website",
      "url": "https://www.automatorr.com",
      "name": "Automatorr",
      "publisher": {
        "@id": "https://www.automatorr.com/#organization"
      },
      "inLanguage": "en-AU"
    }
  ]
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en-AU">
      <body>
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{ __html: JSON.stringify(siteJsonLd) }}
        />
        {children}
      </body>
    </html>
  );
}
```

> **Best practice:** Put `Organization` + `WebSite` in `layout.tsx`. Put `WebPage`, `BlogPosting`, `BreadcrumbList`, and other page-specific schemas inside each page file. This way, every page inherits the global brand identity and adds its own specific context on top.

---

## Dynamic SEO — End to End

### The Mental Model

Static pages are easy — you write your metadata and JSON-LD by hand once, and they never change. But real-world applications have hundreds or thousands of pages generated from data. Think:

- `/blogs/how-to-automate-invoices` — a specific blog post from your CMS
- `/products/blue-running-shoe-42` — a product from your database
- `/case-studies/client-x-rpa-project` — a case study fetched from an API

Every one of these pages needs its own unique metadata and JSON-LD — you can't write it by hand. **Dynamic SEO is the practice of generating all of this automatically from your data.**

Here's the complete mental model for how a dynamic page works in Next.js:

```
User visits: /blogs/how-to-automate-invoices
                      │
                      ▼
          Next.js extracts the slug
          slug = "how-to-automate-invoices"
                      │
          ┌───────────┴───────────────────────────┐
          │                                       │
          ▼                                       ▼
  generateMetadata()                     Page Component
  (runs on the server,                   (runs on the server,
   before the page renders)              renders the UI)
          │                                       │
          ▼                                       ▼
  fetch(`/api/blogs/${slug}`)       fetch(`/api/blogs/${slug}`)
          │                                       │
          ▼                                       ▼
  Builds <title>, <meta>,            Builds JSON-LD object from data
  Open Graph, Twitter Card           Renders <script> + page sections
          │                                       │
          ▼                                       ▼
  Injected into <head>              Injected into <body>
```

Notice that both functions fetch the same API. Next.js automatically **deduplicates** these fetch calls — if you call the same URL with the same options in the same render cycle, it only hits the network once. So there is no performance penalty for fetching in both places.

---

### Step-by-Step: Blog Post Page

Let's walk through building `/blogs/[slug]/page.tsx` from scratch, step by step.

**The file and folder structure:**

```
app/
└── blogs/
    ├── page.tsx              ← The /blogs listing page
    └── [slug]/
        └── page.tsx          ← The individual blog post page (dynamic)
```

The `[slug]` in the folder name is Next.js's dynamic routing syntax. When someone visits `/blogs/invoice-automation`, Next.js extracts `"invoice-automation"` as the `slug` parameter and passes it to your page.

---

### Step 1 — Generate Dynamic Metadata

```ts
// app/blogs/[slug]/page.tsx
import { Metadata, ResolvingMetadata } from 'next';

// The params object Next.js provides — always a Promise in Next.js 15+
type PageProps = {
  params: Promise<{ slug: string }>;
};

// generateMetadata runs SERVER-SIDE before the page renders
// Its job: fetch the blog post data and build the <head> tags from it
export async function generateMetadata(
  { params }: PageProps,
  parent: ResolvingMetadata
): Promise<Metadata> {

  // Step 1: Unwrap the slug from the params Promise
  const { slug } = await params;

  const baseUrl = 'https://www.automatorr.com';
  const postUrl = `${baseUrl}/blogs/${slug}`;

  try {
    // Step 2: Fetch the blog post data from your API using the slug
    // next: { revalidate: 3600 } means Next.js caches this for 1 hour (ISR)
    // After 1 hour, the next visitor triggers a fresh fetch in the background
    const response = await fetch(
      `${baseUrl}/api/blogs/${slug}`,
      { next: { revalidate: 3600 } }
    );

    if (!response.ok) throw new Error('Blog post not found');

    // Step 3: Parse the response
    const data = await response.json();
    // Assuming API returns: { title, subheading, image, tags, createdAt, author }

    // Step 4: Build and return the metadata object from real data
    return {
      title: `${data.title} | Automatorr Blog`,
      description: data.subheading,
      keywords: data.tags,
      alternates: {
        canonical: postUrl,
      },
      openGraph: {
        title: data.title,
        description: data.subheading,
        url: postUrl,
        siteName: 'Automatorr',
        locale: 'en_AU',
        type: 'article',                    // ← "article" not "website" for blog posts
        publishedTime: data.createdAt,      // ← OG supports publish date for articles
        authors: [data.author ?? 'Automatorr'],
        images: [{
          url: data.image,
          width: 1200,
          height: 630,
          alt: data.title,
        }],
      },
      twitter: {
        card: 'summary_large_image',
        title: data.title,
        description: data.subheading,
        images: [{ url: data.image, alt: data.title }],
      },
    };

  } catch (error) {
    // Step 5: Always provide a fallback — if the API fails, don't crash
    // A graceful fallback is better than a broken page with no metadata
    console.error(`Failed to generate metadata for blog/${slug}:`, error);
    return {
      title: 'Blog Post | Automatorr',
      description: 'Read our latest insights on automation and technology.',
    };
  }
}
```

---

### Step 2 — Generate Dynamic JSON-LD

The JSON-LD for a blog post is generated inside the **page component itself**, using the same fetched data. This gives Google rich structured data about the article — author, dates, breadcrumbs, and more.

```tsx
// Still in app/blogs/[slug]/page.tsx

// A helper function that takes your blog data and builds the JSON-LD object
// Keeping this separate makes it clean, testable, and easy to update
function buildBlogJsonLd(data: BlogData, slug: string) {
  const baseUrl = 'https://www.automatorr.com';
  const postUrl = `${baseUrl}/blogs/${slug}`;

  return {
    "@context": "https://schema.org",
    "@graph": [
      {
        // Entity 1: The individual blog post page (the URL)
        "@type": "WebPage",
        "@id": `${postUrl}#webpage`,
        "url": postUrl,
        "name": data.title,
        "description": data.subheading,
        "isPartOf": {
          "@id": `${baseUrl}/#website`         // ← references the WebSite defined in layout.tsx
        },
        "primaryImageOfPage": {
          "@type": "ImageObject",
          "url": data.image,
          "width": 1200,
          "height": 630,
          "caption": data.title
        },
        "inLanguage": "en-AU",
        "breadcrumb": {
          "@id": `${postUrl}#breadcrumb`        // ← references the breadcrumb below
        }
      },
      {
        // Entity 2: The article content itself
        // BlogPosting is a subtype of Article — use it for blog posts specifically
        "@type": "BlogPosting",
        "@id": `${postUrl}#article`,
        "headline": data.title,
        "description": data.subheading,
        "url": postUrl,
        "datePublished": data.createdAt,        // ← ISO 8601 format: "2025-05-01T09:00:00+10:00"
        "dateModified": data.updatedAt ?? data.createdAt,
        "author": {
          "@type": "Person",
          "name": data.author ?? "Automatorr Team",
        },
        "publisher": {
          "@id": `${baseUrl}/#organization`     // ← references the Organization from layout.tsx
        },
        "image": {
          "@type": "ImageObject",
          "url": data.image,
          "width": 1200,
          "height": 630
        },
        "keywords": data.tags?.join(", "),
        "mainEntityOfPage": {
          "@id": `${postUrl}#webpage`           // ← links article back to its WebPage
        },
        "inLanguage": "en-AU"
      },
      {
        // Entity 3: Breadcrumb navigation trail
        // Google shows this under the URL in search results — very high visibility
        "@type": "BreadcrumbList",
        "@id": `${postUrl}#breadcrumb`,
        "itemListElement": [
          {
            "@type": "ListItem",
            "position": 1,
            "name": "Home",
            "item": baseUrl
          },
          {
            "@type": "ListItem",
            "position": 2,
            "name": "Blogs",
            "item": `${baseUrl}/blogs`
          },
          {
            "@type": "ListItem",
            "position": 3,
            "name": data.title,              // ← the actual blog post title from the API
            "item": postUrl
          }
        ]
      }
    ]
  };
}
```

---

### Step 3 — Dynamic Sitemap

When you have many blog posts, you need the sitemap to list all of them — but you can't hard-code them. Fetch all slugs from your API and generate the entries dynamically.

```ts
// app/sitemap.ts
import { MetadataRoute } from 'next';

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const base = 'https://www.automatorr.com';

  // Fetch all blog post slugs from your API
  // Your API should return something like: ["invoice-automation", "crm-guide", "rpa-basics"]
  let blogSlugs: string[] = [];

  try {
    const res = await fetch(`${base}/api/blogs/all-slugs`, {
      next: { revalidate: 3600 }  // Regenerate sitemap hourly
    });
    blogSlugs = await res.json();
  } catch (err) {
    console.error('Failed to fetch blog slugs for sitemap:', err);
    // If the fetch fails, we still return static pages — don't crash the whole sitemap
  }

  // Generate one sitemap entry per blog post
  const blogEntries: MetadataRoute.Sitemap = blogSlugs.map((slug) => ({
    url: `${base}/blogs/${slug}`,
    lastModified: new Date(),
    changeFrequency: 'weekly',
    priority: 0.7,
  }));

  return [
    // Static pages — always present
    { url: base,                  lastModified: new Date(), changeFrequency: 'weekly',  priority: 1.0 },
    { url: `${base}/about`,       lastModified: new Date(), changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/blogs`,       lastModified: new Date(), changeFrequency: 'weekly',  priority: 0.8 },
    { url: `${base}/contact-us`,  lastModified: new Date(), changeFrequency: 'yearly',  priority: 0.6 },

    // Dynamic blog post pages — generated from API data
    ...blogEntries,
  ];
}
```

---

### The Full File Together

Here is the complete `app/blogs/[slug]/page.tsx` — everything assembled into one production-ready file.

```tsx
// app/blogs/[slug]/page.tsx
import { Metadata, ResolvingMetadata } from 'next';
import BlogHero from '@/src/sections/blog/hero';
import BlogBody from '@/src/sections/blog/body';
import BlogRelated from '@/src/sections/blog/related';

// ─────────────────────────────────────────────
// Type definitions
// ─────────────────────────────────────────────
type PageProps = {
  params: Promise<{ slug: string }>;
};

type BlogData = {
  title: string;
  subheading: string;
  image: string;
  tags: string[];
  author: string;
  createdAt: string;
  updatedAt?: string;
  content: string;
};

const BASE_URL = 'https://www.automatorr.com';

// ─────────────────────────────────────────────
// Fetch helper (used by both functions below)
// next: { revalidate: 3600 } means Next.js caches the result for 1 hour.
// Both generateMetadata and the Page component call this — Next.js
// deduplicates the network request automatically (only one real fetch).
// ─────────────────────────────────────────────
async function getBlogData(slug: string): Promise<BlogData | null> {
  try {
    const res = await fetch(
      `${BASE_URL}/api/blogs/${slug}`,
      { next: { revalidate: 3600 } }
    );
    if (!res.ok) return null;
    return res.json();
  } catch {
    return null;
  }
}

// ─────────────────────────────────────────────
// Dynamic Metadata
// ─────────────────────────────────────────────
export async function generateMetadata(
  { params }: PageProps
): Promise<Metadata> {
  const { slug } = await params;
  const data = await getBlogData(slug);
  const postUrl = `${BASE_URL}/blogs/${slug}`;

  if (!data) {
    return {
      title: 'Blog Post | Automatorr',
      description: 'Read our latest insights on automation and technology.',
    };
  }

  return {
    title: `${data.title} | Automatorr Blog`,
    description: data.subheading,
    keywords: data.tags,
    alternates: { canonical: postUrl },
    openGraph: {
      title: data.title,
      description: data.subheading,
      url: postUrl,
      siteName: 'Automatorr',
      locale: 'en_AU',
      type: 'article',
      publishedTime: data.createdAt,
      authors: [data.author ?? 'Automatorr'],
      images: [{ url: data.image, width: 1200, height: 630, alt: data.title }],
    },
    twitter: {
      card: 'summary_large_image',
      title: data.title,
      description: data.subheading,
      images: [{ url: data.image, alt: data.title }],
    },
  };
}

// ─────────────────────────────────────────────
// JSON-LD builder
// ─────────────────────────────────────────────
function buildBlogJsonLd(data: BlogData, slug: string) {
  const postUrl = `${BASE_URL}/blogs/${slug}`;

  return {
    "@context": "https://schema.org",
    "@graph": [
      {
        "@type": "WebPage",
        "@id": `${postUrl}#webpage`,
        "url": postUrl,
        "name": data.title,
        "description": data.subheading,
        "isPartOf": { "@id": `${BASE_URL}/#website` },
        "primaryImageOfPage": {
          "@type": "ImageObject",
          "url": data.image,
          "width": 1200,
          "height": 630,
          "caption": data.title
        },
        "breadcrumb": { "@id": `${postUrl}#breadcrumb` },
        "inLanguage": "en-AU"
      },
      {
        "@type": "BlogPosting",
        "@id": `${postUrl}#article`,
        "headline": data.title,
        "description": data.subheading,
        "url": postUrl,
        "datePublished": data.createdAt,
        "dateModified": data.updatedAt ?? data.createdAt,
        "author": {
          "@type": "Person",
          "name": data.author ?? "Automatorr Team"
        },
        "publisher": { "@id": `${BASE_URL}/#organization` },
        "image": {
          "@type": "ImageObject",
          "url": data.image,
          "width": 1200,
          "height": 630
        },
        "keywords": data.tags?.join(", "),
        "mainEntityOfPage": { "@id": `${postUrl}#webpage` },
        "inLanguage": "en-AU"
      },
      {
        "@type": "BreadcrumbList",
        "@id": `${postUrl}#breadcrumb`,
        "itemListElement": [
          { "@type": "ListItem", "position": 1, "name": "Home",  "item": BASE_URL },
          { "@type": "ListItem", "position": 2, "name": "Blogs", "item": `${BASE_URL}/blogs` },
          { "@type": "ListItem", "position": 3, "name": data.title, "item": postUrl }
        ]
      }
    ]
  };
}

// ─────────────────────────────────────────────
// Page Component
// ─────────────────────────────────────────────
export default async function BlogPostPage({ params }: PageProps) {
  const { slug } = await params;
  const data = await getBlogData(slug);

  // Handle 404 gracefully if post doesn't exist
  if (!data) {
    return <div>Post not found.</div>;
  }

  const jsonLd = buildBlogJsonLd(data, slug);

  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      <BlogHero title={data.title} image={data.image} author={data.author} date={data.createdAt} />
      <BlogBody content={data.content} />
      <BlogRelated tags={data.tags} currentSlug={slug} />
    </>
  );
}
```

**The complete data flow visualised:**

```
/blogs/invoice-automation
         │
         ▼
     slug = "invoice-automation"
         │
         ├──────────────────────────────────────────────┐
         │                                              │
         ▼                                              ▼
  generateMetadata()                         Page Component
  getBlogData("invoice-automation")          getBlogData("invoice-automation")
         │                                              │
         │   [Next.js deduplicates — 1 real fetch]      │
         │                                              │
         ▼                                              ▼
  { title, subheading,              { title, subheading,
    image, tags, author,              image, tags, author,
    createdAt, ... }                  createdAt, content, ... }
         │                                              │
         ▼                                              ▼
  <head>                             <body>
    <title>Invoice Automation          <script type="application/ld+json">
      | Automatorr Blog</title>          { @graph: [WebPage, BlogPosting,
    <meta description="..."/>               BreadcrumbList] }
    <meta og:title="..."/>             </script>
    <meta og:image="..."/>             <BlogHero ... />
  </head>                              <BlogBody ... />
```

---

## Quick Reference Cheatsheet

```
SEO Type        | Controls                        | Next.js File / Location
──────────────────────────────────────────────────────────────────────────────
On-Page         | Metadata, headings, content      | app/page.tsx (metadata export)
Technical       | Sitemap, robots, speed           | app/sitemap.ts, app/robots.ts
Off-Page        | Backlinks, brand mentions        | (External — no code needed)
Dynamic SEO     | Per-page generated metadata      | generateMetadata() function
JSON-LD Static  | Page entity, org, person schemas | Inline in page.tsx component
JSON-LD Dynamic | Article, breadcrumb, product     | Built from API data in page.tsx
JSON-LD Global  | Organization, WebSite schemas    | app/layout.tsx
```

| Task | Where |
|---|---|
| Static page metadata | `export const metadata` in a Server Component |
| Dynamic page metadata | `export async function generateMetadata()` |
| Sitemap (static) | `app/sitemap.ts` or `public/sitemap.xml` |
| Robots | `app/robots.ts` or `public/robots.txt` |
| JSON-LD — site-wide brand data | `<script>` in `app/layout.tsx` |
| JSON-LD — per page (static) | `const jsonLd = {...}` then `<script>` in page component |
| JSON-LD — per page (dynamic) | Build from API data inside the page component after fetching |
| OG image generation | `app/opengraph-image.tsx` (Next.js built-in) |
| Test your JSON-LD | [Google Rich Results Test](https://search.google.com/test/rich-results) |
| Validate schema structure | [Schema.org Validator](https://validator.schema.org) |

**JSON-LD `@id` naming conventions:**

| Entity | Recommended `@id` |
|---|---|
| Organization | `https://www.yourdomain.com/#organization` |
| WebSite | `https://www.yourdomain.com/#website` |
| WebPage | `https://www.yourdomain.com/page-path#webpage` |
| Article | `https://www.yourdomain.com/blogs/slug#article` |
| BreadcrumbList | `https://www.yourdomain.com/page-path#breadcrumb` |
| Person (founder) | `https://www.yourdomain.com/#founder` |

> **Remember:**
> - Static metadata and `generateMetadata` → **Server Components only**. Client Components (`"use client"`) cannot export metadata.
> - `Organization` + `WebSite` JSON-LD → define **once** in `layout.tsx`, reference everywhere else.
> - `WebPage` + `BlogPosting` + `BreadcrumbList` → define **per page**, built from live data on dynamic routes.
> - Always test with [Google's Rich Results Test](https://search.google.com/test/rich-results) after deploying.
