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
`LinkUp AI survey — Attendee` with the answers laid out as a table. Search
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
  part") rather than as a second product. Treat this as a finding, not a complaint — `app_habit`,
  `install_intent` and the "Fans will not install anything" option in `biggest_risk` measure it.
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
- **Each feature is explained where it is judged.** The two streaming scores each carry one grey line of
  microcopy ("Open one link from any country and watch — the main view, plus views filmed by fans in
  different stands"), and the `*384#`, priority-ranking, organiser-page and broadcast-rights questions
  each get a similar one-liner. The organiser-page row carries a four-sentence definition, because the
  first reader could not tell who sits at it. Those lines belong in the Google Forms **Description**
  field, never in the question title.
- **Comprehension is measured, not assumed.** `clarity` plus `unclear_parts` tell you whether the new
  wording works. The two options the first reader picked are still in the list, now phrased as *"What
  the big screen at the stadium would actually show"* and *"Who uses the organiser's page, and what do
  they do with it"* — so you can tell whether the rewrite fixed those two specifically or whether the
  surfaces themselves are unclear. If they are still ticked often, it is a product-shape problem, not a
  copy problem, and the answer is a screenshot on the landing page rather than more words.
- **Branching.** Question 1 (`role`) shows the attendee block, the organiser block, both, or neither.
  A hidden block is *disabled*, so its required fields cannot block submission and half-answered
  blocks cannot enter your data — switch from Attendee to Organiser mid-form and the attendee
  answers disappear from the payload. Nobody sees the whole list: a person now faces **21** questions as
  a friend or judge, **28** as an attendee, **28** as an organiser, and **35** only if they both go to
  events and work them. Of those, **14 / 17 / 19 / 22** are required. The `.note` under the submit
  button says the same thing to the respondent, so nobody braces for a form that is not there.
- **"Pick up to three" is enforced** on `would_use`, because "everything is useful" tells you nothing.
  It now carries the weight the eight 1-5 scores used to carry: three forced choices rank the features
  better than letting someone score everything a 4.
- **Only the two streaming ideas keep a 1-5 score** (`use_stream`, `use_fanmix`), because which of them
  to build first is the genuinely open question. Both sit in the block everyone sees, so a fan in
  Nairobi who has never set foot in Namboole still ranks them.
- **The pivot's three real risks are asked directly**: would remote viewers pay (`stream_pay`,
  `ppv_interest`), does a raw fan-built feed beat the official TV camera (`fan_mix_trust`,
  `stream_blocker`), and whose broadcast rights are you standing on (`stream_rights`).
- **Money is asked directly**: what an organiser pays per event and who should really carry it.
- **Text boxes are always optional**, so nobody abandons the form over a blank they cannot fill.
- **Length is what got cut on 2026-10-20.** Someone who both attends and works events used to face 61
  question groups; it is 35 now, and a plain attendee went from 50 to 28. What went and why:

  | Dropped | Why losing it is safe |
  |---|---|
  | `city` `frequency` `attend_with` `info_source` `seen_app` | Screening and provenance. `role`, `phone` and `data_reality` answer the same thing with more certainty. |
  | `open_trigger` `naming` | `app_habit` + `install_intent` already measure install friction; the MEMOZA-versus-LinkUp-AI name question is an interview question. |
  | `use_sos` `use_group` `use_missing` `use_ussd` `use_polls` `use_shouts` `use_clips` `use_meeting` | Replaced by `would_use` (pick three). |
  | `stream_interest` `stream_device` `stream_latency` | `use_stream`/`use_fanmix` measure demand; latency and device are calls you can make from `stream_blocker` = "my data cost". |
  | `ussd_in_panic` `wall_eagerness` `pay_for_memory` `dealbreaker` `missing_feature` `access_needs` | Already covered by `would_use`, `worries` and the one free-text box that stayed. |
  | `response_time` `staff_burden` `integration` `after_event_report` `organizer_blockers` `biggest_gap` | Organiser depth. Six questions that need a twenty-minute conversation, not a tick box — keep them for the two or three organiser interviews. |
  | `wall_value` `live_cam_use` `positioning` `sponsor_model` `cut_one` | The whole "stream and the big screen" block plus the cut vote. `hook_line`, `fan_mix_trust` and `first_priority` cover that ground from the buyer's side. |

  No `value` key was renamed: the surviving questions keep their exact keys, so responses collected
  before the cut still line up with responses after it. Re-adding a dropped question is a copy-paste
  from `git log -p index.html` if you decide one was load-bearing after all.
- One trade-off to know: nothing is saved until someone presses send, so a form someone abandons
  halfway is lost entirely. That is why the required questions are concentrated early and the free text
  is last — and it is the reason for this cut, not a longer list.

