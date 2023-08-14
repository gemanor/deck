# Python Authorization Anti-Patterns (and How to Aviod Them)
## Introduction
No matter what type of Python application you are writing, authorization is a basic need for many of them.

In the modern developer world, we should use authorization as a service products such as Permit.io, but we still have to implement some python code for it.

The following article, will list some code anti-pattern we found when developers build permissions and authorization in their app, and also how to avoid them to create better apps.

## Impelemntation Anti-Patterns
### Explicitly Mix Code with App Logic

Anti-Pattern: Write `if is_admin:` statements and similars inside the code logic.

Why? Hard to understand, debug and maintain. Break the single responsiblity and seperation of concern pricniles.

Example:
```python
def delete_user(user_id):
    request_user = User.get(request.user)
    user = User.get(user_id)
    if request_user.role == 'admin':
        user.delete()
```

### Declare Roles in the App Code

Anti-Pattern: Use decorator framework that declare roles on functions

Why? for every role change there's a need  in code change.

Example:
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

### Write Code for Every Permission Model

Anti-Pattern: Create multiple frameworks to support multiple permission models (for exmaple, RBAC and ABAC)

Why? Create complexity in audit and debug decisions. Hard to maintain.

Example:
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
# Business logic
@roles_required('admin')
@permissions_required('delete_user')
def delete_user(user_id):
    ...    
```

### Framework is not Enough

Anti-Pattern: Use frameworks that support only endpoint/function scope decorators

Why? sometimes the data in the endpoint/function scope is not enough for the policy decision

Example:
```python
@roles_required('admin')
def enable_workflow(user_id):
    user = User.get(user_id)
    paid_tier = billing_service.get_user_tier(user_id) == 'paid'
    if not paid_tier:
        raise Exception('User is not on paid tier')
```

### Imperative Policy Logic

Anti-Pattern: Use frameworks that not allow checks inside the application logic.

Why? Sometimes the decisions is needed in the middle of the logic.

Example:
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

## Integration Anti-Patterns
The implementations we've showed in the patterns before also lead to more pitfalls in the integration phase of the authorization solution

### Different Permissions for Different Environments
### Keep the same model for mutliple frameworks/languages/applications
### Audit decisions
### Avoid and Debug Performance issues

## Python Authorization Best Practices
### Declerative
By using policy as code such as Cedar language 
### Agnostic
To the permission model, the enforcement layer should only enquiere for the decision and be agnostic to the model we use
### Generic
The authorization service should be generic to the framework using it. The only change needed is the implementation details of the call to the decision point.
### Decoupled
The application code should never made policy decision. The best way to acheive it is to keep every authroization in `allowed`/`not allowed` scope
### Unified
All the policy should sit in one place and managed once
### Easy to audit
Have one centralized place to audit all the decisions made in the app

## Create an Authorization Service
### Using Open-Source
OPAL is an open source tool that can help with one docker file spin-up a fully funcitonal authorization system based on OPA/Cedar language.
This article explain more https://www.permit.io/blog/scaling-authorization-with-cedar-and-opal
### Use Cloud Service
Permit.io is a cloud service that let you manage all the permission in a nice policy editor and also sync the data you need for policy decisions. You can use any kind of permission model such as `RBAC`, `ABAC` and `ReBac`

## Conclusion
Never build permissions again, follow the best practices, start from small and scale with no app changes.