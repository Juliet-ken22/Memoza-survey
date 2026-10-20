# LinkUp AI feedback survey — setup and sharing

A standalone survey for attendees, organisers, friends — and, since the streaming reframe, Ugandans
watching from abroad who would never install a stadium app. It lives **outside** the `linkup-ai/`
app folder on purpose: no backend changes, no database, no models, no extra surface for judges to
confuse with `/console/`. One HTML file plus a free email collector, so you can share it today
whether or not the app is hosted.

```
survey/
  index.html   the whole survey — streaming-first hook, branching questions, inline feature notes, submit logic
  SETUP.md     this file
```

---

## 1. Set where responses land (already done — check it)

`survey/index.html` currently points at **julietken972@gmail.com**, the address in your git config:

```js
var COLLECTOR_EMAIL = "julietken972@gmail.com";
```

Change it if the hackathon contact email is different. Save. That is the only edit the file needs.

**The first submission needs a one-time confirmation.** The very first person to submit triggers an
email from FormSubmit asking *"Is this your email address?"* — click it once, or early responses
look like they vanished. Do that test submission yourself before the link goes out.

Why FormSubmit: no account, no API key, no plan, and each response arrives as a formatted email.
Nothing sits on a third-party dashboard you do not control.

### Where you read the answers
**Your Gmail inbox is the dashboard.** Every submit sends one email titled
`LinkUp AI survey — Attendee (Kampala)` with the answers laid out as a table. Search
`from:FormSubmit` (or subject `LinkUp AI survey`) and everything you have collected is there —
nothing expires, nothing is behind a login you do not control.

To make it a spreadsheet, either forward rows as you go, or run the same questions through
Google Forms in parallel (paste list at the bottom of this file) — Forms gives you a live
"Responses" tab and charts for free. Once the main app is deployed, the better long-term move is
to store responses in your own database and show them as a tab in `/console/`, so the survey and
the product live in one place.

## 2. Get a shareable link

All three give you a plain `https://` link to paste in WhatsApp.

**A. Netlify drag-and-drop (fastest, ~1 minute)**
1. Go to `app.netlify.com/drop`.
2. Drag the `survey` folder onto the page.
3. You get `https://<random-name>.netlify.app` — rename it under *Site configuration* to
   something like `linkup-ai-feedback`.

**B. GitHub Pages (best if you want the file versioned)**
1. New repository, e.g. `linkup-ai-feedback` (public).
2. Upload `index.html` to the root of that repo.
3. *Settings → Pages → Source: Deploy from a branch → `main` / `root`* → Save.
4. Link is `https://<your-username>.github.io/linkup-ai-feedback/` (a couple of minutes the first time).

**C. Your Render app, once the main project is live**
Drop it in `backend/templates/` as `survey.html`, add a Django view and URL, and the survey lives
at `https://<your-app>.onrender.com/feedback/`. Only worth doing after deployment — the whole point
of this build is that it does not have to wait for that.

### Check these six before sending it out
- Open the link on your **phone**, not only the laptop — almost everyone will tap it in WhatsApp.
- Submit a real test response; confirm the email arrives with all your answers, formatted.
- Pick "Attendee", then "Organizer", then "Both" and confirm the blocks swap.
- Pick "Neither", answer, and check the email has **no** attendee or organiser rows but does have the
  "Watching from anywhere" rows — that block is what the diaspora answers.
- Tick four boxes in "Which would you really use?" and confirm the fourth is refused.
- Confirm the thank-you screen replaces the form.

## 3. How the survey is built

- **Streaming is the hero, in that order.** The page opens with one line — a **live-streaming and
  crowd-safety platform** you can watch from anywhere in the world, powered by the fans inside the
  stadium — then three lines: *watch from anywhere*, *the crowd builds the broadcast*, *the offline
  safety net*. About 90 words before question one. `hook_line` then asks which of those lines made the
  person keep reading, so the headline for the landing page and the pitch deck comes from data, not from us.
