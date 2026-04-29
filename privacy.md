---
title: Privacy Policy
permalink: /privacy/
---

# Privacy Policy — Synapse for X

**Last updated: April 29, 2026**

This policy explains what data the **Synapse for X** Chrome extension ("Synapse", "the extension", "we") accesses, where it goes, and what we do with it. The short version: **Synapse runs entirely in your browser. We do not run a server, do not collect any personal data, and never see your X account, your tweets, your bookmarks, or anything else you do on x.com.**

If anything in this policy is unclear or you have a privacy concern, email <asam4r@gmail.com> and we will respond within 7 days.

---

## 1. What Synapse does

Synapse is a Chrome extension that automates and augments your use of X (formerly Twitter). It runs as JavaScript inside your own browser, on x.com tabs you have open. It:

- Performs actions on your behalf (follow, unfollow, like, unlike, retweet, unretweet, block, mute, remove follower) by clicking buttons in the X interface.
- Lets you configure filters, autopilot sequences, and an autonomous AI agent.
- Optionally uses an AI model to summarize threads, draft replies, classify accounts, and operate the autonomous agent — **only if you provide your own AI API key** (see §4).

Synapse never runs in the background on websites other than `x.com` and `twitter.com`.

## 2. Data Synapse stores on your device

All Synapse settings and state are stored locally on your device using Chrome's [`chrome.storage.local`](https://developer.chrome.com/docs/extensions/reference/api/storage) API. This data **never leaves your computer** unless you explicitly export it (e.g., the activity-log CSV export). Specifically, Synapse stores:

| What | Where | Purpose |
| --- | --- | --- |
| Pacing settings (delays, caps, filters) | `chrome.storage.local` | Apply your configured behavior |
| Autopilot configuration | `chrome.storage.local` | Run your scheduled steps |
| Agent settings + persona (seed handles, voice samples, topic keywords) | `chrome.storage.local` | Run the autonomous agent |
| Action queue | `chrome.storage.local` | Track pending agent actions |
| AI configuration (your API key, model name) | `chrome.storage.local` | Authenticate AI requests |
| License key + activation state | `chrome.storage.local` | Unlock paid tier features |
| Activity log (last 1,000 actions: timestamp, action type, target handle, status, reason) | `chrome.storage.local` | Audit trail; user-facing log + CSV export |
| Per-install random instance name (UUID) | `chrome.storage.local` | Distinguish this browser to your license issuer (LemonSqueezy) |

You can wipe all of this at any time by removing the extension or by clicking **Clear** in Options → Tools & Activity → Activity log (clears just the log).

## 3. Data Synapse reads from x.com

When you trigger a Synapse action, the extension reads the X DOM (the page content already visible to you) to:

