# LinkUp AI feedback survey — setup and sharing

A standalone survey for attendees, organisers and friends. It lives **outside** the `linkup-ai/`
app folder on purpose: no backend changes, no database, no models, no extra surface for judges to
confuse with `/console/`. One HTML file plus a free email collector, so you can share it today
whether or not the app is hosted.

```
survey/
  index.html   the whole survey — product description, branching questions, styling, submit logic
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

### Check these five before sending it out
- Open the link on your **phone**, not only the laptop — almost everyone will tap it in WhatsApp.
- Submit a real test response; confirm the email arrives with all your answers, formatted.
- Pick "Attendee", then "Organizer", then "Both" and confirm the blocks swap.
- Tick four boxes in "Which would you really use?" and confirm the fourth is refused.
- Confirm the thank-you screen replaces the form.

## 3. How the survey is built

- **It teaches before it asks.** The first screen explains the problem (Namboole, Blankets & Wine,
  the network dying, the child missing, the crush at Gate C), then walks a real day: Grace checking
  in with `CRANES2024`, her uncle dialing `*384#`, a medical SOS triaged to High priority, the
  organiser resolving it from the console, airtime for voting, the clip wall, a stand camera going
  live, and the missing child marked reunited. Then the four parts. Someone who has never heard of
  the project can answer intelligently.
- **Comprehension is measured, not assumed.** `clarity` plus `unclear_parts` tell you whether the
  pitch wording works — that is a copywriting fix, and cheap to make.
- **Branching.** Question 1 (`role`) shows the attendee block, the organiser block, both, or neither.
  A hidden block is *disabled*, so its required fields cannot block submission and half-answered
  blocks cannot enter your data — switch from Attendee to Organiser mid-form and the attendee
  answers disappear from the payload. Nobody sees the whole list: the question groups a person
  actually faces is **21** for a friend or judge, **30** for an organiser, **39** for an attendee,
  **48** if they do both. Roughly 26 of those are required; every text box is optional.
- **"Pick up to three" is enforced** on `would_use`, because "everything is useful" tells you nothing.
- **Every core feature gets its own 1-5 score** (`use_sos`, `use_group`, `use_missing`, `use_ussd`,
  `use_polls`, `use_shouts`, `use_clips`, `use_meeting`) so you get a ranked feature list instead of
  one vague "do you like it".
- **Money and adoption are asked directly**: price per event, who should pay, willingness to pay for
  the memory video, sponsor slots on the wall, and what would make an organiser say no.
- **Text boxes are always optional**, so nobody abandons the form over a blank they cannot fill.
- One trade-off to know: nothing is saved until someone presses send. A long form means some people
  start and never finish, and those partials are lost. That is why the required questions are
  concentrated early and the free text is last.

## 4. Reading the results

Emails are titled `LinkUp AI survey — Attendee (Kampala)`, so your inbox is the log. The field
names arrive as readable rows:

| Block | Fields |
|---|---|
| About you | `role` `city` `phone` `frequency` `attend_with` `data_reality` `app_habit` `info_source` |
| Description landing | `seen_app` `clarity` `unclear_parts` `install_intent` `open_trigger` `naming` |
| Attendee | `incident` `would_use` `use_sos` `use_group` `use_missing` `use_ussd` `use_polls` `use_shouts` `use_clips` `use_meeting` `ussd_appetite` `ussd_in_panic` `trust` `worries` `language` `airtime_pull` `wall_eagerness` `pay_for_memory` `dealbreaker` `missing_feature` `access_needs` |
| Organiser | `reports_today` `response_time` `console_need` `triage_trust` `staff_burden` `integration` `after_event_report` `willing_to_pay` `payer` `organizer_blockers` `biggest_gap` |
| Screen, fans, survival | `wall_value` `live_cam_use` `positioning` `sponsor_model` |
| Next moves | `first_priority` `cut_one` `biggest_risk` `nps` |
| Last words | `improve` `anything` `follow_up` `contact` |

If you would rather have charts handed to you than assembled, run the same questions through
Google Forms in parallel — the paste-ready list at the bottom matches this form question for question.

## 5. What each answer should decide

