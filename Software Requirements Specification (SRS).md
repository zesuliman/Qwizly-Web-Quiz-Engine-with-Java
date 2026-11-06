# Software Requirements Specification (SRS)
## Web-Based Quiz Engine Tool (Qwizly)

| Document metadata | Value |
|---|---|
| Document version | **1.4** |
| Status | Revised requirements baseline |
| Author | Project Owner\ Zeinab Suliman Mohamed Osman |
| Project type | Portfolio project |
| Date | 6 November 2026 |

---

## 1. Introduction

### 1.1 Purpose

This document specifies the functional and non-functional requirements of the Web-Based Quiz Engine Tool (Qwizly). It describes the system's intended features, behaviour, constraints, architecture, and quality attributes.

It is intended for anyone interested in understanding, evaluating, using, or contributing to the project, including potential users, developers, collaborators, and recruiters reviewing Qwizly as part of a professional portfolio.

### 1.2 Project Description

Qwizly is a lightweight, interactive, web-based quiz engine that enables Quiz Creators to build quizzes from scratch or from ready-to-use templates. Quiz Creators can write questions and answers manually or generate them with AI using their own information sources. They can publish quizzes, distribute access links, and review submitted results. Quiz Respondents participate through a web browser without creating an account.

Qwizly supports self-paced quizzes and host-controlled live quiz sessions.

### 1.3 Vision

To empower educators, trainers, marketers, and community organizers to turn their knowledge and materials into engaging, professionally designed, interactive quizzes, and to reach Quiz Respondents on different devices through a shareable link distributed by email or WhatsApp.

### 1.4 Goals

- **G1:** Allow Quiz Creators to provide information sources (text, documents, and supported links) from which AI generates questions and answers.
- **G2:** Allow Quiz Creators to create quizzes manually or from ready-to-use templates.
- **G3:** Support both self-paced and live quiz modes.
- **G4:** Allow Quiz Creators to distribute published quiz links by email or WhatsApp.
- **G5:** Automatically save attempts, calculate scores, and provide results to Quiz Respondents according to configured release rules.
- **G6:** Provide Quiz Creators with basic statistics and submitted-attempt records.
- **G7:** Enforce ownership and access control. Quiz Creators manage only their own quizzes, while anonymous Quiz Respondents verify a contact channel using a one-time verification code before participating.

### 1.5 Scope

**In scope:**

- Registered Quiz Creator and System Admin accounts.
- Quiz creation, draft editing, templates, preview, publishing, closing, archiving, and permitted duplication.
- Question and answer authoring, question images, ordering, and randomization.
- Uploading or providing information sources for AI question generation.
- AI-generated question review, acceptance, rejection, regeneration, and source references.
- Self-paced and live quiz participation.
- Shareable quiz links and email/WhatsApp distribution.
- Email-default and optional WhatsApp one-time verification.
- Server-enforced availability windows and quiz/question timers.
- Automatic answer saving, attempt resumption, scoring, and result notifications.
- Configurable score release and combined correct-answer/explanation release.
- Basic results statistics, submitted-attempt lists, and CSV export.
- Role-based access control, administration, audit logging, and service monitoring.
- Cloud deployment using Docker and AWS.

**Out of scope:**

- Advanced assessment analytics such as question difficulty indices, discrimination scores, and trend dashboards.
- Payments and subscriptions.
- A native mobile application.
- SMS delivery.
- Taking quizzes or submitting answers inside WhatsApp. WhatsApp is used only for links, verification codes, and result notifications.
- Proctoring.
- A reusable question bank.

**Basic statistics boundary:** Basic statistics are limited to the number of submitted attempts, average score, highest score, lowest score, pass rate, and a list of submitted attempts. These are the statistics specified in FR-54. More advanced analytics are out of scope.

### 1.6 Definitions and Abbreviations

| Term | Definition |
|---|---|
| **Qwizly** | The Web-Based Quiz Engine Tool described in this document. |
| **Quiz Creator** | A registered user who creates, edits, publishes, distributes, and manages quizzes. |
| **Quiz Respondent** | An anonymous participant who opens a shared quiz link, verifies a contact channel, and takes a quiz without registering for an account. |
| **System Admin** | A privileged user who manages system configuration, accounts, moderation, and operational monitoring. |
| **Quiz** | A configured set of questions, answers, settings, release rules, and lifecycle state. |
| **Question** | An individual item presented to a Quiz Respondent. |
| **Option** | A selectable answer associated with a single-choice, multiple-choice, or true/false question. |
| **Attempt** | One Quiz Respondent's individual participation in a quiz. |
| **Answer** | A response submitted or saved for a question within an Attempt. |
| **Template** | A predefined quiz layout, theme, and optional default configuration. |
| **Information Source** | Material supplied by a Quiz Creator for AI question generation. |
| **One-time verification code** | A temporary, single-use code sent to a Quiz Respondent's selected email or WhatsApp channel to verify access. |
| **Self-paced mode** | A mode in which Quiz Respondents complete a quiz independently within its availability window and configured time limit. |
| **Live mode** | A host-controlled mode in which questions are released to participants during a shared live session. |
| **Availability window** | The start and end timestamps during which a published quiz can be accessed. |
| **Score release** | The configured time at which a Quiz Respondent can access their calculated score/result. |
| **Answer and explanation release** | The configured time at which correct answers and their explanations become available to Quiz Respondents. |
| **UTC** | Coordinated Universal Time, used for storing timestamps and evaluating deadlines. |

### 1.7 References

- Qwizly project README: features, technology stack, and high-level architecture.
- WhatsApp Business Platform documentation.
- OWASP Application Security Verification Standard (ASVS).
- Web Content Accessibility Guidelines (WCAG) 2.2.

---

## 2. Overall Description

### 2.1 Domain Problem

Teachers, trainers, community organizers, and event hosts need to assess knowledge or run engaging question sessions. Building quizzes manually can be time-consuming, and distributing them to participants can be inconvenient. Qwizly reduces this effort by providing manual quiz creation, templates, AI-assisted question generation from creator-provided material, shareable links, automatic scoring, and basic results reporting.

### 2.2 Product Perspective

