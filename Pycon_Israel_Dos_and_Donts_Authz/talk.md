theme: Plain Jane
footer: ![inline 58.5%](../Base/media/footer.png)
slide-transition: push(bottom)
autoscale: true
[.code: auto(32)]

[.header: alignment(left)]

<br>
<br>
<br>
# [fit] Building Authorization with Python: Dos and Don’ts
## Gabriel L. Manor @ Conf42 Python 2024

---

^ Asking what is the different between a passport and flight ticket.

[.header: alignment(left)]

# Find the Difference

[.column]
![fit inline](media/passport.png)

[.column]
![fit inline](media/ticket.png)


---

^ The different is similar to authentication and authorization.
We use the passport as a form of idenity to authenticate that we are who we say we are.
We use the flight ticket as a form of authority to authorize us to board the plane.

| | Passport | Flight Ticket |
| --- | --- | --- |
| Form of | Identity | Authority |
| Purpose | Verifies identity | Grants access to a flight |
| Scope | Global | Flight-specific |
| Issued by | Government | Airline desk, app, website, etc. |
| Information | Name, photo, birthdate, etc. | Name, flight number, seat, gate, etc. |
| Validity | Multiple years | One flight |
| Used | Once per flight | Multiple times per flight |
| Uniqueness | Unique to an individual | Unique to a flight and passenger |
| Changeable | No | Yes |
| Permissions | One (to travel) | Multiple (to board, to check bags, etc.) |
| Revocation granularity | All at once | One permission at a time |
| Transferable | No | Yes |

---

^ Let's look better on the differnce between authentication and authorization.


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
[.build-lists: true]

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

^ No one develop those features from scratch, we use OSS or cloud products

![inline 60%](media/authn_providers.png)

---

^ Authorization is a tumble weed

![inline](media/working_hard_engineers.png)

---

^ My name is gabriel and today we are going to talk about authorization.

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

^ Problem 2: We need to support multiple permission models.
Another problem is where we need to support multiple permission models in our framework.
We need to change the code and it start to be a mess.

<br>

[.column]
```python
# Middleware
def roles_required():
    ...
            if user.role == 'admin':
                return func(*args, **kwargs)
            else:
                raise Exception('User is not admin')
    ...
def permissions_required():
    ...
            if permissions == 'delete_user':
                return func(*args, **kwargs)
            else:
                raise Exception('User is not admin')
    ...
```

[.column]
```python
# Business logic
@roles_required('admin')
@permissions_required('delete_user')
def delete_user(user_id):
    user = User.get(user_id)
    user.delete()
```

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

^ Problem 4: People are adding authorization logic in the business logic to support partial authorization.

<br>
<br>

```python
@roles_required('admin')
@permissions_required('enable_workflow')
def enable_workflow():
    user = User.get(user_id)
    step1 = workflow.run()
    step2 = workflow.run()
    if user.sms_enabled:
        send_sms_to_list(user.phone_number, 'Workflow is enabled')
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

^ What are the best practices we want a good authorization framework to support?

[.autoscale: true]
[.text: alignment(center)]

# Authorization Best Practices

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

[.header: alignment(center)]

# #1 Model

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

[.header: alignment(center)]

# #2 Author

---

^ As always, every better software solution, start with a contract. contracts are the best way to make software better, and what better of designing a contract of how to configure policies?

[.header: alignment(center)]

# [fit] Contracts Creates Better Relationships


### 👩🏽‍💻 _Especially in Human <> Machine Relationships_ 🤖

---

^ Today we will cover three permissions contracts that together can solve any kind of authorization problem.

[.header: alignment(center)]

<br>

[.column]
## Open Policy Agent
![inline 50%](media/opa.png)

[.column]
## AWS<br>Cedar
![inline 70%](media/cedar.png)


[.column]
## Google Zanzibar - Open FGA
![inline 12%](media/openfga.mp4)

---

^ The three principles that Cedar follows

[.header-emphasis: color(#9B5EE5)]

# [fit] Is _[User]_ Allowed to Perform _[Action]_ on _[Resource]_
# [fit] Is a Monkey Allowed to Eat a Banana

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

[.header: alignment(center)]

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

[.header: alignment(center)]

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

[.header: alignment(center)]

# #5 Event Driven Authorization

---

^ After we understand the contract, let's understand the right components for the modern policy architecture.

# Authorization System Building Blocks

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

# It's Your Time to Shine 🌟
## Contribute to OPAL 

![inline fit](media/opal.png)

---

^ What we learned?

[.header: alignment(left)]

![left 100%](media/office_hours_pr.png)

<br>
<br>
# Thank You :pray:
## Join our Next OPAL Office Hours
## _io.permit.io/opal\_office_\_hours