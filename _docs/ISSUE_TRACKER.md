# Issue Tracker

## Format Guidelines

- Each issue has a unique ID (e.g., `ISSUE-001`)
- Status options: `Open`, `In Progress`, `Done`, `Post-MVP`, `Blocked`
- Priority levels: `High`, `Medium`, `Low`

## Target for MVP

### ISSUE-011: Page horizontal scroll in the dashport main area

- **Status:** Open
- **Priority:** High
- **Description:** Create the wrappers and presentational comps for Chart and Tablet.
- **Proposed Solution:** Still in scope. In Development.
- **Location:** `frontend/src/components/dashboard`

## High Priority Bugs

### ISSUE-001: Incorrect Redirect Behavior for Invalid Dashboard Routes

- **Status:** Open
- **Priority:** High
- **Description:** Navigating to wrong dashboard endpoints (e.g., `/dashboard/invalid-theme`) redirects to healthcare instead of showing a 404. We need to elegantly handle typos or missing dashboard configs.
- **Proposed Solution:** Show a 404 page or display a toast notification letting users know they were redirected, and update the URL accordingly.
- **Location:** `frontend/src/app/dashboard/[theme]/page.tsx`

### ISSUE-010: Page horizontal scroll in the dashport main area

- **Status:** Open
- **Priority:** High
- **Description:** The main staging area has horizontal scrolling, this is due to imporer sizing of the parent containers.
- **Proposed Solution:** Delegated to POST-MVP.
- **Location:** `frontend/src/app/dashboard/[theme]/page.tsx`

---

## Post-MVP Enhancements

### ISSUE-002: Automated Config Loading for Navbar

- **Status:** Post-MVP
- **Priority:** Medium
- **Description:** Currently, available themes in the navbar are hardcoded in an array. We need an automated script/function to dynamically load available configs.
- **Current Implementation:** Manual array in `frontend/src/components/Navbar.tsx` lines 17-21
- **Proposed Solution:** File system-based discovery of `*.config.ts` files in the configs directory

### ISSUE-003: Graceful Root Route Handling

- **Status:** Post-MVP
- **Priority:** Medium
- **Description:** Need to handle graceful redirecting from various wrong endpoints to a default dashboard (e.g., `/dashboard/` → default dashboard).
- **Current Implementation:** Simple redirect from `/` to `/dashboard/pharmacy` in `frontend/src/app/page.tsx`
- **Scope:** Expand to handle more redirect scenarios and edge cases, can be coupled with ISSUE-001.

## ISSUE-008: Unified 404 page

- **Status:** Post-MVP
- **Priority:** Low
- **Description:** Create a unified 404 page layout that is consitent app wide, allows for unified branding.
- **Current Implementation:** Handling only a limited number of misdirect cases.
- **Scope:** Couple with changes for ISSUE-001 and ISSUE-003 as a unified feature.

---

## Architectural Debt

### ISSUE-004: Theme Generator Background Color Limitation

- **Status:** Post-MVP
- **Priority:** Low
- **Description:** The theme generator forces card backgrounds to near-white by using `mainBgScale[0]`. This limits flexibility for true dark-mode backgrounds.
- **Location:** `frontend/src/lib/theme-generator.ts` lines 100-102
- **Impact:** Restricts theming options for dashboards that might want darker card backgrounds

### ISSUE-005: API Endpoint Schema Validation

- **Status:** Post-MVP
- **Priority:** Medium
- **Description:** Currently, we only validate the existence of `apiEndpoints` object but not its structure against the expected schema for each theme type.
- **Proposed Enhancement:** Runtime validation of endpoint schemas to catch configuration errors early
- **Location:** `frontend/src/app/dashboard/[theme]/page.tsx` - `getDashboardConfig` function

---

## Future Features

### ISSUE-006: Notification System Database Architecture

- **Status:** Blocked
- **Priority:** Low (Post Authentication)
- **Description:** Implement the relational notification model for production (notifications table + user_notifications junction table) to support multi-user notification delivery and read states.
- **Dependencies:** Requires PostgreSQL setup and user authentication system
- **Context:** Currently using simple mock notifications derived from script status. Descoped until the creation of a user authentication flow. There is currently only 1 user
  being served mock data, leading to deprioritization of a genuine multi client notification system.

### ISSUE-007: Script Details Drill-Down Endpoint

- **Status:** Post-MVP
- **Priority:** Medium
- **Description:** Implement `/api/pharmacy/script_details/{scriptId}` endpoint for detailed script information and history.
- **Context:** Currently removed `/pharmacy/script_status` endpoint in favor of this more logical approach.

### ISSUE-009: Implement an Advanced, Flexible Grid Layout System

- **Status:** Post-MVP
- **Priority:** High
- **Description:** The current `layout` system in the dashboard config is too simplistic. It uses a single `gridCols` property, which does not allow for real-world dashboard designs where components have varying widths and heights or occupy specific grid cells. To support complex UIs, we need to transition to a more expressive layout definition.
- **Impact:** This is a major architectural limitation that prevents us from creating non-uniform, professionally designed dashboard layouts. It currently blocks our ability to faithfully implement more advanced UI/UX mockups.
- **Proposed Solution:** Refactor the `DashboardSection` type in `frontend/src/lib/types.ts` to separate the component definitions from their placement on the grid. We should adopt a model similar to popular grid libraries like `react-grid-layout`. This would involve defining explicit placements (x, y, w, h) for each component.

- **Example of Proposed Config Structure:**

```typescript
  // A future, more robust config structure
  layout: [
    {
      id: "overview-section",
      title: "Overview",
      // The layout is defined by explicit component placements
      gridDefinition: {
        placements: [
          { componentId: "kpi-revenue", x: 0, y: 0, w: 1, h: 1 },
          { componentId: "revenue-chart", x: 0, y: 1, w: 2, h: 2 },
          { componentId: "recent-scripts-table", x: 0, y: 3, w: 3, h: 1 },
        ]
      },
      // The components are now just a list of what's available for this view
      components: [
        { id: "kpi-revenue", type: "metricDisplay", ... },
        { id: "revenue-chart", type: "chartDisplay", ... },
        { id: "recent-scripts-table", type: "tableDisplay", ... },
      ]
    }
  ]
```