Qwizly is a standalone, cloud-hosted web application. It uses a React single-page frontend and a Spring Boot REST backend backed by MySQL. Redis provides shared, non-authoritative runtime state and coordination for features such as live sessions. The application integrates with an LLM provider for question generation, an email provider for email delivery, and the WhatsApp Business Platform for optional WhatsApp delivery.

The application is packaged using Docker and is intended to be deployed on AWS.

MySQL is the persistent source of truth for quizzes, questions, attempts, answers, scores, and other business records. Redis must not be the sole store for submitted answers or final scores.

### 2.3 Actors and User Classes

| Actor | Description |
|---|---|
| **Quiz Creator** | Registered user who creates and manages their own quizzes, supplies information sources, uses AI generation, distributes quiz links, and views statistics and submitted attempts for their quizzes. |
| **Quiz Respondent** | Anonymous participant who opens a shared link, verifies using a one-time verification code, takes a quiz, and accesses their own released result. A Quiz Respondent does not have a registered Qwizly account. |
| **System Admin** | Configures system-wide settings, manages and suspends registered users, configures external integrations, and monitors system health and operational failures. |
| **External services** | LLM provider, email provider, WhatsApp Business Platform, AWS infrastructure and object storage. |

A Quiz Respondent is conceptually separate from a registered User. An Attempt belongs to a Quiz and a Quiz Respondent. A Quiz Respondent may have multiple Attempts where the quiz's attempt policy permits them.

### 2.4 Operating Environment

- Web application accessed through modern desktop and mobile browsers.
- Responsive user interface.
- React frontend and Spring Boot backend.
- MySQL database and Redis shared runtime store.
- Docker-based packaging and AWS deployment.
- REST/JSON APIs and WebSocket/STOMP communication for live mode.

### 2.5 Constraints

- **C1:** WhatsApp delivery is subject to WhatsApp Business Platform policies, templates, recipient consent, availability, and rate limits.
- **C2:** AI generation depends on a third-party LLM API and is subject to its cost, latency, availability, and quotas.
- **C3:** Email delivery depends on the configured email provider.
- **C4:** The browser is not authoritative for deadlines, scoring, attempt state, or access control.
- **C5:** All timestamps are stored in UTC. User-facing timestamps are converted to the user's local time by the client.
- **C6:** Uploaded source files are temporary inputs for AI generation and are not retained after generation completes.

### 2.6 Assumptions and Dependencies

- **A1:** Users have a supported modern browser and internet access.
- **A2:** Quiz Creators have the right to use the information sources they provide.
- **A3:** Quiz Respondents can access the email address or WhatsApp number they use for verification.
- **A4:** Email delivery is configured. WhatsApp delivery is optional and requires an approved WhatsApp Business integration.
- **A5:** An LLM API key and an appropriate usage budget are available.
- **A6:** The initial capacity target is up to 1,000 simultaneous Quiz Respondents in one live session, subject to load testing.
- **A7:** The system uses UTC for stored timestamps and deadline evaluation; the client displays dates and times in the user's local timezone.

---

## 3. Functional Requirements

Priority definitions: **M** = Must have, **S** = Should have, **C** = Could have.

### 3.1 Accounts, Roles, and Access

| ID | Requirement | Priority |
|---|---|---|
| FR-1 | The system shall allow Quiz Creators and System Admins to register and authenticate using email and password. Quiz Respondents shall not need registered accounts. | M |
| FR-2 | The system shall support the roles Quiz Creator and System Admin for registered accounts and shall enforce authorization on every protected endpoint. Quiz Respondents shall use a separate, limited verification-based session. | M |
| FR-3 | A Quiz Creator shall be able to access, modify, and delete only quizzes they own, subject to administrative moderation. | M |
| FR-4 | A Quiz Respondent shall be able to access a quiz through a valid shared link after verifying using a one-time verification code, and shall be able to access only their own attempts and released results. | M |
| FR-5 | The system shall support token-based authenticated sessions for registered users, including refresh and logout. | S |
| FR-6 | The system shall support password reset by email. | S |
| FR-7 | The system could support social login using Google. | C |
| FR-66 | A System Admin shall be able to suspend and reinstate a Quiz Creator account without deleting the account's quizzes, attempts, results, or other retained data. A suspended Quiz Creator shall not be able to authenticate or perform creator actions. | S |

### 3.2 Quiz Creation and Management

| ID | Requirement | Priority |
|---|---|---|
| FR-8 | A Quiz Creator shall be able to create a quiz with a title, description, mode, and configurable settings. | M |
| FR-9 | A Quiz Creator shall be able to edit a Draft quiz, including adding, modifying, reordering, and deleting questions. | M |
| FR-10 | A Quiz Creator shall be able to create a quiz from a ready-to-use template. | M |
| FR-11 | A quiz shall follow the lifecycle Draft → Published → Closed → Archived. Only Published quizzes within their availability window shall accept new attempts. | M |
| FR-12 | A Published quiz shall not be editable. A Quiz Creator may duplicate a Published or Closed quiz into a new Draft. An Archived quiz shall not be eligible for duplication. | M |
| FR-13 | A Quiz Creator shall be able to delete a quiz after confirmation. Deletion shall follow the retention and data-handling rules in BR-8. | M |
| FR-14 | A Quiz Creator shall be able to preview a quiz in a view representative of the Quiz Respondent experience. | S |
| FR-67 | The system shall allow a Draft quiz to be saved while it is being configured, but shall prevent publication until all system-controlled publishing validations have passed. | M |
| FR-68 | The system shall require at least one question in a quiz before publication. | M |
| FR-69 | The system shall prevent a question from being treated as complete or publishable when its required answer configuration is missing. | M |
| FR-70 | Before publication, the system shall validate required structural and configuration fields, including quiz title, at least one question, required answer options or accepted text answers, at least one correct answer where automatic scoring applies, valid points, valid timing settings, and valid release settings. | M |
| FR-71 | Publishing validation shall not attempt to judge the educational quality, factual correctness, grammar, clarity, fairness, or wording quality of question content. Responsibility for question phrasing and content remains with the Quiz Creator. | M |