- **One honest line, on purpose.** Directly under the hook, `.honest` states what runs today (attendee app,
  `*384#`, organiser console, projector wall, clip upload) and that the global fan-powered stream is where
  it is heading. Judges and a sceptical organiser can check it: the code pushes a staff webcam frame every
  2 seconds and plays uploaded clips; there is no continuous stream pipeline yet. The promise is framed as
  "would you watch and pay for it", which is the question being asked, not a claim of delivery.
- **Grace's afternoon is folded away.** The long story — PIN circle, uncle on `*384#`, the collapse and AI
  triage, the poll and airtime, her brother in Nairobi watching her stand — sits behind **"See a real day at
  the match"**. Anyone who wants the full picture gets it; nobody is handed four paragraphs first.
- **Each feature is explained where it is judged.** The rating rows carry one grey line of microcopy
  ("Alerts stewards and medical staff instantly about crushes, fires or medical issues"), and the USSD,
  AI-triage, console, live-camera, fan-mix and broadcast-rights questions each get a similar one-liner.
- **Comprehension is measured, not assumed.** `clarity` plus `unclear_parts` (which now include "how a fan in
  the crowd becomes part of the broadcast" and "which parts exist today") tell you whether the new wording
  works — a copywriting fix, and a cheap one.
- **Branching.** Question 1 (`role`) shows the attendee block, the organiser block, both, or neither.
  A hidden block is *disabled*, so its required fields cannot block submission and half-answered
  blocks cannot enter your data — switch from Attendee to Organiser mid-form and the attendee
  answers disappear from the payload. Nobody sees the whole list: the question groups a person
  actually faces is **32** for a friend or judge, **43** for an organiser, **50** for an attendee,
  **61** if they do both. Of those, **21** are required for a friend, **26** for an attendee,
  **27** for an organiser, **32** for both; every text box is optional.
- **"Pick up to three" is enforced** on `would_use`, because "everything is useful" tells you nothing.
- **Every core feature gets its own 1-5 score** (`use_stream`, `use_fanmix`, `use_sos`, `use_group`,
  `use_missing`, `use_ussd`, `use_polls`, `use_shouts`, `use_clips`, `use_meeting`) so you get a ranked
  feature list instead of one vague "do you like it". The two streaming scores sit in the block everyone
  sees, so a diaspora viewer who has never set foot in Namboole still ranks them.
- **The pivot's three real risks are asked directly**: would remote viewers pay (`stream_pay`,
  `ppv_interest`), does a raw fan-built feed beat the official TV camera (`fan_mix_trust`, `stream_blocker`,
  `stream_latency`), and whose broadcast rights are you standing on (`stream_rights`).
- **Money and adoption are asked directly**: price per event, who should pay, willingness to pay for
  the memory video and for the stream, sponsor slots on the wall, and what would make an organiser say no.
- **Text boxes are always optional**, so nobody abandons the form over a blank they cannot fill.
- One trade-off to know: nothing is saved until someone presses send. A long form means some people
  start and never finish, and those partials are lost. That is why the required questions are
  concentrated early and the free text is last. The survey grew by 11 groups with the streaming block —
  if responses arrive half-finished, the fix is to cut the attendee block, not the streaming block.

## 4. Reading the results

Emails are titled `LinkUp AI survey — Attendee (Kampala)`, so your inbox is the log. The field
names arrive as readable rows:

| Block | Fields |
|---|---|
| About you | `role` `city` `phone` `frequency` `attend_with` `data_reality` `app_habit` `info_source` |
| Description landing | `seen_app` `clarity` `unclear_parts` `hook_line` `install_intent` `open_trigger` `naming` |
| Watching from anywhere *(everyone)* | `watch_from` `stream_interest` `stream_what` `stream_device` `stream_pay` `use_stream` `use_fanmix` `fan_mix_trust` `stream_blocker` `stream_latency` |
| Attendee | `incident` `would_use` `use_sos` `use_group` `use_missing` `use_ussd` `use_polls` `use_shouts` `use_clips` `use_meeting` `ussd_appetite` `ussd_in_panic` `trust` `worries` `language` `airtime_pull` `wall_eagerness` `pay_for_memory` `dealbreaker` `missing_feature` `access_needs` |
| Organiser | `reports_today` `response_time` `console_need` `triage_trust` `staff_burden` `integration` `after_event_report` `willing_to_pay` `payer` `stream_rights` `ppv_interest` `organizer_blockers` `biggest_gap` |
| Stream, screen, survival | `wall_value` `live_cam_use` `positioning` `sponsor_model` |
| Next moves | `first_priority` `cut_one` `biggest_risk` `nps` |
| Last words | `improve` `anything` `follow_up` `contact` |

