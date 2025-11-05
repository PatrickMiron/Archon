# Claude Code Agent Creator - Planning Session Summary

**Date:** 2025-01-04
**Project:** Archon Nextria Fork
**Participants:** User + Claude Code (with specialist agent consultations)

## Session Overview

This planning session created a comprehensive implementation plan for adding a Claude Code Agent Creator feature to the Archon UI. The feature enables users to visually design, test, and deploy custom PydanticAI agents without writing backend code.

## Key Deliverables

### 1. Project Created
- **Name:** Archon Nextria Fork
- **Project ID:** `8c38ce1d-6456-491b-a4ff-2f3eaba40172`
- **GitHub:** https://github.com/coleam00/Archon

### 2. Documentation Created (6 Documents)

1. **Architecture Overview** - System design, tech stack, core modules
2. **Development Setup Guide** - Commands, workflows, environment setup
3. **API Documentation** - RESTful endpoints, service patterns
4. **Frontend Development Patterns** - TanStack Query, React patterns, vertical slices
5. **Backend Development Patterns** - Service layer, error handling, code quality
6. **Claude Code Agent Creator - Feature Specification** - Complete feature spec

### 3. Tasks Created (31 Total)

Distributed across 7 implementation phases:

#### Phase 1: Database & Backend Foundation (5 tasks)
- Create database schema for agent templates
- Create Pydantic models for agent templates
- Implement AgentTemplateService
- Implement AgentFactoryService
- Create agent templates API routes

#### Phase 2: Dynamic Agent Runtime (3 tasks)
- Create DynamicAgent class
- Implement tool execution sandbox
- Implement dynamic agent registration

#### Phase 3: Frontend Feature Structure (6 tasks)
- Create frontend feature scaffolding
- Define agent TypeScript types
- Implement agent service layer
- Create agent query hooks
- Create AgentCard component
- Create AgentList component

#### Phase 4: Agent Creator Wizard (6 tasks)
- Create multi-step wizard container
- Create Step 1: Basic Info form
- Create Step 2: System Prompt editor
- Create Step 3: Tool Builder component
- Create Step 4: Schema Builder component
- Create Step 5: Test & Deploy panel

#### Phase 5: Agent Testing Infrastructure (1 task)
- Create agent test API endpoint

#### Phase 6: Agent Management UI (4 tasks)
- Create AgentsView main page
- Create Agent Detail View
- Add agent export/import functionality
- Implement agent usage analytics

#### Phase 7: Integration & Polish (6 tasks)
- Add agents route to navigation
- Write backend unit tests
- Write frontend component tests
- Create agent creator user documentation
- Optimize bundle size and code splitting
- Add agents route to navigation

## Architecture Design

### Database Schema

New table: `archon_agent_templates`

```sql
{
    "id": UUID,
    "name": TEXT,
    "description": TEXT,
    "model": TEXT,                      -- "openai:gpt-4o", etc.
    "system_prompt": TEXT,
    "tools": JSONB,                     -- [{name, description, parameters, code}]
    "dependencies_schema": JSONB,       -- JSON schema for dependency dataclass
    "output_schema": JSONB,             -- JSON schema for output model
    "rate_limiting_enabled": BOOLEAN,
    "max_retries": INTEGER,
    "created_by": TEXT,
    "created_at": TIMESTAMP,
    "updated_at": TIMESTAMP,
    "is_active": BOOLEAN,
    "tags": TEXT[],
    "category": TEXT                    -- "document", "code", "analysis", "custom"
}
```

### Backend Components

**New Files:**
```
python/src/
├── server/
│   ├── api_routes/
│   │   └── agent_templates_api.py       # CRUD endpoints
│   ├── services/
│   │   ├── agent_template_service.py    # Business logic
│   │   └── agent_factory_service.py     # Dynamic instantiation
│   └── models/
│       └── agent_template.py            # Pydantic models
├── agents/
│   ├── dynamic_agent.py                 # Runtime agent class
│   └── tool_executor.py                 # Safe execution sandbox
```

**Key Services:**
- `AgentTemplateService` - CRUD operations for templates
- `AgentFactoryService` - Dynamically instantiate agents from templates
- `DynamicAgent` - Runtime agent that loads configuration from database
- `ToolExecutor` - Sandboxed execution environment for user tools

### Frontend Components

**New Feature Structure:**
```
archon-ui-main/src/features/agents/
├── components/
│   ├── AgentCard.tsx                    # Display agent template
│   ├── AgentList.tsx                    # Grid of agent cards
│   ├── AgentCreatorWizard.tsx           # Multi-step creation wizard
│   ├── AgentConfigForm.tsx              # Basic config
│   ├── AgentToolBuilder.tsx             # Visual tool builder
│   ├── AgentSchemaBuilder.tsx           # JSON schema editor
│   ├── AgentTestPanel.tsx               # Test agent
│   └── AgentDeploymentPanel.tsx         # Activate/deactivate
├── hooks/
│   └── useAgentQueries.ts               # Query hooks + keys
├── services/
│   └── agentService.ts                  # API integration
├── types/
│   └── agent.ts                         # TypeScript interfaces
└── views/
    ├── AgentsView.tsx                   # Main view
    └── AgentCreatorView.tsx             # Creator workflow
```

### API Endpoints

