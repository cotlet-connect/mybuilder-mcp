# MyBuilder API integration and MCP server: connect MyBuilder to ChatGPT, Claude and any AI agent

**MyBuilder API integration and MCP server by Cotlet.dev: yes, Claude, ChatGPT and other AI agents can work in your MyBuilder account.** MyBuilder has no public API for tradespeople and no official MCP server. Cotlet.dev connects your own signed-in MyBuilder account to any AI agent, so the AI sorts your job leads, drafts replies and messages customers for you.

## What you can ask

> List today's MyBuilder leads that match my trade and draft an introduction for each. Send nothing yet.

> Which homeowners shortlisted me this week and have not replied? Draft a message to each.

> How much did I pay in shortlist fees this month, and how many jobs did I win?

> I had a cancellation next week. Show me MyBuilder leads near me I could start in 7 days.

> List finished jobs with no review yet and draft a review request for each.

## What tradespeople ask their AI to do in MyBuilder

From tradespeople' posts on Reddit, Facebook groups, review sites and the MyBuilder help center (checked 25 Nov 2026).

| Job | Ask your AI | Make it repeat |
|---|---|---|
| Answer new leads | "List today's MyBuilder leads that match my trade and draft an introduction for each. Send nothing yet." | When a lead comes in |
| Follow up shortlists | "Which homeowners shortlisted me this week and have not replied? Draft a message to each." | Daily |
| Shortlist fees against wins | "Add up my shortlist fees this month and how many jobs I won from them." | Monthly |
| Fill a gap in the diary | "I had a cancellation next week. Show me leads near me I could start in the next 7 days." | When a job cancels |
| Ask for reviews | "List jobs I finished this month with no review yet and draft a review request for each." | Monthly |
| Reply to reviews | "Show new reviews and draft a reply to each for me to approve." | Weekly |

Your AI asks you before it sends, buys, replies or changes anything. Payments stay in your hands.

## MyBuilder API

MyBuilder has no API, webhooks or data export for a tradesperson's own account (checked 25 Nov 2026). With Cotlet.dev you build your own MyBuilder API: show your AI agent a job once in your signed-in MyBuilder account, save it as an endpoint and call it from n8n, Make, Zapier or your own code. How it works: https://cotlet.dev/integrations/api/mybuilder

Endpoints you can build in a few minutes (none of them exists until you build it):

- `new-leads`: Time, job, area and shortlist fee of each lead
- `shortlists`: Homeowner, job, contact details and fee of each shortlist
- `unanswered-chats`: Homeowner, job and last message
- `reviews`: Job, rating and text of each new review
- `shortlist-fees`: Fees paid and jobs won for the period you name
- `express-interest`: Your introduction on a lead, sent after your OK

Call one with your Cotlet API key: `POST https://cotlet.dev/api/v1/custom/new-leads`. One call runs up to 90 seconds, writes wait for your OK, payments are blocked.

## Connect in 2 minutes

Get your API key at https://cotlet.dev/app and export it as `COTLET_API_KEY`.

### Claude Code

```bash
claude mcp add --transport http cotlet https://cotlet.dev/mcp/v1 --header "Authorization: Bearer $COTLET_API_KEY"
```

### ChatGPT

Settings → Apps → add connector URL `https://cotlet.dev/mcp/v1`, then sign in with Cotlet.dev. No API key needed.

### Cursor

Copy `.cursor/mcp.json` from this repo into your project:

```json
{
  "mcpServers": {
    "cotlet": {
      "url": "https://cotlet.dev/mcp/v1",
      "headers": {
        "Authorization": "Bearer ${COTLET_API_KEY}"
      }
    }
  }
}
```

## Run it locally (stdio)

This repo is a real MCP server. It speaks MCP over stdio, lists its MyBuilder tools without any key, and forwards every tool call to the hosted Cotlet.dev server. The hosted URL above stays the recommended way. Use this when your client only starts local servers.

```json
{"mcpServers":{"mybuilder":{"command":"uvx","args":["mybuilder-mcp"],"env":{"COTLET_API_KEY":"your-key"}}}}
```

Or with Docker:

```bash
docker build -t mybuilder-mcp .
docker run -i --rm -e COTLET_API_KEY mybuilder-mcp
```

Tools: `mybuilder_guide`, `mybuilder_status`, `mybuilder_open`, `mybuilder_read`, `mybuilder_click`, `mybuilder_type`, `mybuilder_close`. Without `COTLET_API_KEY` a tool call answers: "Set COTLET_API_KEY. Get a key at https://cotlet.dev/app".