If you would rather have charts handed to you than assembled, run the same questions through
Google Forms in parallel — the paste-ready list at the bottom matches this form question for question.

## 5. What each answer should decide

| Field | The decision it feeds |
|---|---|
| `data_reality`, `phone` | Whether "no data needed" is the headline or a footnote. If most people run out of bundle mid-event, USSD is the product and the app is the bonus. |
| `app_habit`, `install_intent`, `open_trigger` | Whether you are fighting install friction. "Never install, give me a code to dial" is a build instruction, not a complaint. |
| `clarity`, `unclear_parts`, `naming` | Landing page and 60-second pitch wording. Anything under 4 is a copy problem, fixable this week. "Which parts exist today" ticked by more than a couple of people means the honest-built line needs to move up, not down. |
| `hook_line` | The headline. Whichever line wins becomes sentence one of the landing page and the pitch deck. If "None landed" is common, the hook is wrong and nothing below it matters. |
| `watch_from` | Splits your sample into crowd, home viewers and diaspora. Diaspora answers are the whole point of the pivot — they are the viewers the fans would be filming for. |
| `stream_interest`, `use_stream`, `use_fanmix` | Whether the hero feature has demand at all. `use_stream` high and `use_fanmix` low means people want the event on their phone, not an amateur cut — build the feed before the stitching. |
| `stream_what`, `stream_device` | What to stream first: full match, highlights, or pure stand atmosphere. A TV/tablet-heavy answer changes the bitrate and aspect ratio you design for. |
| `stream_pay` | The diaspora price line. "1-3 USD per match" from viewers abroad is a different business from "free only" in Kampala. |
| `fan_mix_trust`, `stream_blocker`, `stream_latency` | Whether the raw fan-built broadcast is the selling point or the excuse people will not watch. `stream_blocker` = "my data cost" is a product constraint, not a complaint — it sets your target bitrate. |
| `stream_rights`, `ppv_interest` | Whether you can sell streams at all. "A TV broadcaster owns them" plus "no, that is their business" means the pitch to organisers is safety and engagement, and streaming is sold to the rights holder instead. |
| `incident` | Which scenario to lead the demo with, and which one real people actually lived through. |
| `would_use` (max 3) | What to polish first. Features nobody picks are candidates to cut from the pitch. |
| `use_*` scores | A ranked feature list. Low scores on `use_shouts`/`use_clips` mean the fan side is decoration; low `use_group` means the safety circle concept needs rethinking. |
| `ussd_appetite`, `ussd_in_panic` | Whether *384# is usable in panic or only in calm. Decides how short the USSD menu must be. |
| `trust` | Whether the real barrier is human behaviour, not features. Low scores mean steward/stadium integration matters more than UI. |
| `worries` | Your privacy and trust page, in their words. Also tells you whether to add a "we delete your location" line. |
| `language` | Whether localisation is a nice-to-have or the top adoption lever. |
| `airtime_pull`, `pay_for_memory`, `sponsor_model` | The revenue model. "Feels like a gimmick" versus "a big reason to use it" changes the whole FanPulse story. |
| `wall_eagerness` | Whether the crowd wall will actually get content, or whether you are building an empty screen. |
| `dealbreaker`, `missing_feature`, `access_needs` | Your next backlog and your accessibility list, unfiltered. |
| `reports_today`, `response_time` | What you are replacing, and the baseline number to beat. If WhatsApp is the reality, the console must mirror it, not fight it. |
| `console_need` | Which `/console/` tab to make excellent and which to leave rough. If "multi-angle broadcast console" or "stream ticketing" wins, the console is being bought as a production tool, not a safety tool — that changes what you demo first. |
| `triage_trust` | How to present AI triage: as a decision-maker or as a suggestion with a human override. |
| `staff_burden`, `organizer_blockers` | How much deployment friction the product can carry, and what to remove to fit inside it. |
| `integration`, `after_event_report` | The enterprise features that make an organiser sign, and the report that makes the product worth renewing. |
| `willing_to_pay`, `payer` | B2B, sponsor-funded, or ticket-included. "A sponsor should pay" is a real answer and changes the pitch deck. |
| `wall_value`, `live_cam_use`, `positioning` | Whether the crowd content is a feature or a liability, where the staff camera should point, and whether "streaming is the product with safety as the differentiator" is the sentence to say out loud. |
| `first_priority` | Next sprint, decided by vote instead of gut feel. The three streaming options sit at the top of the list on purpose — if they lose to "solid `*384#` on a dying network", the pivot is the wrong call and the safety story leads. |
| `cut_one` | What to stop promoting. Reduction is usually the credible move in a hackathon demo. |
| `biggest_risk` | The risks section of your pitch, written by outsiders rather than by you. |
| `nps` | Traction evidence: "average 8.2 from 20 respondents" is a sentence for the deck. |
| `improve`, `anything` | Quotes. A real attendee sentence lands harder with judges than a feature list. |
| `follow_up`, `contact` | Who to call back for the deep interview — and your warm leads for a pilot venue. |

