theme: Plain Jane
footer: ![inline 42.5%](../Base/../Base/media/footer.png)
slide-transition: true
[.code: auto(32)]

[.header: alignment(left)]

<br>
<br>
<br>
# Revolutionizing API Access Control with Decentralized Authorization
## Gabriel L. Manor @ API World 2023

---

^ Every API developer has a dream (dream meme)

![fit](https://i.imgflip.com/6wwyq.jpg)

---

^ Many word, same dream

![fit 100%](media/word_cloud.png)

---

^ The dream of the ring (you'll see it many times)

![fit 100%](https://i.imgflip.com/gx2j7.jpg)

---

^ Remind me the story of the Tower of Babel

![](media/babylon.png)

---

^ The enemy of the dream is distributed systems and decentralization

![fit](https://i.imgflip.com/84fhj9.jpg)

---

^ The challenge can solve with seperation of concern

# Separation of Concern
## Decentralized **vs** Centralized

---

^ My name is Gabriel, Unfortunatly my last couple of years was around Authorization ___
Today we are going to talk about access control as a case study for our dream


[.footer: ]
[.header: alignment(left)]
![left fit](media/me.jpg)

<br>
<br>
<br>
<br>

## Gabriel L. Manor
### Director of DevRel @ Permit.io
#### 🏅 Struggling with authorization for the last 8y
##### Not an ethical hacker, zero awards winner, dark mode hater.

---

### **Authentication** - When you're trying to enter a club, you need to show your ID to the bouncer, but you left it at home.

### **Authorization** - When you are inside the club but you are not invited to enter the VIP area

---

^ Beside the  basic different, here are a deep dive of the differences between authentication and authorization



| | Authentication | Authorization |
| --- | --- | --- |
| Purpose | Verifies user identity | Determines user permissions |
| Scope | Applies to all users | Specific to each user's role or status |
| Issued by | Identity provider | Any kind of data |
| Information | Username, social identity, biometrics, etc. | User roles, permissions, policy, external data, etc. |
| Validity | Until credentials change or are revoked | Per permissions check |
| Used | Once per session (typically) | Multiple times per session |
| Uniqueness | Unique to an individual user | Unique to a principal, action and resource |
| Changeable | Require session revocation | Yes (permissions can be updated, etc.) |
| Permissions | One (to access the system) | Multiple (to read, write, update, delete, etc.) |
| Revocation granularity | All at once (user is denied access) | One permission at a time |
| Transferable | No (credentials should not be shared) | Yes (policy can be applied to other users) |


---

^ What are all the advanced use cases we need with autehntication?

[.autoscale: true]

# Authentication Advanced Features

* Multi-factor authentication
* Single sign-on / Social login / Passwordless
* User / account management
* Session management
* User registration / UI flows / customizations
* Account verification / recovery
* Audit / reporting / analytics / compliance
* Third party integrations

---

![fit 50%](media/auth.png)

---

^ Everyone agrees this authorization pattern is a bad pattern

<br>
<br>

```python
def delete_user(user_id):
    user = User.get(user_id)
    if user.role == 'admin':
        user.delete()
```

---

^ Problem 1: Every configuration change requires a code change.
The usual implementation of authorization in python is decorators with roles
The problem is our need to change the code for every time we need to add a new role or change the logic of the role.

```python
# Middleware
def roles_required():
    ...
            if user.role == 'admin':
                return func(*args, **kwargs)
            else:
                raise Exception('User is not admin')
    ...

@roles_required('admin')
def delete_user(user_id):
    user = User.get(user_id)
    user.delete()
```

---

^ Problem 5: Testing is hard, we need multiple permissions for multiple environments.

[.header: alignment(left)]

<br>
<br>

[.column]

### Staging

```python
@roles_required('admin')
@permissions_required('enable_workflow')
def enable_workflow():
    user = User.get(user_id)
    step1 = workflow.run()
```

[.column]

### Production

```python
@roles_required('superadmin')
@permissions_required('enable_workflow')
def enable_workflow():
    user = User.get(user_id)
    step1 = workflow.run()
```

---

^ Problem 6: Every new framework/language required a new implementation.

[.header: alignment(left)]

[.column]

### Django

```python
@permission_required('app_name.can_edit')
def my_view(request):
    # Your view logic here
    ...
```

[.column]

## Flask

```python
app = Flask(__name__)
login_manager = LoginManager(app)

class User(UserMixin):
    def __init__(self, id, role):
        self.id = id
        self.role = role

@login_manager.user_loader
def load_user(user_id):
    return User(user_id, 'admin')

@app.route('/admin')
@login_required
def admin():
    if current_user.role != 'admin':
        abort(403)
    ...
```

---

![fit](media/i_knew.jpeg)

---

![fit 50%](media/authz_gateway.png)

---


^ Problem 3: Need for a data and conditions that are not part of the request - decorators are just not enought.

```python
# Middleware
def roles_required():
    ...
            if user.role == 'admin':
                return func(*args, **kwargs)
            else:
                raise Exception('User is not admin')
    ...

# Business logic
@roles_required('admin')
def enable_workflow(user_id):
    user = User.get(user_id)
    paid_tier = billing_service.get_user_tier(user_id) == 'paid'
    if not paid_tier:
        raise Exception('User is not on paid tier')
```

---

^ Problem 7: No way to audit decisions. performance

<br>
<br>

```python
if user.role == 'admin':
    if user.tier == 'paid':
        if user.sms_enabled:
            if user.phone_number:
                send_sms_to_list(user.phone_number, 'Workflow is enabled')
```

---

![fit](media/i_have_no_idea.jpeg)

---

# #1 Model

---

^ Every permissions desicion, starts with the same question

[.header-emphasis: color(#9B5EE5)]
# [fit] User _|_ Action _|_ Resource


---

^ Every permissions desicion, starts with the same question

[.header-strong: color(#000000ff)]
[.header-emphasis: color(#9B5EE5)]
# [fit] Does _[Principal]_ Allowed to Perform _[Action]_ on _[Resource]_
# [fit] Is a Monkey Allowed to Eat a Banana

---

^ TBD maybe diagram

# Authorization Actors

* Developer
* Admissions
* Service to Service
* API User
* End-User

---


^ There are 4 commoly used permissions models today, when we want to add AuthZ to application, we should be familiar with them and choose how to implement it with no matte rof the framework or tech stack we use.

[.header: alignment(left)]

## ACL - Access Control List

## RBAC - Role-Based Access Control

## ABAC - Attribute-Based Access Control

## ReBAC - Relationship-Based Access Control

---

^ ACL is the most simple model, it's a list of users and the resources they can access. It's usually implemented in the application level itself, and it's not scalable at all.
Not scalable, usually used in low-performance appliances

[.header: alignment(left)]
# ACL - Access Control List

[.column]
![inline](media/acl.png)

[.column]
* EOL model
* Widely used in IT systems/networks
* No segmentation/attribution support
* Hard to scale

---

^ RBAC is the most common model, it's a list of roles and the resources they can access. It's usually implemented in the application level itself, and it's not scalable at all.

[.header: alignment(left)]
# RBAC - Role Based Access Control

[.column]
![inline](media/RBAC.png)

[.column]
* The widely-used model for app authorization
* 🤓 Easy to define, use and audit
* No resource inspection
* Limited scalablity

---

^ ABAC is the most complex model, it's a list of rules that define the access to the resources. It's usually implemented in the application level itself, and it's not scalable at all.

[.header: alignment(left)]
# ABAC - Attribute Based Access Control

[.column]
![inline](media/abac.png)

[.column]
* The most robust model for inspection and desicion making
* Configuration could be hard
* Easy to handle multiple data sources
* 🚀 Highly scalable

---

^ ReBAC is the most scalable model, it's a list of roles and the resources they can access. It's usually implemented in the application level itself, and it's not scalable at all.

[.header: alignment(left)]
# ReBAC - Relationship Based Access Control

[.column]
![inline](media/rebac.png)

[.column]
* Best fit for consumer-style applications
* Support in reverse indices and search for allowed data
* Easy to scale for users (>1b) hard in desicion's performance
* Expensive in management and maintainance

---

![fit inline](media/authz_across_stack.png)


---

# #2 Author

---

^ As always, every better software solution, start with a contract. contracts are the best way to make software better, and what better of designing a contract of how to configure policies?

# [fit] Contracts Creates Better Relationships


### 👩🏽‍💻 _Especially in Human <> Machine Relationships_ 🤖

---

^ Today we will cover three permissions contracts that together can solve any kind of authorization problem.

<br>

[.column]
## Open Policy Agent
![inline 50%](media/opa.png)

[.column]
## AWS<br>Cedar
![inline 70%](media/cedar.png)


[.column]
## Google Zanzibar
![inline 12%](media/openfga.mp4)


---

^ OPA, stands for Open Policy Agent, is a CNCF project that was created by Styra, it's a general purpose policy engine that can be used to enforce authorization policies in microservices architecture.
OPA contract core is Rego, a policy multi-prupose language that can be used to define policies in a declarative way.

OPA

[.column]
```haskell
package app.rbac

import future.keywords.contains
import future.keywords.if
import future.keywords.in

default allow := false

allow if user_is_admin

allow if {
	some grant in user_is_granted
	input.action == grant.action
	input.type == grant.type
}

user_is_admin if "admin" in data.user_roles[input.user]

user_is_granted contains grant if {
	some role in data.user_roles[input.user]
	some grant in data.role_grants[role]
}
```

[.column]
```haskell
package app.abac

import future.keywords.if

default allow := false

allow if user_is_owner

allow if {
	user_is_employee
	action_is_read
}

allow if {
	user_is_employee
	user_is_senior
	action_is_update
}

allow if {
	user_is_customer
	action_is_read
	not pet_is_adopted
}

user_is_owner if data.user_attributes[input.user].title == "owner"

user_is_employee if data.user_attributes[input.user].title == "employee"

user_is_customer if data.user_attributes[input.user].title == "customer"

user_is_senior if data.user_attributes[input.user].tenure > 8

action_is_read if input.action == "read"

action_is_update if input.action == "update"

pet_is_adopted if data.pet_attributes[input.resource].adopted == true
```

---

^ OPA pros and cons

[.header: alignment(left)]
# Open Policy Agent

* 👍 Wide ecosystem
* 👍 Rego is comprehensive and robust
* Rego is complex 🤔
* Data cache replica
* Enforcement plugins

---

^ AWS Cedar is a policy engine and language that was created by AWS, it's a general purpose policy engine that can be used to enforce authorization policies in microservices architecture.

![inline 70%](media/cedar.png)

[.column]
```javascript
permit(
    principal in Role::"admin",
    action in [
        Action::"task:update",
        Action::"task:retrieve",
        Action::"task:list"
    ],
    resource in ResourceType::"task"
);
```

[.column]
```javascript
permit (
    principal,
    action in
        [Action::"UpdateList",
         Action::"CreateTask",
         Action::"UpdateTask",
         Action::"DeleteTask"],
    resource
)
when { principal in resource.editors };
```

[.column]
```javascript
permit (
    principal,
    action,
    resource
)
when {
    resource has owner &&
    resource.owner == principal
};
```

---

^ AWS Cedar pros and cons

[.header: alignment(left)]
# AWS Cedar

* 👍 First desgined as application authz language
* 👍 Backed by AWS 
* Just released 🤔
* ReBAC via ABAC
* Benchmark winner

---

^ Google Zanzibar is a standard created by Google for their scalable systems they need. I mostly like the implementation of OpenFGA to it, but it's not a contract, it's an implementation.

![inline](media/zanzibar.png)![inline](media/zanzibarcode.png)

---

OpenFGA Implementation

![right 35%](media/openfga.mp4)


```json
{
  "schema_version": "1.1",
  "type_definitions": [{
      "type": "user"
    }, {
      "type": "document",
      "relations": {
        "reader": {"this": {}},
        "writer": {"this": {}},
        "owner": {"this": {}}
      },
      "metadata": {
        "relations": {
          "reader": {
            "directly_related_user_types": [{"type": "user"}]
          },
          "writer": {
            "directly_related_user_types": [{"type": "user"}]
          },
          "owner": {
            "directly_related_user_types": [{"type": "user"}]
          }
        }
      }
    }
  ]
}
```


---

^ Google Zanzibar pros and cons

[.header: alignment(left)]
# Google Zanzibar - OpenFGA

* Simple ReBAC
* Complex RBAC/ABAC
* Reverse indices support
* Backed by OKTA
* Stateful

---

# Generate Code from UI

![fit inline](https://docs.permit.io/assets/images/grid-view-policy-c4a77c5377eb392d9ea572d2ae26bc0d.png)

---

# #3 Analyze

---

[.header: alignment(left)]

# Cedar Agent

[.column]
![fit](media/cedar_agent_qr.png)

[.column]
* Policy decision maker
* Decentralized continer, run as a sidecar to applications
* Monitored and audited
* Focused in getting very fast decisions >10ms

---

[.header: alignment(left)]

Policy Playgrounds

[.column]
![fit left](media/rego_playground.png)

play.openpolicyagent.org

[.column]
![fit right](media/fga_pg.png)

play.fga.dev

---

# #4 Enforce

---

[.header: alignment(left)]

# Enforcing Authorization Policies

```python
# Call authorization service
# In the request body, we pass the relevant request information
allowed = requests.post('http://host.docker.internal:8180/v1/is_authorized', json={
    "principal": f"User::\"{user}\"",
    "action": f"Action::\"{method.lower()}\"",
    "resource": f"ResourceType::\"{original_url.split('/')[1]}\"",
    "context": request.json
}, headers={
    'Content-Type': 'application/json',
    'Accept': 'application/json'
})
```

---

^ For frontend, we have CASL

# CASL - Frontend Feature Toggling SDK

```javascript
import { createMongoAbility, AbilityBuilder } from '@casl/ability';

// define abilities
const { can, cannot, build } = new AbilityBuilder(createMongoAbility);

can('read', ['Post', 'Comment']);
can('manage', 'Post', { author: 'me' });
can('create', 'Comment');

// check abilities
const ability = build();

ability.can('read', 'Post') // true
```

---

# #5 Audit

---

![fit inline](media/audit.png)

---

^ What are the best practices we want a good authorization framework to support?

[.autoscale: true]
[.text: alignment(center)]

# API Authorization Best Practices


[.column]

📜
Declarative

<br>

🎯
Agnostic

[.column]

🧩
Generic

<br>

🔌
Decoupled

[.column]

🌐
Unified

<br>

🕵🏼‍♀️
Easy to audit

---

^ After we understand the contract, let's understand the right components for the modern policy architecture. TBD new diagram

# Decentralized Authorization System

![fit inline](media/policy_system_arch.png)

---

^ Sync engine - OPAL

[.autoscale: true]

# OPAL - Open Policy Administration Layer

[.column]
![inline fit](media/opal.png)

[.column]
* Open Source, Written in Python
* Sync decision points with data and policy stores
* Auto-scale for engines
* Centralized services such as Audit
* Unified APIs for the enforcement point
* Extensible for any kind of data source
* Supports OPA, Cedar (and soon to be announced more)
* Used by Tesla, Zapier, Cisco, Accenture, Walmart, NBA and thousands more

---

# OPAL Based Authorization Architecture

![inline](media/arch_with_opal.png)

---

^ Open the code of the app and the rego, open the app with two users on Arc

# Demo time 🍿

---

^ TBD replace to google drive

[.autoscale: true]

## Galactic Health Corporation - Requirements

* Each member can see their health plan and medical records
* Each member can belong to a member group of potential caregivers
* Each member can assign permissions to caregivers to view their own medical records or health plan
* The caregiver "membership" can be limited by time
* Each health plan could have different plans and features for its members

---

![92%](media/resources.png)

---

![92%](media/resources_roles.png)

---

![92%](media/resource_relations.png)

---

![92%](media/resources_abac.png)

---

![88%](media/user_modeling_tuples.png)

---

![88%](media/user_modeling_assignments.png)

---

![88%](media/user_modeling_derivations.png)

---

[.header: alignment(left)]

![right 30%](media/opal.png)

<br>
<br>
# Thank You :pray:
## Show your love to OPAL with a GitHub Star :star: :point_right:
### Find more about OPAL on opal.ac
#### Follow me on Twitter @gemanor