## 4. Reading the results

Emails are titled `LinkUp AI survey — Attendee`, so your inbox is the log. The field
names arrive as readable rows:

| Block | Fields |
|---|---|
| About you | `role` `phone` `data_reality` `app_habit` |
| Description landing | `clarity` `unclear_parts` `hook_line` `install_intent` |
| Watching from anywhere *(everyone)* | `watch_from` `stream_what` `stream_pay` `use_stream` `use_fanmix` `fan_mix_trust` `stream_blocker` |
| Attendee | `incident` `would_use` `ussd_appetite` `trust` `worries` `language` `airtime_pull` |
| Organiser | `reports_today` `console_need` `triage_trust` `willing_to_pay` `payer` `stream_rights` `ppv_interest` |
| Next moves | `first_priority` `biggest_risk` `nps` |
| Last words | `anything` `follow_up` `contact` |

That is 21 rows for a friend, 28 for an attendee or an organiser, 35 for someone who is both.

If you would rather have charts handed to you than assembled, run the same questions through
Google Forms in parallel — the paste-ready list at the bottom matches this form question for question.

## 5. What each answer should decide

| Field | The decision it feeds |
|---|---|
| `data_reality`, `phone` | Whether "no data needed" is the headline or a footnote. If most people run out of bundle mid-event, USSD is the product and the app is the bonus. |
| `app_habit`, `install_intent` | Whether you are fighting install friction. "I never install — give me a code to dial" is a build instruction, not a complaint. Put it next to `install_intent` and you have the reason people install: reward, not safety. |
| `clarity`, `unclear_parts` | Landing page and 60-second pitch wording. Anything under 4 is a copy problem, fixable this week. "Which parts exist today" ticked by more than a couple of people means the honest-built line needs to move up, not down. |
| `hook_line` | The headline. Whichever line wins becomes sentence one of the landing page and the pitch deck. If "None landed" is common, the hook is wrong and nothing below it matters. |
| `watch_from` | Splits your sample into crowd, home viewers and fans abroad. The abroad answers are the whole point of the pivot — they are the viewers the fan clips would be filmed for. |
| `use_stream`, `use_fanmix` | Whether the hero feature has demand at all, and which half of it. `use_stream` high and `use_fanmix` low means people want the event on their phone, not an amateur cut — build the feed before the stitching. |
| `stream_what` | What to stream first: full match, highlights, or pure stand atmosphere. |
| `stream_pay` | The diaspora price line. "1-3 USD per match" from viewers abroad is a different business from "free only" in Kampala. |
| `fan_mix_trust`, `stream_blocker` | Whether the raw fan-built broadcast is the selling point or the excuse people will not watch. `stream_blocker` = "my data cost" is a product constraint, not a complaint — it sets your target bitrate. |
| `stream_rights`, `ppv_interest` | Whether you can sell streams at all. "A TV broadcaster owns them" plus "no, that is their business" means the pitch to organisers is safety and engagement, and streaming is sold to the rights holder instead. |
| `incident` | Which scenario to lead the demo with, and which one real people actually lived through. |
| `would_use` (max 3) | What to polish first, ranked by what people would trade other features for. Features nobody picks are candidates to cut from the pitch. |
| `ussd_appetite` | Whether *384# is the primary route or a fallback. Decides how short the menu has to be. |
| `trust` | Whether the real barrier is human behaviour, not features. Low scores mean steward and stadium integration matters more than UI. |
| `worries` | Your privacy and trust page, in their words. Also tells you whether to add a "we delete your location" line. |
| `language` | Whether localisation is a nice-to-have or the top adoption lever. |
| `airtime_pull` | The FanPulse story. "Feels like a gimmick" versus "a big reason to use it" changes the reward design. |
| `reports_today` | What you are replacing. If WhatsApp is the reality, the organiser's page must mirror it, not fight it. |
| `console_need` | Which part of the organiser's page to make excellent and which to leave rough. If "choosing which camera the stream shows" or "selling tickets to the stream" wins, the page is being bought as a production tool, not a safety tool — that changes what you demo first. |
| `triage_trust` | How to present the priority ranking: as a decision-maker or as a suggestion with a human override. |
| `willing_to_pay`, `payer` | B2B, sponsor-funded, or ticket-included. "A sponsor should pay" is a real answer and changes the pitch deck. |
| `first_priority` | Next sprint, decided by vote instead of gut feel. The three streaming options sit at the top of the list on purpose — if they lose to "making `*384#` rock solid", the pivot is the wrong call and the safety story leads. |
| `biggest_risk` | The risks section of your pitch, written by outsiders rather than by you. |
| `nps` | Traction evidence: "average 8.2 from 20 respondents" is a sentence for the deck. |
| `anything` | Quotes. A real attendee sentence lands harder with judges than a feature list. |
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

