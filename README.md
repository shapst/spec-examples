# Job Market System

## Welcome

> *Everything in software architecture is a trade-off.  
First Law of Software Architecture*

This page is architectural documentation for the solution proposal from **Team**.

## Business Case
[Glossary](glossary.md) Glossary

## System Requirements

### Stakeholders

This section describes key stakeholders of the system and their architectural concerns.

* **SH-1**: **Administrator** (security)
    - security is the second name for an administrator; these people deal with user accounts and the billing system.

* **SH-2**: **Account** (availability, performance, scalability, robustness)
    - accounts want the system they're accessing is available anytime they want to use it, and that it responses quickly
      to their actions;

* **SH-3**: **Employer** (availability, performance, scalability, robustness)
    - employers want to track clear and transparent hiring process for their job postings.

* **SH-4**: **Job seeker** (availability, performance, scalability, robustness)
    - job-seekers want to track clear and transparent hiring process.

* **SH-5**: **Visitor** (availability, performance, scalability, robustness)
    - Visitors want to have access job postings and public pages;
    - Don't need to sign-up and sign-in;

### Functional Requirements

* **UC-1**: **Account registration**:
    - accounts register their profile (SH-2);
    - employers register their profile and selects a plan (SH-3);
    - job-seekers register their profile (SH-4);

* **UC-2**: **Account sign in**:
    - accounts sign-in the system using their email and password combination (SH-3, SH-4) ;
    - accounts can request a generation of a reset password link sent by email (SH-3, SH-4);
    - accounts can change their password using reset password link (SH-3, SH-4);


* **UC-3**: **Visitor workflow**:
    - visitors can browse the main, how-it-works page and public pages; (SH-5)
    - visitors can search job-posting; (SH-5)
    - visitors can view job-posting details; (SH-5)
    - visitors can search companies; (SH-5)
    - visitors don't allowed to apply the job; (SH-5)
    - visitors can create a job-seeker and employer accounts; (SH-5)

* **UC-4**: **Job posting submission**:
    - employer fills out and submits job posting details; (SH-3)

* **UC-5**: **Resume submission**:
    - job-seeker submits a resume as an attachment; (SH-4)

* **UC-6**: **Search job postings workflow**:
    - job-seeker can use filters to search companies; (SH-4, SH-5)
    - job-seeker can use filters to search job postings; (SH-4, SH-5)
    - job-seeker can view company details and its job postings; (SH-4, SH-5)
    - job-seeker can view job posting details; (SH-4, SH-5)

* **UC-7**: **Apply job**:
    - job-seeker apply for a job posting; (SH-4)
    - employer receives job-seeker's application; (SH-3)
    - employer changes applicant status (Interested -> Yes - No); (SH-3)

* **UC-8**: **Search candidates workflow**:
    - employer can use filters to search candidates; (SH-3)
    - employer can use unlocks to view candidate details; (SH-3)


* **UC-9**: **Track hiring process**:
    - job-seeker can track his applies; (SH-4)
    - employer can track steps of the hiring process; (SH-3)

* **UC-10**: **Billing**:
    - employer can renew (buy) plan if he used all unlocks; (SH-4)

* **UC-N**: **User maintenance**:
    - administrator maintains internal user accounts (SH-1);
    - administrator maintains interviewer skillset, location, and availability (SH-1);

## Baseline Architecture
---
This section describes the architecture of the current jobmarket system.

Please note that all views are documented in C4 model style, although only System Context, Container and dynamic views
are presented. The most diagrams use informal notation style. All diagrams are supplied with a key explaining meaning of
each shape on the diagram.

The following diagram depicts the containers diagram of the current system:

![Baseline Architecture](images/C4-system-context.jpg "Baseline Architecture")

## Containers

The containers diagram that follows shows the high-level shape of the software architecture and how responsibilities are
distributed across containers.
It also shows the major technology choices and how the containers communicate with one another.

[DROPPED]

## Process Views

This section explains some key use cases to demonstrate how corresponding workflows pass through containers.

### UC-1: Account registration

The following sequence diagram highlights some key requests that the account performs during registration in the system.

