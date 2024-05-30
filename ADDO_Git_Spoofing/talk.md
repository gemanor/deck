theme: Plain Jane
footer: ![inline 58.5%](../Base/media/footer.png)
slide-transition: true
build-list: true

[.header: alignment(left)]

<br>
<br>
<br>
# Git Commit Spoofing in Open Source<br>No False Alarm
## Gabriel L. Manor @ Permit.io Meet & Greet, January 2024

---

[.header: alignment(center)]

# Social Media Overflow Attack

---

^ Original tweet

[.text: color(#fff)]


[.column]
![inline fit](media/tweet.jpg)

[.column]
c

---

^ original tweet with GH tweet

[.column]
![inline fit](media/tweet.jpg)

[.column]
![inline fit](media/gh_tweet.jpg)

---

^ Let's Reproduce

![fit](media/its_not_a_bug.jpeg)

---

[.header: alignment(center)]

# Git Phising

## The attacker **fake** the repository and **spoof** the malicious commit

# Multi Vector Attack

---

^ Single factor attack - Dependabot pretend - Pretendabot

## Git Phishing as a Single Vector Attack

![inline 120%](media/pretendabot.webp)

Source: https://io.permit.io/pretendabot

---

[.footer: ]
[.header: alignment(left)]
![left fit](media/me.jpg)

<br>
<br>
<br>
<br>

# Gabriel L. Manor
## Director of DevRel @ Permit.io
### Not an ethical hacker, zero awards winner, dark mode hater.

---

^ First problem with Git - collaboration vs security

[.header: alignment(center)]

# Collaboration *vs.* Security

---

[.autoscale: true]
[.build-lists: true]

# Collaboration - Default Security Features

- Encryption
- Strong Auth
- Fine-grained Access Control
- Data Erasure
- Least Privileged
- Content Scanning

---

# Git - Default Security Features

[.build-lists: true]

- Nothing

---

[.header: alignment(center)]

# Public *!=* Open

![inline 80%](media/monalisa.jpg)

---

^ Another default configuration problem - Alexa 1M example

### The Ultimate `View Source` Breach - Git Misconfiguration

![inline](media/alexa_1m.png)

Source: https://io.permit.io/alexa\_1m\_git

---

^ Git isn't only code, but also organizations - they are lack in security too Daimler

## Open-House Strategy - Daimler Code Leak

![inline](media/daimler-repository.webp)

Source: https://io.permit.io/daimler-git

---

![fit 100%](media/mercedes_car.jpg)

---

![inline](media/mercedes.png)

Source: https://io.permit.io/mercedes-fail-again

---

^ Supply chain vector - codecov breach

# Supply Chain Attack - Codecov Breach

![inline](media/codecov.jpg)

---

[.header: alignment(center)]

# Should We Stop Using Git?

---

[.header: alignment(center)]

# Should We Stop Using Planes?

![inline](media/planexs.jpeg)

---

[.column]
![inline fit](media/flight_safety.jpeg)

[.column]
![inline fit](media/safety_cis.png)

io.permit.io/cis-git

---

^ Make sure every code you owned managed in SCM and associate with task
Define a code review strategy (2 users), make sure there is no way to bypass it
Enable branch protection with all the relevant security and stability tests
Do not allow force pushes and push directly to branches
Verify commits are signed to users before push
Monitor continuously for ghost branches and make sure branches are up to date before merge
Make sure there are code owners for sensitive code

[.autoscale: true]

# Code Changes

[.column]
- Sign Commits
- Branch Protection with Tests
- Monitor Old Branches
- Changes are Correlated to Tasks

[.column]
- Create Code Owners
- No Force Pushes
- Code Review Strategy

---

^ Carefully manage public repositories, make sure security.md included
Keep least privilege principle on all CRUD actions of repositories
Monitor constantly all the repositories and their forks
Do not leave inactive repositories
Pay extra attention for delete of issues and repos

[.autoscale: true]

# Repository Management
- Security.md
- Least Privilege
- Monitor Forks
- Delete Inactive Repos
- Inspect Delete of Issues & Branches

---

^ Consistently monitor users and teams, make sure you delete inactive users
Users are created only by invitation and require strong identity, access is restricted to IP addresses.
MFA enabled for users and sensitive actions.
Know your administrators, manage their permissions carefully.
Detect anomalous code operations.
Make sure notifications not sends anywhere.

[.autoscale: true]

# Contribution Access

[.column]
- Contribution Access
- Monitor Team's Activities
- Detect Operations Anomaly
- Only Strong Identity Users
- Clean Inactive Users

[.column]
- User Invited to Contribute
- Notifications Control
- Least Privilege Administration
- Enable MFA for Users and Actions

---

^
- Give external application least privilege permissions.
- Only admins allowed to install applications.
- Stale applications are removed completely
- Detect secrets and sensitive credentials in code
- Scan 3rd party libraries for vulnerabilities and licensing
- Scan IaC and CI instructions for misconfiguration
- Scan code for vulnerabilities

[.autoscale: true]

# Code Risks & Third Party

[.column]
- Least Privilege to External Application
- Only Admin Install Applications
- Remove Stale Applications
- Secret & Credential Detection

[.column]
- Vulnerability Scanning
- License Scanning
- IaC & CI Misconfiguration Scanning
- Third Party Assessment

---

^ Pipelines has single responsibility
Configuration is immutable and automated
Everything logged
Access is limited

[.autoscale: true]

# Build Environment

- Single Responsibility Pipelines
- Automated Configuration
- Log Everything
- Limit Access
- Immutable Settings

---

[.autoscale: true]

# Build Workers

[.column]
- Control the network connectivity
- Automate security scanning
- Single use per worker

[.column]
- Workers not pulling data
- All workers configuration saved in SCM
- Workers are monitored

---

[.autoscale: true]

# Pipeline Instructions & Integrity

[.column]
- Restrict Process Triggering Access
- Track Configuration
- Static Scan Configurations
- Secure Output Storage

[.column]
- Define Input and Outputs
- Sign Artifacts
- Lock Dependencies
- Create SBOM

---

![inline](media/thisisfine.jpeg)

---

[.header: alignment(center)]

# **Never**
# Use a Self-Installed Git Server

---

[.header: alignment(left)]

# Default Config Files
## Implement a `Must Have` Policy

[.build-lists: true]

- .gitignore
- SECURITY.md
- LICENSE
- README.md


---

^ Aqua security tool exmaple

Video demo

---

# Chainbench - Git Security Benchmark Tool

[.autoscale: true]
[.build-lists: true]
- Audit and benchmark your Git security posture
- Open source, extensible
- Can run in either organization or repository level
- Run in CI/CD pipelines (GitHub Action available)
- Maintained by Aqua Security

github.com/aquasecurity/chain-bench

---

![inline 90%](media/stepsecurity.png)

github.com/step-security

---

# Git - Infrastructure as Code

![inline fit 150%](media/tf_diagram.png)

---

![original 180%](media/tf_example.png)

---

```go
default allow := false

allow if {
	some grant in user_is_developer

	input.action == operation.type
	input.environment == operation.destination
}
```

---


[.header: alignment(left)]

![right 30%](media/opal.png)

<br>
<br>
# Thank You :pray:
## Show your love to OPAL with a GitHub Star :star: :point_right:
### Find more about OPAL on opal.ac
#### Follow me on Twitter @gemanor