## Command line

Python 3.10+, nothing else to install. From PyPI:

```bash
pip install mybuilder-mcp        # or run it once: uvx mybuilder-mcp connect
mybuilder-mcp connect
```

Or straight from this repo:

```bash
export COTLET_API_KEY="your-key"
python3 mybuilder_agent.py connect   # ready config for Claude Code, Cursor, ChatGPT
python3 mybuilder_agent.py status    # is MyBuilder ready in your Cotlet.dev account
python3 mybuilder_agent.py guide     # MyBuilder screens and sign-in link
python3 mybuilder_agent.py read      # open MyBuilder, read the page, close the browser
python3 mybuilder_agent.py tools     # list Cotlet.dev tools
python3 mybuilder_agent.py serve     # start the stdio MCP server (same as no argument)
```

## What you can ask your AI to do in MyBuilder

These are real jobs MyBuilder users asked about. Cotlet lets ChatGPT, Claude or any MCP client do them in your own signed-in MyBuilder account.

### Leads and shortlists

#### Can ChatGPT or Claude answer new leads in MyBuilder for me?

Yes. With Cotlet, ChatGPT or Claude reads the new leads on your signed-in MyBuilder Leads page, keeps the ones that match your trade and area and drafts an introduction for each. Nothing is sent until you say yes.

Ask your agent: "List today's MyBuilder leads that match my trade and draft an introduction for each. Send nothing yet."

As an API endpoint: Yes. Save the lead check as an endpoint in Cotlet and call it every hour from n8n, Make or Zapier to get matching leads by SMS or Slack.

#### Can ChatGPT or Claude follow up shortlists in MyBuilder for me?

Yes. With Cotlet, your agent reads your Shortlisted page in MyBuilder, finds homeowners who shortlisted you and have not replied and drafts a message to each. Nothing is sent until you say yes.

Ask your agent: "Which homeowners shortlisted me in MyBuilder this week and have not replied? Draft a message to each."

As an API endpoint: Yes. Save the shortlist check as an endpoint in Cotlet and call it every morning from n8n, Make or Zapier to get the quiet homeowners by email.

#### Can ChatGPT or Claude fill a gap in my diary using MyBuilder leads?

Yes. With Cotlet, your agent reads the leads near you in your signed-in MyBuilder account and lists the ones you could start in the next 7 days, with the area and shortlist fee. Nothing is sent until you say yes.

Ask your agent: "I had a cancellation next week. Show me MyBuilder leads near me that I could start in the next 7 days."

As an API endpoint: Yes. Save the lead search as an endpoint in Cotlet and call it from n8n, Make or a script whenever a job cancels in your calendar.

### Fees and costs

#### Can ChatGPT or Claude add up my MyBuilder shortlist fees against the jobs I won?

Yes. MyBuilder says you only pay a fee when both you and the homeowner are interested, and it has no export of your fees. With Cotlet, your agent reads your shortlists and billing in your account and adds up the fees and the jobs you won this month.

Ask your agent: "Add up my MyBuilder shortlist fees this month and how many jobs I won from them."

As an API endpoint: Yes. Save the fee total as an endpoint in Cotlet and call it on the first of each month from n8n, Make or Zapier to fill a Google Sheet.

### Reviews

#### Can ChatGPT or Claude ask my customers for reviews in MyBuilder?

Yes. With Cotlet, your agent lists the jobs you finished this month in your MyBuilder account that have no review yet and drafts a review request for each customer. Nothing is sent until you say yes.

Ask your agent: "List MyBuilder jobs I finished this month with no review yet and draft a review request for each customer."

As an API endpoint: Yes. Save the unreviewed job list as an endpoint in Cotlet and call it monthly from n8n, Make or Zapier to get the list by email.

#### Can ChatGPT or Claude reply to my reviews in MyBuilder?

Yes. MyBuilder's support centre suggests thanking the customer after a positive review. With Cotlet, your agent reads the new reviews on your Profile and drafts a reply to each for you to approve. Nothing changes until you say yes.

Ask your agent: "Show my new MyBuilder reviews and draft a reply to each for me to approve."

As an API endpoint: Yes. Save the new-review read as an endpoint in Cotlet and call it every week from n8n, Make or Zapier to get the reviews and draft replies by email.

## Questions from MyBuilder users

**Does MyBuilder have an API?**

