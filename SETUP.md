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

> **Read this before editing any wording: the `value="…"` attributes are your data keys.** In the
> streaming pass of October 2026 every *display* string was rewritten into plain English, and not one
> `value` was touched — `value="The crowd builds the broadcast"` now displays as **"The fans make
> it"**, `value="What the stadium screen shows"` now displays as **"What the big screen at the
> stadium would actually show"**. That keeps responses collected before the rewrite comparable with
> responses after it. If you rename a `value`, the spreadsheet column changes meaning halfway through
> your results. Only change a `value` deliberately, and note the date here.

- **The fans are the product, safety is the guarantee — in that order.** The page opens with a
  **fan-powered live stream** you can watch from anywhere in the world, then three lines: *watch from
  anywhere*, *the fans make it*, *the safety part, for people who are there*. About 140 words before
  question one. `hook_line` then asks which of those lines made the person keep reading, so the
  headline for the landing page and the pitch deck comes from data, not from us.
  **Why the order changed:** the first real reader's objection was that *"a typical Ugandan won't
  install an event safety app — they came to enjoy, not to worry."* That is correct, and it is now
  designed for. Nobody installs for safety; they install for the stream, the airtime and seeing
  themselves on the big screen. Safety is what makes the organiser and the sponsor buy it, and it is
  stated as a one-line promise you will probably never need ("Most days you will never touch this
  part") rather than as a second product. Treat this as a finding, not a complaint — `install_intent`,
  `open_trigger` and the "Fans will not install anything" option in `biggest_risk` measure it.
- **No private vocabulary on the respondent's side.** Every internal word was replaced in the text a
  person actually reads: *clip wall / crowd wall* → **the big screen at the stadium playing fan
  videos**; *projector* → **the big screen at the stadium**; *the console* → **the organiser's page**
  (defined in full at first use); *safety circle* → **a group list with a PIN**; *live angles* →
  **views filmed by fans in different stands**; *stitched into one continuous feed* → **played one
  after another as a single live stream**; *AI triage* → **the system reads the report and marks it**;
  *the diaspora* → **fans watching from other countries**; *memory mix* → **the day back to you as one
  edited video**; *sponsor slot on the wall* → **an advert from a sponsor on the big screen** (and
  "memory" never appears as a noun on its own — it is always *the finished video of your day*). Jargon
  survives only inside `value` keys. If a future question tempts you to write
  "wall", "console" or "mix" in display text, a reader will not know what you mean — they answered
  `unclear_parts` on exactly those words.
- **One honest line, on purpose.** Directly under the hook, `.honest` states what runs today (the app,
  `*384#`, the big screen playing fan videos, the organiser's page, and one link you can open from
  anywhere to watch those videos) and says plainly that a full live stream of the whole match is not
  built yet. Judges and a sceptical organiser can check it: the code pushes a staff webcam frame every
  2 seconds, plays uploaded clips, and `/watch/<join code>/` serves that feed to the public; there is
  no continuous pipeline for the match itself. The promise is framed as "would you watch and pay for
  it", which is the question being asked, not a claim of delivery.
- **Grace's afternoon is folded away.** The long story — her group with a PIN, uncle on `*384#`, the
  collapse and how the system ranks it, the poll and airtime, her brother in Nairobi watching her
  stand — sits behind **"See a real day at the match"**. Anyone who wants the full picture gets it;
  nobody is handed four paragraphs first. Each term is explained inside the story the first time it
  appears ("That group is just a small list of the people she came with").
- **Each feature is explained where it is judged.** The rating rows carry one grey line of microcopy
  ("Alerts stewards and medical staff instantly about crushes, fires or medical issues"), and the USSD,
  priority-ranking, organiser-page, live-camera, fan-video and broadcast-rights questions each get a
  similar one-liner. The organiser-page row now carries a four-sentence definition, because the first
  reader could not tell who sits at it.
- **Comprehension is measured, not assumed.** `clarity` plus `unclear_parts` tell you whether the new
  wording works. The two options the first reader picked are still in the list, now phrased as *"What
  the big screen at the stadium would actually show"* and *"Who uses the organiser's page, and what do
  they do with it"* — so you can tell whether the rewrite fixed those two specifically or whether the
  surfaces themselves are unclear. If they are still ticked often, it is a product-shape problem, not a
  copy problem, and the answer is a screenshot on the landing page rather than more words.
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
| The stream and the big screen | `wall_value` `live_cam_use` `positioning` `sponsor_model` |
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
anywhere" block stays with everyone — fans watching from other countries answer it and nothing else,
so do not hide it behind the branch.

Every question below lists only the **option text a respondent reads**. The plain-English
explainer lines (`What the organiser's page is`, the `<small>` text under each 1-5 rating, the
`Example:` hints) go in the Google Forms **Description** field of the same question — they are what
stopped people from giving up on this survey last time, so do not drop them. The words in
`value="…"` inside `index.html` are your analysis keys and never change; the option text does.

**About you**
1. Which best describes you? *(Single choice, required)* I attend big events — matches, concerts, festivals / I organise, steward or run safety at events / Both — I go to events and I work them / Neither — a friend, judge or just curious
2. City you usually attend events in *(Short answer, optional)*
3. What do you carry to an event? *(Single choice, required)* Android smartphone / iPhone / Basic phone only / One of each
4. How often are you in a crowd of 1,000+ people? *(Single choice, required)* Never / 1-2 a year / Monthly / Weekly or more
5. How do you usually attend? *(Single choice)* Alone / With 2-5 friends / With family / With an organised group
6. What is your data bundle situation at a big event? *(Single choice, required)* I always have data that works / It runs out during the event / I turn data off to save money / The network is too jammed to use anyway / I have no data at all
7. How do you behave about event apps? *(Single choice, required)* I install whatever the event asks / Only if it clearly pays me or protects me / I never install — give me a code to dial / I download it and never open it
8. Where do you normally get event information from? *(Checkboxes)* WhatsApp / Twitter/X / Poster or ticket / A friend / Radio/TV / Organiser announcement

**Did that description land?**
9. Had you seen LinkUp AI before this page? *(Single choice, required)* Used it / Seen a demo / Heard of it / First time
10. After reading the description above, how clear is it what this does? *(Linear scale 1-5, required)* 1 = Still confusing, 5 = Crystal clear
11. Which parts still do not make sense? *(Checkboxes)* Why this needs to exist at all / How a tap on SOS actually reaches a steward / How *384# can work when there is no internet / What the big screen at the stadium would actually show / Who uses the organiser's page, and what do they do with it / How the airtime actually reaches my phone / How the system decides a report is high priority / How a fan's phone video ends up in the stream / Which parts exist today and which are still an idea / Nothing — it was clear
12. Which line made you want to keep reading? *(Single choice, required)* "Watch from anywhere" — the stream you open on any screen / "The fans make it" — the people inside are the camera crew / "The safety part" — works on *384# with no internet / The safety side — SOS, group PINs, lost children / None of them landed
13. Would you have it on your phone at the next big event? *(Single choice, required)* Definitely / Probably / Only if required / No / Not sure
14. When would you actually open it? *(Single choice)* Before I leave home / At the gate when I check in / Only when something goes wrong / For polls and content during the event
15. Does the name make sense to you? What would you call it? *(Paragraph, optional)*

**Watching from anywhere** *(everyone — this is the streaming block)*
16. Where would you be when a big event is on? *(Single choice, required)* In the stadium, in the crowd / At home in Uganda / Abroad — I follow Uganda from a distance / I do not really follow live events
17. How useful would a live stream of these events be to you? *(Linear scale 1-5, required)* 1 = Useless, 5 = Exactly what I want
18. What would you actually watch? *(Checkboxes)* The whole match or festival, live / Goals, highlights and key moments only / The crowd and the atmosphere in the stands, filmed by fans / Backstage and set content / Pre and post-event talk / Nothing — I only watch in person
19. On what screen? *(Checkboxes)* Phone / Television / Laptop / Tablet / Projected with friends
20. Would you pay to watch? *(Single choice, required)* Only if it is free / UGX 2,000-5,000 for one event / UGX 10,000 or more / 1-3 USD per match, from abroad / A monthly subscription
21. A live stream room for big events — how much do you want it? *(Linear scale 1-5)* Open one link from any country and watch — the main view, plus views filmed by fans in different stands
22. A stream built from fan clips — how much do you want it? *(Linear scale 1-5)* Short videos from fans in every stand, played one after another as a single live stream for people watching from far away
23. A stream made from fans' phone videos instead of a proper TV camera — honest reaction? *(Single choice, required)* That is the point, I want the real feel / Fine, as long as it stays easy to watch / I would rather have the TV camera / Not interested either way
24. What would stop you from watching a stream like this? *(Single choice, required)* My data cost / It is not the full match / Poor video quality / No commentary in my language / I watch with people on TV / Nothing, I would watch
25. Does it matter if the stream is about 30 seconds behind live? *(Single choice, required)* No, the atmosphere is enough / Yes, I want it live / Only matters for scores

**Your experience as an attendee** *(attendees and "Both")*
26. At a big event, which of these has happened to you? *(Checkboxes)* Separated from my people and could not reconnect / Lost track of a child / A medical emergency right next to me / A crush or stampede scare / My phone or the network completely failed / Fighting, theft or harassment / None of these
27. Which of these would you really use? *(Checkboxes, up to three)* One-tap SOS to organisers and on-duty security / A group list so I know the people I came with are safe / Reporting a lost child or lost friend by name and where they were last seen / Doing all of it on *384# with no data / Live polls that pay airtime / Putting my message or my video on the big screen at the stadium / Finding the nearest safe meeting point / Sending a video of my stand, and getting the day back as one edited video / Watching the match live on my phone when I am not at the ground / My clip going into the stream the whole country is watching
28-35. Rate each one — how useful would it be to you? *(Eight linear scales, 1-5)* One-tap SOS / A group list with a PIN / Missing person reporting / The *384# menu / Live polls with airtime / Your message or video on the big screen / Sending a clip, and getting your day back / Safe meeting point finder — each keeps its own one-line explanation in the Description field
36. Would you genuinely dial *384# for safety instead of an app? *(Single choice, required)* Yes, definitely / Probably / Not sure / No
37. In a real emergency, would you stop and dial a menu, or just shout? *(Single choice)* I would dial — it is faster than finding a steward / One tap in the app, if I have it open / I would shout and wave my arms / Walk to the nearest security person
38. Would you report an emergency through the app instead of to a person? *(Linear scale 1-5, required)*
39. What would you worry about? *(Checkboxes)* My location being tracked / My number being stored and sold / The crowd or screen knowing I raised an alert / Getting flooded with SMS after / Battery dying because the app runs / It eating my bundle / Someone abusing it as a prank / None of these worry me
40. Which language would you use it in? *(Single choice)* English / Luganda / Both / Swahili / Runyankole/Rukiga / Another — say below
41. Does airtime for voting bring you back, or is it noise? *(Single choice, required)* A big reason to use it / A nice bonus / Does not interest me / Feels like a gimmick
42. Would you put your own message or video on the big screen at the stadium? *(Single choice)* Yes, always / Only if my friends would see it / No — too embarrassing / Never
43. Would you pay for the finished video of your day — all the clips from the crowd edited into one? *(Single choice, required)* Yes, UGX 2,000-5,000 / Yes, around UGX 10,000 / Only if it is free / No interest
44. What would stop you from using an event app like this? *(Paragraph, optional)*
45. What is missing that you would want? *(Paragraph, optional)*
46. Anything that would make it hard for you, or someone you look after, to use it? *(Paragraph, optional)*

**Your work as an organiser or steward** *(organisers and "Both")*
47. How do incident reports reach you today? *(Single choice, required)* Radio / WhatsApp / Phone calls / Runners / Nothing formal
48. How long does a report usually take to reach someone who can act? *(Single choice)* Under 2 min / 2-10 min / 10-30 min / Over 30 min / Never reliably
49. Which part of the organiser's page would help you the most during an event? *(Checkboxes)* Reports sorted by how serious they are (medical / security / fire / crush) / One lost-person list the whole team can see and update / Putting a steward's phone camera on the big screen / Setting up an event quickly and getting its join code / Picking which fan videos play on the big screen / Live poll results and how the crowd is feeling / One safety number for the whole event, going up or down as reports come in / Which groups have checked in, and which are still missing people / Choosing which camera the stream shows, like a TV director / Selling tickets to the stream and seeing who bought
50. Would you let the system decide how serious a report is? *(Single choice, required)* Yes, if a human can override it / Only for sorting the small stuff / No — staff must decide / Only if every decision is logged
51. How much change could your team absorb before an event? *(Single choice, required)* A steward app is fine / Under 30 min training / Only if it replaces radio / None, we cannot add tools
52. What would you want it connected to? *(Checkboxes)* CCTV / PA / Ticketing or gate scanning / Steward roster / Stadium radio / Ambulance-police channels / Nothing yet
53. Which report would you want after the event? *(Checkboxes)* Incident timeline with response times / Missing person reconciliation / Crowd sentiment and poll data / The clip library / Zone and gate counts / Evidence for insurers or police
54. Would an organiser pay for this per event? *(Single choice, required)* Only if free / Up to UGX 100k / UGX 100k-500k / More, for a stadium / A sponsor should pay
55. Who should actually pay? *(Single choice)* The organiser / The venue / A sponsor / A telco / Federation or government / Baked into the ticket
56. Streaming changes who owns the footage. Whose is it today? *(Single choice, required)* A TV broadcaster owns them / We keep them ourselves / The venue or federation / Nobody has settled it / Not my area
57. Would you sell tickets to watch the stream rather than the seats? *(Single choice, required)* Yes — money the stadium cannot hold / Yes, if the fans in the crowd get most of the value / Only if it does not hurt ticket sales / No — that is the broadcaster's business / I need to see what it earns first
58. What would make an organiser say no? *(Paragraph, optional)*
59. What is the biggest gap in your operations today? *(Paragraph, optional)*

**The stream and the big screen** *(everyone)*
60. Does showing fan videos and fan messages on the big screen make the event better? *(Single choice, required)* A lot better / A little better / No difference / Can be a danger — distraction
61. What should the live camera show? *(Checkboxes)* The stand energy — fans, colours, singing / Gates and crowd density, for safety / The pitch or the stage / Fan interviews and reactions / I do not need a live camera
62. Which is the honest truth about this product? *(Single choice, required)* Streaming is the product; safety is what makes it allowed to exist / Safety first; the stream and the fans pay for it / Both, equally / Make it safety only, drop the stream / They are two different products for two different buyers
63. Would you allow adverts from a sponsor on the big screen, if paying for them kept this tool free? *(Single choice, required)* Yes, that is fine / Yes, but keep it small / No, it would make the event feel like an advert / Only if it pays for safety

**What we should do next** *(everyone)*
64. What should we build or fix first? *(Single choice, required)* The live stream — watch from anywhere / Making the fan-video stream dependable and easy to watch / Stream tickets, payment and who gets the money / Making *384# rock solid when the network is dying / Faster SOS, with real response times / The lost-child process, and joining a found person back to their family / The live camera and the big screen at the stadium / Real airtime rewards for voting / A smoother, prettier app / Working when the network dies completely / A clear price and pitch for organisers / Luganda, Swahili and Runyankole support / A separate view for stewards on the ground
65. What should we cut or stop promoting? *(Single choice)* Fan polls / Fan videos on the big screen / Shout-outs / Meeting point finder / The live camera / The stream built from fan videos / Selling stream tickets / Nothing — keep it all
66. What do you think kills this project? *(Checkboxes)* Organisers never adopt it / The network dies at the exact moment it matters / Fans will not install anything / Stadium or government rules / No one pays / Half-built features that fail in public / Nobody trusts an app in an emergency / One bad incident the press blames it for / A TV rights deal blocks the fan stream / Streaming costs too much data for Ugandan fans
67. How likely are you to tell someone about it? *(Rating 0-10, required)*

**Last words** *(everyone)*
68. If you could change one thing about how big events keep you safe, what would it be? *(Paragraph, optional)*
69. Anything else — praise, doubt, a warning, a story from a match. *(Paragraph, optional)*
70. May we contact you about the results or a follow-up question? *(Single choice, required)* Yes / Results only / No
71. Your email or phone *(Short answer, optional)*