**Publishing validation boundary:** The system validates whether the quiz is structurally complete and technically configurable. It does not certify that a question is well-written, unambiguous, factually correct, age-appropriate, or pedagogically sound. Those content decisions remain the Quiz Creator's responsibility.

### 3.3 Questions and Answers

| ID | Requirement | Priority |
|---|---|---|
| FR-15 | The system shall support single-choice, multiple-choice, and true/false questions. | M |
| FR-16 | The system shall support short-text answers using normalized matching. Normalization shall ignore capitalization, surrounding or repeated whitespace, and punctuation. The Quiz Creator shall be responsible for accounting for acceptable spelling and accent/diacritic variations, including by defining accepted answer variants where supported. | S |
| FR-17 | For each question, the Quiz Creator shall specify its points, correct answer or accepted answer(s), and optional explanation. | M |
| FR-18 | A Quiz Creator shall be able to add optional hints to questions. | S |
| FR-19 | A Quiz Creator shall be able to attach an image to a question. | C |
| FR-22 | The system shall allow the Quiz Creator to configure question-order and answer-option randomization for each quiz. | S |
| FR-72 | The system shall require each question to contain non-empty question text before the quiz can be published. This is a structural check and does not assess wording quality or meaning. | M |
| FR-73 | For single-choice, multiple-choice, and true/false questions, the system shall require the necessary answer options and at least one option marked as correct before publication. | M |
| FR-74 | For short-text questions that are automatically scored, the system shall require at least one accepted correct answer before publication. | M |
| FR-75 | The system shall allow the Quiz Creator to configure how unanswered questions are scored. The default rule shall award zero points for an unanswered question. | M |
| FR-76 | The system shall support a configurable multiple-choice scoring policy, with all-or-nothing scoring as the default. | S |

### 3.4 Information Sources and AI Generation

| ID | Requirement | Priority |
|---|---|---|
| FR-23 | A Quiz Creator shall be able to provide information sources by uploading supported PDF, DOCX, or TXT files, pasting text, or supplying supported links. | M |
| FR-24 | The system shall validate uploaded files by supported type and configured size limits and reject invalid or unsafe files. | M |
| FR-25 | A Quiz Creator shall be able to request AI generation of questions and answers from selected sources and specify the number of questions, question types, and difficulty. | M |
| FR-26 | Generated questions shall be saved as drafts that the creator can review, edit, accept, or reject before they enter the quiz. | M |
| FR-27 | AI generation shall run asynchronously, showing progress and a clear error if it fails or times out. | S |
| FR-28 | The system shall limit AI generation per Quiz Creator to control cost. | S |
| FR-77 | The system shall assess whether the supplied source material contains sufficient information for the requested generation. If it is insufficient, the system shall display a clear message and request additional supported resources or replacement of the current source. | M |
| FR-78 | The system shall generate questions and answers only when they are supported by the supplied source material and shall not intentionally introduce unsupported facts. | M |
| FR-79 | The system shall display source references alongside AI-generated questions so that the Quiz Creator can review the supporting source or passage. | M |
| FR-80 | If generation fails or only partially succeeds, the system shall display an error and shall not present an incomplete question set as a successful generation result. | M |
| FR-81 | The system shall allow a Quiz Creator to regenerate questions they have rejected. | S |
| FR-82 | The system shall delete uploaded source files and temporary extracted source content after the generation job has completed, whether successfully or unsuccessfully. Temporary files left by interrupted jobs shall be cleaned up within 24 hours. | M |

### 3.5 Quiz Configuration and Modes

| ID | Requirement | Priority |
|---|---|---|
| FR-29 | A Quiz Creator shall be able to select one mode for each quiz: self-paced or live. | M |
| FR-30 | A Quiz Creator shall be able to configure the quiz availability start and end timestamps, an attempt limit, and a time-limit strategy. The time-limit strategy shall be either one duration for the entire attempt or a separate duration for each question. | M |
| FR-31 | A Quiz Creator shall be able to configure the score-release schedule separately from the correct-answer and explanation-release schedule. Each schedule shall support release immediately after submission, when the quiz availability end time is reached, or at a specified future date and time. | M |
| FR-32 | A Quiz Creator shall be able to configure a passing score. | S |
| FR-33 | A Quiz Creator shall be able to apply a visual theme from the available templates. | S |
| FR-83 | When a whole-quiz duration is configured, the Quiz Respondent shall be able to allocate the available time among the quiz questions. | M |
| FR-84 | When per-question durations are configured, the system shall enforce the configured duration for each question using server-authoritative timestamps. | M |
| FR-85 | The system shall enforce quiz availability start and end timestamps on the server. Client-side clocks and countdowns shall be informational only. | M |
| FR-86 | When a Quiz Respondent opens a quiz before its availability start time, the system shall display a message that the quiz is not yet available and show the exact scheduled opening date and time in the respondent's local timezone. | M |
| FR-87 | The system shall reject invalid time configurations, including an end timestamp that is not later than the start timestamp and non-positive duration values. | M |

### 3.6 Distribution and Access Granting

| ID | Requirement | Priority |
|---|---|---|
| FR-34 | The system shall generate a unique, unguessable shareable link for each published quiz. | M |
| FR-35 | The system could support restricting a quiz to invited respondents identified by email address or WhatsApp number in a future version. The initial release shall use an open shared link protected by one-time verification. | C |
| FR-36 | A Quiz Creator shall be able to disable a quiz's shared link so that no new Quiz Respondent can start an attempt through it. | M |
| FR-37 | A Quiz Creator shall be able to distribute a published quiz link through WhatsApp to a group of contacts, subject to WhatsApp Business Platform requirements. The quiz itself shall always be taken in the web application. | S |
| FR-39 | A quiz link shall support an optional expiry date and maximum number of uses. | S |
| FR-88 | The system shall allow Quiz Creators to distribute published quiz links to Quiz Respondents through email. | M |

### 3.7 Taking a Quiz (Self-Paced Mode)

