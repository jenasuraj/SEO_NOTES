# SEO Notes

## What is SEO?
SEO (Search Engine Optimization) is the process of improving a website so that search engines like Google can clearly understand its content and show it to users when they search for related topics. Imagine you create a website, but you don’t do any SEO—your pages may exist, but search engines don’t know what each page is about, so maybe only one page (or none) appears in search results, and that too very low. It’s like having a shop in a hidden alley with no signboard—people simply won’t find it. SEO solves this by organizing your website properly: adding clear titles, relevant keywords, good content, proper structure, and technical improvements. After applying SEO, each page of your site becomes meaningful and searchable, so instead of one unclear result, you now have multiple pages (URLs) appearing for different searches. For example, one page can rank for “buy shoes,” another for “best running shoes,” and another for “how to clean shoes.” This increases your visibility, brings more users without paying for ads, and improves overall user experience. In simple terms, SEO is needed because without it your website remains invisible, but with it, your website becomes discoverable from many different paths.
<br/>
<br/>




## How Search Engines Work ?
Search engines like Google work in three main steps: crawling, indexing, and ranking. These steps decide whether your website will appear in search results or not.

First comes **crawling**. Search engines use automated bots (often called spiders) that move across the internet by following links from one page to another. When your website is connected through links (either internal or from other websites), these bots can discover your pages. If your site is not properly linked or structured, the bots may not even find it, which means your content won’t exist for the search engine.

Next is **indexing**. Once a page is discovered, the search engine tries to understand what it is about. It analyzes the content, keywords, headings, images, and overall structure, and then stores this information in its database (called an index). Think of it like adding your page to a huge digital library. If your content is unclear, poorly structured, or blocked (for example via robots.txt), it may not get indexed properly, meaning it won’t show up in search results.

Finally comes **ranking**, which is the most important step. When a user searches for something, Google looks into its index and shows the most relevant and high-quality pages in order. This is where competition happens. Your page is compared with thousands of others, and only the best ones appear at the top. Ranking depends on many factors like how relevant your content is, how well it matches the search intent, how fast your site loads, how mobile-friendly it is, and how many trusted websites link to you.

Several things affect ranking. The most important ones include content quality (useful, clear, and relevant information), proper use of keywords, website speed, mobile responsiveness, user experience, and backlinks (links from other websites). If your website performs well in these areas, search engines consider it more trustworthy and rank it higher.
In simple terms, if crawling means “finding your page,” indexing means “understanding your page,” and ranking means “deciding where your page should appear,” then SEO is about improving all three so your website can be discovered, understood, and shown to users at the top of search results.
<br/>
<br/>




## SEO Implementation & Types :

SEO is 3 types - >
<br/>
1 - on page SEO
<br/>
2 - off page SEO
<br/>
3 - Technical SEO
<br/>
<br/>

### ON Page SEO
On-page SEO traditionally means optimizing everything inside a single web page so that search engines like Google can clearly understand what the page is about and show it for the right searches. Earlier (before frameworks like Next.js), developers manually wrote HTML and placed SEO elements directly inside the <head> and content of the page. The idea was simple: clearly tell the search engine the topic of the page and make the content useful for users.