| Field | The decision it feeds |
|---|---|
| `data_reality`, `phone` | Whether "no data needed" is the headline or a footnote. If most people run out of bundle mid-event, USSD is the product and the app is the bonus. |
| `app_habit`, `install_intent`, `open_trigger` | Whether you are fighting install friction. "Never install, give me a code to dial" is a build instruction, not a complaint. |
| `clarity`, `unclear_parts`, `naming` | Landing page and 60-second pitch wording. Anything under 4 is a copy problem, fixable this week. |
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
| `console_need` | Which `/console/` tab to make excellent and which to leave rough. |
| `triage_trust` | How to present AI triage: as a decision-maker or as a suggestion with a human override. |
| `staff_burden`, `organizer_blockers` | How much deployment friction the product can carry, and what to remove to fit inside it. |
| `integration`, `after_event_report` | The enterprise features that make an organiser sign, and the report that makes the product worth renewing. |
| `willing_to_pay`, `payer` | B2B, sponsor-funded, or ticket-included. "A sponsor should pay" is a real answer and changes the pitch deck. |
| `wall_value`, `live_cam_use`, `positioning` | Whether the entertainment side is a feature or a liability, and where the staff camera should point. |
| `first_priority` | Next sprint, decided by vote instead of gut feel. |
| `cut_one` | What to stop promoting. Reduction is usually the credible move in a hackathon demo. |
| `biggest_risk` | The risks section of your pitch, written by outsiders rather than by you. |
| `nps` | Traction evidence: "average 8.2 from 20 respondents" is a sentence for the deck. |
| `improve`, `anything` | Quotes. A real attendee sentence lands harder with judges than a feature list. |
| `follow_up`, `contact` | Who to call back for the deep interview — and your warm leads for a pilot venue. |

## 6. Getting answers worth having

- Aim for 20+ responses, and make sure at least 4 are not your friends.
- Send it to two or three organisers **personally** — they are your small sample and each answer
  matters more than any attendee's.
- Ask one person who uses a torchlight phone only. Their answers are the ones your USSD flow needs.
- Post the link, not a screenshot of the questions — you want answers, not opinions about answers.
- When you report back, say what you changed because of it. That is what makes the next round of
  people answer honestly.

---

## Google Forms paste-ready version

Same questions, same order. Use section breaks between blocks; the attendee/organiser split becomes
two sections people skip themselves, or a Google Forms branch on question 1.

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
11. Which parts still do not make sense? *(Checkboxes)* Why this needs to exist / How an SOS reaches a steward / How *384# works without internet / What the stadium screen shows / Who sits at the console / How airtime gets paid / How AI decides priority / Nothing, it was clear
12. Would you have it on your phone at the next big event? *(Single choice, required)* Definitely / Probably / Only if required / No / Not sure
13. When would you actually open it? *(Single choice)* Before I leave home / At the gate on check-in / Only when something goes wrong / For polls and content
14. Does the name make sense? What would you call it? *(Paragraph, optional)*

**Attendee experience** *(attendees and "Both")*
15. At a big event, which of these has happened to you? *(Checkboxes)* Separated from my people / Lost track of a child / Medical emergency next to me / Crush or stampede scare / Phone or network failed / Fighting, theft or harassment / None
16. Which would you really use? *(Checkboxes, limit 3)* One-tap SOS / Safety circle with a PIN / Missing-person report / Doing it all on *384# / Polls with airtime / Shout-outs on the wall / Nearest safe meeting point / Clip upload and memory mix
17-24. Rate each 1-5 *(Linear scale)* One-tap SOS / Safety circle / Missing-person reporting / *384# menu / Live polls with airtime / Wall shout-outs / Clip upload and memory mix / Meeting point finder
25. Would you genuinely dial *384# for safety instead of an app? *(Single choice, required)* Yes definitely / Probably / Not sure / No
26. In a real emergency, would you stop and dial a menu, or just shout? *(Single choice)* I would dial, it is faster / One tap in the app / I would shout and wave / Walk to the nearest security person
27. Would you report an emergency through the app instead of to a person? *(Linear scale 1-5, required)*
28. What would you worry about? *(Checkboxes)* Location tracking / Number stored and sold / The crowd knowing I raised an alert / Spam SMS afterwards / Battery / Data cost / Prank misuse / None
29. Which language would you use it in? *(Single choice)* English / Luganda / Both / Swahili / Runyankole-Rukiga / Another
30. Does airtime for voting bring you back, or is it noise? *(Single choice, required)* Big reason / Nice bonus / Does not interest me / Feels like a gimmick
31. Would you put your own message or video on the stadium wall? *(Single choice)* Yes always / Only if friends would see it / No, too embarrassing / Never
32. Would you pay for the finished video of your day? *(Single choice, required)* Yes UGX 2,000-5,000 / Yes around UGX 10,000 / Only if free / No interest
33. What would stop you from using an event app like this? *(Paragraph, optional)*
34. What is missing that you would want? *(Paragraph, optional)*
35. Anything that would make it hard for you, or someone you look after, to use it? *(Paragraph, optional)*

