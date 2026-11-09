---
layout: post
title: Is Discord End-to-End Encrypted?
description: Discord text chat is not end-to-end encrypted. Voice and video calls now use DAVE by default; messages still stay readable for safety, search, and features.
date: 2026-11-09 07:00
categories: [Diode, Security]
tags: [Diode, Diode Collab, Discord, Encryption, E2EE, Privacy, Collaboration]
author: MNJR
image: assets/img/blog/is-discord-end-to-end-encrypted.jpg
---

## Is Discord End-to-End Encrypted?

**No for text. Yes for most voice and video.** Discord text chat, in server channels and in direct messages, is not end-to-end encrypted. Discord stores those messages and can read them. Voice and video are a separate system. On May 18, 2026, Discord said that as of early March 2026 every voice and video call in direct messages, group DMs, voice channels, and Go Live streams is end-to-end encrypted by default, with no opt-in. Stage channels are the exception. The same post says Discord has no current plans to extend end-to-end encryption to text.

A lock icon on a call does not cover channel history, the DM, or files in the chat. If the operator must be unable to read team messages, Discord text does not meet that bar. Related reads: [Is Slack end-to-end encrypted?](/blog/is-slack-end-to-end-encrypted), [Is Microsoft Teams end-to-end encrypted?](/blog/is-microsoft-teams-end-to-end-encrypted), and [a secure Discord alternative](/blog/diode-collab-a-secure-discord-alternative).

## Transport Encryption, Encryption at Rest, and End-to-End Encryption

Only one of these keeps the service from reading the content.

| Model | What it protects | Who can read it in normal operation |
| --- | --- | --- |
| **Transport encryption (TLS)** | Bytes on the network path | The service, after it receives them |
| **Encryption at rest** | Stored copies if disks or backups are taken | The service, when it needs the data for delivery, search, or moderation |
| **End-to-end encryption (E2EE)** | Content from the sender's device to the recipients' devices | Participants only. The service routes ciphertext and does not hold the keys |

Discord's [privacy policy](https://discord.com/privacy) says data in its services is encrypted in transit and at rest, and names TLS for text and images. The same section says voice and video are designed to be end-to-end encrypted. TLS and at-rest encryption protect a message on the wire and on disk, and they leave Discord able to store, search, and moderate it. The server can still see that a call or a message exists.

## What Discord End-to-End Encrypts