| ID | Requirement | Priority |
|---|---|---|
| FR-40 | The system shall render questions according to the quiz configuration, including question order, randomization, and display preferences. | M |
| FR-41 | The system shall enforce configured attempt and question deadlines on the server. Client-side timers shall not determine whether an attempt is valid. | M |
| FR-42 | The system shall automatically save changed answers to persistent storage no later than one second after an answer change under normal network conditions. It shall allow an unfinished attempt to be resumed after the Quiz Respondent verifies the same contact channel, including from another device. | M |
| FR-43 | The system shall prevent duplicate submissions and enforce attempt limits using concurrency-safe processing. | M |
| FR-44 | The system shall automatically submit an attempt when its configured server-side time limit expires, using the answers successfully saved by that time. | M |
| FR-89 | If a network disconnection occurs during an attempt and the quiz remains Published and the attempt remains within its permitted time window, the Quiz Respondent shall be able to resume from the latest answers saved by the server. | M |
| FR-90 | If a Quiz Respondent reconnects after the quiz has Closed, the system shall not allow the respondent to resume the attempt and shall display a message advising them to contact the Quiz Creator. | M |
| FR-91 | The system shall use an idempotent submission mechanism so that retrying a submission after a timeout or uncertain network response does not create duplicate attempts, answers, scores, or notifications. | M |
| FR-92 | The system shall not expose incomplete, unsubmitted attempts to Quiz Creators. Only submitted attempts shall appear in creator-facing attempt lists and basic statistics. | M |

### 3.8 Live Mode

| ID | Requirement | Priority |
|---|---|---|
| FR-45 | A Quiz Creator acting as host shall be able to start a live session and provide a join code or link. | M |
| FR-46 | Quiz Respondents shall be able to join a live-session lobby, and the host shall be able to see the participants who have joined. | M |
| FR-47 | The host shall control question release. Connected Quiz Respondents shall receive each released question through real-time communication, with server-enforced timing. | M |
| FR-48 | The system shall show live results after quiz submission, including a leaderboard when enabled for the quiz. | S |
| FR-49 | The system shall support at least 1,000 simultaneous Quiz Respondents in one live session, subject to the performance targets in NFR-6 and NFR-9. | M |
| FR-50 | A Quiz Respondent who disconnects shall be able to reconnect and continue from the current live-session state, subject to session and quiz rules. | S |
| FR-93 | The system shall allow a Quiz Respondent to join a live session after it has started, subject to the session's access and attempt rules. A late-joining respondent shall be shown the current question or current session state. | M |
| FR-94 | The system shall detect host connection-state changes and notify connected Quiz Respondents whenever the host disconnects or reconnects during the live session. | M |
| FR-95 | The system shall preserve the live-session state during a host disconnection. It shall not silently represent the host as connected while disconnected. The handling of question advancement during a host disconnection shall follow the server-enforced session timing and host-control rules. | M |

### 3.9 Scoring and Results

| ID | Requirement | Priority |
|---|---|---|
| FR-51 | The system shall calculate each submitted attempt's score on the server and make the score available according to the configured score-release schedule. | M |
| FR-52 | The system shall store each attempt with its Quiz Respondent reference, answers, status, score, start timestamp, and submission timestamp. | M |
| FR-53 | A Quiz Respondent shall be able to access only their own results for quizzes they have taken, and only after those results are released. | M |
| FR-54 | A Quiz Creator shall be able to view a basic results summary for each owned quiz, including the number of submitted attempts, average score, highest score, lowest score, pass rate, and a list of submitted attempts. | M |
| FR-55 | A Quiz Creator shall be able to export submitted results as CSV. | S |
| FR-56 | The system shall send a notification containing a link to a Quiz Respondent's result through the email or WhatsApp channel selected by that respondent, according to the configured score-release schedule. | S |
| FR-96 | The system shall save an attempt and calculate its score when the Quiz Respondent submits early, without waiting for the quiz availability end time. | M |
| FR-97 | The system shall allow the Quiz Creator to configure the release of correct answers and explanations together, independently of the score-release schedule. | M |
| FR-98 | The score-release schedule and the correct-answer/explanation-release schedule shall each support: immediately after submission, when the quiz availability end time is reached, or at a specified future date and time. | M |
| FR-99 | The system shall not expose correct answers or explanations to a Quiz Respondent before the configured answer-and-explanation release time. | M |
| FR-100 | The system shall calculate and persist eligible results promptly when an attempt is submitted or its deadline expires. If release is scheduled for a later time, the result shall remain stored and shall be delivered when it becomes eligible for release. | M |
| FR-101 | The system shall calculate basic statistics using submitted attempts only. Incomplete and unsubmitted attempts shall be excluded from attempt counts, averages, highest/lowest scores, and pass-rate calculations. | M |

### 3.10 Administration and Notifications

| ID | Requirement | Priority |
|---|---|---|
| FR-57 | A System Admin shall be able to configure system-wide settings, including default language, external service credentials, and configurable usage limits. Timestamps shall remain stored in UTC. | M |
| FR-58 | A System Admin shall be able to list, suspend, reinstate, and delete registered users, subject to authorization and retention rules. | S |
| FR-59 | The system shall send email notifications for account invitations where applicable, password reset, and eligible result notifications. | S |
| FR-60 | The system shall record an audit log of security-relevant actions, including authentication events, access grants, account suspension, and deletion actions. | S |
| FR-102 | The system shall retry transient email, WhatsApp, and background-processing failures according to a bounded retry policy. | M |
| FR-103 | When a submission-processing or notification-delivery operation repeatedly fails after the configured retry limit, the system shall record the failure with diagnostic context and a correlation identifier. | M |
| FR-104 | After repeated failures reach the configured retry limit, the system shall notify the System Admin through the configured operational notification channel. | M |

### 3.11 Quiz Respondent Verification (One-Time Verification Code)

