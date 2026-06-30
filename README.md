# github-contribution-log
# Contribution [#]: [Bug]: Schedule Tab Shows 2 "Security Exception" on new account without admin

**Contribution Number:** [1 / 2 / 3]  
**Student:** Adonai Weldemicael  
**Issue:**(https://github.com/carlos-emr/carlos/issues/2689)
**Status:** Phase IV completed ( but the issue is still blocked. The co-maintainer has reached out to the main maintainer to unblock my issue. I will wait until the end of this week for a response from the main maintainer. If the issue remains blocked, I will move on to a different issue so I can continue contributing)

---

## Why I Chose This Issue
Fits my skills — Java + a bit of SQL, which is what I know.
Right difficulty — "good first issue," 
Diagnosis already done — the reporter traced the cause and named the files, so I can focus on the fix.
It's available — no one assigned, no PR in progress.
Teaches the full workflow — reproduce → fix → test → PR


---

## Understanding the Issue

A new staff account in CARLOS gets a login but no permissions. So when that person logs in, the system lets them through the front door — but the first page it sends them to (the schedule) checks "are you allowed to see appointments?", the answer is "this person has no permissions at all," and instead of saying "access denied" nicely, the page crashes and pops up 2 error alerts.

### Problem Description
When an admin creates a new user account, the account gets a login but no role (no permissions). The user can sign in, but the first page they land on the schedule checks whether they're allowed to view appointments. Since the account has no role, that check fails, the page errors out, and the user sees two "Security Exception" alerts instead of their schedule


### Expected Behavior

A newly created provider account should log in and land on the schedule page normally, with no error alerts.

### Current Behavior

Immediately after login, the schedule page returns HTTP 500 — SecurityException: missing required sec object (_appointment) — shown as two "Security Exception" alerts (or a full "CARLOS Error: 500" page).

### Affected Components

 1. SecurityAddSecurityHelper — creates the security login but never assigns a secUserRole (root cause)
 2. SecurityInfoManagerImpl#hasPrivilege — denies access because the account has no role
 3. BaseProviderViewGate2Action / ViewProviderControl2Action — throws the _appointment privilege check on the schedule landing page

---

## Reproduction Process

### Environment Setup

  1. Container deployment took extremely long to build. The slow step was a line in .devcontainer/development/Dockerfile that ran npx playwright install --with-deps chromium firefox, which downloads the Chromium and Firefox browsers (and system dependencies) at build time — a multi-hour download on a slow connection.
  2. Resolution: Commented out / replaced that line with a simple echo so the build skips the Playwright browser download, which let the container deploy quickly.   3. (Trade-off: the in-container Playwright UI tests won't run until the line is re-enabled — fine here since the bug was reproduced via curl + SQL instead of browser automation.)


### Steps to Reproduce

1. Log in as an admin (carlosdoc / carlos2026 / PIN 2026) and go to Administration → User Management. Create a new provider record, then create a login record for it — but do not assign a role.
2. Log out, then log in with the new account's credentials.
3. Observed result: Immediately after login, the schedule page fails with HTTP 500 — SecurityException: missing required sec object (_appointment) — surfaced as two "Security Exception" alerts (or a full "CARLOS Error: 500" page).

### Reproduction Evidence

- **Commit showing reproduction:** [[Link to commit in your fork]](https://github.com/adulegendary/carlos/tree/fix-issue-2689)
- **Screenshots/logs:** <img width="1397" height="897" alt="image" src="https://github.com/user-attachments/assets/125d6de9-371a-46cf-8617-75881efecb46" />


- **My findings:** The account is created and saved correctly — login itself succeeds (the credentials are found and authenticated). The failure is not missing or unsaved data. The problem is that admin account creation persists the security login row but never creates a matching secUserRole row, so the new user has zero roles. Immediately after login, the schedule landing page (providercontrol) checks for _appointment read permission, which is granted only through roles — with no role, the check fails and BaseProviderViewGate2Action throws SecurityException: missing required sec object (_appointment). Struts has no result mapping for that exception, so it renders the error page as HTTP 500 (shown as two "Security Exception" alerts). Confirmed in the DB that the working seed user carlosdoc has roles (doctor, admin) while a freshly-created account has none — and assigning any role makes the 500 disappear, proving the missing role assignment is the root cause.



---

## Solution Approach

### Analysis
The root cause was that SecurityAddSecurityHelper successfully created and persisted a new security login but never created a corresponding SecUserRole entry. As a result, newly created users could authenticate successfully but had no role-based permissions. When the application redirected the user to the schedule page after login, the authorization check for the _appointment privilege failed, resulting in an HTTP 500 error and two "Security Exception" alerts.
[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution
After creating a new user account, automatically assign the user a default role using the existing SecUserRoleDao. This gives the new user the required permissions to access the application.

### Status
Blocked – Awaiting maintainer confirmation on the default role assignment before making PR.

### Implementation Plan[ [Link to your branch/commits as you work]](https://github.com/adulegendary/carlos/tree/fix-issue-2689)
 **Plan:** [Step-by-step implementation plan]
   1. Edit only SecurityAddSecurityHelper.java.
   2. Add role-assignment after the security persist; guard against double-insert if a role already exists.
   3. Add a unit/integration test asserting a created account gets exactly one secUserRole.


**Match:** [What similar patterns/solutions exist in the codebase?]


**Implement:** [[Link to your branch/commits as you work]](https://github.com/adulegendary/carlos/tree/fix-issue-2689)

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** 
   1. It will follow all the test casess and pass 
   2. Check if the output is co

---

## Testing Strategy

### Unit Tests

- [x] Verified that a newly created account automatically receives a default SecUserRole.
- [x] Verified that duplicate roles are not created if one already exists.
- [x] Verified that existing account creation behavior remains unchanged.

### Integration Test

- [x] Verified that a newly created user can successfully log in after account creation.
- [x] Verified that the schedule page loads without throwing a SecurityException.
### End-to-End Tests
- [ ] End-to-end Playwright tests are still in progress due to the pending maintainer confirmation regarding the default role assignment.
### Manual Testing
- [x] Created a new provider account through the administration page.
- [x] Confirmed that the security login and role were successfully persisted.
- [x] Logged in using the newly created account.
- [x] Verified that the schedule page loaded successfully without displaying the previous HTTP 500 error or "Security Exception" alerts.


---

## Implementation Notes

### Week [1] Progress
The root cause was in SecurityAddSecurityHelper.java — when an admin creates a new provider login, the process() method persisted the Security login record but never created a secUserRole row. The new user could authenticate but had zero role-derived privileges, causing the schedule landing page (providercontrol) to fail its _appointment r check with HTTP 500.

Fix: after securityDao.persist(s), a Secuserrole entity is now saved via SecuserroleDao.save() assigning a default role with activeyn=1. The role constant is marked with a TODO(#2689) pending confirmation of the final value from @yingbull — current placeholder is "doctor".

### Code Changes
    Branch: fix-issue-2689
    Key commits:
    Integeration Test and End to End test in Progress
    83a1d29 — issue fixed and unit test done
    70a6588 — initial fix
    dc15c4406 — local deployment confirmed, bug reproduced consistently

- **Files modified:** [SecurityAddSecurityHelper.java , CarlosUnitTestBase]
- **Key commits:** [[Links to important commits](https://github.com/adulegendary/carlos.git)]
### validation steps taken in Testing Strategy
     Unit test and integeration Test has done
     End to End test is in progress
## Blocking
     We waiting from the maintenare what we need to assign for the role for the admin 
     This is belonges to admin -  @yingbull



### Week [Y] Progress

## Pull Request
 - [ ] Pending maintainer confirmation before submitting the final pull request.

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [ Pending ]
    
This change fixes Issue #2689 by ensuring that newly created user accounts receive a default SecUserRole immediately after the security login is created.Previously, new users could authenticate successfully but had no permissions, causing an HTTP 500 error when accessing the schedule page. The fix also includes automated tests to verify the new behavior and prevent regressions.

**Maintainer Feedback:**[Haven't submitted yet]
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]
 Blocked-
---

## Learnings & Reflections
Through this project, I learned how to debug and fix bugs in an unfamiliar codebase. I developed a structured debugging process: first reproducing the issue, then monitoring and tracing the root cause, applying a fix, and finally verifying the solution through testing. I also learned the importance of following the testing pyramid by writing unit, integration, and end-to-end tests to ensure the fix did not introduce regressions.

### Technical Skills Gained

I gained hands-on experience with several technologies and tools, including Apache Tomcat, the Spring Framework, JUnit for unit testing, and Playwright for end-to-end testing. I also improved my understanding of how these tools work together in a large Java application.

### Challenges Overcome

The biggest challenge was not the technical implementation itself, but understanding a large and unfamiliar codebase. It took time to understand the application's architecture, trace the bug to its root cause, and determine the correct place to implement the fix. 

### What I'd Do Differently Next Time
Next time, I would spend more time understanding the project before diving into the code. I would carefully read the README and project documentation, study the issue requirements, and familiarize myself with the application's architecture. Building a solid understanding upfront would help me identify the root cause more quickly and make more confident code changes.

---

## Resources Used

- CARLOS Project README
- CLAUDE.md contribution guide
- Spring Framework Documentation
- JUnit 5 Documentation
- Playwright Documentation
- GitHub Issue #2689 discussion
- Discussions with the CARLOS project maintainers like @Ben-Heerema