## 6. Getting answers worth having

- Aim for 20+ responses, and make sure at least 4 are not your friends.
- Post it in **one diaspora or Uganda-abroad group** too. If every response is from someone who can walk
  to Namboole, the streaming numbers mean nothing — that audience is the whole pivot.
- Send it to two or three organisers **personally** — they are your small sample and each answer
  matters more than any attendee's.
- Ask one person who uses a torchlight phone only. Their answers are the ones your USSD flow needs.
- Post the link, not a screenshot of the questions — you want answers, not opinions about answers.
- When you report back, say what you changed because of it. That is what makes the next round of
  people answer honestly.

---

## Google Forms paste-ready version

Same questions, same order. Use section breaks between blocks; the attendee/organiser split becomes
two sections people skip themselves, or a Google Forms branch on question 1. The "Watching from
anywhere" block stays with everyone — diaspora viewers answer it and nothing else, so do not hide it
behind the branch.

**About you**
1. Which best describes you? *(Single choice, required)* I attend big events / I organise, steward or run safety at events / Both / Neither — a friend, judge or just curious
2. City you usually attend events in *(Short answer, optional)*
3. What do you carry to an event? *(Single choice, required)* Android smartphone / iPhone / Basic phone only / One of each
4. How often are you in a crowd of 1,000+ people? *(Single choice, required)* Never / 1-2 a year / Monthly / Weekly or more
5. How do you usually attend? *(Single choice)* Alone / With 2-5 friends / With family / With an organised group
6. What is your data bundle situation at a big event? *(Single choice, required)* Always have data that works / Runs out during the event / I turn data off to save money / Too congested to use anyway / No data at all
7. How do you behave about event apps? *(Single choice, required)* I install whatever the event asks / Only if it clearly pays or protects me / Never install, give me a code to dial / I download it and never open it
8. Where do you normally get event information from? *(Checkboxes)* WhatsApp / Twitter-X / Poster or ticket / A friend / Radio-TV / Organiser announcement

**Did that description land?**
9. Had you seen LinkUp AI before this page? *(Single choice, required)* Used it / Seen a demo / Heard of it / First time
10. After reading the description, how clear is it what this does? *(Linear scale 1-5, required)*
11. Which parts still do not make sense? *(Checkboxes)* Why this needs to exist / How an SOS reaches a steward / How *384# works without internet / What the stadium screen shows / Who sits at the console / How airtime gets paid / How AI decides priority / How a fan in the crowd becomes part of the broadcast / Which parts exist today and which are still an idea / Nothing, it was clear
12. Which line made you want to keep reading? *(Single choice, required)* "Watch from anywhere" / "The crowd builds the broadcast" / "The offline safety net" / The safety side — SOS, PIN circles, missing children / None of them landed
13. Would you have it on your phone at the next big event? *(Single choice, required)* Definitely / Probably / Only if required / No / Not sure
14. When would you actually open it? *(Single choice)* Before I leave home / At the gate on check-in / Only when something goes wrong / For polls and content
15. Does the name make sense? What would you call it? *(Paragraph, optional)*

