theme: Plain Jane
footer: ![inline 61.2%](../Base/media/footer.png)
slide-transition: true

[.header: alignment(left)]

<br>
<br>
<br>
# So...The Secret Has Been Exposed. Now What?
## Gabriel L. Manor @ DevOpsDays TLV 2023

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

^ I like to stalk on the internet, and attackers love it too

# We All Love to Stalk

---

^ Why look for data breaches where you can search for the keys to this data?

# Keys are Better than Breaches

---

^ Where do attackers find secrets? (GitHub, package managers, frontend applications, everywhere)

# GitHub. NPM. PiPy. Websites

---

^ What kind of secrets do we have? (environment passwords/certificates, external services keys, cloud provider credentials, etc.)

# Passwords. Keys. Certificates. Tokens

---

^ What vulnerabilities can leaked secrets lead to?

# Data breaches. Crypto mining. Account takeover. Ransomware

---

^ Our horror story exposed SSH’s secret, which led to crypto mining costs of $70k

# $70k

---

^ The Internet never forgets. Removing the keys will help but with limitations.

# The Internet Never Forgets

---

^ The most important thing is to revoke the secrets, not to remove them.

# Revokation Over Removal

---

^ Ensure you have an API for revoking sessions, so you can call an API to revoke existing sessions. Some providers (such as Slack) also offer such auth revoke

# Revoke Sessions

---

^ Make risk assessments, keep watch access, and audit logs for any suspicious actions. 

# Assess Risk

---

^ Be open, communicate the issue and potential damage, and ensure you tell the customers everything you can.

# Be Open

---

^ Exposed tokens can be helpful as honey pot tokens.

# Set Honey Pot Tokens

---

^ Prevent following cases by using a continuous approach to secrets, using secret detection tools in the CI/CD pipeline such as GitLeaks. 

# Continuous is the Key

---

^ Entropy and regex for secret detection, pros and cons.

# Entropy and Regex

---

^ A vault is not the only tool to help your secrets safe. The continuous approach requires awareness of more best practices, such as keeping the principle of separating configuration from code, even for non-secret variables.

# Vault is Not Enough

---

^ Make sure you have clear guidelines for keeping secrets, and make sure you are using products with good DevEx.

# Clear Guidelines

---

^ Keep the least privileged principle for tokens and secrets.

# Least Privileged

---

^ Conclusion

# Don't Be a Victim

---

[.header: alignment(left)]

![right 30%](media/opal.png)

<br>
<br>
# Thank You :pray:
## Show your love to OPAL with a GitHub Star :star: :point_right:
### Find more about OPAL on opal.ac
#### Follow me on Twitter @gemanor