| ID | Requirement | Priority |
|---|---|---|
| FR-61 | A Quiz Respondent shall not need a registered account. To access a quiz through its shared link, the respondent shall provide an email address or WhatsApp number. | M |
| FR-62 | The system shall send a six-digit one-time verification code to the supplied email address by default. | M |
| FR-63 | The system shall support sending the one-time verification code to the supplied WhatsApp number when WhatsApp verification is enabled by the System Admin. SMS shall not be supported. | S |
| FR-64 | A one-time verification code shall expire after five minutes, be usable once, be stored only as a secure hash, and be invalidated when a replacement code is issued. Verification attempts and code requests shall be rate-limited. The initial proposed limit is three code requests per 15 minutes per contact and per IP address, and five incorrect code entries per code. | M |
| FR-65 | After successful verification, the system shall issue a short-lived session scoped to the relevant quiz. Attempt limits shall be counted using the verified respondent contact identity for that quiz. | M |
| FR-105 | Email shall be the default one-time verification code delivery channel. WhatsApp shall be an optional channel when enabled. SMS shall not be available. | M |
| FR-106 | A Quiz Respondent shall be able to switch between email and WhatsApp when requesting a new one-time verification code. The newly requested code shall be sent through the newly selected channel, and the previously issued code shall be invalidated. | M |
| FR-107 | A Quiz Respondent who resumes an unfinished attempt on another device shall verify using the same contact identity associated with that attempt. | M |

---

## 4. Non-Functional Requirements

| ID | Category | Requirement |
|---|---|---|
| NFR-1 | Usability | A first-time Quiz Creator shall be able to create a basic quiz from a template in under five minutes without formal training. Common respondent tasks, including opening a link, verifying, answering, and viewing a released result, shall be understandable without specialist knowledge. |
| NFR-2 | Accessibility | The user interface shall target WCAG 2.2 Level AA conformance. At minimum, core workflows shall support keyboard navigation, visible focus, accessible form labels, sufficient text contrast, meaningful error identification, and information that is not conveyed by colour alone. |
| NFR-3 | Security | All network traffic shall use HTTPS. Registered-user passwords shall be stored using a strong adaptive password hash such as Argon2id or bcrypt. The application shall apply secure session handling, input validation, authorization checks, rate limiting, and protections against relevant OWASP Top 10 risks. One-time verification codes shall be stored only as hashes. |
| NFR-4 | Privacy | Quiz Creators shall access only their own management data and quiz results, subject to System Admin privileges. Quiz Respondents shall access only their own attempts and released results. Contact information shall be collected only as needed for verification and result delivery and shall be deleted or anonymized according to the retention policy. |
| NFR-5 | Responsive design | Core workflows shall work on supported desktop and mobile browsers without requiring a native mobile application. |
| NFR-6 | Performance | Under the defined expected load, 95% of ordinary API requests shall complete within two seconds and 95% of typical page loads shall become usable within three seconds, excluding external-provider processing and large file uploads. Performance shall be verified through repeatable tests. |
| NFR-7 | Scalability | The backend shall be stateless where practical and support horizontal scaling. Shared live-session state, coordination, and cache data shall be externalized from individual application instances. MySQL shall remain the persistent source of truth for business records. |
| NFR-8 | Availability | The system shall be designed for 24-hour, 7-day online access, with a target monthly availability of 99.5%, excluding announced scheduled maintenance. This is a project target, not a contractual service-level guarantee. |
| NFR-9 | Reliability and integrity | The system shall support up to 1,000 simultaneous Quiz Respondents in one live session, subject to load-test verification. Submitted answers shall not be lost or counted more than once. Score calculation shall be server-side. Under normal operating conditions, 95% of submitted attempts shall have their score calculated and persisted within two seconds. Eligible result notifications shall be queued within five seconds for 95% of submissions, and handed to the configured delivery provider within 30 seconds for 95% of notifications. External-provider delivery time is outside the system's direct control. |
| NFR-10 | Maintainability | The backend shall be implemented as a modular monolith with clearly separated modules. The project shall include automated tests, OpenAPI documentation, CI checks, and versioned database migrations. |
| NFR-11 | Deployment | The system shall run in Docker containers and support a Docker Compose setup for local development. The target production environment is AWS. |
| NFR-12 | Localization and time | All timestamps shall be stored and evaluated in UTC. The client shall convert timestamps to the user's local timezone for display. The interface should support multiple languages, including right-to-left layouts. |
| NFR-13 | Observability | The system shall provide structured application logs, error logs, health checks, basic operational metrics, and correlation identifiers for failed requests and background jobs. Sensitive values, passwords, one-time verification codes, and full authentication tokens shall not be written to logs. |
| NFR-14 | Cost control | AI and WhatsApp usage shall be metered and subject to configurable quotas or rate limits. |
| NFR-15 | Recovery and background processing | Background jobs and result notifications shall use a retryable processing mechanism. Operations that must not be lost shall be recorded durably, for example through a database-backed job or outbox pattern. Redis shall not be the only record of a notification or submitted attempt. |
| NFR-16 | Maintenance | Planned maintenance shall be announced in advance where practicable and scheduled during periods of low expected usage. Maintenance events and service interruptions shall be documented. The project does not promise 24/7 human support. |

**Performance measurement notes:**

- Percentiles should be measured under a documented test load, not inferred from a single local request.
- External LLM, email, and WhatsApp provider response times should be measured separately from Qwizly processing time.
- The 1,000-respondent target requires load testing of concurrent joins, answer updates, question broadcasts, submissions, and result processing.

---

## 5. Data and Business Rules

### 5.1 Key Business Rules