```mermaid
sequenceDiagram
    actor u as User
    participant app as Application
    participant account as Account Service
    app->>u: Request email address and password
    u->>app: User enters an email, password and confirm password
    app->>app: Verify password and confirm password are equal
    app->>+account: Create a new account
    account-->>account: Verify account data
    account-->>+app: [500] If the email is already registered
    account-->>account: Create disabled account
    account->>-account: Generate confirmation token and send by email
    account-->>+u: Confirmation token to confirm email and activate account
    account-->>+app: [200] Created Account ID
    u->>app: User enters the confirmation token
    app-->>+account: Verify the confirmation token
    alt valid token
        account-->>account: Enable account
        account-->>+app: [200] Send access token and refresh token
        app-->>-u: Store authentication tokens
    else broken or expired token
        account-->>+app: [401] Security exception
        app-->>-u: Authentication error
    end
```

### UC-2: Account sign in

The following sequence diagram highlights some key requests that the account performs during sign in the system.

```mermaid
sequenceDiagram
    actor u as User
    participant app as Application
    participant account as Account Service
    app->>u: Request email address, password
    u->>app: User enters an email and password
    app->>+account: Verify users’ credentials
    account-->>account: Confirms that the provided credentials are valid
    alt valid credentials
      account->>-account: Generate access token and refresh token
      account-->>+app: [200] Access token and refresh token
      app->>u: The system logs the user in
    else invalid credentials or account disabled
      account-->>+app: [401] Credentials are not valid or account is not active
      app->>u: The system deny the user in
    end
    
```

## Architecture Decision Records
---

- [ADR-1](ADR/ADR-1-passwordless-magic-link.md) Use password-less sign-in process.
- [ADR-2](ADR/ADR-2-sign-up-in.md) Implements a custom JWT-based authentication process.
- [ADR-3](ADR/ADR-3-upgrade.md) Upgrade(buy) subscription plan.
- [ADR-4](ADR/ADR-4-restapi.md) REST API Status codes.

## Planning
---

```mermaid
gantt
    dateFormat  YYYY-MM-DD
    title       Sprint 1
    excludes    weekends
    %% (`excludes` accepts specific dates in YYYY-MM-DD format, days of the week ("sunday") or "weekends", but not the word "weekdays".)

    section UC-1 Account registration
      sign-up                                     :done, uc-1-1, 2022-11-01, 3d
      employers profile                           :done, uc-1-2, after uc-1-1, 2d
      job-seekers profile                         :done, uc-1-3, after uc-1-1, 2d
    
    section UC-2 Account sign in
      sign-in                                     :done, uc-2-1, 2022-11-04, 1d
      reset password                              :uc-2-2, 2022-11-22, 1d
    
    section UC-3 Visitor workflow
      public pages                                :done, uc-3-1, 2022-11-07, 1d
      jobs list                                   :done, uc-3-2, 2022-11-11, 1d
      filter jobs                                 :active, uc-3-3, 2022-11-14, 1d
      company list                                :done, uc-3-4, 2022-11-11, 1d
      filter companies                            :active uc-3-5, 2022-11-14, 1d
      disable apply                               :uc-3-6, after uc-3-3, 1d
    
    section UC-4 Job posting submission
      submit job                                  :done, uc-4-1, after uc-1-2, 2d

    section UC-5 Resume submission
      submit resume                               :done, uc-5-1, after uc-1-2, 2d
      upload doc                                  :uc-5-2, 2022-11-18, 1d

    
    section UC-6 Search job postings workflow (Job Seeker)
      company list                                :done, uc-6-1, 2022-11-11, 1d
      filter companies                            :active, uc-6-2, 2022-11-14, 1d
      job list                                    :done, uc-6-3, 2022-11-11, 1d
      filter jobs                                 :active, uc-6-4, 2022-11-14, 1d
      company details                             :done, uc-6-5, after uc-6-2, 2d
      job details                                 :done, active, uc-6-6, after uc-6-2, 2d

    
    section UC-7 Apply job
      apply job posting                           :active, uc-7-1, after uc-6-6, 1d
      view application                            :uc-7-2, after uc-7-1, 1d
      change application status                   :uc-7-3, after uc-7-2, 2d
    
    section UC-8 Search candidates workflow
      candidates list                             :active uc-8-1, 2022-11-11, 1d
      use unlock                                  :uc-8-2, after uc-10-1, 2d
      filter candidate                            :active uc-8-2, 2022-11-14, 1d

    
    section UC-9 Track hiring process
      hiring process(job-seeker)                  :uc-9-1, after uc-7-3, 3d
      hiring process(employer)                    :uc-9-1, after uc-7-3, 3d
    
    section UC-10 Billing
      upgrade plan                                :uc-10-1, 2022-11-21, 2d
  
```