Same 35 questions, same order, in the order they appear in `index.html`. Use section breaks between
blocks; the attendee/organiser split becomes two sections people skip themselves, or a Google Forms
branch on question 1. The "Watching from anywhere" block stays with everyone — fans watching from other
countries answer it and nothing else, so do not hide it behind the branch. In the HTML the two streaming
ratings (12 and 13) sit under one heading, "Rate the two streaming ideas"; in Forms they are two
linear-scale questions, which is why the numbering here is 35 and the progress bar counts 33.

Every question below lists only the **option text a respondent reads**. The plain-English
explainer lines (`What the organiser's page is`, the `<small>` text under each 1-5 rating, the
`Example:` hints) go in the Google Forms **Description** field of the same question — they are what
stopped people from giving up on this survey last time, so do not drop them. The words in
`value="…"` inside `index.html` are your analysis keys and never change; the option text does.

**About you**
1. Which best describes you? *(Single choice, required)* I attend big events — matches, concerts, festivals / I organise, steward or run safety at events / Both — I go to events and I work them / Neither — a friend, judge or just curious
2. What do you carry to an event? *(Single choice, required)* Android smartphone / iPhone / Basic phone only / One of each
3. What is your data bundle situation at a big event? *(Single choice, required)* I always have data that works / It runs out during the event / I turn data off to save money / The network is too jammed to use anyway / I have no data at all
4. How do you behave about event apps? *(Single choice, required)* I install whatever the event asks / Only if it clearly pays me or protects me / I never install — give me a code to dial / I download it and never open it

**Did that description land?**
5. After reading the description above, how clear is it what this does? *(Linear scale 1-5, required)* 1 = Still confusing, 5 = Crystal clear
6. Which parts still do not make sense? *(Checkboxes)* Why this needs to exist at all / How a tap on SOS actually reaches a steward / How *384# can work when there is no internet / What the big screen at the stadium would actually show / Who uses the organiser's page, and what do they do with it / How the airtime actually reaches my phone / How the system decides a report is high priority / How a fan's phone video ends up in the stream / Which parts exist today and which are still an idea / Nothing — it was clear
7. Which line made you want to keep reading? *(Single choice, required)* "Watch from anywhere" — the stream you open on any screen / "The fans make it" — the people inside are the camera crew / "The safety part" — works on *384# with no internet / The safety side — SOS, group PINs, lost children / None of them landed
8. Would you have it on your phone at the next big event? *(Single choice, required)* Definitely / Probably / Only if required / No / Not sure

**Watching from anywhere** *(everyone — this is the streaming block)*
9. Where would you be when a big event is on? *(Single choice, required)* In the stadium, in the crowd / At home in Uganda / Abroad — I follow Uganda from a distance / I do not really follow live events
10. What would you actually watch? *(Checkboxes)* The whole match or festival, live / Goals, highlights and key moments only / The crowd and the atmosphere in the stands, filmed by fans / Backstage and set content / Pre and post-event talk / Nothing — I only watch in person
11. Would you pay to watch? *(Single choice, required)* Only if it is free / UGX 2,000-5,000 for one event / UGX 10,000 or more / 1-3 USD per match, from abroad / A monthly subscription
12. A live stream room for big events — how much do you want it? *(Linear scale 1-5)* Description: Open one link from any country and watch — the main view, plus views filmed by fans in different stands
13. A stream built from fan clips — how much do you want it? *(Linear scale 1-5)* Description: Short videos from fans in every stand, played one after another as a single live stream for people watching from far away
14. A stream made from fans' phone videos instead of a proper TV camera — honest reaction? *(Single choice, required)* That is the point, I want the real feel / Fine, as long as it stays easy to watch / I would rather have the TV camera / Not interested either way
15. What would stop you from watching a stream like this? *(Single choice, required)* My data cost / It is not the full match / Poor video quality / No commentary in my language / I watch with people, on TV / Nothing — I would watch

