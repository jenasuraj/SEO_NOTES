# ⚡ React Performance — Complete Developer Notes

> Performance optimization is the discipline of making your React app load faster, render faster, and feel faster — so users on every device and every network get a great experience, not just you on your MacBook on fiber.

---

## Table of Contents

1. [Why Performance Matters](#why-performance-matters)
2. [How the Browser Actually Runs Your Code](#how-the-browser-actually-runs-your-code)
   - [The Event Loop](#the-event-loop)
   - [What Blocks the Event Loop](#what-blocks-the-event-loop)
   - [How to Keep the Event Loop Free](#how-to-keep-the-event-loop-free)
3. [How React Renders](#how-react-renders)
   - [The Virtual DOM](#the-virtual-dom)
   - [The Render Cycle](#the-render-cycle)
   - [What Triggers a Re-render](#what-triggers-a-re-render)
4. [Bundle Size and Code Splitting](#bundle-size-and-code-splitting)
   - [What is a Bundle?](#what-is-a-bundle)
   - [Why Bundle Size Kills Initial Load](#why-bundle-size-kills-initial-load)
   - [Code Splitting](#code-splitting)
5. [React.lazy and Suspense](#reactlazy-and-suspense)
   - [What React.lazy Solves](#what-reactlazy-solves)
   - [How React.lazy Works](#how-reactlazy-works)
   - [Suspense — The Loading Boundary](#suspense--the-loading-boundary)
   - [React.lazy + Suspense Together](#reactlazy--suspense-together)
   - [Limitations of React.lazy](#limitations-of-reactlazy)
6. [Dynamic Import in Next.js](#dynamic-import-in-nextjs)
   - [What next/dynamic Does Differently](#what-nextdynamic-does-differently)
   - [next/dynamic vs React.lazy](#nextdynamic-vs-reactlazy)
   - [SSR Control](#ssr-control)
   - [Loading States with next/dynamic](#loading-states-with-nextdynamic)
7. [Intersection Observer — True Scroll-Based Lazy Loading](#intersection-observer--true-scroll-based-lazy-loading)
   - [Why React.lazy Alone is Not Enough](#why-reactlazy-alone-is-not-enough)
   - [What IntersectionObserver Does](#what-intersectionobserver-does)
   - [Building a LazySection Component](#building-a-lazysection-component)
   - [The LazySection Pattern Explained Line by Line](#the-lazysection-pattern-explained-line-by-line)
   - [Using LazySection in a Page](#using-lazysection-in-a-page)
9. [Memoization](#memoization)
   - [What is Memoization?](#what-is-memoization)
   - [React.memo](#reactmemo)
   - [useMemo](#usememo)
   - [useCallback](#usecallback)
   - [When NOT to Memoize](#when-not-to-memoize)
10. [Images and Assets](#images-and-assets)
    - [The Problem with Raw img Tags](#the-problem-with-raw-img-tags)
    - [Next.js Image Component](#nextjs-image-component)
    - [Image Formats](#image-formats)
    - [Fonts](#fonts)
11. [Rendering Strategies](#rendering-strategies)
    - [CSR](#csr--client-side-rendering)
    - [SSR](#ssr--server-side-rendering)
    - [SSG](#ssg--static-site-generation)
    - [ISR](#isr--incremental-static-regeneration)
    - [Choosing the Right Strategy](#choosing-the-right-strategy)
12. [Performance Measurement Tools](#performance-measurement-tools)
13. [Quick Reference Cheatsheet](#quick-reference-cheatsheet)

---

## Why Performance Matters

You might think — "my page loads in milliseconds, what's the problem?" And on your machine, on your network, you're right. But consider who actually uses your site:

```
You testing locally
→ MacBook, 100mbps WiFi, everything cached
→ 0.5ms vs 2.5ms — you'll never feel it

Your real users
→ Budget Android phone, 3G signal in a patchy area
→ That same page: 8 to 15 seconds of blank white screen
→ They leave. They never come back.
```

And Google does not test your site on your machine. Google's Lighthouse and Core Web Vitals scores are measured using a **simulated mid-range Android on throttled 4G** — every single time. So even if your actual users have fast internet, your SEO ranking is being calculated under slow conditions.

```
Your machine    →  LCP 0.3s  ✅ feels instant
Google's test   →  LCP 4.1s  ❌ poor score, ranking drops
```

The business impact is real too — a 1 second delay in page load reduces conversions by approximately 7%. At scale, that is money.

---

## How the Browser Actually Runs Your Code

Before you can understand React performance, you need to understand what runs React — the browser's JavaScript engine and its event loop. This is the foundation everything else builds on.

### The Event Loop

JavaScript is **single-threaded** — it can only do one thing at a time. There is one worker (the main thread) and it has a queue of tasks to process. The mechanism that manages this queue is called the **event loop**.

```
                    ┌─────────────────────────────┐
                    │         Call Stack          │
                    │  (currently executing code) │
                    └──────────────┬──────────────┘
                                   │ pops tasks from
                    ┌──────────────▼──────────────┐
                    │          Task Queue         │
                    │  [onClick] [setTimeout]     │
                    │  [fetch response] [scroll]  │
                    └─────────────────────────────┘

The event loop's job:
  → Is the call stack empty?
  → Yes → grab the next task from the queue → push it onto the stack
  → Repeat forever
```

When the call stack is empty and no tasks are queued, the event loop is free. This is when the browser can:
- Paint frames to the screen (animations, UI updates)
- Respond to user input (clicks, scrolls, keyboard)
- Process network responses

**A smooth UI runs at 60 frames per second — meaning each frame has only 16ms to complete.** If your JavaScript takes longer than 16ms to execute, the browser misses a frame. The user sees a stutter or freeze.

---

### What Blocks the Event Loop

If a task takes a long time to run, the event loop is stuck on it — nothing else can happen. This is called **blocking the main thread**.

```
Heavy JavaScript running
        │
        ▼
Event loop is stuck on this task
        │
        ▼
User clicks a button  → nothing happens (click is queued but can't run)
Browser tries to paint a frame → can't (stack is busy)
Scroll handler fires → ignored
        │
        ▼
User experiences: frozen UI, unresponsive page, janky scroll
```

Common causes of main thread blocking in React apps:

| Cause | Why it Blocks |
|---|---|
| Parsing a huge JS bundle | Browser must read and compile every byte before executing |
| Rendering hundreds of list items | React diffs and commits every single one synchronously |
| Running expensive calculations on every render | Recalculates even when inputs haven't changed |
| Unoptimized third-party libraries | Often include code you don't use |
| Large JSON processing | Parsing 10,000 item arrays synchronously |
| Synchronous animations | Calculated in JS instead of offloaded to CSS/GPU |

---

### How to Keep the Event Loop Free

The goal is to give the event loop as many "breathing gaps" as possible — short tasks, then free, then short tasks, then free.

**1. Break work into smaller chunks using `setTimeout` or `requestIdleCallback`**

Instead of doing 10,000 items of work in one synchronous pass, break it into batches:

```ts
// ❌ Bad — processes all 10,000 items in one blocking task
function processAll(items: Item[]) {
  items.forEach(item => heavyProcess(item));
}

// ✅ Good — processes in batches, yields control between each batch
function processInChunks(items: Item[], chunkSize = 100) {
  let index = 0;

  function processChunk() {
    const end = Math.min(index + chunkSize, items.length);
    for (; index < end; index++) {
      heavyProcess(items[index]);
    }
    if (index < items.length) {
      // Yield control back to the event loop, then continue
      setTimeout(processChunk, 0);
    }
  }

  processChunk();
}
```



**3. Offload animations to CSS and the GPU**

JavaScript animations run on the main thread. CSS `transform` and `opacity` animations are handled by the GPU — completely separate from the event loop:

```css
/* ❌ Bad — JS calculates position every frame, blocks event loop */
/* element.style.left = x + 'px' inside requestAnimationFrame */

/* ✅ Good — GPU handles this, main thread is free */
.card {
  transition: transform 0.3s ease;
}
.card:hover {
  transform: translateY(-4px);
}
```

**4. Load less JavaScript upfront — code splitting and lazy loading**

This is the biggest lever. Less JS downloaded = less JS parsed and compiled = event loop is free sooner. This is what the rest of these notes are about.

---

## How React Renders

### The Virtual DOM

React does not directly manipulate the real browser DOM — that operation is expensive. Instead, React maintains a lightweight JavaScript copy of the DOM called the **Virtual DOM**.

```
Your Component Returns JSX
          │
          ▼
React creates a Virtual DOM tree (plain JS objects)
          │
          ▼
React compares it to the previous Virtual DOM tree (diffing)
          │
          ▼
React calculates the minimum changes needed
          │
          ▼
React applies ONLY those changes to the real DOM (reconciliation)
```

This diffing process is called **reconciliation**. It is faster than re-rendering the entire real DOM, but it still has a cost — especially when it runs unnecessarily.

---

### The Render Cycle

Every React component goes through this lifecycle:

```
1. Render    →  React calls your component function, gets JSX back
2. Diff      →  React compares new output to previous output
3. Commit    →  React applies real DOM changes
4. Effects   →  useEffect callbacks run after paint
```

The critical insight is that **step 1 runs every time state or props change** — even if the output is identical. If your component re-renders 50 times unnecessarily, React diffs 50 times. That is wasted CPU on the main thread.

---

### What Triggers a Re-render

Understanding this is fundamental to knowing where to optimize:

```
1. State changes         → useState, useReducer setter called
2. Props change          → parent passes new value down
3. Context changes       → any consumer of a context re-renders when context value changes
4. Parent re-renders     → by default, ALL children re-render when parent re-renders
                           even if the child's props didn't change
```

Point 4 is the most surprising for newcomers. If your root component re-renders, without any optimization, **the entire tree re-renders.** This is where React.memo, useMemo, and useCallback come in — covered later.

---

## Bundle Size and Code Splitting

### What is a Bundle?

When you write a React app, you write dozens of files — components, utilities, hooks, styles. Before the browser can run any of this, a build tool (Webpack, Vite, Turbopack) **bundles** all these files into one or more output JavaScript files.

```
Your source files:
  src/
  ├── app/page.tsx
  ├── components/Hero.tsx
  ├── components/About.tsx
  ├── components/Services.tsx
  ├── components/Team.tsx
  ├── utils/helpers.ts
  └── ... 40 more files

After build (without code splitting):
  .next/
  └── main.js  ← everything merged into one file (could be 2MB+)
```

The browser must download, parse, and compile this entire file before it can show anything. Every byte counts, especially on slow connections.

---

### Why Bundle Size Kills Initial Load

Here is what happens in sequence when a user visits your page:

```
1. Browser requests the page HTML               ~50ms
2. Browser sees <script src="main.js">
3. Browser downloads main.js                    ← this is where size matters
   - 100kb bundle  →  ~200ms on 3G
   - 1MB bundle    →  ~2000ms on 3G
   - 3MB bundle    →  ~6000ms on 3G
4. Browser parses and compiles main.js          ← CPU intensive, blocks event loop
5. React hydrates the page
6. User can finally interact                    ← THIS is when LCP is measured
```

The user stares at a blank screen or a non-interactive skeleton through all of steps 1–5. Every KB you eliminate from the initial bundle directly reduces this wait time.

---

### Code Splitting

Code splitting is the practice of telling your build tool to split the bundle into multiple smaller chunks instead of one large file — and only deliver the chunks that are actually needed for the current page.

```
Without code splitting:
  main.js (3MB) → downloaded entirely on first visit

With code splitting:
  main.js       (300kb) → downloaded immediately (just the current page)
  about.js      (150kb) → downloaded only when user visits /about
  services.js   (200kb) → downloaded only when user visits /services
  team.js       (100kb) → downloaded only when user scrolls to Team section
```

The user gets only what they need, when they need it. The event loop gets breathing room between each chunk instead of being buried under 3MB at once.

---

## React.lazy and Suspense

### What React.lazy Solves

`React.lazy` is React's built-in mechanism for code splitting at the **component level**. It lets you tell React: "don't include this component in the initial bundle — load it separately, on demand."

Without `React.lazy`:

```
import HeavyChart from './HeavyChart';   // included in main bundle always
import VideoPlayer from './VideoPlayer'; // included in main bundle always
// even if user never sees these components
```

With `React.lazy`:

```ts
const HeavyChart  = React.lazy(() => import('./HeavyChart'));
const VideoPlayer = React.lazy(() => import('./VideoPlayer'));
// These are now separate chunks — loaded only when React tries to render them
```

---

### How React.lazy Works

`React.lazy` takes a function that calls a dynamic `import()`. The dynamic import returns a Promise that resolves to the module. React.lazy unwraps that promise and uses the default export as the component.

```ts
// This is static — bundled immediately
import HeavyChart from './HeavyChart';

// This is dynamic — bundled separately, fetched on demand
const HeavyChart = React.lazy(() => import('./HeavyChart'));
//                              ↑
//                   This function is called only when React
//                   first tries to render <HeavyChart />
//                   Until then, the chunk is never downloaded
```

The dynamic `import()` is a browser-native feature. It returns a Promise. When you call `import('./HeavyChart')`, the browser sends a network request for that chunk and resolves the Promise when it arrives.

```
React tries to render <HeavyChart />
          │
          ▼
React.lazy calls import('./HeavyChart')
          │
          ▼
Browser sends network request for HeavyChart chunk
          │
          ▼
Chunk arrives, Promise resolves
          │
          ▼
React renders the component
```

---

### Suspense — The Loading Boundary

There is a problem: while React is waiting for the lazy component's chunk to download, what should it show? The component isn't ready yet — rendering it would crash.

**Suspense is the answer.** It acts as a boundary that catches components that are "not ready yet" (suspended) and shows a fallback UI in their place while they load.

```tsx
import { Suspense } from 'react';

<Suspense fallback={<div>Loading chart...</div>}>
  <HeavyChart />   {/* suspended while chunk downloads */}
</Suspense>

// While HeavyChart chunk is downloading → shows "Loading chart..."
// Once chunk arrives and component is ready → shows <HeavyChart />
```

Suspense works on the concept of **throwing a Promise**. When React tries to render a lazy component and the chunk isn't ready, the component throws a Promise internally. Suspense catches that thrown Promise, shows the fallback, and waits for the Promise to resolve before trying to render again. You never write this yourself — React.lazy handles it internally.

---

### React.lazy + Suspense Together

```tsx
// app/dashboard/page.tsx
import { lazy, Suspense } from 'react';

// These components are split into separate chunks
const RevenueChart  = lazy(() => import('@/components/RevenueChart'));
const ActivityFeed  = lazy(() => import('@/components/ActivityFeed'));
const StatsPanel    = lazy(() => import('@/components/StatsPanel'));

// A reusable skeleton that shows while any section loads
function SectionSkeleton() {
  return (
    <div className="w-full h-64 bg-gray-100 animate-pulse rounded-xl" />
  );
}

export default function DashboardPage() {
  return (
    <div className="flex flex-col gap-8">

      {/* Each Suspense boundary is independent.
          If RevenueChart is slow, ActivityFeed can still load
          and show itself independently. */}

      <Suspense fallback={<SectionSkeleton />}>
        <RevenueChart />
      </Suspense>

      <Suspense fallback={<SectionSkeleton />}>
        <ActivityFeed />
      </Suspense>

      <Suspense fallback={<SectionSkeleton />}>
        <StatsPanel />
      </Suspense>

    </div>
  );
}
```

**Why separate Suspense boundaries matter:**

```
One Suspense wrapping all three:
  → If RevenueChart takes 2s to load, the entire page shows skeleton for 2s
  → ActivityFeed and StatsPanel (which loaded in 0.3s) are hidden too

Separate Suspense per component:
  → ActivityFeed loads in 0.3s → shows immediately ✅
  → StatsPanel loads in 0.5s → shows immediately ✅
  → RevenueChart loads in 2s → its skeleton shows for 2s, rest of page is already visible ✅
```

---

### Limitations of React.lazy

This is the part most tutorials skip, and it is critical to understand.

`React.lazy` splits the bundle — the JS chunk is separate. But it does **not** control *when* that chunk is fetched. The chunk is fetched the moment React tries to **render** the component. If all your components render on mount (because they're all in the JSX tree from the start), all their chunks get fetched almost immediately after page load.

```
// All of these render on page load
<HeroSection />          → chunk fetched immediately
<React.lazy(About) />    → chunk fetched immediately (it's in the JSX)
<React.lazy(Services) /> → chunk fetched immediately (it's in the JSX)
<React.lazy(Team) />     → chunk fetched immediately (it's in the JSX)

// The bundles are separate — but they all download within milliseconds of each other
// The event loop still processes all of them in sequence
// LCP is not significantly improved
```

**React.lazy reduces bundle parse time marginally — but it does not defer execution until the user actually needs the content.** For true scroll-based deferred loading, you need IntersectionObserver. That is covered next.

---

## Dynamic Import in Next.js

### What next/dynamic Does Differently

Next.js provides its own wrapper around React.lazy called `next/dynamic`. It does everything React.lazy does, plus adds features specific to the Next.js environment — particularly around server-side rendering.

```ts
// React.lazy — client only, no SSR control
const Chart = React.lazy(() => import('./Chart'));

// next/dynamic — SSR aware, more control
import dynamic from 'next/dynamic';
const Chart = dynamic(() => import('./Chart'));
```

---

### next/dynamic vs React.lazy

| Feature | React.lazy | next/dynamic |
|---|---|---|
| Code splitting | ✅ | ✅ |
| Works in App Router | ✅ | ✅ |
| SSR control (`ssr: false`) | ❌ | ✅ |
| Built-in loading state | ❌ (need Suspense) | ✅ (loading option) |
| Named exports | ❌ (default only) | ✅ |

---

### SSR Control

Some components simply cannot run on the server — they use `window`, `document`, browser APIs, or third-party libraries that expect a browser environment. Trying to SSR these will crash your build.

`next/dynamic` lets you disable SSR for specific components cleanly:

```ts
import dynamic from 'next/dynamic';

// This component uses window.innerWidth and browser canvas APIs
// It will crash if rendered on the server
const CanvasAnimation = dynamic(
  () => import('@/components/CanvasAnimation'),
  {
    ssr: false,           // ← never run this on the server
    loading: () => <div className="h-64 bg-gray-100 animate-pulse" />
  }
);

// InteractiveMap uses the Leaflet library which requires browser APIs
const InteractiveMap = dynamic(
  () => import('@/components/InteractiveMap'),
  { ssr: false }
);
```

Without `ssr: false`, Next.js would try to render these on the server during SSR/SSG, hit `window is not defined`, and crash.

---

### Loading States with next/dynamic

Instead of wrapping in Suspense, `next/dynamic` accepts a `loading` option directly:

```ts
const HeavyEditor = dynamic(
  () => import('@/components/RichTextEditor'),
  {
    loading: () => (
      <div className="border rounded-lg p-4 h-64 animate-pulse bg-gray-50">
        <div className="h-4 bg-gray-200 rounded w-3/4 mb-3" />
        <div className="h-4 bg-gray-200 rounded w-1/2 mb-3" />
        <div className="h-4 bg-gray-200 rounded w-5/6" />
      </div>
    ),
    ssr: false,
  }
);
```

---

## Intersection Observer — True Scroll-Based Lazy Loading

### Why React.lazy Alone is Not Enough

As established above, React.lazy splits the bundle but still downloads chunks when React renders the component. If your page mounts all components immediately, all chunks still load upfront.

The real question is: **why render below-the-fold components at all on initial load?**

The user cannot see them. They are wasted CPU and network. The event loop is processing JS for components the user hasn't scrolled to — components they may never even reach.

```
Without scroll-based lazy loading:
Page loads → Hero + About + Services + Team + Contact all execute
           → 5 API calls, 5 chunk downloads, 5 renders
           → Event loop is busy for 2.5ms
           → LCP is delayed

With scroll-based lazy loading:
Page loads → Only Hero executes
           → 1 chunk, 1 render
           → Event loop is free after 0.5ms ← LCP measured here
           → User scrolls → About executes
           → User scrolls → Services executes
           → etc.
```

---

### What IntersectionObserver Does

`IntersectionObserver` is a browser-native API that watches one or more DOM elements and fires a callback the moment they enter (or leave) the viewport.

```ts
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      // This element just entered the viewport
      console.log('Element is visible!');
    }
  });
}, {
  rootMargin: '200px', // fire 200px BEFORE the element enters the visible area
  threshold: 0,        // fire as soon as any pixel is visible
});

observer.observe(document.getElementById('my-section'));
```

The `rootMargin: '200px'` is important — it means you start loading the component 200px before the user actually reaches it. By the time they scroll there, it's already loaded and feels instant.

---

### Building a LazySection Component

Here is the production pattern — a reusable wrapper that combines IntersectionObserver with dynamic imports to achieve true scroll-triggered lazy loading.

```tsx
// hooks/useInView.ts
// A simple hook that tracks whether an element has entered the viewport.
// Uses react-intersection-observer under the hood (install: npm i react-intersection-observer)
import { useInView } from 'react-intersection-observer';
export { useInView };
```

```tsx
// components/LazySection.tsx
import { useState, useEffect, ComponentType } from 'react';
import { useInView } from 'react-intersection-observer';

type Props = {
  // loader is a function that returns a dynamic import Promise
  // example: () => import('@/sections/About')
  loader: () => Promise<{ default: ComponentType }>;

  // minHeight reserves space before the component loads
  // prevents layout shift (the page "jumping" as content appears)
  minHeight?: string;

  // rootMargin controls how early to start loading before it's visible
  // "400px" means start loading when element is 400px away from viewport
  rootMargin?: string;
};

export default function LazySection({
  loader,
  minHeight = "60vh",
  rootMargin = "400px",
}: Props) {

  // useInView returns a ref to attach to the placeholder div,
  // and a boolean inView that flips to true when the div enters the viewport
  // triggerOnce: true means it only fires once — no need to watch after that
  const { ref, inView } = useInView({ triggerOnce: true, rootMargin });

  // We store the loaded component in state.
  // Initially null (nothing loaded), set to the actual component once loaded.
  const [Component, setComponent] = useState<ComponentType | null>(null);

  useEffect(() => {
    // Only load when:
    // 1. The placeholder has entered the viewport (inView is true)
    // 2. We haven't already loaded the component (!Component)
    if (inView && !Component) {
      loader().then((mod) => {
        // mod.default is the React component from the dynamic import
        // We wrap it in () => to tell useState this is a function value,
        // not a state updater function (React would otherwise call it)
        setComponent(() => mod.default);
      });
    }
  }, [inView]); // re-runs when inView changes (false → true)

  return (
    <div
      ref={ref}        // ← IntersectionObserver watches this div
      className="w-full"
      style={{
        // Before Component loads: reserve the space so the page doesn't jump
        // After Component loads: let it take its natural height
        minHeight: Component ? undefined : minHeight
      }}
    >
      {/* Only render the component once it's loaded */}
      {Component && <Component />}
    </div>
  );
}
```

---

### The LazySection Pattern Explained Line by Line

Let's trace through exactly what happens when a user visits your page:

```
Page loads
    │
    ▼
React renders the page component
    │
    ├── <HeroSection /> → renders immediately, JS executed ✅
    │
    ├── <LazySection loader={() => import('@/sections/About')} />
    │       │
    │       └── renders a div with ref attached
    │           inView = false (not scrolled to yet)
    │           Component = null
    │           Shows: empty div with minHeight="60vh" (placeholder)
    │           import() has NOT been called yet → no network request
    │
    ├── <LazySection loader={() => import('@/sections/Services')} />
    │       └── same — empty placeholder, no import called
    │
    └── <LazySection loader={() => import('@/sections/Contact')} />
            └── same — empty placeholder, no import called

User scrolls down → About placeholder enters viewport (within 400px)
    │
    ▼
IntersectionObserver fires → inView becomes true
    │
    ▼
useEffect runs → calls loader() → calls import('@/sections/About')
    │
    ▼
Browser fetches About.js chunk from the server
    │
    ▼
Promise resolves → mod.default = AboutSection component
    │
    ▼
setComponent(() => AboutSection) → state updates → re-render
    │
    ▼
<AboutSection /> renders in the div ✅
minHeight is removed, div takes natural height
```

---

### Using LazySection in a Page

```tsx
// app/page.tsx
import HeroSection from '@/sections/Hero';      // eager — user sees this first
import LazySection from '@/components/LazySection';

export default function HomePage() {
  return (
    <>
      {/* Hero loads immediately — no lazy wrapper */}
      <HeroSection />

      {/* Everything below loads only when scrolled to */}

      <LazySection
        loader={() => import('@/sections/About')}
        minHeight="80vh"    // approximate height of About section
        rootMargin="400px"  // start loading 400px before visible
      />

      <LazySection
        loader={() => import('@/sections/Services')}
        minHeight="100vh"
        rootMargin="400px"
      />

      <LazySection
        loader={() => import('@/sections/Team')}
        minHeight="80vh"
        rootMargin="300px"
      />

      <LazySection
        loader={() => import('@/sections/Contact')}
        minHeight="60vh"
        rootMargin="200px"  // contact is last — less lead time needed
      />
    </>
  );
}
```

**What Google and the user experience:**

```
Initial page load:
  ✅ Hero section rendered and visible in 0.5ms
  ✅ LCP measured here — excellent score
  ✅ Event loop is free after Hero executes
  ✅ Page is interactive immediately
  ✅ Total JS executed: ~20% of what it would have been

User scrolls:
  ✅ Each section loads ~400px before they reach it
  ✅ Feels instant — no visible loading states needed
  ✅ Event loop handles each section individually with gaps between
```


## Memoization

### What is Memoization?

Memoization is a caching technique where you store the result of an expensive operation and return the cached result when the same inputs occur again — instead of recomputing every time.

```
First call:  calculate(1000000 items) → takes 200ms → stores result
Second call: calculate(1000000 items) → inputs unchanged → returns stored result instantly
Third call:  calculate(1000000 items) → inputs unchanged → returns stored result instantly
```

In React, memoization is used to prevent unnecessary re-renders and unnecessary recalculations. There are three tools: `React.memo`, `useMemo`, and `useCallback`.

---

### React.memo

`React.memo` is a higher-order component that wraps your component and tells React: "only re-render this if its props actually changed."

Without `React.memo`, every time a parent re-renders, all its children re-render — even if they received identical props.

```tsx
// ❌ Without React.memo
// This re-renders every time Dashboard re-renders,
// even if title and count haven't changed
function StatCard({ title, count }: { title: string; count: number }) {
  console.log(`${title} rendered`); // fires constantly
  return (
    <div className="p-4 border rounded-lg">
      <h3>{title}</h3>
      <p>{count}</p>
    </div>
  );
}

// ✅ With React.memo
// React shallowly compares props before re-rendering
// If title and count are the same as last render → skip re-render entirely
const StatCard = React.memo(function StatCard({
  title,
  count
}: {
  title: string;
  count: number
}) {
  console.log(`${title} rendered`); // fires only when props actually change
  return (
    <div className="p-4 border rounded-lg">
      <h3>{title}</h3>
      <p>{count}</p>
    </div>
  );
});

// Usage
export default function Dashboard() {
  const [unrelated, setUnrelated] = useState(0);

  return (
    <div>
      <button onClick={() => setUnrelated(n => n + 1)}>
        Update unrelated state ({unrelated})
      </button>

      {/* These do NOT re-render when unrelated state changes ✅ */}
      <StatCard title="Revenue" count={50000} />
      <StatCard title="Users" count={1200} />
      <StatCard title="Orders" count={340} />
    </div>
  );
}
```

**React.memo uses shallow comparison.** This means it compares primitive props (strings, numbers, booleans) by value — but objects, arrays, and functions by reference. If you pass a new object or function on every render, React.memo will think the props changed and re-render anyway. This is where useMemo and useCallback come in.

---

### useMemo

`useMemo` caches the **result of a computation** between renders. It only recalculates when its dependencies change.

```tsx
import { useMemo, useState } from 'react';

type Product = { id: number; name: string; price: number; category: string };

export default function ProductList({ products }: { products: Product[] }) {
  const [search, setSearch] = useState('');
  const [sortBy, setSortBy] = useState<'price' | 'name'>('name');

  // ❌ Without useMemo:
  // This runs on EVERY render — even when only sortBy changes.
  // If products has 10,000 items, this is 10,000 filter operations every keystroke.
  const filtered = products
    .filter(p => p.name.toLowerCase().includes(search.toLowerCase()))
    .sort((a, b) => a[sortBy] > b[sortBy] ? 1 : -1);

  // ✅ With useMemo:
  // Only recalculates when products, search, or sortBy actually changes.
  // If only an unrelated state changes → returns the cached array immediately.
  const filteredMemo = useMemo(() => {
    return products
      .filter(p => p.name.toLowerCase().includes(search.toLowerCase()))
      .sort((a, b) => a[sortBy] > b[sortBy] ? 1 : -1);
  }, [products, search, sortBy]); // ← recalculate only when these change

  return (
    <div>
      <input
        value={search}
        onChange={e => setSearch(e.target.value)}
        placeholder="Search products..."
      />
      <select onChange={e => setSortBy(e.target.value as 'price' | 'name')}>
        <option value="name">Sort by name</option>
        <option value="price">Sort by price</option>
      </select>
      <ul>
        {filteredMemo.map(p => (
          <li key={p.id}>{p.name} — ${p.price}</li>
        ))}
      </ul>
    </div>
  );
}
```

`useMemo` is also useful for stabilizing object references passed as props to memoized children:

```tsx
// ❌ New object created every render → React.memo child always re-renders
const config = { theme: 'dark', lang: 'en' };
<MemoizedChild config={config} />

// ✅ Same object reference returned if theme and lang haven't changed
const config = useMemo(() => ({ theme: 'dark', lang: 'en' }), []);
<MemoizedChild config={config} />
```

---

### useCallback

`useCallback` caches a **function reference** between renders. It returns the same function instance as long as its dependencies don't change.

This is specifically useful when passing callbacks to memoized child components — because a new function is created on every render, and a new function reference breaks React.memo's shallow comparison.

```tsx
import { useState, useCallback, memo } from 'react';

// This child is memoized — it only re-renders if its props change
const DeleteButton = memo(function DeleteButton({
  id,
  onDelete
}: {
  id: number;
  onDelete: (id: number) => void;
}) {
  console.log(`DeleteButton ${id} rendered`);
  return <button onClick={() => onDelete(id)}>Delete</button>;
});

export default function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: 'Buy groceries' },
    { id: 2, text: 'Write docs' },
    { id: 3, text: 'Fix bug' },
  ]);
  const [count, setCount] = useState(0);

  // ❌ Without useCallback:
  // New function created every render → DeleteButton always re-renders
  // React.memo is useless here
  const handleDelete = (id: number) => {
    setTodos(prev => prev.filter(t => t.id !== id));
  };

  // ✅ With useCallback:
  // Same function reference returned as long as setTodos doesn't change
  // DeleteButton's React.memo actually works now
  const handleDeleteMemo = useCallback((id: number) => {
    setTodos(prev => prev.filter(t => t.id !== id));
  }, []); // setTodos is stable, so no dependencies needed

  return (
    <div>
      <button onClick={() => setCount(n => n + 1)}>
        Unrelated counter: {count}
      </button>

      {todos.map(todo => (
        <div key={todo.id}>
          {todo.text}
          {/* Does not re-render when counter updates ✅ */}
          <DeleteButton id={todo.id} onDelete={handleDeleteMemo} />
        </div>
      ))}
    </div>
  );
}
```

---

### When NOT to Memoize

Memoization is not free. It has a cost — React must store the previous value, compare dependencies on every render, and manage the cached reference in memory. For small or cheap operations, this overhead can actually be **slower** than just re-computing.

**Do NOT memoize when:**

```
❌ The computation is trivially cheap
   const double = useMemo(() => count * 2, [count]);
   // Multiplication is microseconds — memo costs more than it saves

❌ The component is simple and re-renders are not a bottleneck
   // Wrapping every small component in React.memo adds overhead
   // without measurable benefit

❌ The dependencies change on every render anyway
   const fn = useCallback(() => doSomething(value), [value]);
   // If value changes every render, useCallback buys nothing

❌ You haven't measured a problem yet
   // Premature memoization makes code harder to read and maintain
   // Profile first, optimize second
```

**Do memoize when:**
- The computation is genuinely expensive (filtering/sorting large arrays, complex math)
- The component is re-rendering frequently and profiling confirms it's a bottleneck
- You're passing callbacks or objects to memoized child components that are proven to be slow

> **Rule of thumb:** measure first with React DevTools Profiler. If you don't see the component highlighted as a performance problem, don't memoize it.

---

## Images and Assets

### The Problem with Raw img Tags

Images are almost always the largest assets on a page and the leading cause of poor LCP scores. A raw `<img>` tag does nothing to help:

```html
<!-- ❌ Raw img tag — zero optimization -->
<img src="/hero.png" />
<!-- Problems:
  - Downloads full resolution even on mobile (2400px on a 390px screen)
  - No lazy loading — downloads even if it's below the fold
  - No modern format — sends PNG instead of WebP or AVIF
  - No size hints — browser doesn't know dimensions until download completes
  - Layout shift — page jumps when image loads (hurts CLS score)
-->
```

---

### Next.js Image Component

The `next/image` component solves all of the above automatically:

```tsx
import Image from 'next/image';

// ✅ Optimized image — Next.js handles everything
<Image
  src="/hero.png"
  alt="Hero section background"
  width={1200}
  height={600}
  priority          // ← above the fold: load eagerly (don't lazy load)
  quality={85}      // ← compress to 85% quality (visually identical, much smaller)
/>

// For below-the-fold images — lazy load by default
<Image
  src="/team-photo.jpg"
  alt="Our team"
  width={800}
  height={600}
  // priority is omitted → lazy loaded automatically
/>
```

**What Next.js Image does automatically:**
- Serves WebP or AVIF to browsers that support it (50–70% smaller than PNG/JPEG)
- Resizes the image to match the container — no downloading a 2400px image for a 400px slot
- Lazy loads by default (below-fold images don't download until near viewport)
- Reserves space before the image loads — prevents layout shift (CLS)
- Generates multiple sizes for different screen widths (responsive srcset)

---

### Image Formats

| Format | Best For | Size vs JPEG |
|---|---|---|
| **JPEG** | Photos, complex images | Baseline |
| **PNG** | Logos, transparency | Larger than JPEG |
| **WebP** | Everything — modern replacement | 25–35% smaller than JPEG |
| **AVIF** | Everything — next gen | 40–55% smaller than JPEG |
| **SVG** | Icons, logos, illustrations | Tiny, scales infinitely |

Always use SVG for icons and logos. Use WebP or AVIF for photos. Next.js handles the conversion automatically — just supply the original PNG or JPEG.

---

### Fonts

Fonts are a common but overlooked performance killer. An unoptimized font can cause **FOUT (Flash of Unstyled Text)** — where the page renders with a fallback font, then jumps to the real font when it loads. This hurts CLS and feels jarring.

```ts
// app/layout.tsx
// next/font downloads the font at build time and self-hosts it
// No runtime request to Google Fonts = faster, no layout shift
import { Inter, Geist_Mono } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],       // only download latin characters, not entire font
  display: 'swap',          // show fallback font immediately, swap when ready
  variable: '--font-inter', // expose as CSS variable
  preload: true,            // preload the font file in <head>
});

export default function RootLayout({ children }) {
  return (
    <html className={inter.variable}>
      <body>{children}</body>
    </html>
  );
}
```

**Never load fonts from Google Fonts directly in production:**

```html
<!-- ❌ Bad — external request to Google's servers on every page load -->
<link href="https://fonts.googleapis.com/css2?family=Inter&display=swap" rel="stylesheet">

<!-- ✅ Good — use next/font which self-hosts and preloads at build time -->
```

---

## Rendering Strategies

Choosing the right rendering strategy is one of the highest-impact performance decisions in a Next.js app. Each strategy has a different trade-off between speed, freshness, and server cost.

### CSR — Client Side Rendering

The server sends an empty HTML shell. The browser downloads JS, executes React, fetches data, and renders everything on the client.

```
Server → sends empty HTML + JS bundle
Browser downloads + executes JS
React renders the UI
API calls made from the client
User sees content
```

**When to use:** Dashboards, admin panels, anything behind a login wall (no SEO needed), highly interactive tools.

**Performance profile:** Slow initial load (blank screen while JS loads), fast subsequent navigation.

---

### SSR — Server Side Rendering

The server renders the full HTML on every request, sends it to the browser already populated. The browser displays it immediately while JS hydrates in the background.

```
User requests page
Server fetches data + renders HTML (on this request, in real time)
Browser receives complete HTML → displays immediately ✅
JS hydrates in background → page becomes interactive
```

**When to use:** Pages with user-specific or frequently-changing data that also needs SEO (e-commerce product pages, user dashboards that need to be indexed).

**Performance profile:** Fast initial display, but every request hits the server.

---

### SSG — Static Site Generation

HTML is generated at **build time** — once, when you deploy. Every user gets the same pre-built HTML file served from a CDN. No server computation on each request.

```
Build time: Next.js fetches data + renders HTML → saves as static files
User requests page → CDN serves the pre-built HTML instantly ✅
Fastest possible response time
```

**When to use:** Marketing pages, blogs, documentation, landing pages — anything that doesn't change per user or per request.

**Performance profile:** Fastest possible — CDN-served static HTML. No server overhead.

---

### ISR — Incremental Static Regeneration

SSG but with automatic background revalidation. Pages are pre-built like SSG, but can be refreshed in the background after a set interval without a full redeploy.

```ts
// app/blogs/[slug]/page.tsx
export const revalidate = 3600; // regenerate this page every 1 hour

// Or per-fetch:
const data = await fetch('/api/blog', { next: { revalidate: 3600 } });
```

```
Build time: pages pre-generated → served from CDN ✅
After 1 hour: next visitor triggers background regeneration
New version built in background → CDN cache updated
Subsequent visitors get fresh version ✅
```

**When to use:** Blogs, product pages, content that changes occasionally but needs near-real-time accuracy.

**Performance profile:** Same as SSG (CDN speed) with automatic content freshness.

---

### Choosing the Right Strategy

```
Is the content the same for all users?
  YES → Is it updated frequently?
          NO  → SSG (blog posts, marketing pages, documentation)
          YES → ISR (news sites, product catalogs, pricing pages)
  NO  → Does it need SEO?
          NO  → CSR (admin panels, logged-in dashboards)
          YES → SSR (user-specific pages that must be indexed)
```

In practice, most Next.js apps use a **mix** — SSG for marketing pages, ISR for blogs, SSR for user-specific pages, and CSR for dashboard sections.

---

## Performance Measurement Tools

Never optimize blindly. Always measure first — find the actual bottleneck before writing a single line of optimization code.

---

### 🌐 PageSpeed Insights — [pagespeed.web.dev](https://pagespeed.web.dev)

Google's official tool. Runs Lighthouse on your live URL from real Google infrastructure. This is the closest thing to how Google actually scores your site.

**What to look at:**
- **LCP** (Largest Contentful Paint) — should be under 2.5s
- **FID / INP** (Interaction to Next Paint) — should be under 200ms
- **CLS** (Cumulative Layout Shift) — should be under 0.1
- **Speed Index** — overall visual completeness
- **Opportunities** section — specific actionable fixes with estimated savings

**How to interpret the score:**
```
90–100  ✅ Good
50–89   ⚠️ Needs improvement
0–49    ❌ Poor
```

Run it on both Mobile and Desktop — Google scores mobile first.

---

### 🔦 Lighthouse — Built into Chrome DevTools

Open Chrome DevTools → Lighthouse tab. Runs the same analysis locally — useful for testing before you deploy.

**How to get accurate results:**
1. Open an incognito window (no extensions)
2. Open DevTools → Lighthouse
3. Select "Mobile" device
4. Check "Performance" category
5. Click "Analyze page load"

---

### ⚛️ React DevTools Profiler

Install the [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools) Chrome extension. The Profiler tab records exactly which components rendered, how long each took, and why they rendered.

**How to use:**
1. Open DevTools → Profiler tab
2. Click the record button ⏺
3. Interact with your app (click buttons, scroll, type)
4. Stop recording
5. Look for components highlighted in red/orange — these are your slow renders

**What to look for:**
```
Component took 50ms to render  → investigate
Component renders 30 times for one button click  → unnecessary re-renders
Same component re-renders with identical props   → add React.memo
```

---

### 📦 Bundle Analyzer — @next/bundle-analyzer

Shows you exactly what is inside your JS bundles — which libraries take the most space.

```bash
npm install @next/bundle-analyzer
```

```js
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
});

module.exports = withBundleAnalyzer({});
```

```bash
ANALYZE=true npm run build
```

This opens an interactive treemap in your browser showing every module in your bundle. Common findings:
- `moment.js` — famously huge, replace with `date-fns`
- `lodash` — import individual functions instead of the whole library
- Duplicate dependencies — two versions of the same package
- Unused third-party code included by accident

---

### 🌊 WebPageTest — [webpagetest.org](https://www.webpagetest.org)

More detailed than PageSpeed — lets you test from real devices in specific locations, simulate specific network conditions, and see a filmstrip of your page loading frame by frame.

**Useful for:** Finding exactly which resource is blocking your page, testing from a location close to your users, comparing before/after optimizations.

---

### 📊 Chrome DevTools — Network Tab

For diagnosing specific network issues:
1. Open DevTools → Network tab
2. Check "Disable cache" and set throttling to "Slow 3G"
3. Reload the page
4. Look for:
   - Large files (sort by Size column)
   - Render-blocking resources (red vertical line = blocking)
   - Waterfall — are requests happening in sequence that could be parallel?

---

### 📈 Chrome DevTools — Performance Tab

For diagnosing JavaScript execution and event loop blocking:
1. Open DevTools → Performance tab
2. Click record ⏺ → interact with page → stop
3. Look for **Long Tasks** (red triangles in the Main thread) — tasks longer than 50ms block the event loop
4. Click on a Long Task to see which function caused it

---

## Quick Reference Cheatsheet

```
Problem                          Solution
────────────────────────────────────────────────────────────────────
Slow initial load                Code split with React.lazy / next/dynamic
Below-fold JS executing on load  LazySection with IntersectionObserver
Large bundle                     Analyze with @next/bundle-analyzer, remove/replace heavy libs
Event loop blocking              Break work into chunks, use Web Workers, CSS animations
Unnecessary re-renders           React.memo, check with React DevTools Profiler
Expensive recalculations         useMemo
Unstable function references     useCallback
Slow images                      next/image with correct priority and sizing
Layout shift (CLS)               Set width/height on images, minHeight on lazy placeholders
Font flash                       next/font instead of Google Fonts link tag
Static content serving slowly    Move to SSG or ISR, serve from CDN
User-specific content blank      SSR or CSR with loading skeleton
Too many API calls               Cache with next: { revalidate } or React Query / SWR
```

**Rendering strategy selector:**

```
Content same for all users?
  YES → changes often? → ISR    (blogs, product pages)
  YES → rarely changes → SSG    (landing pages, docs)
  NO  → needs SEO?    → SSR    (user dashboards that are indexed)
  NO  → no SEO needed → CSR    (admin panels, private apps)
```

**Memoization selector:**

```
Preventing child re-renders?           → React.memo
Caching expensive calculation result?  → useMemo
Stabilizing a function reference?      → useCallback
Not sure if it's actually slow?        → Profile first, memoize after
```

**Core Web Vitals targets:**

| Metric | Good | Needs Work | Poor |
|---|---|---|---|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5–4.0s | > 4.0s |
| INP (Interaction to Next Paint) | < 200ms | 200–500ms | > 500ms |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1–0.25 | > 0.25 |

**Tools summary:**

| Tool | Best For |
|---|---|
| [pagespeed.web.dev](https://pagespeed.web.dev) | Overall score, Google's actual measurement |
| Chrome DevTools → Lighthouse | Local testing before deploy |
| React DevTools Profiler | Finding slow components and unnecessary re-renders |
| Chrome DevTools → Network | Finding large files and render-blocking resources |
| Chrome DevTools → Performance | Finding event loop blocking and long tasks |
| [webpagetest.org](https://www.webpagetest.org) | Real-device testing, location testing, filmstrip |
| @next/bundle-analyzer | Finding what is bloating your JS bundle |

> **The golden rule of performance:** Measure first. Identify the actual bottleneck. Optimize that specific thing. Measure again. Never optimize by instinct alone — the bottleneck is almost never where you expect it.
