# Greenfield Workflow: Building from Scratch with SDD

## When to Use Greenfield Workflow

Use this workflow when:
- Starting a brand new project (0-to-1 development)
- Building a proof-of-concept or prototype
- Creating a new microservice or application component
- No existing codebase exists yet

For existing codebases, see [Brownfield Workflow](brownfield.md).

## The 6-Step Greenfield Workflow

### Step 1: Initialize Project

```bash
# Basic initialization
specify init my-project

# With specific AI assistant
specify init my-project --ai claude
specify init my-project --ai cursor-agent
specify init my-project --ai windsurf
specify init my-project --ai copilot

# Initialize in current directory
specify init . --ai claude
# or
specify init --here --ai claude

# With PowerShell scripts (Windows/cross-platform)
specify init my-project --ai copilot --script ps

# Skip git initialization
specify init my-project --ai gemini --no-git

# Debug mode
specify init my-project --ai claude --debug

# With GitHub token (corporate environments)
specify init my-project --ai claude --github-token ghp_your_token_here
```

### Step 2: Establish Project Principles

Launch AI assistant in project directory and use:

```
/speckit.constitution
```

**Purpose**: Create project's governing principles and development guidelines

**Example prompt to give user:**
```
/speckit.constitution Create principles focused on code quality, testing standards, user experience consistency, and performance requirements
```

**After this step - Summarize constitution.md:**

Read `.speckit/constitution.md` and use the **10-point summary template** from SKILL.md:

**Example enhanced summary:**
```
## ✅ Constitution Completed - Here's What Just Happened

### 🎯 Key Decisions Made
1. **Testing requirement: 80%+ code coverage** - Rationale: Ensures reliability and reduces production bugs
2. **TypeScript for all code** - Rationale: Type safety catches errors at compile-time, improves maintainability
3. **Performance target: <3s page load, 60fps** - Rationale: Industry standards for good user experience

### 📋 What Was Generated
- `.speckit/constitution.md`: Complete project principles covering quality, UX, performance, and coding standards

### 🔍 Important Items to Review
1. **80%+ test coverage requirement** - Verify this is achievable for your team's velocity and timeline
2. **WCAG 2.1 AA accessibility** - Confirm you have the expertise or resources for accessibility compliance
3. **TypeScript mandate** - Ensure team is comfortable with TypeScript or plan for learning curve

### ⚠️ Watch Out For
- **Functional programming preference** - How to avoid: May require team training if team is more OOP-oriented
- **Comprehensive JSDoc requirement** - How to avoid: This adds documentation overhead; ensure team buy-in

### 🔄 What This Enables Next
- **Option 1:** Run `/speckit.specify` to define feature requirements - Best if: Principles look good
- **Option 2:** Modify constitution.md - Best if: You need to adjust standards or add principles

📊 **Feature Status:** Initializing project (Constitution created)
   Progress: [●○○○○] 0% features (no features specified yet)

**Your options:** [A] Proceed to specification [B] Adjust principles [C] Explain testing requirements [D] Show full status
```

### Step 3: Create the Specification

```
/speckit.specify
```

**Purpose**: Describe what to build - focus on WHAT and WHY, not tech stack

**Example prompt to give user:**
```
/speckit.specify Build an application that can help me organize my photos in separate photo albums. Albums are grouped by date and can be re-organized by dragging and dropping on the main page. Albums are never in other nested albums. Within each album, photos are previewed in a tile-like interface.
```

**Required: Gherkin acceptance criteria per user story.** Every user story `/speckit.specify` produces must carry Given/When/Then scenarios (see SKILL.md § "Acceptance Criteria: Gherkin Format") — the happy path plus that story's edge/negative cases. If the generated `specify.md` lacks them (some spec-kit templates emit prose-only stories), add them before moving on: read each story, write 1–4 scenarios in the story's own vocabulary, and don't let `/speckit.plan` run against a story with no acceptance criteria.

Example, for the photo-album story "user re-organizes albums by dragging and dropping":
```gherkin
Scenario: Reorder albums via drag-and-drop
  Given the user has 3+ albums on the main page
  When the user drags an album to a new position and drops it
  Then the album list re-renders in the new order
  And the new order persists after a page reload

Scenario: Drop target rejects a nested-album drop
  Given the user is dragging Album A over Album B
  When the user drops Album A onto Album B
  Then Album A does NOT become nested inside Album B
  And both albums remain at the top level
```

**After this step - Summarize specify.md:**

Read `.speckit/features/[feature-number]/specify.md` and use the **10-point summary template** from SKILL.md.

**Also ask:** "Do you have multiple features to implement, or is this a single-feature project?" This helps establish feature tracking.