**Your experience as an attendee** *(attendees and "Both")*
16. At a big event, which of these has happened to you? *(Checkboxes)* Separated from my people and could not reconnect / Lost track of a child / A medical emergency right next to me / A crush or stampede scare / My phone or the network completely failed / Fighting, theft or harassment / None of these
17. Which of these would you really use? *(Checkboxes, up to three — enforce the cap in Forms)* One-tap SOS to organisers and on-duty security / A group list so I know the people I came with are safe / Reporting a lost child or lost friend by name and where they were last seen / Doing all of it on *384# with no data / Live polls that pay airtime / Putting my message or my video on the big screen at the stadium / Finding the nearest safe meeting point / Sending a video of my stand, and getting the day back as one edited video / Watching the match live on my phone when I am not at the ground / My clip going into the stream the whole country is watching
18. Would you genuinely dial *384# for safety instead of an app? *(Single choice, required)* Yes, definitely / Probably / Not sure / No — Description: a torchlight phone dials *384# and joins the same group as everyone else's smartphones — same alert, same meeting point, no data needed
19. Would you report an emergency through the app instead of to a person? *(Linear scale 1-5, required)* 1 = Never, 5 = Always — Description: you report a lost child with a name and where he was last seen; everyone in your group is told the moment you send it
20. What would you worry about? *(Checkboxes)* My location being tracked / My number being stored and sold / The crowd or screen knowing I raised an alert / Getting flooded with SMS after / Battery dying because the app runs / It eating my bundle / Someone abusing it as a prank / None of these worry me
21. Which language would you use it in? *(Single choice)* English / Luganda / Both / Swahili / Runyankole/Rukiga / Another — say below
22. Does airtime for voting bring you back, or is it noise? *(Single choice, required)* A big reason to use it / A nice bonus / Does not interest me / Feels like a gimmick

**Your work as an organiser or steward** *(organisers and "Both")*
23. How do incident reports reach you today? *(Single choice, required)* Radio / WhatsApp / Phone calls / Runners / Nothing formal
24. Which part of the organiser's page would help you the most during an event? *(Checkboxes)* Reports sorted by how serious they are (medical / security / fire / crush) / One lost-person list the whole team can see and update / Putting a steward's phone camera on the big screen / Setting up an event quickly and getting its join code / Picking which fan videos play on the big screen / Live poll results and how the crowd is feeling / One safety number for the whole event, going up or down as reports come in / Which groups have checked in, and which are still missing people / Choosing which camera the stream shows, like a TV director / Selling tickets to the stream and seeing who bought — Description: the organiser's page is one website open on a laptop at the stadium. Every SOS and lost-person report lands there, so the team sees which one to send help to first. The organiser also creates the event and its join code there, chooses which fan videos play on the big screen, and can put a steward's phone camera onto that screen.
25. Would you let the system decide how serious a report is? *(Single choice, required)* Yes, if a human can override it / Only for sorting the small stuff / No — staff must decide / Only if every decision is logged — Description: someone reports a crowd crush, the system marks it medical and high priority straight away, so the team sends help to the worst report first instead of the oldest one
26. Would an organiser pay for this per event? *(Single choice, required)* Only if free / Up to UGX 100k / UGX 100k-500k / More, for a stadium / A sponsor should pay
27. Who should actually pay? *(Single choice)* The organiser / The venue / A sponsor / A telco / Federation or government / Baked into the ticket
28. Streaming changes who owns the footage. Whose is it today? *(Single choice, required)* A TV broadcaster owns them / We keep them ourselves / The venue or federation / Nobody has settled it / Not my area — Description: if a TV broadcaster already holds the rights to your event, a fan stream can collide with that deal
29. Would you sell tickets to watch the stream rather than the seats? *(Single choice, required)* Yes — money the stadium cannot hold / Yes, if the fans in the crowd get most of the value / Only if it does not hurt ticket sales / No — that is the broadcaster's business / I need to see what it earns first

**What we should do next** *(everyone)*
30. What should we build or fix first? *(Single choice, required)* The live stream — watch from anywhere / Making the fan-video stream dependable and easy to watch / Stream tickets, payment and who gets the money / Making *384# rock solid when the network is dying / Faster SOS, with real response times / The lost-child process, and joining a found person back to their family / The live camera and the big screen at the stadium / Real airtime rewards for voting / A smoother, prettier app / Working when the network dies completely / A clear price and pitch for organisers / Luganda, Swahili and Runyankole support / A separate view for stewards on the ground
31. What do you think kills this project? *(Checkboxes)* Organisers never adopt it / The network dies at the exact moment it matters / Fans will not install anything / Stadium or government rules / No one pays / Half-built features that fail in public / Nobody trusts an app in an emergency / One bad incident the press blames it for / A TV rights deal blocks the fan stream / Streaming costs too much data for Ugandan fans — Description: tick everything that feels like a real risk
32. How likely are you to tell someone about it? *(Rating 0-10, required)* 0 = Not likely, 10 = Very likely

**Last words** *(everyone)*
33. Anything else — praise, doubt, a warning, a story from a match. *(Paragraph, optional)*
34. May we contact you to show you the results or ask a follow-up question? *(Single choice, required)* Yes / Results only / No
35. Your email or phone *(Short answer, optional)* — Description: only if you said yes above. Leave blank to stay anonymous.