| ID | Rule |
|---|---|
| BR-1 | Each quiz belongs to exactly one Quiz Creator. Only that creator and authorized System Admins may manage it. |
| BR-2 | A Quiz Respondent may start an attempt only through a valid shared link, after successful one-time verification, while the quiz is Published and within its availability window. |
| BR-3 | Attempt timing, answer acceptance, and scoring shall be enforced by the server. Client-side values shall not be trusted. |
| BR-4 | A Quiz Respondent shall not exceed the configured attempt limit. A submitted attempt shall be immutable. |
| BR-5 | Correct answers and explanations shall not be sent to the respondent's browser before their configured release time. |
| BR-6 | AI-generated questions shall remain drafts until reviewed and accepted by the Quiz Creator. |
| BR-7 | The score shall be calculated from the points awarded for each question according to the configured scoring policy. Multiple-choice questions shall use all-or-nothing scoring by default. |
| BR-8 | Deleting a quiz shall use soft deletion or an equivalent recoverable data-handling process. Attempt data and personal data shall be retained, deleted, or anonymized according to the retention policy in Section 5.3. |
| BR-9 | All timestamps shall be stored in UTC. The client shall convert timestamps for display. |
| BR-10 | Uploaded source files and extracted source content shall be private and used only for the requested AI generation process. They shall be deleted after that process completes. |
| BR-11 | A Published quiz shall not be edited. A Quiz Creator may duplicate a Published or Closed quiz into a new Draft. An Archived quiz shall not be duplicated. |
| BR-12 | A Draft may be incomplete, but it cannot be Published until all system-controlled publishing validations pass. |
| BR-13 | At least one question is required for publication. Each question must have the answer configuration required by its type and scoring mode. |
| BR-14 | The Quiz Creator is responsible for the wording, clarity, correctness, and suitability of manually authored question content. The system validates structure, not educational quality. |
| BR-15 | An unfinished, unsubmitted attempt is not visible to the Quiz Creator. Creator-facing statistics and attempt lists include submitted attempts only. |
| BR-16 | A Quiz Respondent's attempt is associated with their verified contact identity and can be resumed on another device after re-verification, if the quiz and attempt remain eligible. |
| BR-17 | A submission request shall be idempotent. Repeating the same logical submission shall not create duplicate records or duplicate result notifications. |
| BR-18 | Score release is configured separately from correct-answer and explanation release. Correct answers and explanations are released together. |
| BR-19 | An unanswered question is scored according to the Quiz Creator's configured rule; zero points is the default. |
| BR-20 | Redis may hold shared live-session state and cache data, but MySQL remains the source of truth for attempts, answers, scores, and durable business records. |
| BR-21 | A suspended Quiz Creator cannot authenticate or perform creator actions. Suspension does not delete the creator's retained data. |

### 5.2 Quiz Lifecycle

| State | Description | Permitted next state |
|---|---|---|
| Draft | Quiz is being created or edited. It is not available to respondents. | Published |
| Published | Quiz is published. Access is controlled by the availability window and share-link settings. | Closed |
| Closed | No new attempts may be started or resumed. Submitted results remain available according to release rules. | Archived |
| Archived | Quiz is retained for historical reference according to the retention policy. It cannot be reopened or duplicated. | None |

A quiz may be closed when its configured availability end time is reached or when the Quiz Creator closes it. The system shall enforce the transition on the server. A Closed quiz may be archived by its Quiz Creator. An Archived quiz cannot be republished or duplicated.

### 5.3 Basic Data Retention Policy

The following values are proposed project defaults. They should be reflected in the implementation and privacy notice and reviewed against applicable legal obligations and provider requirements.

| Data | Retention rule |
|---|---|
| Uploaded source files and extracted source content | Delete after the AI generation job completes, whether successful or unsuccessful. |
| Temporary files from interrupted generation jobs | Delete within 24 hours. |
| One-time verification codes | Valid for five minutes; expired and superseded codes are eligible for cleanup. |
| Quiz Respondent contact details | Retain only while needed for participation and result delivery; delete or anonymize 90 days after the quiz is archived. |
| Attempt records | Retain for 12 months after quiz archival, then delete or anonymize. |
| Aggregated statistics | May be retained after anonymization, provided they cannot reasonably identify a Quiz Respondent. |
| Application logs | Retain for 30 days. |
| Security audit logs | Retain for 90 days. |
| Backups | Retain for no more than 30 days. |

Personal data shall be retained only as long as necessary for the service. Deletion or anonymization shall include relevant derived records where practical. Uploaded question images are distinct from temporary AI source files and may be retained while the associated quiz remains available, subject to quiz deletion and retention rules.

### 5.4 Data Entities

| Entity | Key attributes | Relationships |
|---|---|---|
| User | id, name, email, password_hash, role, locale, status, created_at | Owns Quizzes; may be suspended by an Admin |
| QuizRespondent | id, verified_contact, contact_type, verified_at, created_at | Has Attempts; receives OneTimeVerificationCodes |
| Template | id, name, theme, default_settings | Used to create Quizzes |
| Quiz | id, owner_id, title, description, mode, status, availability_start_utc, availability_end_utc, timing_strategy, attempt_limit, passing_score, release_settings | Has Questions, ShareLinks, Attempts, and optional GenerationJobs |
| Question | id, quiz_id, type, text, points, hint, explanation, position, unanswered_scoring_rule | Has Options and accepted text answers |
| Option | id, question_id, text, is_correct, position | Belongs to a Question |
| AcceptedTextAnswer | id, question_id, normalized_value or protected answer value | Belongs to a short-text Question |
| SourceReference | id, generation_job_id, source_label, page_or_location, supporting_excerpt_or_reference | Supports a generated question; does not require retaining the uploaded source file |
| GenerationJob | id, quiz_id, parameters, status, error_code, created_at, completed_at | Produces draft Questions and SourceReferences |
| ShareLink | id, quiz_id, token_hash, expires_at, max_uses, uses_count, revoked_at | Grants access to a Quiz |
| Attempt | id, quiz_id, quiz_respondent_id, status, started_at, submitted_at, score, idempotency_key | Has Answers |
| Answer | id, attempt_id, question_id, text_value, is_correct, points_awarded, saved_at | Belongs to an Attempt and Question; references selected Options where applicable |
| AnswerOptionSelection | answer_id, option_id | Resolves selected options for multiple-choice answers |
| LiveSession | id, quiz_id, host_user_id, join_code_hash, status, current_question_id, host_connection_state | Has participating Attempts |
| OneTimeVerificationCode | id, respondent_contact, channel, code_hash, expires_at, failed_attempts, status | Verifies a Quiz Respondent |
| NotificationJob | id, recipient, channel, notification_type, scheduled_at, status, attempts, last_error | Delivers invitations, verification codes, or result links |
| AuditLog | id, actor_id, action, target_type, target_id, timestamp, correlation_id | Records security-relevant actions |

**Integrity constraints:**

- A Question must belong to exactly one Quiz.
- An Option must belong to exactly one Question.
- An Answer must belong to exactly one Attempt and one Question.
- Selected options must belong to the question being answered.
- A unique constraint or equivalent concurrency-safe mechanism shall prevent duplicate attempt numbers for the same Quiz Respondent and Quiz.
- Submission idempotency shall prevent repeated requests from creating duplicate submissions.
- Correct-answer data shall be excluded from respondent-facing API responses until the configured release time.

---

