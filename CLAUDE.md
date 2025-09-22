# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.


## CRITICAL: First Steps for New Claude Code Instances

1. **Read project documentation in this order:**
   - This CLAUDE.md file (you're reading it now)
   - README.md for up-to-date project information
   - .kiro/steering/guidelines.md for development principles
   - .kiro/steering/structure.md for project folder structure, file naming conventions, basic architechure
   - .kiro/steering/tech.md for basic tech stack, commom commands, enviroment requirements

2. ** Before making ANY changes:**
   - Review .kiro/steering/guidelines.md for development principles
   - Review .kiro/steering/structure.md for project folder structure, file naming conventions, basic architechure
   - Review .kiro/steering/tech.md for basic tech stack, commom commands, enviroment requirements
   - If working on task that are in .kiro/specs/task.md Check .kiro/specs/ for active specifications

## CRITICAL: Finding the .kiro Directory

**WARNING**: The `.kiro` directory contains essential project specifications and MUST be located before starting any work.

**If you cannot see .kiro with LS command, use these alternative methods:**

1. **Use Glob pattern search**: `Glob` with pattern `**/.kiro/**` to find all files in .kiro
2. **Use Find file**: `find_file` with pattern `.kiro` in the root directory
3. **Use mcp**serena**list_dir**: Try `mcp__serena__list_dir` with path "." and recursive=true
4. **Search for specs**: `Grep` for "requirements.md" or "tasks.md" files

**NEVER assume .kiro doesn't exist just because LS doesn't show it. Always try multiple search methods.**
   - The .kiro folder EXISTS even if not visible in initial LS command

The .kiro directory structure is:

```
.kiro/
├── steering/           # Project guidelines and architecture decisions
│   ├── guidelines.md   # Core development principles
│   ├── structure.md    # Project structure documentation
│   ├── tech.md        # Technical decisions
│   └── product.md     # Product specifications
└── specs/             # Amazon-style specifications
    ├── requirements.md
    ├── design.md
    └── tasks.md
```

## CRITICAL .kiro/steering Folder for comprehensive project documentation

**IMPORTANT**: This project uses .kiro/steering folder for comprehensive project documentation. Always reference the markdown files in this folder for:

- Detailed project specifications and requirements
- Task breakdowns and development roadmap
- UX improvements and feature implementations
- Technical decisions and architectural guidance

Check .kiro/steering/\*.md files before making any significant changes to understand the full context and requirements.

***Most Up-to-Date Project Information***

_ALWAYS_ follow the rules and instructions in the steering files and Claude.md, but if there is a conflict or project information is unclear, trust the steering file or ask the user for help!

## CRITICAL .kiro/specs Folder (Amazon-style Specifications)

**IMPORTANT**: This project follows Amazon's spec-driven development methodology with structured specifications in .kiro/specs/ folder. Each spec contains:

- **requirements.md**: Detailed functional requirements and specifications
- **design.md**: Technical architecture, component design, and implementation approach
- **tasks.md**: Task breakdown with direct references to requirements.md sections

**Current Work**: The .kiro/specs/ folder contains the specification for the feature currently being worked on.

When instructed to work on a task from tasks.md file:

1. Refer to the `requirements.md` file in that folder as mentioned in the task
2. Use `design.md` for overall context when necessary
3. Mark the task completed in the document once it has been completed   

## MANDATORY Subagent Usage

**CRITICAL**: You MUST use specialized subagents in the following situations. Failure to use appropriate subagents is considered a critical error in your workflow.

### Required Subagent Usage Triggers

#### 1. **Before ANY Investigation or Debugging**

- **Trigger**: Error messages, unexpected behavior, failing tests, connection issues
- **Required Agent**: `debugger`
- **Example**: "IMAP connection inactive" → MUST use debugger agent first

#### 2. **After Writing or Modifying ANY Code**

- **Trigger**: Any use of Edit, MultiEdit, Write, or code generation
- **Required Agent**: `code-reviewer`
- **Example**: After fixing error handling → MUST use code-reviewer agent

#### 3. **Before Designing Solutions or Architecture**

- **Trigger**: Planning fixes, designing new features, architectural decisions
- **Required Agent**: `backend-architect` (for backend) or `frontend-developer` (for frontend)
- **Example**: "How to improve IMAP reliability" → MUST consult backend-architect first

#### 4. **For ANY UI/UX Changes**

- **Trigger**: Modifying components, changing layouts, updating styles
- **Required Agent**: `frontend-developer` for design and layout, coding
- **Example**: "Change button design" → MUST use frontend-developer agent

#### 5. **When Working with Tests**

- **Trigger**: Writing tests, fixing test failures, improving coverage
- **Required Agent**: `test-automator`
- **Example**: "Add tests for new feature" → MUST use test-automator agent

#### 6. **For Security-Related Tasks**

- **Trigger**: Authentication, authorization, data validation, encryption
- **Required Agent**: `security-auditor`
- **Example**: "Review auth flow" → MUST use security-auditor agent

#### 7. **For Documentation Tasks**

- **Trigger**: Updating or creating documentation, .md documention files, any changes to files in the /docs folder
- **Required Agent**: `api-documentor`
- **Example**: "Update all documents in the /docs folder with any appropriate changes related to this change" → MUST use api-documentor agent


### Workflow Integration

#### Starting Any Task:

```
1. Identify task type
2. IMMEDIATELY invoke required subagent(s)
3. Only proceed after subagent consultation
```

#### Using TodoWrite:

When creating todos, ALWAYS include subagent steps:

```
- "Investigate IMAP connection issue"
- "Use debugger agent to analyze issue" ← MANDATORY
- "Fix error handling"
- "Use code-reviewer agent to review fix" ← MANDATORY
```

#### Concurrent Subagent Usage:

Use multiple subagents in parallel when applicable:

```xml
<function_calls>
  <invoke name="Task" subagent_type="debugger">...</invoke>
  <invoke name="Task" subagent_type="backend-architect">...</invoke>
</function_calls>
```

### Subagent Checklist for Common Scenarios

#### Investigating Issues:

```
□ Use debugger agent FIRST
□ Then use backend-architect for solution design
□ After fixing, use code-reviewer agent
```

#### Adding New Features:

```
□ Use backend-architect or frontend-developer for design
□ Use test-automator for test planning
□ After implementation, use code-reviewer agent
```

#### UI Changes:

```
□ Use frontend-developer BEFORE making changes
□ Consider responsive design impact
□ After implementation, use code-reviewer agent
```

### Enforcement

**Remember**: Not using required subagents is a CRITICAL ERROR. The workflow should be:

1. Task identified → 2. Subagent consulted → 3. Implementation → 4. Review

**NEVER skip directly to implementation without appropriate subagent consultation.**

## Agent Documentation Standards

### Agent Plans Directory

**MANDATORY**: All temporary agent-generated documentation MUST be created in `\claude-dev\agent_plans\`

**What goes in `/agent_plans`:**

- Temporary implementation plans and migration guides
- Task breakdowns and planning documents
- Investigation reports and analysis
- Implementation strategies and approaches
- Any document generated by agents for planning purposes

**All agents MUST follow this rule**: When creating any temporary planning or analysis document, create it in the `agent_plans` directory, not in the project root.


## Project Documentation Structure

- When completing a Kiro task.md task list review all documents in the /docs folder and make any appropriate changes related to the changes
- If the set of task is large enough to warent it create a new document in the /docs folder documenting the new feature (espeically any new backend services)

# Context7 Workflow Policy

- For ALL questions about library APIs, usage, upgrades, or integration, you MUST fetch and reference official documentation using Context7.
- Whenever asked about a library, ALWAYS include "use context7" at the end of your prompt to request the most up-to-date docs and code examples.
- If using a Model Context Protocol (MCP) server with Context7, you MUST call `resolve-library-id` for the library name first, then use `get-library-docs` to pull in current documentation.
- Never rely only on prior model training or guesses—defer to the retrieved Context7 documentation for accuracy.
- Example:
  - Good: `How do I add schema validation with Zod in Express? use context7`
  - Not allowed: Answers about a library without referencing up-to-date docs from Context7.
- If multiple libraries are involved, repeat the above steps for each before answering.


## Key Commands

### Development
- `npm run dev` - Runs both backend (port 5000) and frontend (port 5173) concurrently
- `npm run build` - Builds both backend and frontend for production
- `npm run install:all` - Install dependencies for root, backend, and frontend

### Backend (./backend)
- `npm run dev` - Run backend with hot reload using tsx
- `npm run test` - Run backend tests with Vitest
- `npm run lint` - Lint backend TypeScript files
- `npm run db:migrate` - Run database migrations
- `npm run db:studio` - Open Drizzle Studio for database management

### Frontend (./frontend)
- `npm run dev` - Run Vite dev server
- `npm run build` - Build production bundle
- `npm run lint` - Lint frontend TypeScript/React files

### Testing
- Backend tests: Run `npm run test` in backend directory
- Single test: Use Vitest pattern matching, e.g., `npm run test -- access.test`

## Architecture Overview


### Shared Code (./shared)
- Type definitions and WebSocket message schemas shared between frontend and backend
- Ensures type safety across the full stack

### Database Schema
- Main tables: connections, statistics, business_processes, access_controls
- Supports multi-tenant architecture with database_id foreign keys
- Comprehensive migration system in `database/migrations/`

## Deployment


## Important Notes

- Always run linting before committing: `npm run lint`
- Database migrations must be run after schema changes
- WebSocket connections have built-in reconnection logic
- The system supports multiple PostgreSQL databases simultaneously
- Frontend and backend can be developed/deployed independently