The protocol is DAVE. Stephen Birarda introduced it on September 17, 2024, in [Meet DAVE](https://discord.com/blog/meet-dave-e2ee-for-audio-video). That post said Discord would start migrating voice and video in DMs, group DMs, voice channels, and Go Live streams, and that messages are not end-to-end encrypted because they stay on the moderation model. The protocol and client libraries are open source, and Trail of Bits reviewed the design and the implementation. One client without DAVE held that call at transport-only encryption until the person left.

The later account is [Every Voice and Video Call on Discord Is Now End-to-End Encrypted](https://discord.com/blog/every-voice-and-video-call-on-discord-is-now-end-to-end-encrypted) (Mark Smith, May 18, 2026):

- In 2025, DAVE reached web browsers, gaming consoles, bots and apps, and the Social SDK. One call can mix a laptop, a phone, a PlayStation, an Xbox, and a browser.
- The migration finished at the beginning of March 2026. Those calls are end-to-end encrypted by default. The help center says you cannot turn it off.
- Clients had to support DAVE to join. As of May 2026, Discord was still removing unencrypted-fallback code.
- Stage channels are excluded. Discord describes them as broadcasts for events, AMAs, and town halls.

The help center dates the requirement [March 2, 2026](https://support.discord.com/hc/en-us/articles/25968222946071-End-to-End-Encryption-for-Audio-and-Video). The [developer voice docs](https://docs.discord.com/developers/topics/voice-connections) say March 1, 2026. That help article was last updated February 20, 2026, and still describes parts of the rollout, including web and console support in the second half of 2025. Use the May 2026 post for the finished state.

Members exchange keys with Messaging Layer Security. Discord does not have the media keys. Developer docs say frames use AES-128-GCM. Forwarding servers still pass the packets, under transport encryption, and the 2024 write-up says they cannot decrypt the audio and video inside. Keys change when someone joins or leaves.

A green lock and a Voice Privacy Code show a shared call state. Each Go Live stream has a Stream Privacy Code. Compare them out of band, at the same time, because they change as people come and go. The check verifies the call. It does not turn encryption on. Persistent verification is optional, tied to one device, and lost on logout.

## What Call Encryption Leaves Open

**Text, including chat during a call.** Channels, threads, DMs, and the text panel in a voice channel are messages. AutoMod's FAQ lists that voice-channel text as something it can filter, which requires the product to see it.

**Files and voice messages.** Uploads and short voice messages sent in chat are stored content in the privacy policy. They are not the live call.

**Stage channels and previews.** Stage channels are not end-to-end encrypted. Neither are Go Live previews, the snippet visible before you join. Previews can be turned off entirely or per stream.

**Metadata.** The voice service has to know who is in the session in order to add people and route key exchange. The privacy policy also lists IP address, operating system, browser, servers and channels you open, friends, and bots you use. A locked call still shows who met, when, and where.

**Recordings and extra participants.** Discord says it generally does not store voice or video call contents, and that it would say so before changing that. It does keep a stream thumbnail briefly. Anyone on the call can record locally. A bot that joins and implements DAVE is a participant and can decrypt the media. Discord's servers do not hold those keys.

## What Discord Can See in Text

Discord is inside the trust boundary for channels and DMs. The privacy policy limits which employees and contractors can open nonpublic personal information. It also says Discord stores the messages you send so it can display them.

- **Content.** Channel text, threads, DMs, and drafts. Search works because that text is readable. Discord says it scans attachments and other content for illegal or harmful activity. A data download comes back in ordinary file formats, including types you uploaded.
- **Who, when, and where.** Author, time, and channel are part of delivery. Around them: servers, channels visited, friends, roles, and device data such as an IP address. People allowed into a channel can read it. Admins set that access. They do not get a view into someone else's DMs. Discord holds the DMs because it stores them.
- **The account.** Username, password, a contact method (email, phone, or both), and a birthday. Discord may ask for more to check age in some cases, and may require a verified phone number.

The help center, updated December 12, 2025, documents [search in servers and DMs](https://support.discord.com/hc/en-us/articles/115000468588-Using-Search) with filters for person, channel, date, and files. Messages remain until you delete them. Moderators with Manage Messages can delete in their server. Trust and Safety can still remove material that breaks the rules.

## Why Text Stays Readable

The 2024 DAVE post tied readable messages to moderation. The May 2026 post says there is no current plan to encrypt text, because many features assume it is readable and rebuilding them is a large engineering job.

**Trust and Safety.** Reports go to Discord's safety team, and Discord also looks for harmful content: photo matching and machine-learning tools for child sexual abuse material, models that use metadata, human review, and AutoMod. Investigations can include transcriptions. See Discord's [moderation write-up](https://discord.com/safety/our-approach-to-content-moderation).

**AutoMod and bots.** [AutoMod](https://support.discord.com/hc/en-us/articles/4421269296535-AutoMod-FAQ) can block a keyword before a message posts, in channels, threads, and voice-channel text, and can alert moderators with who, what, and where. Admins can also add apps. Message content is a privileged intent: an approved app receives text, embeds, and attachments. Without it, those fields are empty, aside from cases such as a DM with the app, a mention, or a message the app was asked to handle.

**Search and larger spaces.** History and search need the text. The privacy policy says content from larger spaces may be used for features such as catch-up and safety, and settings can limit some of that use. That passage is about larger spaces, not a claim that DMs train a public model.

**Legal process.** Discord may disclose information when it believes the law requires it, and it publishes a transparency report. Its [law-enforcement page](https://discord.com/safety/360044157931-working-with-law-enforcement) says it responds to valid legal process, accepts preservation requests, and may notify you first where the law allows. Transit and at-rest encryption do not hide a stored message from the company that holds it. [Slack works the same way](/blog/can-slack-read-my-messages). Discord kept text readable so that moderation can keep working.

## When Discord Is the Right Choice

Pick Discord when you want a community, not a private office.

- **Public servers.** Discovery, invites, and roles. A public channel may be readable by strangers, and owners can change who is allowed in.
- **Gaming and voice.** Voice channels and Go Live, on computers, phones, browsers, and consoles. DAVE covers that media, outside stage channels, without key management.
- **Bots and moderation.** Moderation bots exist because a server can let an app read messages. Staff who must block harassment, scams, and illegal content have to see the text.
- **A free core.** You can run a server without a per-seat license. Paid upgrades are optional.

Teams that want a public Discord and a private working room often keep both.

## Diode Collab for Private Team Chat

[Diode Collab](https://collab.diode.io/) is for teams that need the vendor out of the read path, without public discovery or a bot marketplace.

It is local-first. Keys stay on team devices. Chat, files, accounts, and personal information are not stored on a Diode vendor server. People work in **Zones**. The blockchain holds membership proofs for a Zone, not the messages and not the files. Content syncs encrypted between devices in the Zone.

The same app includes encrypted file sharing and ZTNA-style remote access, the path teams otherwise buy a VPN for. Business adds secure equipment access and regional tunnels. A phone number is not required.

You give up public discovery, bots, and community voice rooms, and you own device recovery, because there is no vendor copy of the chat. The longer guide is [a secure Discord alternative](/blog/diode-collab-a-secure-discord-alternative). The same questions for workplace chat are in the [encrypted Slack alternative](/blog/encrypted-slack-alternative) guide.

## Diode Collab Pricing

Pricing is per user. The yearly figure is the monthly price on annual billing.

| Plan | Monthly per user | Yearly per user / month |
| --- | --- | --- |
| **Group** | $3 | $2.50 |
| **Team** | $10 | $8.50 |
| **Business** | $15 | $12.50 |

Group is messaging. Team adds file collaboration. Business adds secure equipment access and regional tunnels. Limits are on the [pricing page](/pricing/).

## Frequently Asked Questions

### Is Discord text chat end-to-end encrypted?

No. Channels, threads, and DMs use TLS in transit and encryption at rest. Discord stores them. It said that in September 2024, and in May 2026 said it has no current plan to encrypt text.

### Are Discord calls end-to-end encrypted now?

Voice and video in DMs, group DMs, voice channels, and Go Live streams are, by the May 18, 2026 account of a migration finished in early March 2026. Stage channels are not. Clients without DAVE cannot join. The help center says March 2, 2026. Developer docs say March 1, 2026.

### Can Discord read my direct messages?

Discord stores DMs and can process them for delivery, search, safety, and legal process. Employee access is limited. Server admins cannot open someone else's DMs. They can read channels their role allows, and add a bot that reads those channels.

### Does a locked call cover the chat, or stop a recording?

No. Text in a voice channel is still a message. Discord generally does not store live call contents. A participant can record locally. Go Live previews are not end-to-end encrypted. A bot in the call can receive the media.

### Should a team use Discord for confidential projects?

Use it for a community: open membership, gaming, bots, and moderators who must read the chat. Discord does not end-to-end encrypt text or files. Many groups keep Discord for the community and move the private work.

### Is Diode Collab ISO, SOC 2, or HIPAA certified?

Do not infer certifications that Diode has not published. Diode Collab does not publish ISO, SOC 2, or HIPAA certifications. Its value proposition is local-first end-to-end encryption, device-held keys, and reduced vendor data custody—not a claim that a certificate replaces your compliance program. Organizations with regulated workloads should evaluate their own deployment, policies, legal requirements, and qualified advisors.

## Next Steps

Keep Discord for community voice, bots, and moderators who can read the chat. The call lock does not change the text. If the operator should not read team messages, start with [a secure Discord alternative](/blog/diode-collab-a-secure-discord-alternative).

1. Review [Diode Collab pricing](/pricing/) for Group ($3), Team ($10), and Business ($15) per user each month.
2. [Download Diode Collab](/download/) on the devices the team already uses.
3. Set Zone membership and device recovery before moving a sensitive conversation.

<div class="story__buttons">
  <a href="/pricing/" class="btn" target="">View Pricing</a>
  <a href="/download/" class="btn" target="">Download Diode Collab</a>
</div>