MyBuilder has no public API for tradespeople (checked 25 Nov 2026). Leads, shortlists and chats live in the MyBuilder for Trades app and on the site. Cotlet.dev works in your own signed-in MyBuilder account, so your AI agent can read job leads and message customers.

**How do I connect MyBuilder to ChatGPT?**

Add Cotlet.dev as an app in ChatGPT (Settings → Apps, connector URL `https://cotlet.dev/mcp/v1`), sign in to MyBuilder once in your Cotlet.dev Cloud Browser, and ask ChatGPT in plain words. No code and no MyBuilder API key.

**Is there a way to use Claude with MyBuilder?**

Yes. In Claude Code run the one-line `claude mcp add` command above; in the Claude app add `https://cotlet.dev/mcp/v1` as a custom connector. Claude then works in your own MyBuilder account.

**Is there a MyBuilder MCP server?**

MyBuilder has no official MCP server. Cotlet.dev is a hosted MCP server that works in MyBuilder for you, 24/7, with Claude, ChatGPT, Cursor, Codex, n8n, Make and Zapier.

**Can AI follow up MyBuilder shortlists?**

Yes. Your AI lists homeowners who shortlisted you and went quiet, drafts a message to each and sends it in your MyBuilder chats after your OK.

**How do I track what MyBuilder costs me?**

MyBuilder has no export of your fees (checked 25 Nov 2026). Your AI reads your shortlists and billing in your account and sums fees and jobs won for the month.

## Find it on

