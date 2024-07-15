theme: Plain Jane
footer: ![inline 58.5%](media/footer.png)
slide-transition: true

[.footer: ]

![](media/open.png)

---

^ Disclaimer: this is not about get you fear of AI

![inline](media/wormgpt.png)

---

^ Same as cloud, AI isn't a blackbox, it is something that being accessible to understand

![inline](media/always_has_been.jpg)

---

^ Before talking about idenitities, let's talk about cyborgs

![inline](media/cyborg.jpg)

> A cyborg (/ˈsaɪbɔːrɡ/)—a portmanteau of cybernetic and organism—is a being with both organic and biomechatronic body parts.
-- Wikipedia

---

^ I started a long chat with chatgpt, and then copilot, and it was just about to never end

![inline](media/kill_myself.png)

---

^ I had to convert piece of code from Python to JS
I'm a good JS developer

```python
import requests

url = 'https://developer.remaker.ai/api/remaker/v1/face-swap/create-job'
headers = {
    'accept': 'application/json',
    'Authorization': 'your token',
}
files = {
    'target_image': open('demo1.jpg', 'rb'),
    'swap_image': open('demo2.jpg', 'rb')
}

response = requests.post(url, headers=headers, files=files)

print(response.json())  # Print the response content
```

---

^ The real cyborgs is fatigue humans and machines

![inline](media/pain.jpg)

---

^ There's also an opoosite side to this fatigue, machine identity also got weak

![90%](media/hack_reddit.jpg)

---

^ Am I a hacker now?

![inline](media/hacker.jpg)

---

[.footer: ]
[.header: alignment(left)]
![left fit](media/me.jpg)

<br>
<br>
<br>
<br>

## Gabriel L. Manor
### Director of DevRel @ Permit.io
#### Not an ethical hacker, zero awards winner, dark mode hater.

---

^ There are 4 questions we need to ask ourselves

#[fit] Who? **|** What? **|** Where? **|** When?

---

^ First question

# **Who** Are They?

---

^ The historical distinction between a human and machine identity was very clear

| | Human | Machine |
|---|---|---|
| Authentication Methods | Passwords, biometrics, PINs | Certificates, API keys, tokens |
| Lifespan | Shorter | Longer |
| Renwal | Challenge | Automated |
| Main Risk Factor | Human weaknesses | Mismanagement of keys |
| Audit Granularity | High | Low |
| Management | IAM System | Infrastructure as Code |
| Login | Interactive | Non-interactive |

---

^ Example of a machine identity that behave as a human


![inline left 120%](media/grmmarly.png)

- Installed by the end user
- Identify as a human user
- Grant access to all the data the user has access to
- Sends this data to a 3rd party server

---

^ Example of a human identity that behave as a machine

![inline](media/aner.jpeg)

---

^ The obvious, make authentication stronger

[.header: alignment(left)]

# Something You _Know_

# Something You _Have_

# Something You _Are_

---

^ The truth is that we need to shift authentication even to the left, make more authorization

![inline](media/mfa.png)

---

^ The next step is to make authentication more a question of ranking than verification

![inline](media/verification_ranking.png)

---

^ First step, rank level of bots