```
GET    /api/agents/templates              # List all templates
POST   /api/agents/templates              # Create template
GET    /api/agents/templates/{id}         # Get single template
PUT    /api/agents/templates/{id}         # Update template
DELETE /api/agents/templates/{id}         # Delete template
POST   /api/agents/templates/{id}/test    # Test execution (SSE)
POST   /api/agents/templates/{id}/activate # Toggle active status
```

### Security Features

**Tool Execution Sandbox:**
- Whitelist of allowed imports
- Timeout limits per tool (10 seconds default)
- Memory limits
- No file system access (except via MCP tools)
- Restricted exec() with custom namespace
- JSON schema validation for all inputs
- Code static analysis before saving

## Agent Creator Wizard Flow

### Step 1: Basic Information
- Name, description, category, tags
- Model selection (OpenAI, Anthropic)
- Live preview card

### Step 2: System Prompt
- Rich text editor
- Template variables (`{project_id}`, `{user_id}`)
- Preview panel
- Character count

### Step 3: Tool Configuration
- List of tools with add/remove
- Per-tool editor:
  - Name, description
  - Parameters (JSON schema)
  - Implementation (Python code with Monaco editor)
  - MCP tool selector

### Step 4: Schemas
- Dependencies schema (JSON schema builder)
- Output schema (JSON schema builder)
- TypeScript type preview

### Step 5: Test & Deploy
- Dynamic input form from schema
- Test execution with streaming
- Performance metrics (latency, tokens)
- Activation toggle

## Key Patterns & Standards

### Frontend Patterns
- **Vertical Slice Architecture** - Each feature owns its full stack
- **TanStack Query** - All data fetching with smart polling
- **Optimistic Updates** - Using nanoid for stable local IDs
- **Glassmorphism Styling** - Matching existing Archon design
- **Direct Database Values** - No mapping layers

### Backend Patterns
- **Service Layer Pattern** - API Route → Service → Database
- **Fail Fast Philosophy** - Detailed errors over graceful failures
- **ETag Caching** - Browser-native HTTP caching
- **Microservices Architecture** - Agent service, MCP service, API service

### Code Quality
- **Backend:** Ruff (linting), MyPy (typing), Pytest (testing)
- **Frontend:** Biome (features dir), ESLint (legacy), Vitest (testing)
- **Target:** 80%+ test coverage

## Specialist Agent Contributions

### Explore Agent (Codebase Analysis)
Provided comprehensive reports on:
- Backend agent infrastructure (BaseAgent, DocumentAgent, RagAgent)
- MCP server integration patterns
- Frontend UI component patterns
- Data fetching architecture

### Project Analyst
Analyzed:
- Current project structure
- Feature requirements
- Integration points
- Success metrics

### Architect
Designed:
- Database schema
- Service architecture
- Security model
- API structure

### React Specialist
Defined:
- Component hierarchy
- Form patterns
- State management
- UI/UX workflow

### Python Specialist
Specified:
- Backend services
- Dynamic agent runtime
- Tool execution sandbox
- Testing strategy

### TypeScript Specialist
Outlined:
- Type definitions
- Service layer
- Query hooks
- Integration patterns

## Implementation Timeline Estimate

**Phase 1-2 (Backend Foundation):** 2-3 weeks
- Database, services, dynamic agent runtime

**Phase 3-4 (Frontend Core):** 3-4 weeks
- Feature structure, wizard UI

**Phase 5-6 (Testing & Management):** 2 weeks
- Testing infrastructure, management UI

**Phase 7 (Polish):** 1-2 weeks
- Integration, testing, documentation, optimization

**Total Estimate:** 8-11 weeks for full implementation

## Success Metrics

- Users can create functional agents in <5 minutes
- 80%+ test coverage for new code
- No performance degradation in existing features
- Agent execution completes within configured timeouts
- Zero security vulnerabilities in sandbox

## Future Enhancements

- Agent marketplace for sharing templates
- Visual flow builder for complex agent workflows
- Multi-agent coordination and handoffs
- Built-in debugging and logging viewer
- Version control for agent templates
- A/B testing for agent configurations

## Files Created During Session

1. `ARCHITECTURE.md` - Architecture documentation
2. `DEVELOPMENT_SETUP.md` - Development guide
3. `API_DOCUMENTATION.md` - API reference
4. `FRONTEND_PATTERNS.md` - Frontend guide
5. `BACKEND_PATTERNS.md` - Backend guide
6. `FEATURE_SPEC.md` - Feature specification
7. `AGENT_CREATOR_PLANNING_SESSION.md` - This summary

## Next Steps

1. Review and approve the plan
2. Prioritize phases based on business needs
3. Assign tasks to development team
4. Set up development environment
5. Begin Phase 1: Database & Backend Foundation
6. Regular check-ins after each phase completion

## Resources

- **Project URL:** Check Archon UI at http://localhost:3737 (when running)
- **API Documentation:** Available at http://localhost:8181/docs (when running)
- **MCP Server:** http://localhost:8051 (when running)
- **Agent Service:** http://localhost:8052 (when running with --profile agents)

## Team Assignments

- **Python Dev Specialist:** 11 backend tasks
- **React Specialist:** 13 frontend tasks
- **TypeScript Dev Specialist:** 3 integration tasks
- **User/Documentation:** 1 documentation task

---

**Session Completed:** All planning tasks complete, implementation ready to begin.

**Contact:** For questions about this plan, refer to the project documentation in Archon or the task list in project management.
