You are helping me design an E2E test framework (Playwright + TypeScript, separate repository)
for the application in this workspace. Analyse this repository READ-ONLY and produce a technical
brief that another engineer will use without access to this code.

## Hard rules
1. Do NOT modify, create or delete any file. Read only.
2. Anonymise everything:
   - Replace product, client, company, team and people names with placeholders:
     <APP>, <CLIENT>, <TEAM>, <PERSON>.
   - Replace hostnames, URLs, IPs, tenant/client IDs, emails and storage account names with
     <host-dev>, <tenant-id>, <email> and so on. Keep only the SHAPE (e.g. "https://<host-dev>/api/v1/").
   - Business domain: describe it generically only ("back-office case management",
     "B2B ordering"), never the client's real terms if they reveal who it is.
   - Name entities and screens generically ("Case", "Invoice", "Approval list"); keep a real name
     only when it is a generic technical term.
3. Never output secrets or values from .env files, appsettings, application*.yml, key vaults,
   pipeline variables or connection strings. Say only THAT a setting exists and what it is for.
4. Code snippets: at most 10 lines each, only when the shape matters (e.g. how the test id
   attribute is used, how the token is stored). Anonymise them too.
5. Tag every fact: [code] seen in source, [config] seen in config/pipeline, [inferred] your
   deduction, [unknown] not determinable from this repo. Do not guess; "unknown" is a fine answer.
6. If front end and back end live in different repositories, cover what is here and list what
   is missing in section 12.

## Output: one Markdown document, these sections, terse bullet points, no filler prose

### 1. Repository shape
Mono-repo or not; languages; frameworks WITH versions (Angular, Java, Spring Boot, Node, build
tools, package managers); folder layout in 5-10 lines.

### 2. Front end
- Routing: main routes (generic names), lazy-loaded modules, route guards (and what they check).
- UI component library (Angular Material, PrimeNG, Kendo, ag-Grid, custom...) and which widgets
  are used heavily: tables/grids (paging, sorting, virtual scroll), dialogs/overlays (CDK overlay
  renders outside the component), date pickers, autocompletes, selects, file upload/download,
  rich text editors, iframes, shadow DOM, canvas/charts.
- Forms: reactive/template, validation display, wizards/multi-step flows.
- State management (NgRx, signals, services), HTTP interceptors and what they do.
- Loading/readiness signals: global spinner, skeletons, progress bars, "busy" flags, how the UI
  shows that a request is pending. This decides how tests wait, so be precise.
- Notifications: toasts/snackbars, how long they stay, whether they block clicks.
- Real-time: WebSockets, SSE, polling and what they update.
- i18n: languages, library, locale-dependent formats (dates, numbers, currency), default locale.
- Animations (can they be disabled?), feature flags, global error handler, console errors or
  warnings that appear during normal use.

### 3. Testability hooks
- Which attribute is used for test ids (data-testid, data-test, data-cy, id, none)? Rough coverage:
  which areas have them and which do not.
- Quality of accessible names/roles (aria-label, labels bound to inputs, button texts) as an
  alternative locator strategy.
- Dynamically generated ids or CSS classes that would make locators unstable.

### 4. Authentication and authorisation
- Mechanism: Microsoft Entra ID/MSAL, other OIDC/OAuth (Keycloak...), form login, Windows auth.
- Where the token/session lives: cookie, localStorage, sessionStorage, IndexedDB, memory.
  (Important: Playwright storageState does not keep sessionStorage.)
- Token lifetime, silent refresh, MFA, conditional access, lockout policy, logout behaviour.
- Roles/permissions: list them generically, how they change the UI (hidden vs disabled vs 403
  page), whether one user can have several roles.
- How the API authenticates (bearer, cookie, API key) and whether a token can be obtained without
  the UI (client credentials, ROPC, test endpoint).

### 5. Back end and API
- Style (REST/GraphQL/gRPC), base path/context path, versioning scheme.
- OpenAPI/Swagger: available? at what path (shape only)? Generated from code or hand-written?
- Error response format (RFC 7807 ProblemDetails or custom), correlation/trace id headers.
- Pagination, filtering and sorting conventions.
- Main resources (generic names) with available operations (create/read/update/delete); mark the
  ones useful for creating and cleaning up test data.
- Any test-only, seed, reset or admin endpoints.
- Asynchronous processing (queues, schedulers, batch jobs): what it does and how a client can tell
  it finished.
- Rate limiting, CORS, file size limits, idempotency.

### 6. Data
- Database type, migrations tool (Flyway/Liquibase/EF), seed/reference data and where it comes from.
- Can tests create and delete their own data through the API? Soft delete? Cascades?
- Unique constraints that would collide when tests run in parallel (names, numbers, emails).
- Multi-tenancy, data isolation between users/organisations.
- Time zones, server vs client time, date-dependent business rules (deadlines, expiry).

### 7. Environments and infrastructure
- Environments (placeholders), hosting (Azure App Service, AKS, VMs...), whether they are public
  or behind VPN/private network.
- Differences between environments (feature flags, mocked integrations, data resets).
- External integrations (payments, e-mail, SMS, maps, document generation, other internal
  systems): which are real vs stubbed in test environments, and how a test could observe them
  (e.g. how to read a sent e-mail).

### 8. CI/CD
- Pipelines present (Azure DevOps YAML, GitHub Actions...): stages, triggers, agent pools
  (Microsoft-hosted vs self-hosted), artifacts, where tests run today.
- Deployment flow to test environments and how a test run could be triggered after a deploy.

### 9. Existing tests
- Unit/integration tests per layer (Jasmine/Karma/Jest, JUnit, Testcontainers, REST Assured...):
  frameworks, rough count, what they cover.
- Any E2E tests already (Protractor, Cypress, Selenium, Playwright): patterns used, coverage,
  how they run, signs of flakiness (retries, sleeps, skipped tests), TODOs about tests.
- Reusable assets: test data builders, fixtures, mocks, test users described in config (names
  of variables only).

### 10. Key user journeys
5-10 most important flows, each as: actor role -> steps -> expected outcome. Mark smoke
candidates. Mention multi-user flows (role A creates, role B approves), wizards, exports/imports,
PDF/report generation, e-mail/notification steps.

### 11. E2E risks
Things that will make E2E tests hard or flaky here: long operations, eventual consistency,
animations, timing-dependent UI, third-party widgets, captcha, file downloads, popups/new tabs,
known bugs, heavy grids, anything you noticed.

### 12. Unknowns
What you could not determine from this repository and which kind of role would likely know
(front-end dev, back-end dev, DevOps, product owner).

### 13. Top 10 facts for the framework design
The ten findings from above that most affect how an E2E framework for this app should be built,
one line each.
