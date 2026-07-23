Scene: Release Manager standing at a desk surrounded by sticky notes or fake printouts. Basically Looking stressed.<br>

**Dev:** Morning! You look like you've been debugging production since Friday.<br>
**Release Manager:**  I wish.<br>
I've got one release. Just one.
But somehow I need to check Jira...
...GitHub...
...CI pipelines...
...policy documents...
...deployment configs...
...dependency scans...
...approval emails...
...and somehow prove everything is compliant before anyone lets me deploy. I don’t know what would have happened if there were more releases.<br>
**Dev:** Sounds...fun.<br>
**RM:** Fun? Yesterday I fixed a changelog. Then someone merged another PR. Now the version numbers don't match. A dependency scan failed. Ankit forgot test evidence. Compliance wants proof from a 200-page PDF. And every fix becomes another PR that has to be merged in exactly the right order. One deployment...
...takes two weeks.<br>
**Dev:** Why are you doing all that manually?<br>
**RM:** Because that's release engineering. Unless you've invented an AI release manager overnight.<br>
**Dev:** Actually... Open your browser. Go to:<br>
localhost:8080/ship-it (local url)<br>
**Dev:**<br>
Just paste your Jira Fix Version. And Click Generate.<br>
Now watch.<br>
The agent gathers all the linked evidence...<br>
looks at the pull requests...<br>
reads the test logs...<br>
checks configuration...<br>
parses the policy documents...<br>
and builds a complete release manifest.<br>

Here's your release summary.<br>
Evidence checklist.<br>
Blocked controls.<br>
And instead of giving you twenty problems...<br>
it tells you exactly what can safely be fixed.<br>

For example— Your changelog is missing. The agent creates a draft PR automatically. Then it figures out where that PR belongs in the merge sequence so nothing conflicts.<br>
Finally...<br>
it send the approval email and trigger chase phase for the unapproved PR.<br>
Now the code owner only has one thing to review — the complete release package.<br>
**RM:** So if humans still review everything... <br> what's the benefit? <br>
**Dev:** Today you spend days collecting evidence.<br>
Tomorrow you spend minutes reviewing it.
The AI does the repetitive work—humans make the important decisions.
That's how you do Human in the loop correctly.
**RM:** Wow... That would've saved me weeks. <br> How did you actually build all of this?
**Architect:** That's the interesting part. We didn't build one giant AI.
We built a team of specialized agents orchestrated by Google ADK. <br> 
Everything starts when you paste a Jira Fix Version. <br> ADK coordinates the entire workflow and decides which agent should act next. <br>
First, Gemini understands the complete release context by reading the Jira ticket, pull requests, commit history, test logs, and infrastructure changes together.<br>
At the same time, Document AI reads compliance documents and converts policy PDFs into structured controls that the agents can reason about. <br>
Then our Resolver Agents classify every release control into one of three categories:
* Passed
* Safe to auto-remediate
* Human review required
Only the safe controls are automatically handled. <br>
For example, generating a changelog PR, fixing a version number, or updating configuration consistency. <br>
Anything involving secrets, production approvals, security findings, or mandatory human sign-offs is immediately escalated. <br>
Finally, ADK uses function calling to create draft pull requests, Jira tasks, approval emails, and optional deployment requests. <br>
Everything is packaged into one release bundle, while BigQuery stores deployment metrics for audit history and DORA reporting. <br>



If time is left <br>
**RM:** Okay... I'm convinced. But how is this actually working? <br>
**Dev:** Here's the architecture. <br>
The workflow is orchestrated using Google ADK.<br>
Gemini analyzes the ticket, pull requests, commits, logs, and infrastructure changes using its long context window.
Document AI reads compliance PDFs and extracts policy clauses.
Specialist resolver agents classify each control into:<br>
* Passed
* Safe to auto-resolve
* Human escalation
If it's safe...<br>
ADK uses function calling to generate draft PRs, Jira tasks, approval emails, or deployment requests.
Finally, release metrics are stored in BigQuery for audit history and DORA reporting. Everything ends with one human-reviewable release package.<br>
**RM:** So instead of spending two weeks preparing a release... <br>
I spend a few minutes reviewing one package.
**Dev:** Exactly. <br>
Don't replace the release manager.
Give them their evenings back.