**Example enhanced summary:**
```
## ✅ Specify Completed - Here's What Just Happened

### 🎯 Key Decisions Made
1. **Drag-and-drop interface for album organization** - Rationale: Natural, intuitive interaction for visual content organization
2. **Local-only storage (no cloud)** - Rationale: Privacy-first approach, no server costs, works offline
3. **SQLite for metadata** - Rationale: Lightweight, serverless, perfect for local desktop apps

### 📋 What Was Generated
- `.speckit/features/001-photo-album-manager/specify.md`: 3 core features, 5 user stories (9 Gherkin acceptance-criteria scenarios), 4 success criteria, 3 edge cases

### 🔍 Important Items to Review
1. **2-second load time for 1000 photos** - Verify this is achievable with thumbnails and lazy loading; may need caching strategy
2. **HEIC format support** - Check if browser/platform supports HEIC natively or if conversion library needed
3. **Keyboard navigation requirement** - Review specific keyboard shortcuts needed for accessibility compliance

### ⚠️ Watch Out For
- **No nested albums constraint** - How to avoid: Make sure UI clearly communicates this limitation to users
- **100+ photo drag-and-drop performance** - How to avoid: May need virtual scrolling or batching for large selections

### 🔄 What This Enables Next
- **Option 1:** Run `/speckit.plan` to design technical architecture - Best if: Requirements are clear
- **Option 2:** Run `/speckit.clarify` to explore edge cases - Best if: You want to refine edge case handling first
- **Option 3:** Modify specify.md - Best if: Requirements need adjustment

📊 **Feature Status:** photo-album-manager (Specified) → Next: (ask user if more features planned)
   Progress: [●○○○○] 20% | Completed: 0 of 1 features | Dependencies: None

**Your options:** [A] Proceed to planning [B] Modify requirements [C] Clarify edge cases [D] Show full status

**Do you have multiple features planned?** If yes, tell me what other features you're considering so I can track them.
```

### Step 4: Create Technical Implementation Plan

```
/speckit.plan
```

**Purpose**: Provide tech stack and architecture choices

**Example prompt to give user:**
```
/speckit.plan The application uses Vite with minimal number of libraries. Use vanilla HTML, CSS, and JavaScript as much as possible. Images are not uploaded anywhere and metadata is stored in a local SQLite database.
```

**After this step - Summarize plan.md:**

Read `.speckit/features/[feature-number]/plan.md` and use the **10-point summary template** from SKILL.md.

**Example enhanced summary:**
```
## ✅ Plan Completed - Here's What Just Happened

### 🎯 Key Decisions Made
1. **Vite + Vanilla JavaScript** - Rationale: Fast dev experience without framework lock-in; keeps bundle small
2. **sql.js for SQLite in browser** - Rationale: Client-side database without server; full SQL capabilities
3. **Web Workers for image processing** - Rationale: Prevents UI blocking when generating thumbnails for large albums

### 📋 What Was Generated
- `.speckit/features/001-photo-album-manager/plan.md`: Complete technical architecture with 5 implementation phases, 3 key dependencies

### 🔍 Important Items to Review
1. **sql.js browser compatibility** - Verify it works in target browsers (especially Safari); may need fallback to IndexedDB
2. **Web Worker browser support** - Check that all target browsers support Workers; have fallback for older browsers
3. **10-day implementation timeline** - Validate this is realistic for your team; includes buffer time?

### ⚠️ Watch Out For
- **sharp library is Node.js only** - How to avoid: Use browser-native Canvas API or WebAssembly solution like @squoosh/lib
- **Service Worker requires HTTPS** - How to avoid: Plan for HTTPS in local dev (Vite supports this) and production

### 🔄 What This Enables Next
- **Option 1:** Run `/speckit.tasks` to break down into actionable tasks - Best if: Architecture is solid
- **Option 2:** Modify plan.md - Best if: You want to adjust tech stack or dependencies
- **Option 3:** Run `/speckit.analyze` to check consistency - Best if: Want to validate against constitution

📊 **Feature Status:** photo-album-manager (Planned) → Next: (same feature continues to tasks)
   Progress: [●●○○○] 40% | Completed: 0 of 1 features | Dependencies: None

**Your options:** [A] Proceed to tasks [B] Adjust architecture [C] Analyze consistency [D] Show full status
```

### Step 5: Break Down into Tasks

```
/speckit.tasks
```

**Purpose**: Create actionable task list from implementation plan

**After this step - Summarize tasks.md:**

Read `.speckit/features/[feature-number]/tasks.md` and use the **10-point summary template** from SKILL.md.