**Watching from anywhere** *(everyone — this is the streaming block)*
16. Where would you be when a big event is on? *(Single choice, required)* In the stadium / At home in Uganda / Abroad, I follow Uganda from a distance / I do not really follow live events
17. How useful would a live stream of these events be to you? *(Linear scale 1-5, required)* Useless to exactly what I want
18. What would you actually watch? *(Checkboxes)* The whole match or festival live / Goals, highlights and key moments only / The crowd and stand atmosphere from fan angles / Backstage and set content / Pre and post-event talk / Nothing, I only watch in person
19. On what screen? *(Checkboxes)* Phone / Television / Laptop / Tablet / Projected with friends
20. Would you pay to watch? *(Single choice, required)* Only if it is free / UGX 2,000-5,000 for one event / UGX 10,000 or more / 1-3 USD per match, from abroad / A monthly subscription
21. Global live streaming room — how much do you want it? *(Linear scale 1-5)* Watch big Ugandan events from anywhere, switching between the main feed and live angles from the stands
22. The fan-powered broadcast mix — how much do you want it? *(Linear scale 1-5)* Short clips from fans in every stand, stitched into one continuous live reel for remote viewers
23. A broadcast built from fan clips rather than a polished TV camera — honest reaction? *(Single choice, required)* That is the point, raw is better / Fine, if it stays watchable / I would rather have the official feed / Not interested either way
24. What would stop you from watching a stream like this? *(Single choice, required)* My data cost / It is not the full match / Poor video quality / No commentary in my language / I watch with people on TV / Nothing, I would watch
25. Does it matter if the stream is about 30 seconds behind live? *(Single choice, required)* No, the atmosphere is enough / Yes, I want it live / Only matters for scores

**Attendee experience** *(attendees and "Both")*
26. At a big event, which of these has happened to you? *(Checkboxes)* Separated from my people / Lost track of a child / Medical emergency next to me / Crush or stampede scare / Phone or network failed / Fighting, theft or harassment / None
27. Which would you really use? *(Checkboxes, limit 3)* One-tap SOS / Safety circle with a PIN / Missing-person report / Doing it all on *384# / Polls with airtime / Shout-outs on the wall / Nearest safe meeting point / Clip upload and memory mix / Watching the match live when I am not at the ground / My clip going into the live broadcast
28-35. Rate each 1-5 *(Linear scale)* One-tap SOS / Safety circle / Missing-person reporting / *384# menu / Live polls with airtime / Shout-outs on the wall and the stream / Clip upload and memory mix / Meeting point finder
36. Would you genuinely dial *384# for safety instead of an app? *(Single choice, required)* Yes definitely / Probably / Not sure / No
37. In a real emergency, would you stop and dial a menu, or just shout? *(Single choice)* I would dial, it is faster / One tap in the app / I would shout and wave / Walk to the nearest security person
38. Would you report an emergency through the app instead of to a person? *(Linear scale 1-5, required)*
39. What would you worry about? *(Checkboxes)* Location tracking / Number stored and sold / The crowd knowing I raised an alert / Spam SMS afterwards / Battery / Data cost / Prank misuse / None
40. Which language would you use it in? *(Single choice)* English / Luganda / Both / Swahili / Runyankole-Rukiga / Another
41. Does airtime for voting bring you back, or is it noise? *(Single choice, required)* Big reason / Nice bonus / Does not interest me / Feels like a gimmick
42. Would you put your own message or video on the stadium wall? *(Single choice)* Yes always / Only if friends would see it / No, too embarrassing / Never
43. Would you pay for the finished video of your day? *(Single choice, required)* Yes UGX 2,000-5,000 / Yes around UGX 10,000 / Only if free / No interest
44. What would stop you from using an event app like this? *(Paragraph, optional)*
45. What is missing that you would want? *(Paragraph, optional)*
46. Anything that would make it hard for you, or someone you look after, to use it? *(Paragraph, optional)*