- Find user cells, tweets, follow buttons, etc., and click them.
- Extract handles and tweet IDs for the activity log.
- Apply filters (skip verified, no-bio, follower count, tweet age).
- Build a persona from seed profiles (when you click "Train persona" — Synapse navigates to each seed's profile in your tab and reads the rendered tweets).

**Synapse never sends X content to our servers because we do not run servers.** The content stays in your browser unless you have explicitly enabled an AI feature, in which case selected snippets are sent to your chosen AI provider (see §4).

## 4. Third parties Synapse communicates with

Synapse only contacts external services in these specific cases:

### 4.1 Your AI provider — only if AI features are enabled

If you enable AI features, you provide your own API key (currently for [Groq](https://groq.com)). Synapse sends your AI provider:

- The system + user prompts that power features such as **bot likelihood classification**, **thread summarization**, **reply drafting**, and **autonomous agent decisions**.
- These prompts include the relevant slice of X content the feature is operating on (a UserCell's bio + handle for the bot filter; a thread's tweet texts for summarization; a parent tweet plus your past tweets used as voice samples for reply drafting; a candidate tweet/profile plus your persona for the agent).

This is **bring-your-own-key (BYOK)**: your API key, your contract, your data. **We do not see, proxy, log, or have any access to these requests.** Read your AI provider's privacy policy for how they handle the requests.

You can disable AI features at any time in Options → AI by unchecking "Enable AI features" or by deleting your API key.

### 4.2 LemonSqueezy — only if you purchase a paid tier

If you upgrade to Pro or Ultra, Synapse uses [LemonSqueezy](https://www.lemonsqueezy.com)'s public **License Key API** to:

- **Activate** your license key (`POST /v1/licenses/activate`) — sends your key + a randomly-generated instance name (e.g., `synapse-<uuid>`).
- **Validate** your license key once per day (`POST /v1/licenses/validate`) — sends your key + the instance ID.
- **Deactivate** an instance when you click "Deactivate this device" (`POST /v1/licenses/deactivate`).

LemonSqueezy is the merchant of record for your purchase and handles billing, refunds, taxes, and customer support related to the transaction. Their privacy policy is at <https://www.lemonsqueezy.com/privacy>. Synapse never sends LemonSqueezy any X content, your AI key, or any data beyond what is required for licensing.

### 4.3 No analytics, no telemetry, no third-party servers

Synapse does **not** include any analytics SDK, error reporter, telemetry pipeline, ad network, or our own server. We do not have visibility into how often you use the extension, which features you open, or anything else about your usage. Our only signal that the extension is in use is the daily license-validation ping for paying customers — and that ping contains only the license key and instance ID.

## 5. Data we never collect

For clarity, **Synapse does NOT collect, transmit, or store on any server we control:**

- Your X username, handle, password, email, or any account credentials.
- Your tweets, replies, DMs, bookmarks, follows, followers, or any other X data.
- Your AI provider API key (it is stored only on your device).
- Your IP address, device fingerprint, browser version, or location.
- Your browsing history, including history on x.com.
- Any payment-card information (LemonSqueezy handles all payment processing — they do not share card data with us).
- Any usage metrics or analytics events.

## 6. Permissions Synapse requests

The extension declares these permissions in its manifest:

- **`storage`** — to persist your settings and state in `chrome.storage.local`.
- **`alarms`** — to schedule autopilot ticks, agent ticks, and daily license re-validation.
- **`tabs`** — to find or create x.com tabs for autopilot/agent execution and to open external URLs (LemonSqueezy customer portal, AI provider sign-up).
- **`downloads`** — to save the activity-log CSV export to your device.
- **Host permissions** for `https://x.com/*`, `https://twitter.com/*`, `https://api.groq.com/*`, `https://api.lemonsqueezy.com/*`, and `localhost` — needed to read x.com content for actions and to call the AI provider and LemonSqueezy APIs respectively.

## 7. Children's privacy

Synapse is intended for adult users authorized to operate an X account. It is not directed to children under the age of 13, and we do not knowingly collect any data from children.

## 8. Your rights and how to exercise them

Because Synapse stores your data only on your device, you have full control:

- **Access**: Open Options → Tools & Activity to view your activity log, settings, and license state.
- **Delete**: Uninstall the extension from `chrome://extensions` to remove all local Synapse data. To delete just the activity log, open Options → Tools & Activity → Activity log → Clear.
- **Export**: Options → Tools & Activity → Export CSV (Pro tier).
- **Cancel subscription / delete LemonSqueezy data**: Use the customer portal at <https://app.lemonsqueezy.com/my-orders> to manage or cancel your subscription. Contact LemonSqueezy directly to request deletion of your purchase records.

If you are in the EU/UK, you have rights under GDPR (access, rectification, erasure, portability). To exercise them with respect to anything Synapse handles, email <asam4r@gmail.com>. For purchase records, contact LemonSqueezy.

## 9. Use of X (Twitter) and the X rules

Synapse interacts with X by automating clicks in your own browser. Aggressive automation may violate the X Rules and X's Developer Agreement, and could result in rate-limiting, shadowban, or suspension of your X account. **You are solely responsible for how you use Synapse.** We have built in conservative defaults, rate-limit detection, captcha detection, and per-day caps, but we cannot guarantee any specific X-platform outcome.

## 10. Security

Synapse runs entirely in your browser, so its security posture is the same as your browser's. We do not store, transmit, or have access to your data. AI keys you enter are stored in `chrome.storage.local`, which is sandboxed to the extension by Chrome.

**You should not paste your AI API key into any chat, screenshot, or any environment outside the Synapse Options page.** If you suspect your key has been exposed, regenerate it immediately at your AI provider's dashboard.

## 11. Changes to this policy

We may update this policy as Synapse evolves. The "Last updated" date at the top of this page reflects the most recent revision. Material changes (e.g., introducing a new third-party service or beginning to collect data) will be communicated via:

- An updated "Last updated" date, and
- A note in the Synapse Options page or release notes for the relevant version.

Continued use of Synapse after changes means you accept the updated policy.

## 12. Contact

For privacy questions, complaints, or to exercise any of the rights described above:

- Email: <asam4r@gmail.com>
- Subject line: `Synapse for X — Privacy`

We aim to respond within 7 calendar days.
