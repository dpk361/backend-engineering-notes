---
title: Why Security Exists
parent: Security
nav_order: 1
---

# Why Security Exists in Backend Systems

Before Spring Security, before JWT, before OAuth2, the real question is:

> Why do backend systems need security at all?

Because every backend system handles something valuable:

* user identity
* private data
* money
* permissions
* business actions
* internal system access

If a backend is not secured properly, anyone can:

* read data they should not see
* modify data they should not touch
* call admin APIs
* impersonate users
* abuse services
* break compliance rules
* damage trust and revenue

So security is not just a feature. It is a fundamental requirement of backend design.

---

# The Core Problem

A backend service is usually exposed over HTTP, messaging, or internal network calls.

That means the service must answer four questions for every request:

1. Who are you?
2. Are you allowed to do this?
3. Can we trust this request?
4. Should we block this request immediately?

Spring Security helps with all of these, but the broader concept is larger than Spring itself.

---

# Real-World Example

Imagine an online banking app.

Endpoints might be:

```http
GET /accounts/1001
POST /transfer
GET /admin/users
DELETE /admin/users/9
```

Without security, any client could call any endpoint.

That would mean:

* one user can see another user’s account
* a normal customer can transfer from arbitrary accounts
* an attacker can delete admin users
* internal business rules are completely bypassed

Security exists to prevent exactly this.

---

# What Security Protects

Backend security usually protects these layers:

## 1. Identity

Is this request from a real known user, service, or client?

Examples:

* username/password login
* OTP
* SSO
* JWT token
* API key
* client certificate

---

## 2. Permissions

What can this identity do?

Examples:

* only admins can delete users
* only account owners can view balances
* only services with a valid token can call an internal API

---

## 3. Data Integrity

Was the request changed in transit?

Examples:

* HTTPS prevents tampering
* signatures prevent token modification
* checksums or signatures validate messages

---

## 4. Confidentiality

Can unauthorized people read the data?

Examples:

* TLS/HTTPS
* encrypted secrets
* masked logs
* role-based data access

---

## 5. Availability

Can attackers overwhelm or exhaust the system?

Examples:

* rate limiting
* brute-force protection
* request throttling
* circuit breakers for dependent systems

---

# What Can Go Wrong Without Security

## Unauthorized Data Access

A customer can read another customer’s records.

## Privilege Escalation

A normal user can become admin by calling a hidden endpoint.

## Credential Theft

Passwords or tokens get exposed in logs, URLs, or plain HTTP.

## Replay Attacks

An attacker reuses an old valid request or token.

## CSRF

A browser sends a malicious request on behalf of a logged-in user.

## Broken Authentication

Bad session handling, weak password storage, no expiration.

## Broken Authorization

User is authenticated but still allowed to perform actions they should not.

## Abuse of Internal Services

One microservice calls another without proving identity.

---

# Security Is Not One Thing

A lot of people think security means “login page.”

That is only a tiny part.

In backend engineering, security is a combination of:

* authentication
* authorization
* token validation
* password hashing
* session handling
* CSRF protection
* CORS policy
* secure headers
* transport security
* secret management
* auditing
* monitoring
* rate limiting
* input validation
* vulnerability prevention

So Spring Security is really a framework for controlling trust in backend systems.

---

# Common Industry Use Cases

## 1. Public Web Application

Users log in with email/password.
A session or token keeps them authenticated.
Pages and APIs are protected by roles and ownership rules.

---

## 2. REST API for SPA/Mobile App

The frontend never stores server sessions.
The client sends a JWT or OAuth2 access token on every request.

---

## 3. Microservices

Service A calls Service B using a service token, client credentials, or mTLS.
Each service proves its identity.

---

## 4. Enterprise SSO

Employees log in through Okta, Keycloak, Azure AD, or Google Workspace.
The application trusts a centralized identity provider.

---

## 5. Admin Portal

Only internal employees with admin privileges can access certain screens and endpoints.

---

## 6. Banking / Fintech / Healthcare

Security must support audit logs, strict access control, encryption, token expiration, and compliance.

---

# Security Mindset in Backend Design

A senior developer does not ask:

> “How do I make this endpoint work?”

A senior developer asks:

> “Who can call this endpoint, how do we verify them, what can they do, what if they retry, what if the token leaks, what if the service is attacked?”

That mindset is what separates a normal backend from a production-ready backend.

---

# Example of Insecure Thinking

```java
@GetMapping("/admin/users")
public List<User> getAllUsers() {
    return userService.findAll();
}
```

This looks fine at first glance.

But if there is no security layer, then any user can fetch all users.

A secure version is not only about code. It is about policy:

* who is allowed
* how that rule is enforced
* where that enforcement happens
* what happens if auth fails

---

# The Rule of Secure Backend Development

Every request should be treated as untrusted until proven otherwise.

That means:

* do not trust the client
* do not trust headers blindly
* do not trust request parameters blindly
* do not trust JWTs unless verified
* do not trust the browser
* do not trust another microservice just because it is internal
* do not trust any input until checked

This is a major security principle.

---

# How Spring Security Fits In

Spring Security provides a standard way to enforce these checks in Spring applications.

It helps you:

* authenticate users
* authorize actions
* protect endpoints
* manage sessions or tokens
* integrate with OAuth2 / SSO
* secure microservices
* protect against common attacks

But before using Spring Security correctly, you must understand the reason it exists:

> to ensure only the right identity can perform the right action at the right time.

---

# A Practical Mental Model

Think of backend security like a building:

* authentication = showing your ID card
* authorization = whether your ID gives access to a room
* filters = security guards at the entrance
* SecurityContext = the visitor record for the current person
* roles/permissions = which floors or rooms they can enter

Without guards, anyone walks in.
Without ID checks, anyone claims to be someone else.
Without room rules, even authenticated visitors roam anywhere.

---

# Industry Best Practices at This Level

At the foundation level, good security practice means:

* always use HTTPS in real environments
* never store plain passwords
* always separate authentication from authorization
* deny by default, allow by explicit rule
* use least privilege
* log sensitive actions
* expire sessions and tokens properly
* validate every external request
* protect admin APIs strongly
* plan for token revocation and rotation
* treat internal services as needing security too

---

# What You Should Remember From This Concept

Security exists because backend systems protect valuable resources from untrusted access.

At the highest level, security ensures:

* the caller is known
* the caller is verified
* the caller has permission
* the request is safe
* the system remains trustworthy

That is the foundation behind everything else in Spring Security.