**Organiser experience** *(organisers and "Both")*
47. How do incident reports reach you today? *(Single choice, required)* Radio / WhatsApp / Phone calls / Runners / Nothing formal
48. How long does a report take to reach someone who can act? *(Single choice)* Under 2 min / 2-10 min / 10-30 min / Over 30 min / Never reliably
49. Which console feature would save you the most? *(Checkboxes)* Prioritised alerts / Shared missing-person board / Staff camera to the wall / Quick event setup with join codes / Crowd clip wall / Live poll results / Event health score / Which safety circles are complete / Multi-angle broadcast console / Selling pay-per-view stream tickets
50. Would you trust a model to set incident priority? *(Single choice, required)* Yes with human override / Only for small stuff / No, staff must decide / Only if every decision is logged
51. How much change could your team absorb before an event? *(Single choice, required)* A steward app is fine / Under 30 min training / Only if it replaces radio / None, we cannot add tools
52. What would you want it connected to? *(Checkboxes)* CCTV / PA / Ticketing or gate scanning / Steward roster / Stadium radio / Ambulance-police channels / Nothing yet
53. Which after-event report would you want? *(Checkboxes)* Incident timeline with response times / Missing-person reconciliation / Sentiment and poll data / Clip library / Zone and gate counts / Evidence for insurers or police
54. Would an organiser pay for this per event? *(Single choice, required)* Only if free / Up to UGX 100k / UGX 100k-500k / More for a stadium / A sponsor should pay
55. Who should actually pay? *(Single choice)* Organiser / Venue / Sponsor / Telco / Federation or government / Baked into the ticket
56. Streaming changes who owns the footage. Whose is it today? *(Single choice, required)* A TV broadcaster owns them / We the organisers keep them / The venue or federation / Nobody has settled it / Not my area
57. Would you sell tickets to watch the stream rather than the seats? *(Single choice, required)* Yes, money the stadium cannot hold / Yes, if the fans in the crowd get most of the value / Only if it does not hurt ticket sales / No, that is the broadcaster's business / I need to see what it earns first
58. What would make an organiser say no? *(Paragraph, optional)*
59. What is the biggest gap in your operations today? *(Paragraph, optional)*

**The stream, the screen, and how it survives** *(everyone)*
60. Does a stadium screen showing crowd content make the event better? *(Single choice, required)* A lot / A little / No difference / Can be a danger
61. What should the live camera show? *(Checkboxes)* Stand energy / Gates and crowd density / Pitch or stage / Fan interviews / Not needed
62. Which is the honest truth about this product? *(Single choice, required)* Streaming is the product, safety is what makes it allowed to exist / Safety first, the stream funds it / Both equally / Make it safety only, drop the stream / They are two different products for two different buyers
63. Would you accept sponsor slots on the wall to keep it free? *(Single choice, required)* Yes fine / Yes but keep it small / No, it cheapens it / Only if it pays for safety

**What we should do next** *(everyone)*
64. What should we build or fix first? *(Single choice, required)* The global live stream / Making the fan-clip mix reliable and watchable / Stream tickets, payment and revenue splits / Solid *384# on a dying network / Faster SOS with dispatch times / Missing-person workflow / Live camera and wall / Real airtime rewards / Smoother app / Working fully offline / A clear price and pitch for organisers / Luganda, Swahili, Runyankole / A separate steward view
65. What should we cut or stop promoting? *(Single choice)* Fan polls / Clip wall / Shout-outs / Meeting point finder / Live camera / The fan-powered broadcast mix / Selling stream tickets / Nothing, keep it all
66. What do you think kills this project? *(Checkboxes)* Organisers never adopt it / Network dies at the worst moment / Fans will not install / Stadium or government rules / Nobody pays / Half-built features failing in public / Nobody trusts an app in an emergency / One bad incident blamed on it / A TV rights deal blocks the fan stream / Streaming costs too much data for Ugandan fans
67. How likely are you to tell someone about it? *(Rating 0-10, required)*

**Last words** *(everyone)*
68. If you could change one thing about how big events keep you safe, what would it be? *(Paragraph, optional)*
69. Anything else — praise, doubt, a warning, a story from a match. *(Paragraph, optional)*
70. May we contact you about the results or a follow-up question? *(Single choice, required)* Yes / Results only / No
71. Your email or phone *(Short answer, optional)*
