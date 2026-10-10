# Analytics options after Beam

Research date: 2 October 2026. Status: parked; no replacement deployed.

## Current decision

Keep Cloudflare Web Analytics for now. At low traffic, improving useful content is likely to be more valuable than building an analytics system. Revisit when existing reports leave a concrete question unanswered, particularly which articles send visitors to affiliate products.

Beam was removed from Rationaldev, Archlinks, Enlucent and Portable Coffee. All four passed type checks and production builds after related fixes. These changes were local at the time of this note.

## Options

All options below can collect basic traffic without cookies. This does not automatically establish a consent exemption for every configuration or jurisdiction. Free tiers and features can change.

| Option | Useful features / limitations | Hosting |
| --- | --- | --- |
| [Cloudflare Web Analytics](https://developers.cloudflare.com/web-analytics/faq/) | Basic traffic reports, six months of history and Core Web Vitals. No UTM campaign reporting or custom events currently. | Free managed service; already in use. |
| [Counterscale](https://github.com/benvinegar/counterscale) | UTM reports, editable tracker/dashboard and access to the dataset. Dashboard history is 90 days; R2 archives support longer storage separately. Primarily pageview tracking, rather than rich conversion analysis. | Open source, Cloudflare Workers + Analytics Engine. Project advertises up to 50,000 pageviews/day on the free Workers plan, subject to account quotas. |
| [Edgemetry](https://github.com/hayaran/Edgemetry) | Named custom events, UTM reports, entry/exit pages, bounce rate, time spent, multiple sites and retained daily summaries. Smaller project: pilot before adopting widely. | Open source, Cloudflare Worker + D1; designed around free-tier limits. |
| [Umami](https://docs.umami.is/docs) | Custom events with properties, campaigns, goals, funnels and journeys. Strongest shortlist option for richer affiliate-click reporting. | Free software; standard self-hosting needs Node + PostgreSQL. Hosted free tier also available, with limits. |
| [GoatCounter](https://www.goatcounter.com/) | Simple traffic/campaign reports and data export, with minimal setup. | Free hosted service for reasonable public usage, or self-hosted. |
| [Plausible CE](https://plausible.io/self-hosted-web-analytics) | Privacy-focused dashboard; CE lacks some paid-cloud features, including marketing funnels. | Free software; requires maintained server infrastructure. Managed cloud is paid. |

Counterscale's main gains over Cloudflare Web Analytics are campaign reporting and control over the code/data. Cookie-free tracking alone is not an improvement over Cloudflare. Counterscale also uses sampling at higher volumes; self-hosting does not guarantee exact counts.

## Amazon affiliate clicks

Your tracker can observe a click leaving the site, but cannot independently observe the subsequent Amazon purchase. Clicks measure interest, not sales. Amazon can hide or aggregate low-volume breakdowns, so precise earnings attribution may remain unavailable.

If adopting an event-capable tracker, record aggregate outbound clicks with:

- Source page and product identifier.
- Link position, such as comparison table or article text.
- Amazon marketplace and existing tracking ID, where useful.

Keep normal affiliate links intact and send the analytics event separately. Avoid personal identifiers. Edgemetry documents named events; verify its support for additional properties before assuming the full event schema above works without customisation.

Amazon tracking IDs can separate content groups or selected high-traffic articles. Attribution is only as granular as the grouping, and only as detailed as Amazon's available reports. Do not assign tags to individual visitors. Amazon's US help documents a default limit of 100 tracking IDs per account.

Useful measurements are affiliate clicks per pageview and which products attract clicks. Earnings per pageview or outbound click are useful only when Amazon provides a matching earnings breakdown. Small samples cannot reliably distinguish better placements or conversion rates.

Review monthly and look for repeated patterns over several months. Revisit the platform choice when the results would change which content gets written or improved. If switching anyway, add basic affiliate-click events from the start; avoid a custom analytics build until an existing tool fails a specific requirement.

## References for revisiting

- [Cloudflare dimensions](https://developers.cloudflare.com/web-analytics/data-metrics/dimensions/) and [Core Web Vitals](https://developers.cloudflare.com/web-analytics/data-metrics/core-web-vitals/).
- [Counterscale overview and advertised free capacity](https://counterscale.dev/) and [Analytics Engine pricing](https://developers.cloudflare.com/analytics/analytics-engine/pricing/).
- [Umami installation requirements](https://docs.umami.is/docs/install) and [hosted free-tier FAQ](https://docs.umami.is/docs/cloud/faq).
- [Amazon reporting help](https://affiliate-program.amazon.com/help/node/topic/GMWAK55DQX8JEK7C): some descriptions may not reflect every current report or account's available detail.
- [Amazon Content Insights threshold-based aggregation](https://affiliate-program.amazon.com/help/node/topic/GZ2P5RSW6AMWWKTM): documents aggregation into “Others”; no universal current sales cutoff was verified.
- [Amazon tracking-ID creation and limits](https://affiliate-program.amazon.com/help/node/topic/GJDYPQZK6E37RLPU) and [UK linking requirements prohibiting user-specific sub-tags](https://affiliate-program.amazon.co.uk/help/operating/linking). Check the relevant marketplace's current terms before implementation.