## 6. External Interface Requirements

### 6.1 User Interface

The responsive React interface shall provide pages or views for:

- Registration, login, password reset, and account management.
- Quiz Creator dashboard and quiz management.
- Quiz builder, question editor, templates, and preview.
- Information-source upload, AI generation progress, generated-question review, and source references.
- Quiz distribution and share-link management.
- Quiz Respondent verification and quiz-taking.
- Attempt resumption and result viewing.
- Live-session host and participant experiences.
- Basic statistics and submitted-attempt results.
- System Admin account management, configuration, audit records, and operational failure visibility.

The interface shall display clear validation and error messages. Quiz availability timestamps shall be displayed in the user's local timezone.

### 6.2 Software Interfaces

| Interface | Purpose |
|---|---|
| REST API (JSON, versioned and documented with OpenAPI) | Frontend/backend communication and ordinary application operations |
| WebSocket (STOMP) | Live-session events, question release, participant updates, and host connection-state notifications |
| LLM provider API | AI question generation from creator-supplied source content |
| Email provider (SMTP or AWS SES) | Verification codes, invitations, password reset, result notifications, and operational notifications |
| WhatsApp Business Platform API | Optional verification codes, quiz links, and result links |
| MySQL | Persistent business data and source of truth |
| Redis | Shared live-session state, caching, and coordination |
| Object storage (AWS S3) | Temporary source processing and retained quiz images, subject to retention rules |

---

## 7. System Architecture and Technology

- **Frontend:** React, HTML, CSS, and Bootstrap.
- **Backend:** Java and Spring Boot, implemented as a modular monolith.
- **Backend modules:** accounts and authorization; quiz management; questions and answers; source processing and AI generation; distribution and share links; respondent verification; attempts and scoring; live sessions; results and notifications; administration and audit.
- **Database:** MySQL, with schema changes managed through versioned migrations.
- **Shared runtime state:** Redis for live-session state, cache, and coordination across backend instances.
- **Real-time communication:** WebSocket/STOMP for live mode.
- **Background processing:** A durable job/outbox mechanism for result notifications and retryable external-provider operations. Redis shall not be the only record of a pending notification.
- **Object storage:** AWS S3 for temporary source processing and question images, with lifecycle rules for temporary source deletion.
- **Local deployment:** Docker and Docker Compose.
- **Production target:** AWS containers, managed MySQL, Redis, object storage, and email delivery service.

**Scaling note:** The 1,000-simultaneous-respondent target may require at least two backend instances behind a load balancer. WebSocket session routing, shared live state, and message broadcasting must work across instances. The target must be validated through load tests; the architecture alone does not prove capacity.

---

## 8. Error Handling

| Situation | Expected behaviour |
|---|---|
| Invalid or missing required input | Return field-level validation messages using a consistent error format containing an error code, message, and relevant details. |
| Attempt to publish an incomplete quiz | Reject publication and identify each missing structural or configuration requirement. Do not evaluate question wording or educational quality. |
| Quiz opened before its start time | Display that the quiz is not yet available and show its exact opening date and time in the respondent's local timezone. |
| Expired or revoked link | Display a clear message that the link is no longer valid. |
| Quiz is Closed | Prevent new attempts and resumption; advise the Quiz Respondent to contact the Quiz Creator where applicable. |
| Time expires during an attempt | Automatically submit the attempt using the latest answers successfully persisted by the server. |
| Network loss during an attempt | Preserve server-saved answers. Allow resumption on the same or another device after verification if the quiz remains Published and the attempt remains eligible. |
| Network loss during submission | Permit safe retry using the same idempotency identity. Return the original submission result if the first request was already processed. |
| Duplicate or concurrent submission | Return the original result for a repeated logical submission; do not create duplicate attempts, scores, or notifications. |
| Live host disconnects or reconnects | Update the host connection state and notify connected Quiz Respondents on every detected state transition. |
| Late join to live session | Show the current live-session state and current question, subject to access and attempt rules. |
| Insufficient AI source material | Explain that the source does not contain enough information and request additional supported material or replacement. |
| AI generation failure or partial success | Display an error. Do not present partial output as a successful question set. Allow retry or regeneration where appropriate. |
| Email or WhatsApp delivery failure | Record delivery status and error details, retry transient failures within the configured limit, and notify the System Admin after repeated failures. |
| Repeated submission-processing failure | Record the failure with a correlation identifier, retain enough diagnostic context for investigation, and notify the System Admin. |
| Unauthorized or forbidden access | Return 401/403 or a non-enumerating 404 response as appropriate, without revealing protected resource details. |
| Unexpected server error | Show a generic user-facing message and log diagnostic details with a correlation identifier. Do not log passwords, verification codes, or full tokens. |

---

## 9. Acceptance Criteria (Examples)

