---
title: "Thoughts on React Native"
taxonomies:
  tags: ["tech_thoughts", "react_native", "mobile_development"]
---
I often find people comparing native Android and iOS vs. React Native (RN) and saying they're more performant than RN. Well, if you need performance, use C.

Don't try convincing me that RN isn't performant when your company's app is less than half-scale than Discord's built-on RN.

Some of the reasons to consider RN:

- Less expensive
- You don't need that much control (tho RN provides API to bridge the native realm and JS realm)

And be reasonable when reading tech news. The tech market is about something other than using the correct tools tech-wise. It is about using the right tool for the job, market, and budget. When we hear that a company rewrote its backend in Rust/C++ instead of TypeScript, that doesn't mean TS is bad. It may mean that the company scaled enough so that the cost of computation taxing is more expensive than the engineering time saved from TS.

In the company's early stages, they adopt cheaper technologies. When they scale, they start adopting more expensive tech but correct ones. RN is famous in the startup and freelancing world. In contrast, native Android and iOS are used in medium-sized companies and startups. Big companies use system languages (C/C++/Rust) to create the core business logic and port it to other platforms.

Indeed there are exceptions, like a startup targeting Apple's ecosystem, so they use Swift intensively, or a startup targeting a non-mobile Android (like an android PoS).
