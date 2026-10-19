---
layout: post
title: Is Microsoft Teams End-to-End Encrypted?
description: Teams encrypts chats and files in transit and at rest, but they are not end-to-end encrypted. See where Teams E2EE applies and who can still read messages.
date: 2026-10-19 07:00
categories: [Diode, Security]
tags: [Diode, Diode Collab, Microsoft Teams, Encryption, E2EE, Privacy, Collaboration, Slack Alternative]
author: MNJR
image: assets/img/blog/is-microsoft-teams-end-to-end-encrypted.jpg
---

## Is Microsoft Teams End-to-End Encrypted?

**No — not for chats, channel messages, or files.** Teams encrypts that content in transit and at rest. By default Microsoft holds the keys. With [Microsoft Purview Customer Key](https://learn.microsoft.com/en-us/purview/customer-key-overview), the tenant supplies root keys for another at-rest layer and authorizes Microsoft 365 to use them for services such as eDiscovery and search. Messages stay available to Microsoft 365 services, to compliance tools, and to Microsoft Copilot when the signed-in user may already see that content.

End-to-end encryption in Teams is a media feature. It covers opt-in one-to-one VoIP calls, and scheduled meetings when the organizer has Teams Premium and turns E2EE on. It protects audio, video, and video-based screen sharing, not chat. Turning it on disables recording, live captions, and transcription. In an E2EE meeting it also disables Microsoft 365 Copilot in meetings and events.

For ordinary Teams chat, Microsoft 365 is inside the trust boundary. See [Is Slack end-to-end encrypted?](/blog/is-slack-end-to-end-encrypted) and [Can Slack read my messages?](/blog/can-slack-read-my-messages).

## What End-to-End Encryption Means

End-to-end encryption means content is encrypted on the sender’s device and decrypted only on the recipients’ devices. The operator does not hold keys that reveal message bodies or file payloads.

Teams docs also use two weaker phrases:

- **Encryption in transit** (TLS, and SRTP for voice and meetings) protects bytes on the network. The service on the far side can still process them.
- **Encryption at rest** protects stored copies if hardware is stolen. It does not remove the service from the read path when the service holds the keys, or when you have authorized it to use yours.

| Model | Who can read it in normal operation? |
| --- | --- |
| **End-to-end encryption** | Participants, on their devices |
| **In transit + at rest (Teams chat and files)** | Participants, Microsoft 365 services, and compliance admins using tools such as eDiscovery |
| **Customer Key** | The same service access, with root keys the tenant controls. You authorize Microsoft 365 to use those keys for eDiscovery, search, anti-malware, and anti-spam |

E2EE complicates retention, recording, and Copilot, which is why Teams’ default is built for those jobs. It does not meet “the vendor cannot read the chat.” See [Encryption in Microsoft Teams](https://learn.microsoft.com/en-us/microsoftteams/teams-encryption).

## What Teams Encrypts, and How

Standard encryption is on by default. It is not end-to-end encryption.

- **In transit.** TLS 1.2 or later with AES-256 between clients and Microsoft servers, plus SRTP for calls and meetings. Support articles describe this default as covering calling, messages, files, and meetings.
- **At rest.** Data in Azure, SharePoint, OneDrive, and other Microsoft 365 services uses BitLocker and per-file encryption, with volume-level protection from BitLocker and Distributed Key Manager. Those keys are Microsoft-managed unless you add Customer Key.
- **Service encryption.** Microsoft-managed keys can encrypt Teams data before it is stored. Microsoft says this layer is available for media content, with other services still being explored. That is not a separate key wrapped around every chat message.
- **Customer Key.** Optional at-rest encryption, not E2EE. A multi-workload policy can cover new Teams chat messages, media messages, recordings kept in Teams storage, notifications, and status messages. Files and recordings saved in SharePoint or OneDrive sit outside it; a SharePoint and OneDrive policy covers that content, including Teams files in SharePoint. The hierarchy uses your keys and an availability key Microsoft protects.

Microsoft’s modality table marks chat messages, shared files, group calls, channel meetings, recordings, transcripts, and meeting recap as standard encryption only. E2EE on each of those rows is **No**.

[eDiscovery for Teams](https://learn.microsoft.com/en-us/purview/edisc-search-teams) searches compliance records in Exchange Online. Microsoft says the primary message data stays in Azure Cosmos DB, so the tool is reading a copy that the product keeps on purpose.

## Where Teams Offers End-to-End Encryption

The admin guide is [Manage end-to-end encryption for Microsoft Teams meetings and one-to-one calls](https://learn.microsoft.com/en-us/microsoftteams/teams-end-to-end-encryption).

### One-to-one VoIP calls

Available to commercial Teams users, and Teams Premium is not required. It is **off by default**. An admin must allow it. The PowerShell value for “not enabled, but users can enable” is `DisabledUserOverride`. After that, **both people** turn on “End-to-end encrypted calls” in Teams settings. The choice syncs across supported devices.

The call then protects audio, video, and screen sharing with DTLS and GCM_AES_256. Keys are generated on the devices. Teams shows a shield with a lock and a **20-digit** code from the SHA-256 thumbprints of the two call certificates. If the codes differ, hang up.

Chat in that call is not end-to-end encrypted; Microsoft 365 secures it separately, and the overview says E2EE media “doesn’t cover chat.” PSTN calls are out. Group calls are standard encryption only. A support FAQ points “group meetings and calls” at Teams Premium, which fits scheduled meetings, not an ad-hoc group call.

While one-to-one E2EE is on, Microsoft turns off recording, live captions, transcription, and call features that need a service in the path, including transfer, merge, park, adding someone, and moving the call to another device. A user subject to compliance recording cannot use E2EE at all. Steps: [Use end-to-end encryption for Microsoft Teams calls](https://support.microsoft.com/en-us/teams/calls-devices/use-end-to-end-encryption-for-microsoft-teams-calls).

### Scheduled meetings (Teams Premium)

The organizer needs Teams Premium. Attendees do not. The org policy that allows meeting E2EE is on by default, but the meeting is end-to-end encrypted only if the organizer turns it on before it starts (Meeting options → Advanced protection). Templates and sensitivity labels can force it.

Only audio, video, and video-based screen sharing are covered. Apps, avatars, reactions, chat, filters, and Q&A are not. Participants compare a **16-digit** code. The meeting allows at most **200** people, can take slightly longer to join, and blocks unsupported clients: anything other than Teams for Windows and Mac, iOS and Android, and Teams Rooms on Windows and Android. Web, virtual desktop (VDI), and Cloud Video Interoperability devices cannot join.

An E2EE meeting drops recording, live captions, transcription, phone dial-in, breakout rooms, and **Microsoft 365 Copilot in meetings and events**, plus other service-side tools such as PowerPoint Live. It also limits retention and eDiscovery for that protected media. Chat history stays on the standard model. Steps: [Use end-to-end encryption for Teams meetings](https://support.microsoft.com/en-us/teams/meetings/use-end-to-end-encryption-for-teams-meetings).

### Channel meetings: the docs disagree

The encryption overview lists channel meetings as not available for E2EE. The meeting support article says they can be. Confirm it in your tenant. Channel messages and files are not E2EE either way.

## Who Can Read Teams Messages

Ordinary Teams chat is built so Microsoft 365 can process it. That is different from “a Microsoft employee reads your channel for curiosity.” The service can decrypt the data to run the product.

**Microsoft 365 services.** Search, transcription, translation, data loss prevention, retention, eDiscovery, supervisory review, and Copilot rely on access to stored content. Customer Key does not remove that path: you authorize Microsoft 365 to use your keys for eDiscovery, anti-malware, anti-spam, and search indexing.

**Compliance admins.** eDiscovery searches compliance copies: one-to-one and group chats in participants’ Exchange mailboxes, standard channel messages in the team mailbox, private-channel messages in members’ mailboxes, and shared-channel messages in a system mailbox for that channel. Channel files sit on the team SharePoint site; chat files sit in the sender’s OneDrive. Search needs the eDiscovery Manager role; deletion needs a further role such as Search And Purge. Retention can keep or delete the same chats. Teams does not offer “admins cannot read this.”

**Copilot.** [Copilot’s privacy documentation](https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-privacy) says it uses Microsoft Graph content the user can already view — chats, meetings, and context such as the meeting they are in — and does not bypass those permissions. Microsoft says that data is not used to train foundation models. Admins can review Copilot interactions with Content search or Purview, and Teams chats with Copilot through Teams Export APIs. Meeting E2EE turns Copilot off in that meeting only.

See also [Can Slack read my messages?](/blog/can-slack-read-my-messages).

## When Teams Is Still the Right Choice

Stay on Teams when the governed Microsoft 365 workspace is the point.

- **The rest of the suite is the workplace.** Office, Outlook, SharePoint, OneDrive, and Teams already share identity, files, and calendars. Moving only chat splits that context.
- **You want compliance search.** Retention, eDiscovery, DLP, and supervisory review need a service that can read messages. E2EE chat fights that requirement.
- **Identity is already Entra ID.** Conditional access, guests, and shared channels stay in the directory you operate.
- **Meetings are large, recorded, or transcribed.** Standard meetings keep recording, captions, transcription, and Copilot. E2EE meetings stop at 200 people and turn those off. Town halls, training, and customer calls fit Teams.
- **The other party already has a tenant.** Reaching them in Teams is easier than asking them to install something else.

Teams wins on integration, compliance tooling, enterprise identity, and large meetings. The call toggle does not change the chat archive.

## What to Look for in an End-to-End Encrypted Alternative

Judge where decryption happens, not the word “encrypted.”

- If servers decrypt messages in order to search them, you are back to transit and at-rest encryption.
- Chat and files both have to be in scope. A call-only E2EE switch leaves history and attachments behind, which is what Teams does.
- Account records, archives, file blobs, and contact graphs are part of the trust boundary. “We don’t look” is a policy. Not holding the content or the keys is an architecture.
- Phone numbers and work email bind chat to a directory. Keep that only if you want it.
- E2EE removes central legal search, so any hold has to live on devices and in your own policy.
- Files left in SharePoint or OneDrive stay on the Microsoft 365 key model.

More on the category: [encrypted Slack alternative](/blog/encrypted-slack-alternative), [self-custody Slack alternative](/blog/self-custody-slack-alternative), and [Diode Collab vs. Slack, Discord, and Teams](/blog/diode-collab-vs-discord-slack-teams).

## How Diode Collab Approaches It

[Diode Collab](https://collab.diode.io/) is local-first: encrypted messaging, encrypted file sharing, and ZTNA-style remote access in one app. Regional tunnels reach internal tools without a separate VPN concentrator. Keys stay on team devices. Chat content, files, accounts, and PII are not stored on a Diode vendor server. People work in **Zones**. The blockchain holds membership proofs, not messages or files.

Sign-up needs no phone number, and there is no vendor copy of the chat to index. Files use the same device-held keys. Your team owns devices, membership, and recovery. Purview-style search of every message is a Teams job, not a Diode Collab one.

Diode Collab does not publish ISO, SOC 2, HIPAA, or GDPR certifications. Fit depends on your data, your devices, and your policies.

### Pricing

Pricing is per user. Paid plans include unlimited users, with storage on your devices.

| Plan | Monthly per user | Yearly per user / month |
| --- | --- | --- |
| **Group** | $3 | $2.50 |
| **Team** | $10 | $8.50 |
| **Business** | $15 | $12.50 |

Group is messaging, Team adds files, and Business adds regional access tunnels. Yearly figures are the per-user monthly rate. Limits are on the [pricing page](/pricing/).

## Frequently Asked Questions

### Are Teams chats end-to-end encrypted if we enable call E2EE?

No. Call and meeting E2EE cover audio, video, and video-based screen sharing. Chat and files stay on standard encryption, with Microsoft-managed keys or Customer Key if you assigned it.

### Does Customer Key make Teams end-to-end encrypted?

No. It encrypts data at rest. You supply root keys and authorize Microsoft 365 to use them for eDiscovery and search. An availability key Microsoft protects is in the same hierarchy.

### Can a Teams admin read our messages?

Not merely by sharing the tenant. A compliance admin can. eDiscovery searches the mailbox and SharePoint copies Microsoft documents, once those roles are assigned.

### Does Copilot read Teams messages?

It can use chats and meetings the signed-in user may already view, inside those permissions. Microsoft says that data is not used to train foundation models. Meeting E2EE disables Copilot in that meeting only.

### Is Teams still a sound security choice?

Yes, when the goal is tenant protection and governance: TLS, encryption at rest, Entra ID, and Purview. That stack still lets Microsoft 365 process messages.

## Next Steps

Keep Teams when integration, large meetings, and compliance search are the job, and use E2EE only where losing recording and transcription is acceptable. If the operator must not be able to read chat and files, Teams has no switch that end-to-end encrypts channel history.

1. Compare the other default in [Is Slack end-to-end encrypted?](/blog/is-slack-end-to-end-encrypted).
2. Review [pricing](/pricing/) for Group ($3), Team ($10), and Business ($15) per user each month.
3. [Download Diode Collab](/download/) on the devices your team already uses.

<div class="story__buttons">
  <a href="/pricing/" class="btn" target="">View Pricing</a>
  <a href="/download/" class="btn" target="">Download Diode Collab</a>
</div>