1. **Create a Draft:** Given an authenticated Quiz Creator, when they create a quiz, the system saves it as Draft even if configuration is incomplete.
2. **Minimum question count:** Given a Draft with no questions, when the Quiz Creator attempts to publish it, publication is rejected and the system reports that at least one question is required.
3. **Answer configuration:** Given a question without required answer options or a correct answer, when the Quiz Creator attempts to publish the quiz, publication is rejected and the missing configuration is identified.
4. **Creator content responsibility:** Given a structurally complete quiz with grammatically poor or factually questionable question wording, the system does not claim to validate educational quality; the Quiz Creator remains responsible for the content.
5. **Lifecycle and duplication:** Given a Published or Closed quiz, when its creator duplicates it, the system creates a new Draft. Given an Archived quiz, duplication is rejected.
6. **Early access message:** Given a Published quiz whose start time is in the future, when a Quiz Respondent opens its link, the system displays that the quiz is not yet available and shows the exact opening time in the respondent's local timezone.
7. **One-time verification:** Given a quiz link, when a visitor requests a code, email is selected by default and a code is sent to the supplied email address. A valid code permits access; an expired, invalidated, or incorrect code does not.
8. **Channel switching:** Given a Quiz Respondent who requests a new code, when they switch from email to WhatsApp and WhatsApp is enabled, the replacement code is sent through WhatsApp and the earlier code is invalidated.
9. **AI source grounding:** Given a source that lacks enough information, when generation is requested, the system displays an insufficient-information message and does not produce unsupported questions.
10. **AI source references:** Given a successful AI generation job, each generated question includes a reference to its supporting source location where available.
11. **AI partial failure:** Given a generation job that partially fails, the system displays an error and does not present the partial question set as a successful result.
12. **Source deletion:** Given a completed generation job, the uploaded source file and extracted temporary content are deleted according to the retention policy.
13. **Whole-quiz timer:** Given a quiz with a 30-minute whole-attempt limit, the Quiz Respondent can allocate the time among questions, while the server enforces the overall deadline.
14. **Per-question timer:** Given a quiz with per-question durations, the server enforces each question's configured deadline.
15. **Autosave:** Given a connected respondent changing an answer, the changed answer is persisted no later than one second after the change under normal network conditions.
16. **Cross-device resumption:** Given an unfinished eligible attempt, when the Quiz Respondent verifies the same contact on another device, the system restores the latest server-saved answers.
17. **Closed quiz after disconnection:** Given that the quiz has Closed while the respondent was disconnected, when the respondent reconnects, the system prevents resumption and advises them to contact the Quiz Creator.
18. **Idempotent submission:** Given repeated requests for the same attempt submission, the system stores one submission and returns the same result without duplicating notifications.
19. **Unanswered scoring:** Given a quiz with a configured unanswered-question scoring rule, the system applies that rule consistently during server-side scoring.
20. **Early submission:** Given a Quiz Respondent who submits before the quiz availability end time, the system saves the attempt and calculates its score without waiting for the quiz to end.
21. **Release schedules:** Given independently configured score and answer/explanation release schedules, the system releases each category only when its respective schedule becomes eligible.
22. **Creator visibility:** Given an incomplete unsubmitted attempt, the Quiz Creator cannot view it in attempt lists or basic statistics.
23. **Live late join:** Given a live session that has started, a permitted late-joining Quiz Respondent is shown the current session state and current question.
24. **Host connectivity:** Given a live session, every detected host disconnect and reconnect event is communicated to connected Quiz Respondents.
25. **Basic statistics:** Given a set of submitted attempts, the creator's attempt count, average, highest, lowest, and pass rate match the stored submitted-attempt data. Unsubmitted attempts are excluded.
26. **Notification retries:** Given repeated provider delivery failures, the system retries within its configured limit, records the final failure, and notifies the System Admin.
27. **Admin suspension:** Given a suspended Quiz Creator, authentication and creator actions are denied while the creator's retained data remains intact.
28. **Timezones:** Given a quiz configured using a local date and time, the system stores the equivalent UTC timestamp and displays the correct local time to respondents in another timezone.
29. **Concurrency and live capacity:** Under a documented load test with up to 1,000 simultaneous live respondents, question broadcasts, answer submissions, and leaderboard results meet the defined performance targets.

---

## 10. Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | AI produces inaccurate, ambiguous, or weak questions. | High | High | Require creator review; display source references; allow editing, rejection, and regeneration. |
| R2 | AI generation introduces unsupported information. | Medium | High | Ground generation in supplied sources, request references, validate output, and keep creator approval mandatory. |
| R3 | LLM cost or quota overrun. | Medium | Medium | Apply per-creator quotas, usage metering, request limits, and budget alerts. |
| R4 | WhatsApp policy restrictions or template approval delays. | High | Medium | Keep email as the default channel; make WhatsApp optional; follow opt-in and template requirements. |
| R5 | Email or WhatsApp provider outages delay verification or result notifications. | Medium | High | Retry transient failures, record delivery status, alert the System Admin, and provide clear user-facing feedback. |
| R6 | Concurrency bugs cause duplicate submissions or attempt-limit violations. | Medium | High | Use database constraints, idempotency keys, atomic state transitions, and concurrency tests. |
| R7 | Live mode cannot sustain 1,000 simultaneous respondents. | Medium | High | Load test joins, broadcasts, answer updates, reconnects, and submissions; scale backend instances and shared infrastructure as required. |
| R8 | WebSocket disconnections or host connectivity changes disrupt live sessions. | Medium | High | Track connection state, notify respondents, preserve session state, and test reconnection behaviour. |
| R9 | Unsafe uploaded files or malicious source links compromise processing. | Medium | High | Validate file types and sizes, scan uploads, sandbox extraction, restrict outbound fetching, and protect against SSRF. |
| R10 | Correct answers or private quiz data are exposed prematurely. | Low | High | Keep answer keys server-side, enforce authorization, test API response filtering, and audit security-sensitive actions. |
| R11 | Autosave frequency creates unnecessary load. | Medium | Medium | Save changed answers rather than repeatedly sending unchanged data; load test the one-second persistence target. |
| R12 | Retained personal data creates privacy or storage concerns. | Medium | High | Apply the documented retention schedule, delete temporary sources, anonymize where appropriate, and protect backups and logs. |

---

## 11. Current Implementation Status

The statuses in this section describe the current implementation, not the target requirements in the rest of this document.

| Area | Status |
|---|---|
| Quiz creation and editing, questions, and correct answers | Implemented |
| Sharing quizzes and respondent quiz-taking | Implemented |
| Score and basic statistics calculation | Implemented |
| Registered-user roles and creator/respondent access rules | Implemented |
| Docker Compose deployment, MySQL, and Spring Security | Implemented |
| Responsive user interface | Partially implemented |
| Templates, source upload, and AI generation | Not implemented |
| Live mode | Not implemented |
| WhatsApp distribution and verification | Not implemented |
| Advanced timing, cross-device resumption, and one-second autosave | Not implemented |
| Configurable result and answer/explanation release schedules | Not implemented |
| Email quiz-link distribution and result notifications | Not implemented |
| UTC storage and client-side timezone conversion | Not implemented |
| Redis shared state and multi-instance coordination | Not implemented |
| AWS deployment, CI/CD, monitoring, and operational alerts | Not implemented |
| Data-retention automation and anonymization | Not implemented |

**Status definitions:**

- **Implemented:** The feature is implemented in the current project.
- **Partially implemented:** Some of the feature exists, but the full requirement is not yet satisfied.
- **Not implemented:** The feature has not yet been implemented.

---