[.autoscale: true]
[.text-emphasis: color(#FF953F)]

- `NOT_ANALYZED` - Request analysis failed; might lack sufficient data. Score: 0.
- `AUTOMATED` - Confirmed bot-originated request. Score: 1.
- `LIKELY_AUTOMATED` - Possible bot activity with varying certainty, scored 2-29.
- `LIKELY_NOT_A_BOT` - Likely human-originated request, scored 30-99.
- `VERIFIED_BOT` - Confirmed beneficial bot. Score: 100.

_Source: docs.arcjet.com_

---

^ Ranking bots only isn't enough, we want, als secondly to rank the level of access (use one or more)

#[fit] Ownership **|** Relationships **|** Conditions

---

^ First method, for ranking is the use of ownership

![inline](media/ownership.png)

---

^ Second method of ranking, is relationships

![inline](media/relationships.png)

---

^ Third, fine-grained authorizaiton using conditions

![inline](media/conditions.png)

---

^ The most powerful method of multi-dimensional ranking is the one that combines them all

![inline](media/multi_dimentional.png)

---

^ Tools to use for modeling such

![inline](media/OPAL_arcjet.png)

---

^ Enforcement is easy too

<br>
<br>

```javascript
check(
    { id: "user123", botScore: 35 },
    "assign",
    "document:documentA"
);
```

---

^ The new question of who is a question of ranking, not verification

[.autoscale: true]
[.header-emphasis: color(#aaa)]
[.header-strong: color(#974EF2)]

#[fit] Who? **|** _What?_ **|** _Where?_ **|** _When?_

- Always challange
- Ranking over verification
- Accept blurred borders
- Incorporate authorization into authentication

---

^ The next question is What the can do?

# **What** They Can Do?

---

^ The traditional inbound permissions authority

![inline](media/what_step_1.png)

---

^ The new usage in LLM agents in our applications, make this inbound gateways in danger. The internal agents can behave in a way that is not allowed to the inbound connection

![inline](media/what_step_2.png)

---

^ The solution? Egress API gateway

![inline](media/what_step_3.png)

---

^ With proper implementation of fine-grained authorization, we can give the same level of permissions that our internal AI agent can have

![inline](media/what_step_4.png)

---

^ You don't know what your agents would like to do in your application, we need proactive 

![inline](media/sir.jpeg)

---

^ From MAC, to DAC, to Proactive

#[fit] Mandatory _>_ Discretionary _>_ Proactive 

---

^ Now we also know how to answer better the What question

[.autoscale: true]
[.header-emphasis: color(#aaa)]
[.header-strong: color(#974EF2)]

#[fit] _Who?_ **|** What? **|** _Where?_ **|** _When?_

- Egrees === Ingress
- Streamlining permissions
- Centralized configuration, decentralized enforcement
- Proactive access control

---

# Where They Can Get?

---

^ As you might notice for the last years, one of the main questions in AI is Where they can data from?

![inline](media/robots_txt.png)

---

^ First way to deal with this problem in enterprise application, is education

![inline](media/always_has_been.jpg)

---

^ RAG is a pipeline where we can control the data between the chains

![inline](media/rag.png)

---

^ To implement a proper enterprise-level data-access authorization, we can use RAG that analyze the permsissions of a data that is accessible by the agents

![inline](media/opal_rag.png)

---

^ So we know what we should do when we re-ask the where questions

#[fit] _Who?_ **|** _What?_ **|** Where? **|** _When?_

- Educate
- Use RAGs for authorization
- Monitor access to data
- Conditional and relationship based access control

---

^ To the last question

# **When** They Can Perform?

---

^ For years, we consider the authentication lifecycle of computers with sessions

![55%](media/session_based_auth.png)

---

^ At some point, sessions has problems, and we moved to tokens, but the lifecycle stay the same

![55%](media/token_based_auth.png)

---

^ With AI agents, the straight lifecycle could have multiple distrutption

![55%](media/token_based_auth_problems.png)

---

^ We need to start to think of the auth lifecycle as a monitor graph instead of a straight line

![55%](media/continous_auth.png)

---

^ One standard that is very common in this continous way to authenticate and authorize requests is CAEP

# CAEP
## Continuous Access Evaluation Profile
### www.caep.dev

---

^ Here's an example how we use real time updates of policies to detect and authorize security events

![inline](media/caep.png)

---

^ With all this in mind, we are now done with the fourth question need to re-ask, When

#[fit] _Who?_ **|** _What?_ **|** _Where?_ **|** When?

- Continous security
- Event-driven authorization
- Peaks and Vallyes over a straight line
- Zero standing priviledges

---

[.footer: ]

^ You probably saw this logo for multiple times over the deck, what is OPAL?
TBD adopt the diagram style

![](media/opal_diagram.png)

---

[.header: alignment(left)]

![right 120%](media/slack.png)

<br>
<br>

# Thank You :pray:
## Join Our Authorization Community (>2k members)
### io.permit.io/slack