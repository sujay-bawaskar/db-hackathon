Scene: Release Manager standing at a desk surrounded by sticky notes or fake printouts. Basically Looking stressed.

**Dev:** Morning! You look like you've been debugging production since Friday.
**Release Manager:**  I wish.
I've got one release. Just one.
But somehow I need to check Jira...
...GitHub...
...CI pipelines...
...policy documents...
...deployment configs...
...dependency scans...
...approval emails...
...and somehow prove everything is compliant before anyone lets me deploy. I don’t know what would have happened if there were more releases.
**Dev:** Sounds...fun.
**RM:** Fun? Yesterday I fixed a changelog. Then someone merged another PR. Now the version numbers don't match. A dependency scan failed. Ankit forgot test evidence. Compliance wants proof from a 200-page PDF. And every fix becomes another PR that has to be merged in exactly the right order. One deployment...
...takes two weeks.
**Dev:** Why are you doing all that manually?
**RM:** Because that's release engineering. Unless you've invented an AI release manager overnight.
**Dev:** Actually... Open your browser. Go to:
localhost:8080/ship-it (local url)
**Dev:**
Just paste your Jira Fix Version. And Click Generate.
Now watch.
The agent gathers all the linked evidence...
looks at the pull requests...
reads the test logs...
checks configuration...
parses the policy documents...
and builds a complete release manifest.

Here's your release summary.
Evidence checklist.
Blocked controls.
And instead of giving you twenty problems...
it tells you exactly what can safely be fixed.

For example— Your XYZ is missing. The agent creates a draft PR automatically. Then it figures out where that PR belongs in the merge sequence so nothing conflicts.
Finally...
it send the approval email and trigger chase phase for the unapproved PR.
Now the code owner only has one thing to review—
the complete release package.
**RM:** Couldn't I just automate this with shell scripts?
**Dev:** Shell scripts automate steps. Ship-It Agent understands the entire release. It reasons over tickets, code changes, logs, infrastructure, and policy together. It doesn't just check rules— it explains why something is blocking the release and proposes safe fixes.
**RM:** Okay...
But is this compliant?
I can't let AI deploy to production.
**Dev:** Exactly.
Neither can we.
The agent only auto-remediates safe, predefined controls—
things like changelogs, version updates, or configuration consistency.
Anything involving
* secrets,
* production approvals,
* security exceptions,
* or human sign-offs
is immediately escalated.
The final decision always stays with the release owner.
So if humans still review everything...
what's the benefit?
**Dev:** Today you spend days collecting evidence.
Tomorrow you spend minutes reviewing it.
The AI does the repetitive work—
humans make the important decisions.
That's human-in-the-loop done right.




If time is left 
**RM:** Okay... I'm convinced. But how is this actually working?
**Dev:** Here's the architecture.
The workflow is orchestrated using Google ADK.
Gemini analyzes the ticket, pull requests, commits, logs, and infrastructure changes using its long context window.
Document AI reads compliance PDFs and extracts policy clauses.
Specialist resolver agents classify each control into:
* Passed
* Safe to auto-resolve
* Human escalation
If it's safe...
ADK uses function calling to generate draft PRs, Jira tasks, approval emails, or deployment requests.
Finally, release metrics are stored in BigQuery for audit history and DORA reporting. Everything ends with one human-reviewable release package.
**RM:** So instead of spending two weeks preparing a release...
I spend a few minutes reviewing one package.
**Dev:** Exactly.
Don't replace the release manager.
Give them their evenings back.
