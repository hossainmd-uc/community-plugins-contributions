# Contribution [1]: Announcements: Dynamic colour changing/countdown feature #7546

**Contribution Number:** [1 / 2 / 3]  
**Student:** [Your Name]  
**Issue:** https://github.com/backstage/community-plugins/issues/7546  
**Status:** [Phase I] [In Progress]

---

## Why I Chose This Issue

This issue asks for a new UI feature within the announcements plugin to introduce dynamic, time-sensitive banner color shifts and countdowns that alert developers to high-priority maintenance or release schedules. Implementing this prevents users from missing critical system updates by visually indicating urgency (shifting from blue to yellow, and ultimately red) as a deadline approaches. 

I chose this issue because I previously worked with frontend systems in my intro to software development class, where I created functional components for frontend features. This project offers a perfect opportunity to expand on those skills by building an interactive, state-driven UI feature inside a major, real-world open-source framework, helping me learn how to manage dynamic component states based on real-time date calculations.

---

## Understanding the Issue

### Problem Description

Currently, in the announcements workspace, wthere is a working NewAccoundementBanner file. However, the current banner is very static and lacks visual clarity to alert users of the impending deadline.

### Expected Behavior

Based on the until_date of the announcement, the color of the banner should change and reflect the urgency level, increasing in intensity as the day approaches. A proposed format was: "4-5 days" = Blue (status="info"), "3 days" = Yellow (status="warning"), "1-2 days" = Red (status="danger"), "Past date but still active" = Green (status="success").

### Current Behavior

There is no visual color change indication based on due date distance.

### Affected Components

The Announcements Workspace.

---

## Reproduction Process

### Environment Setup

Initially, I attempted to deploy the app using yarn. However, some components were failing. As I brainstormed with AI, I realized that there was an alpha directory, which has a newer frontend implementation. This prompted me to use yarn:next to resolve the issue and start the app.

### Steps to Reproduce

From the workspaces/announcements/ directory, start the app using the new frontend system:

1. yarn start:next
2. Open http://localhost:3000 and sign in as Guest.
3. Navigate to Admin Portal in the left sidebar.
4. Click Create announcement and fill in a title, excerpt, and set an Active Until date that is 1–2 days from today. Submit the form.
5. Navigate to the Home page in the left sidebar.
6. Observed result: The NewAnnouncementBanner appears at the top of the page with a static blue (status="info") color regardless of how close the Active Until date is. There is no color change, no urgency indication, and no countdown — the banner looks identical whether the deadline is 5 days away or 1 day away.

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** The banner renders with a static blue color regardless of the `until_date` value. No visual urgency change is present.
- **My findings:** The `AnnouncementBanner` component in `NewAnnouncementBanner.tsx` renders `<Alert status="info" ...>` with the status hardcoded at line 156. The `Announcement` type already has an `until_date?: string | null` field (defined in `announcements-common/src/types.ts`), but the banner component never reads it. There is no date-based logic anywhere in the banner rendering path.

---

## Solution Approach

### Analysis

The root cause is a single hardcoded value in `AnnouncementBanner` inside `workspaces/announcements/plugins/announcements/src/components/NewAnnouncementBanner/NewAnnouncementBanner.tsx` at line 156:

```tsx
<Alert status="info" ... />
```

The `status` prop controls the banner color. It is never computed — it is always `"info"` (blue). The `until_date` field already exists on the `Announcement` object passed into the component via props, but is simply never used for styling.

### Proposed Solution

Add a helper function that computes the correct Alert status by comparing until_date against the current date using the luxon library (already imported in the file). Replace the hardcoded status="info" with the computed value.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The `NewAnnouncementBanner` always renders in blue regardless of how close or past the announcement's `until_date` is. It should change color to reflect urgency: blue when far out, yellow when approaching, red when imminent, and green when the date has passed but the announcement is still active.

**Match:** The `<Alert>` component from `@backstage/ui` already accepts `status="info"`, `status="warning"`, `status="danger"`, and `status="success"` — these map exactly to the four states the issue requests. The `luxon` `DateTime` library is already imported in `NewAnnouncementBanner.tsx` and used for date comparisons elsewhere in the same file. No new dependencies are needed.

**Plan:**
1. In `NewAnnouncementBanner.tsx`, add a helper function `getAlertStatus(until_date?: string | null)` above the `AnnouncementBanner` component that returns the appropriate status string based on days remaining: no `until_date` or more than 4 days away → `"info"`, 3–4 days → `"warning"`, 1–2 days → `"danger"`, past due date → `"success"`.
2. Call `getAlertStatus(announcement.until_date)` inside `AnnouncementBanner` — the `announcement` object is already available via props.
3. Replace the hardcoded `status="info"` on line 156 with the computed value.
4. Add new test cases to `NewAnnouncementBanner.test.tsx` covering each threshold.

**Implement:** [Link to issue fix branch](https://github.com/hossainmd-uc/community-plugins/tree/fix-issue-7546)

**Review:**
- [ ] `yarn tsc` passes with no type errors
- [ ] `yarn lint` passes
- [ ] `yarn prettier:check` passes
- [ ] No files modified outside the scope of this issue
- [ ] Commit message follows project convention (e.g. `feat(announcements): add dynamic banner color based on until_date`)
- [ ] Changeset added via `yarn changeset` before PR submission

**Evaluate:** New tests will be added to the existing `NewAnnouncementBanner.test.tsx` file, which already uses `renderInTestApp` with a mocked `announcementsApiRef`. Tests will cover each color threshold: `until_date` more than 4 days away, 3 days away, 1 day away, past due with `active: true`, and no `until_date` set. Run with `yarn test` from `workspaces/announcements/`.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