**Organiser experience** *(organisers and "Both")*
36. How do incident reports reach you today? *(Single choice, required)* Radio / WhatsApp / Phone calls / Runners / Nothing formal
37. How long does a report take to reach someone who can act? *(Single choice)* Under 2 min / 2-10 min / 10-30 min / Over 30 min / Never reliably
38. Which console feature would save you the most? *(Checkboxes)* Prioritised alerts / Shared missing-person board / Staff camera to the wall / Quick event setup with join codes / Crowd clip wall / Live poll results / Event health score / Which safety circles are complete
39. Would you trust a model to set incident priority? *(Single choice, required)* Yes with human override / Only for small stuff / No, staff must decide / Only if every decision is logged
40. How much change could your team absorb before an event? *(Single choice, required)* A steward app is fine / Under 30 min training / Only if it replaces radio / None, we cannot add tools
41. What would you want it connected to? *(Checkboxes)* CCTV / PA / Ticketing or gate scanning / Steward roster / Stadium radio / Ambulance-police channels / Nothing yet
42. Which after-event report would you want? *(Checkboxes)* Incident timeline with response times / Missing-person reconciliation / Sentiment and poll data / Clip library / Zone and gate counts / Evidence for insurers or police
43. Would an organiser pay for this per event? *(Single choice, required)* Only if free / Up to UGX 100k / UGX 100k-500k / More for a stadium / A sponsor should pay
44. Who should actually pay? *(Single choice)* Organiser / Venue / Sponsor / Telco / Federation or government / Baked into the ticket
45. What would make an organiser say no? *(Paragraph, optional)*
46. What is the biggest gap in your operations today? *(Paragraph, optional)*

**The screen, the fans, and how it survives** *(everyone)*
47. Does a stadium screen showing crowd content make the event better? *(Single choice, required)* A lot / A little / No difference / Can be a danger
48. What should the live camera show? *(Checkboxes)* Stand energy / Gates and crowd density / Pitch or stage / Fan interviews / Not needed
49. Which is the honest truth about this product? *(Single choice, required)* Safety first, fun pays for it / Both equally / Entertainment is the way in / Make it safety only
50. Would you accept sponsor slots on the wall to keep it free? *(Single choice, required)* Yes fine / Yes but keep it small / No, it cheapens it / Only if it pays for safety

**What we should do next** *(everyone)*
51. What should we build or fix first? *(Single choice, required)* Solid *384# on a dying network / Faster SOS with dispatch times / Missing-person workflow / Live camera and wall / Real airtime rewards / Smoother app / Working fully offline / A clear price and pitch for organisers / Luganda, Swahili, Runyankole / A separate steward view
52. What should we cut or stop promoting? *(Single choice)* Fan polls / Clip wall / Shout-outs / Meeting point finder / Live camera / Nothing, keep it all
53. What do you think kills this project? *(Checkboxes)* Organisers never adopt it / Network dies at the worst moment / Fans will not install / Stadium or government rules / Nobody pays / Half-built features failing in public / Nobody trusts an app in an emergency / One bad incident blamed on it
54. How likely are you to tell someone about it? *(Rating 0-10, required)*

**Last words** *(everyone)*
55. If you could change one thing about how big events keep you safe, what would it be? *(Paragraph, optional)*
56. Anything else — praise, doubt, a warning, a story from a match. *(Paragraph, optional)*
57. May we contact you about the results or a follow-up question? *(Single choice, required)* Yes / Results only / No
58. Your email or phone *(Short answer, optional)*
