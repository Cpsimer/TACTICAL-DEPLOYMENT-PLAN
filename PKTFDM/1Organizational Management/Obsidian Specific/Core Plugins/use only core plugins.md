[

### Steph Ango   • Following

CEO at Obsidian

https://www.linkedin.com/in/stephango

[Petr Meissner](https://www.linkedin.com/in/myneur/) Yes the approach is similar to the "marketplace protections" Microsoft gradually implemented for VS Code. It involves a trusted developer program and more automated tests, but I can't give you a exact timeline.  
  
Note that on iOS, iPadOS, and Android the app is sandboxed so plugins are more constrained.  
  
Powerful tools let you do powerful things. A chainsaw can cut down a tree but it can also cut your arm off. This is part of the risk users accept. The risks of using plugins are explained in intentionally scary language for this reason:  
[https://help.obsidian.md/plugin-security#Plugin+capabilities](https://help.obsidian.md/plugin-security#Plugin+capabilities) 

Yes, on desktop, Obsidian plugins can access files on your system, unless you run it in a container. On iOS, iPadOS, and Android the app is sandboxed so plugins are more constrained.

This is not unique to Obsidian. VS Code (and Cursor) works the same way despite Microsoft being a multi-trillion dollar company. This is why Obsidian ships in restricted mode and there's a full-screen warning before you turn on community plugins.

VS Code and Obsidian have similar tradeoffs, both being powerful file-based tools on the Electron stack. This fear about plugins was raised on the Obsidian forums in 2020 when Obsidian was still new, and [Licat explained](https://forum.obsidian.md/t/security-of-the-plugins/7544/3) why it’s not possible to effectively sandbox plugins without making them useless.

So... what do you do?

The drastic option is to simply not use community plugins. You don't have to leave restricted mode. For businesses there are several ways to [block network access and community plugins](https://help.obsidian.md/teams/deploy). And we're currently planning to add more IT controls via a policy.json file I [described here](https://www.reddit.com/r/ObsidianMD/comments/1nl8xjq/comment/nf8td42/?utm_source=share)

The option of using Obsidian without plugins is more viable in 2025 than it was in 2020, as the app has become more full-featured. And we're now regularly doing third-party [security audits](https://obsidian.md/security).

But realistically, most people want to use community plugins, and don't have the technical skills to run Obsidian in a container, nor the ability and time to review the code for every plugin update.

So the solution that appeals to us most is similar to the "[Marketplace protections](https://code.visualstudio.com/docs/configure/extensions/extension-runtime-security#_marketplace-protections)" that Microsoft gradually implemented for VS Code. For example, implementing a trusted developer program, and automated scanning of each new plugin update. We plan to significantly revamp the community directory over the coming year and this is part of it.

Finally, I'd like to say thank you to everyone who has financially supported Obsidian over the years via Catalyst, Sync, Publish, etc. Obsidian is a team of 7 people. We're [100% user-supported](https://stephango.com/vcware) and competing with massive companies like Microsoft, Apple, Google, etc. Security audits are not cheap. Building an entire infrastructure like the one I described above is not easy. We're committing to doing it, but it wouldn't be possible without our supporters.