- [MyBuilder on Cotlet.dev](https://cotlet.dev/integrations/mcp/mybuilder?utm_source=github&utm_medium=readme&utm_campaign=mybuilder&utm_content=find)
- [PyPI package mybuilder-mcp](https://pypi.org/project/mybuilder-mcp/)
- [Odel](https://odel.app/modules/cotlet/mybuilder)
- [mcpbeat](https://mcpbeat.com/mcp-servers/cotlet/mybuilder/)
- [Step-by-step guide (PDF) on Internet Archive](https://archive.org/details/cotlet-connect-mybuilder-to-ai-agents-guide)

## Also works with

- [MyJobQuote MCP](https://github.com/cotlet-connect/myjobquote-mcp)
- [Mangomint MCP](https://github.com/cotlet-connect/mangomint-mcp)
- [Wanderlog MCP](https://github.com/cotlet-connect/wanderlog-mcp)
- [SmugMug MCP](https://github.com/cotlet-connect/smugmug-mcp)
- [Garmin Connect MCP](https://github.com/cotlet-connect/garmin-mcp)
- [Housecall Pro MCP](https://github.com/cotlet-connect/housecall-pro-mcp)
- [Medium MCP](https://github.com/cotlet-connect/medium-mcp)
- [Crexi MCP](https://github.com/cotlet-connect/crexi-mcp)
- [DistroKid MCP](https://github.com/cotlet-connect/distrokid-mcp)
- [LinkedIn MCP](https://github.com/cotlet-connect/linkedin-mcp)
- [EstimateOne MCP](https://github.com/cotlet-connect/estimateone-mcp)
- [Google Search Console MCP](https://github.com/cotlet-connect/google-search-console-mcp)
- [Lichess MCP](https://github.com/cotlet-connect/lichess-mcp)
- [Strava MCP](https://github.com/cotlet-connect/strava-mcp)
- [StreamElements MCP](https://github.com/cotlet-connect/streamelements-mcp)
- [Meetup MCP](https://github.com/cotlet-connect/meetup-mcp)
- [AccuRanker MCP](https://github.com/cotlet-connect/accuranker-mcp)
- [GetYourGuide MCP](https://github.com/cotlet-connect/getyourguide-mcp)
- [Idealista MCP](https://github.com/cotlet-connect/idealista-mcp)
- [Riverside MCP](https://github.com/cotlet-connect/riverside-mcp)
- [Udemy MCP](https://github.com/cotlet-connect/udemy-mcp)
- [Faire MCP](https://github.com/cotlet-connect/faire-mcp)
- [Teachable MCP](https://github.com/cotlet-connect/teachable-mcp)
- [Vagaro MCP](https://github.com/cotlet-connect/vagaro-mcp)
- [Buildertrend MCP](https://github.com/cotlet-connect/buildertrend-mcp)
- [Zoopla MCP](https://github.com/cotlet-connect/zoopla-mcp)
- [Dribbble MCP](https://github.com/cotlet-connect/dribbble-mcp)
- [Hostelworld MCP](https://github.com/cotlet-connect/hostelworld-mcp)
- [Mashvisor MCP](https://github.com/cotlet-connect/mashvisor-mcp)
- [Rent Manager MCP](https://github.com/cotlet-connect/rent-manager-mcp)
- [ThriveCart MCP](https://github.com/cotlet-connect/thrivecart-mcp)
- [Fresha MCP](https://github.com/cotlet-connect/fresha-mcp)
- [Booksy MCP](https://github.com/cotlet-connect/booksy-mcp)
- [BookingKoala MCP](https://github.com/cotlet-connect/bookingkoala-mcp)
- [Milanote MCP](https://github.com/cotlet-connect/milanote-mcp)
- [Partiful MCP](https://github.com/cotlet-connect/partiful-mcp)
- [Contractor Foreman MCP](https://github.com/cotlet-connect/contractor-foreman-mcp)
- [Pixieset MCP](https://github.com/cotlet-connect/pixieset-mcp)
- [GlossGenius MCP](https://github.com/cotlet-connect/glossgenius-mcp)
- [ZenMaid MCP](https://github.com/cotlet-connect/zenmaid-mcp)
- [AroFlo MCP](https://github.com/cotlet-connect/aroflo-mcp)
- [Simpro MCP](https://github.com/cotlet-connect/simpro-mcp)
- [Workiz MCP](https://github.com/cotlet-connect/workiz-mcp)
- [Kickserv MCP](https://github.com/cotlet-connect/kickserv-mcp)
- [Service Autopilot MCP](https://github.com/cotlet-connect/service-autopilot-mcp)
- [Mindbody MCP](https://github.com/cotlet-connect/mindbody-mcp)
- [DaySmart MCP](https://github.com/cotlet-connect/daysmart-mcp)
- [Acuity Scheduling MCP](https://github.com/cotlet-connect/acuity-scheduling-mcp)
- [Jane App MCP](https://github.com/cotlet-connect/jane-app-mcp)
- [FieldEdge MCP](https://github.com/cotlet-connect/fieldedge-mcp)
- [Phorest MCP](https://github.com/cotlet-connect/phorest-mcp)
- [DINGG MCP](https://github.com/cotlet-connect/dingg-mcp)
- [Invoice Simple MCP](https://github.com/cotlet-connect/invoice-simple-mcp)
- [PhotoShelter MCP](https://github.com/cotlet-connect/photoshelter-mcp)
- [RazorSync MCP](https://github.com/cotlet-connect/razorsync-mcp)
- [ShootProof MCP](https://github.com/cotlet-connect/shootproof-mcp)
- [Invoice2go MCP](https://github.com/cotlet-connect/invoice2go-mcp)
- [Appointy MCP](https://github.com/cotlet-connect/appointy-mcp)
- [Contractor+ MCP](https://github.com/cotlet-connect/contractor-plus-mcp)
- [Petpooja MCP](https://github.com/cotlet-connect/petpooja-mcp)
- [Teachers Pay Teachers MCP](https://github.com/cotlet-connect/teachers-pay-teachers-mcp)
- [Bloom MCP](https://github.com/cotlet-connect/bloom-mcp)
- [hipages MCP](https://github.com/cotlet-connect/hipages-mcp)
- [ServiceSeeking MCP](https://github.com/cotlet-connect/serviceseeking-mcp)
- [Yelp for Business MCP](https://github.com/cotlet-connect/yelp-for-business-mcp)
- [Rosie MCP](https://github.com/cotlet-connect/rosie-mcp)
- [Roofr MCP](https://github.com/cotlet-connect/roofr-mcp)
- [FieldPulse MCP](https://github.com/cotlet-connect/fieldpulse-mcp)
- [YourTradebase MCP](https://github.com/cotlet-connect/yourtradebase-mcp)
- [JobNimbus MCP](https://github.com/cotlet-connect/jobnimbus-mcp)
- [Ascora MCP](https://github.com/cotlet-connect/ascora-mcp)
- [eLocal MCP](https://github.com/cotlet-connect/elocal-mcp)
- [All apps connected through Cotlet.dev](https://github.com/cotlet-connect)

---

More: https://cotlet.dev/integrations/mcp/mybuilder?utm_source=github&utm_medium=readme&utm_campaign=mybuilder

Start free: https://cotlet.dev/signup?app=mybuilder&utm_source=github&utm_medium=readme&utm_campaign=mybuilder