The most important parts were the title tag, meta description, headings (H1, H2), URL structure, and content with proper keywords. The title tag was the main ranking signal—it had to include the primary keyword. The meta description didn’t directly affect ranking but improved click rate. Headings structured the content so both users and search engines could understand it easily. Keywords were placed naturally in the content, not forced. Images were given alt text so search engines could understand them. Internal links connected related pages, helping both navigation and crawling.
```
<!DOCTYPE html>
<html>
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

<br/>
<br/>

### OFF Page SEO
Off-page SEO is about building your website’s reputation outside your own pages so that search engines like Google can decide whether your site is trustworthy enough to rank higher. When other websites link to your content, mention your brand, or share your pages, it acts like a signal that your content is valuable. But search engines don’t treat all signals equally—they look at the quality and relevance of those sources. A link from a well-known, respected website in your niche carries much more weight than many links from random or low-quality sites. This is because search engines are designed to give users the best possible results, so they rely on trusted sources as a way to judge credibility. If a reliable website recommends your content, it’s similar to a strong recommendation in real life—it builds confidence that your page is worth showing to users.

This is why only great and trusted content tends to rank higher through off-page SEO. High-quality content naturally attracts genuine backlinks, shares, and mentions, while low-quality or spammy content either gets ignored or attracts poor-quality links that don’t help. Search engines analyze patterns like who is linking to you, how relevant those sites are, and whether users engage with your content. If everything points to your site being useful and reliable, your rankings improve. But if the signals look artificial or low quality, your site won’t gain authority and may even be pushed down. In simple terms, off-page SEO is not just about being popular—it’s about earning trust from the right sources, and that trust is what search engines use to decide which websites deserve to be at the top.
<br/>
<br/>






### Technical SEO
Technical SEO is mainly about making sure search engines like Google can properly discover, access, and understand your website, and in that process, sitemap and robots.txt act like the backbone of communication between your site and search engines. Without these, search engines have to guess your site structure, which often leads to missed pages, poor crawling, or incorrect indexing. A sitemap is basically a structured list of all important URLs on your website—it tells search engines “these are my key pages, please look at them.” This becomes extremely important when your site grows, because not every page is easily discoverable through links. On the other hand, robots.txt acts like a gatekeeper—it tells search engine bots what they are allowed to crawl and what they should ignore. For example, you don’t want bots wasting time on admin pages, private routes, or unnecessary endpoints. Together, these two ensure efficient crawling: sitemap guides bots where to go, and robots.txt controls where not to go. If either of them is missing or misconfigured, search engines might skip important pages, index irrelevant ones, or even fail to understand your site properly. That’s why they are considered the backbone of technical SEO—they directly influence how well your website is discovered and processed.

<br/>
<br/>



# How to implement SEO with Next.js

Metadata in Next.js is a simple way to describe what your page is about so that search engines like Google and social platforms can understand and display it correctly. Instead of manually writing <head> tags, you define a metadata object, and Next.js automatically generates the correct title, description, and sharing previews. This helps your page appear properly in search results and when someone shares your link, making it more clickable and clear.
```
export const metadata: Metadata = {
  title: 'Automatorr | Automation, CRM & Technology Solutions — Sydney, Australia',
  description: 'Automatorr is Sydneys trusted technology partner. Delivering automation, CRM, managed IT, software engineering and global talent across Australia and APAC. Est. 2006.',
  alternates: { canonical: 'https://www.automatorr.com/'},
  keywords:["automation services Australia","workflow automation Sydney, CRM implementation Australia, RPA consulting, managed IT services Sydney, technology partner Australia"],
  openGraph: {
    siteName:"Automatorr",
    locale: 'en_AU',
    url: 'https://www.automatorr.com/',
    title: 'Automatorr — Automation & Technology Solutions, Sydney',
    description: 'Automation, CRM, managed IT and global talent for Australian businesses. 50+ clients. Est. 2006.',
    images: [{ url: 'https://www.automatorr.com/og/homepage.png', width: 1200, height: 630, alt: 'Automatorr — Automation & Technology Solutions, Sydney' }],
    type: 'website',
  },
  twitter: {
    site:"@automatorr_nair",
    card: 'summary_large_image',
    title: 'Automatorr — Automation & Technology Solutions, Sydney',
    description: 'Automation, CRM, managed IT and global talent for Australian businesses. 50+ clients. Est. 2006.',
    images: ['https://www.automatorr.com/og/homepage.png'],
  },
};
```

A sitemap is a file that lists all the important pages of your website so search engines can easily discover them. Instead of relying only on links, the sitemap directly tells search engines which pages exist and should be checked. This is especially useful when your site has many pages or some pages are not easily reachable through navigation.

```
import { MetadataRoute } from 'next';

export default function sitemap(): MetadataRoute.Sitemap {
  const base = 'https://www.automatorr.com';
  const now = new Date();

  return [
    { url: base, lastModified: now, changeFrequency: 'weekly', priority: 1.0 },
    { url: `${base}/about`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions`, lastModified: now, changeFrequency: 'monthly', priority: 0.9 },
    { url: `${base}/solutions/coms`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/ai-bot`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/invoice-automation`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/contract-automation`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/clockit`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/creatio`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/business-central`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/solutions/compliance`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services`, lastModified: now, changeFrequency: 'monthly', priority: 0.9 },
    { url: `${base}/services/automation-services`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/crm-services`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/msp-services`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/software-engineering`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/services/global-talent`, lastModified: now, changeFrequency: 'monthly', priority: 0.8 },
    { url: `${base}/case-studies`, lastModified: now, changeFrequency: 'weekly', priority: 0.7 },
    { url: `${base}/blogs`, lastModified: now, changeFrequency: 'weekly', priority: 0.7 },
    { url: `${base}/contact-us`, lastModified: now, changeFrequency: 'yearly', priority: 0.6 },
    { url: `${base}/hockey`, lastModified: now, changeFrequency: 'monthly', priority: 0.7 },
  ];
}
```

The robots.txt file is used to control what search engine bots are allowed to access on your website. It acts like a guide, telling bots which pages they can crawl and which ones they should ignore. This helps prevent unnecessary or sensitive pages from being indexed and ensures that search engines focus only on your important content.
```
User-agent: *
Allow: /

# Block Next.js internals
Disallow: /_next/
Disallow: /api/

# Sitemap
Sitemap: https://www.automatorr.com/sitemap.xml
```

