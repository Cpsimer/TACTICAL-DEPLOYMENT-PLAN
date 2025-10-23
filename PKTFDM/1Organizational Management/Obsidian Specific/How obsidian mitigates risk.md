---
title: "Less is safer: how Obsidian reduces the risk of supply chain attacks"
source: "https://obsidian.md/blog/less-is-safer/"
author:
  - "[[Obsidian]]"
published:
created: 2025-09-23
description: "Supply chain attacks are malicious updates that sneak into open source code used by many apps. Here’s how we design Obsidian to ensure that the app is a secure and private environment for your thoughts."
tags:
  - "clippings"
---
Supply chain attacks are malicious updates that sneak into open source code used by many apps. Here’s how we design Obsidian to ensure that the app is a [secure and private](https://obsidian.md/security) environment for yourthoughts.

### Less is safer

It may sound obvious but the primary way we reduce the risk of supply chain attacks is to avoid depending on third-party code. Obsidian has a low number of dependencies compared to other apps in our category. See a list of open source libraries on our [Credits page](https://help.obsidian.md/credits#Third+party+acknowledgements).

Features like [Bases](https://help.obsidian.md/bases) and [Canvas](https://help.obsidian.md/plugins/canvas) were implemented from scratch instead of importing off-the-shelf libraries. This gives us full control over what runs in Obsidian.

- **For small utility functions** we almost always re-implement them in our code.
- **For medium modules** we fork them and keep them inside our codebase if the licenses allows it.
- **For large libraries** like pdf.js, Mermaid, and MathJax, we include known-good, version-locked files and only upgrade occasionally, or when security fixes land. We read release notes, look at upstream changes, and test thoroughly befores witching.

This approach keeps our dependency graph shallow with few sub-dependencies. A smaller surface area lowers the chance of a malicious update slipping through.

### What actually ships in the app

Only a handful of packages are part of the app you run, e.g. Electron, Code Mirror, moment.js. The other packages help us build the app and never ship to users, e.g. esbuild oreslint.

### Version pinning and lockfiles

All dependencies are strictly version-pinned and committed with a lockfile. The lockfile is the source of truth for builds so we get deterministic installs. This gives us a straightforward audit trail when reviewing changes.

We do not run post install scripts. This prevents packages from executing arbitrary code during installation.

### Slow, deliberate upgrades

When we do dependency updates, we:

1. Read the dependency’s change logline-by-line.
2. Check sub-dependencies introduced by the new version.
3. Diff upstream when the change set is large or risky.
4. Run automated and manual tests across platforms and critical user paths.
5. Commit the new lockfile only after these reviews pass.

In practice, we rarely update dependencies because they generally work and do not require frequent changes. When we do, we treat each change as if we were taking a new dependency.

### Time is a buffer

We don’t rush upgrades. There is a delay between upgrading any dependency and pushing a release. That gap acts as an early-warning window: the community and security researchers often detect malicious versions quickly. By the time we’re ready to ship, the ecosystem has usually flagged any problematic releases.

---

No single measure can eliminate supply chain risk. But choosing fewer dependencies, shallow graphs, exact version pins, no postinstall, and a slow, review-heavy upgrade cadence together make Obsidian much less likely to be impacted, and give us a long window to detect problems before code reachesusers.

If you’re curious about our broader approach to security, see our [security page](https://obsidian.md/security) and pastaudits.

---
title: "Less is safer: how Obsidian reduces the risk of supply chain attacks"
source: "https://www.reddit.com/r/ObsidianMD/comments/1nl8xjq/less_is_safer_how_obsidian_reduces_the_risk_of/"
author:
  - "[[kepano]]"
published: 2025-09-19
created: 2025-09-23
description:
tags:
  - "clippings"
---
![r/ObsidianMD - Less is safer: how Obsidian reduces the risk of supply chain attacks](https://external-preview.redd.it/less-is-safer-how-obsidian-reduces-the-risk-of-supply-chain-v0-zMXPIQv3Ay2AQPtwJ908oSE8egPawgAwdCZIEwNBzQc.png?width=640&crop=smart&auto=webp&s=8aa9a434df4c328c73565b3366b381e88cb1f515)

---

## Comments


> **KetosisMD** • [28 points](https://reddit.com/r/ObsidianMD/comments/1nl8xjq/comment/nf4cklu/) •
> 
> Dependencies:
> 
> Capacitor
> 
> \*CodeMirror
> 
> DOMPurify
> 
> \*Electron
> 
> i18next
> 
> Lezer
> 
> Lucide
> 
> MathJax
> 
> Mermaid
> 
> \*Moment.js
> 
> pdf.js
> 
> PixiJS
> 
> Prism
> 
> rbush
> 
> remark
> 
> reveal.js
> 
> scrypt
> 
> Turndown
> 
> Webpack
> 
> YAML
> 
> source: [https://help.obsidian.md/credits#Third+party+acknowledgements](https://help.obsidian.md/credits#Third+party+acknowledgements)
> 
> \* = Only a handful of packages are part of the app you run, e.g. Electron, CodeMirror, moment.js. 

> **Emiroda** • [17 points](https://reddit.com/r/ObsidianMD/comments/1nl8xjq/comment/nf8ghxb/) •
> 
> Give admins more control over the plugin store and I'll buy it. But as it stands now, it just takes one plugin maintainer to get pwned before we see a supply chain compromise of Obsidian users.
> 
> The plugin store is a big reason why mature organisations' security teams cannot approve Obsidian for corporate use. A malicious update to a widely used plugin could either be used for initial access by a malicious actor, which can be used to either deploy an infostealer (which does not need administrator permissions), be used for command and control communications or perform data exfiltration through Obsidian Sync or another plugin.
> 
> I want Obsidian to thrive in mature orgs and enterprises, so I'm trying to be constructive. Here are my suggestions based on what Chromium-based browsers do ([here's the docs for Microsoft Edge](https://learn.microsoft.com/en-us/deployedge/microsoft-edge-manage-extensions)):
> 
> - Obsidian should use a config store appropriate for each OS that is only writable by administrators. For Windows, it would probably be in the registry, in the HKLM hive. For Mac and Linux, it would probably be a config file in /etc or similar.
> - That config store must be processed first, before user settings made inside Obsidian. The point of the config store is to restrict themes and plugins, not necessarily to set app/plugin defaults (probably better handled some other way)
> - ==We must be able to completely block the store.==
> - We must be able to allowlist certain plugins, so only those will run/be installable. Optionally, we could specify which plugins to auto-install for a consistent experience if a particular plugin is used for productivity/collab.
> - **\[Low proprity\]** We should be able to blocklist certain plugins, but this is not advisable. Admins should be advised to use the allowlist, as blocklists will always be out of date and inadequate to protect against new threats.
> - We should be able to specify our own company-managed store. It could work kind of like BRAT does, just officially supported. It could work within the existing plugin store ecosystem, where Obsidian has its own "repository", and where we can add our own repositories to the list, like Nuget. We should then in the config store be able to specify which repositories that users are allowed to access. This could work in conjunction with plugin auto-install to install from the allowed repositories.
> - Obsidian should write a security considerations doc, that helps
> 	- corporate IT admins decide which measures they should take to protect Obsidian against plugin-based supply chain compromise, if any
> 	- corporate risk and compliance officers decide if Obsidian aligns with the risk appetite of the business, and which measures would need to be implemented to allow Obsidian
> 
> I'm not trying to be difficult, I just get sad every time I see a post on this subreddit saying their IT teams can't allow Obsidian because of plugin risks. I love Obsidian and I want to see it used more places, not just by hobbyists and by rogue employees who doesn't want to use whatever OneNote/Notion/Confluence crap their workplace offers. 
> 
> The timeline should be the allowlist functionality first, so we can reduce our supply chain to very few maintainers, and then after that should be the ability to have our own custom store. The custom store would allow companies to pull verified, trusted versions from the Obsidian store, and update them when they feel comfortable and have vetted new versions. Startups might allow the full store, small-medium sized businesses might allowlist certain plugins and enterprises might want to stand up their own store with vetted plugins. This would give orgs of any size the ability to allow the use of Obsidian within their own risk appetite.
> 
> > **kepano** • [8 points](https://reddit.com/r/ObsidianMD/comments/1nl8xjq/comment/nf8td42/) •
> > 
> > Thanks for the constructive feedback. A lot of this is covered by the proposed [policy.json](https://x.com/kepano/status/1957927003254059290?s=46&t=IdiFvLmqpZWfOF6HUeEQrg) controls that we're considering adding.
> > 
> > 1. ==New policy.json file with options for disabling community plugins, themes, Sync, Publish==
> > 2. ==Command line options for --policy-file "path/to/policy.json"==
> > 3. ==Command line options for --policy-json "{json contents}"==
> > 4. ==Environment variable for OBSIDIAN\_POLICY\_FILE and OBSIDIAN\_POLICY\_JSON same as the command line options==
> > 
> > It should be noted that [many](https://obsidian.md/enterprise/) mature enterprises and security focused orgs do use Obsidian. There are already ways to do most of what you're describing by [controlling access](https://help.obsidian.md/teams/deploy) to network connections and config files, but it could be easier, and that's what we're working on.
> > 
> > > **Emiroda** • [7 points](https://reddit.com/r/ObsidianMD/comments/1nl8xjq/comment/nf9rerp/) •
> > > 
> > > Re policy.json: I like the idea, and it satisfies my first 2 points. Will it fail secure, ie. not start if policy.json cannot be found?
> > > 
> > > Re network access: That's probably what we will do for most users, but it's an all or nothing approach. But that absolutely satisfies my "must be able to block the store" point.
> > > 
> > > Which leaves partially allowing the store, or using a custom store. Which would unlock a lot of productivity gains for teams or individuals who rely on particular plugins.
> > > 
> > > I will give you a lot of credit for the docs you linked. They really need exposure, you should consider putting a link to them right below the Download button on the frontpage. Would help teams make more informed decisions.
> 
> **cbowers** • [2 points](https://reddit.com/r/ObsidianMD/comments/1nl8xjq/comment/nfc23vk/) •
> 
> I have to echo this, when in a context of a CyberSecurity team lead for a global multinational, most of our Security and IT teams (never mind general staff) would love to have used Obsidian on work PC’s, but the plugins as an unmanageable surface kept it blocked.
> 