**Example enhanced summary:**
```
## ✅ Tasks Completed - Here's What Just Happened

### 🎯 Key Decisions Made
1. **18 tasks organized into 5 categories** - Rationale: Logical grouping by technical domain for clear separation of concerns
2. **Database setup as early critical path** - Rationale: Many features depend on database; completing early unblocks parallel work
3. **High complexity tasks: 3 (Database integration, DnD state, Image caching)** - Rationale: These require careful architecture and testing

### 📋 What Was Generated
- `.speckit/features/001-photo-album-manager/tasks.md`: 18 actionable tasks across UI, DnD, Database, Image handling, Testing

### 🔍 Important Items to Review
1. **10-day timeline estimate** - Realistic for team? Includes testing time? May need buffer for unknowns
2. **Critical path dependencies** - Database must complete first; this could block progress if delayed
3. **Testing tasks at the end** - Consider moving some testing earlier (TDD approach) for better quality

### ⚠️ Watch Out For
- **3 high-complexity tasks** - How to avoid: Allocate senior developers; consider pairing or extra time estimates
- **All UI before DnD** - How to avoid: This sequential approach may delay integration; consider overlapping if possible

### 🔄 What This Enables Next
- **Option 1:** Run `/speckit.implement` to start implementation - Best if: Tasks are clear and prioritized
- **Option 2:** Reorder tasks - Best if: You want to adjust priorities or dependencies
- **Option 3:** Run `/speckit.checklist` for quality verification - Best if: Want comprehensive testing criteria
- **Option 4:** Run `/speckit.analyze` - Best if: Want to validate task coverage against requirements

📊 **Feature Status:** photo-album-manager (Tasked - ready for implementation) → Next: Implementation
   Progress: [●●●○○] 60% | Completed: 0 of 1 features | Dependencies: None

**Your options:** [A] Start implementation [B] Reorder tasks [C] Generate checklist [D] Show full status
```

### Step 6: Execute Implementation

```
/speckit.implement
```

**Purpose**: Execute all tasks and build the feature

## Optional Enhancement Commands

Use these for additional quality and validation:

### Clarify Underspecified Areas

```
/speckit.clarify
```

**When to use**: After `/speckit.specify`, before `/speckit.plan`
**Purpose**: Identify and clarify ambiguous requirements

### Analyze Consistency & Coverage

```
/speckit.analyze
```

**When to use**: After `/speckit.tasks`, before `/speckit.implement`
**Purpose**: Cross-artifact consistency and coverage analysis

### Generate Quality Checklists

```
/speckit.checklist
```

**Purpose**: Create custom quality checklists that validate requirements completeness, clarity, and consistency (like "unit tests for English")

## Managing Multiple Features

### Adding New Features

When working with multiple features in a greenfield project, Claude automatically tracks feature status and helps you manage them.

**Natural Language Commands:**

```
"Add a new feature for user notifications"
"Let's work on email integration next"
"Move the admin dashboard feature before reporting"
"Show me all planned features"
"What features depend on user-authentication?"
```

**Claude will:**
1. Detect your feature management request
2. Ask clarifying questions (priority, dependencies, description)
3. Update the feature list
4. Show updated feature status dashboard

### Feature Status Tracking

After establishing multiple features (typically after first `/speckit.specify`), Claude tracks:

- **Current feature** being worked on
- **Completed features** (100% through workflow)
- **Upcoming features** (not yet started)
- **Dependencies** between features
- **Progress** for each feature (Specified, Planned, Tasked, In Progress, Complete)

**Example status display:**
```
📊 Project Feature Status Dashboard

🎯 CURRENT FEATURE
├─ user-authentication (Planning - 40% complete)
│  ├─ ✅ Requirements specified
│  ├─ 🔄 Implementation plan in progress
│  ├─ ⏸️  Tasks not started
│  └─ ⏸️  Implementation not started
│  Dependencies: database-setup ✅

✅ COMPLETED FEATURES (1)
└─ database-setup (100% complete)

📋 UPCOMING FEATURES (2)
├─ profile-management (depends on: user-authentication)
└─ admin-dashboard (depends on: user-authentication, profile-management)
```

### Reordering Features

**User request:** "Let's do profile-management before admin-dashboard"

**Claude response:**
```
Current order:
1. user-authentication (In Progress)
2. admin-dashboard (Pending)
3. profile-management (Pending)

Proposed new order:
1. user-authentication (In Progress)
2. profile-management (Pending)
3. admin-dashboard (Pending - will update dependency)

Is this correct? (yes/no)
```

### Feature Dependencies

Claude automatically tracks dependencies when you mention them:

```
"User authentication depends on database-setup"
"Profile management needs user-authentication first"
```

**Dependency checking:**
```
📊 Can we start profile-management?
   Checking dependencies...
   ✅ user-authentication (complete)
   ✅ database-setup (complete)

   All dependencies satisfied! Ready to proceed.
```

For complete feature management capabilities, see [Feature Management Guide](feature_management.md).

## Artifacts Generated

After running SDD commands, the following artifacts are created:

### Project Structure
```
project-name/
├── .speckit/
│   ├── constitution.md      # Project principles
│   ├── features/
│   │   └── 001-feature-name/
│   │       ├── specify.md    # Requirements & user stories
│   │       ├── plan.md       # Technical implementation plan
│   │       ├── tasks.md      # Actionable task list
│   │       └── checklist.md  # Quality validation checklist
│   └── .claude/
│       └── commands/         # Slash command definitions
└── [your application code]
```

### Key Artifacts to Reference

1. **constitution.md**: Project-wide principles and guidelines
2. **specify.md**: Requirements and user stories for current feature
3. **plan.md**: Technical implementation plan with architecture decisions
4. **tasks.md**: Task breakdown for implementation
5. **checklist.md**: Quality validation criteria

## Workflow Best Practices

### For Users New to SDD

1. **Start small**: Begin with a simple feature to learn the workflow
2. **Follow the sequence**: Don't skip steps (constitution → specify → plan → tasks → implement)
3. **Be specific in specify**: The more detailed your requirements, the better the output
4. **Review artifacts**: After each step, review the generated artifacts before proceeding
5. **Use clarify**: Don't hesitate to use `/speckit.clarify` if requirements are unclear

### For Experienced Users

1. **Parallel exploration**: Use creative exploration phase for multiple implementation approaches
2. **Custom checklists**: Define project-specific quality gates with `/speckit.checklist`
3. **Analyze before implement**: Always run `/speckit.analyze` to catch issues early
4. **Iterate on constitution**: Refine project principles as you learn

### For Enterprise Teams

1. **Establish constitution early**: Include organizational constraints, compliance requirements, design systems
2. **Version control everything**: All `.speckit/` artifacts should be in Git
3. **Use feature branches**: Let Git branches drive feature detection
4. **Document tech stack constraints**: Be explicit in `/speckit.plan` about approved technologies

## Advanced Usage Patterns

### Multi-Stack Exploration

For creative exploration of different tech stacks:

```bash
# Create multiple feature branches
git checkout -b feature-001-react
/speckit.plan Use React with TypeScript...
/speckit.tasks
/speckit.implement

git checkout -b feature-001-vue
/speckit.plan Use Vue 3 with Composition API...
/speckit.tasks
/speckit.implement
```

### Corporate/Enterprise Setup

```bash
# Initialize with corporate GitHub token
specify init my-project --ai claude --github-token $GITHUB_TOKEN

# Constitution with enterprise constraints
/speckit.constitution
- Must use approved cloud providers (AWS, Azure)
- Follow internal design system
- Comply with SOC2 requirements
- Use approved open source licenses only
```

## Command Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `specify init` | Initialize project | Start of new project |
| `/speckit.constitution` | Set principles | After init, before any feature work |
| `/speckit.specify` | Define requirements | Start of each feature |
| `/speckit.clarify` | Clarify ambiguities | After specify, if requirements unclear |
| `/speckit.plan` | Create tech plan | After specify (and optional clarify) |
| `/speckit.tasks` | Break down tasks | After plan |
| `/speckit.analyze` | Validate consistency | After tasks, before implement |
| `/speckit.checklist` | Quality gates | Any time to define validation criteria |
| `/speckit.implement` | Execute tasks | After tasks (and optional analyze) |

## Example: Complete Greenfield Workflow

```bash
# Step 1: Install and initialize
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
specify init photo-organizer --ai claude

# Step 2: In your AI agent
/speckit.constitution Create principles focused on:
- Simple, intuitive user interfaces
- Data privacy (no cloud uploads)
- Fast, responsive performance
- Minimal dependencies

# Step 3: Specify the feature
/speckit.specify Build a photo organization app with drag-and-drop albums,
date-based grouping, and tile-based photo previews within albums

# Step 4: Technical planning
/speckit.plan Use Vite, vanilla JS, HTML5 drag-and-drop API,
and SQLite for local storage

# Step 5: Break down tasks
/speckit.tasks

# Step 6: Implement
/speckit.implement
```

## Next Steps

After completing greenfield implementation:
- Run tests and validation
- Review generated code against constitution
- Iterate with additional features following the same workflow
- Consider using `/speckit.checklist` to define quality standards for future work
