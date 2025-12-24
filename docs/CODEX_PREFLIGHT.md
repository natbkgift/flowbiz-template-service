# Codex Pre-flight Checklist

## Purpose
This checklist ensures all changes align with FlowBiz template standards before merging.

## Pre-Commit Checklist

### Code Quality
- [ ] Code follows existing style conventions
- [ ] No unused imports or variables
- [ ] No hardcoded credentials or secrets
- [ ] Error handling is appropriate
- [ ] Logging is consistent with existing patterns

### Testing
- [ ] All new code has corresponding tests
- [ ] Tests are deterministic (no random data, no network calls)
- [ ] `pytest -q` passes locally
- [ ] No tests were removed without justification

### Linting
- [ ] `ruff check .` passes without errors
- [ ] Code is formatted consistently
- [ ] Import order follows convention (stdlib, third-party, local)

### Documentation
- [ ] README.md updated if public interface changes
- [ ] Inline comments added for complex logic
- [ ] Docstrings present for public functions
- [ ] Environment variables documented in .env.example

## API Contract Verification

### Endpoints
- [ ] `/healthz` returns `{"status": "ok", "service": "...", "version": "..."}`
- [ ] `/v1/meta` returns `{"service": "...", "environment": "...", "version": "...", "build_sha": "..."}`
- [ ] No new endpoints added outside approved scope

### Response Format
- [ ] All responses are valid JSON
- [ ] Status codes are appropriate (200, 404, 500)
- [ ] Error responses follow schema

## Environment Conventions

### Variable Naming
- [ ] Runtime config uses `APP_*` prefix
- [ ] Metadata uses `FLOWBIZ_*` prefix
- [ ] No other prefixes introduced

### Configuration
- [ ] All new variables in `.env.example`
- [ ] Defaults provided in code
- [ ] Sensitive values not committed

## Docker & Infrastructure

### Docker
- [ ] `docker compose up` starts successfully
- [ ] Services can communicate internally
- [ ] Logs are visible with `docker compose logs`

### Ports
- [ ] API runs on internal port 8000
- [ ] Nginx exposes 80/443 externally
- [ ] No port conflicts

### Security
- [ ] Security headers present in Nginx config
- [ ] No security vulnerabilities introduced
- [ ] HSTS configured for production

## Scope Compliance

### ✅ In Scope
- [ ] Changes are template infrastructure
- [ ] No business-specific logic added
- [ ] Follows FlowBiz conventions

### ❌ Out of Scope (Must NOT be present)
- [ ] No authentication/authorization code
- [ ] No database models or migrations
- [ ] No billing or payment logic
- [ ] No user management
- [ ] No async workers or queues
- [ ] No cron jobs or scheduled tasks
- [ ] No UI/frontend code

## CI/CD

### Workflows
- [ ] `.github/workflows/ci.yml` runs successfully
- [ ] `.github/workflows/guardrails.yml` reports no violations
- [ ] `.github/workflows/pr-labels.yml` suggests correct labels

### PR Template
- [ ] Summary section completed
- [ ] Testing section completed
- [ ] Appropriate persona labels applied

## Documentation

### Core Docs
- [ ] `docs/PROJECT_CONTRACT.md` reflects current API
- [ ] `docs/DEPLOYMENT.md` includes new deployment steps (if any)
- [ ] `docs/GUARDRAILS.md` updated with new rules (if any)
- [ ] This file updated with new checks (if any)

### README
- [ ] Installation instructions current
- [ ] Usage examples work
- [ ] Links are valid

## Final Verification

### Local Testing
```bash
# Clean start
docker compose down -v
docker compose up --build

# Test endpoints
curl http://localhost:8000/healthz
curl http://localhost:8000/v1/meta

# Run checks
ruff check .
pytest -q
```

### Deployment Simulation
```bash
# Test production compose
docker compose -f docker-compose.yml -f docker-compose.prod.yml config

# Verify no errors in output
```

## Sign-off

### Developer
- [ ] All checklist items reviewed
- [ ] No scope creep
- [ ] Ready for review

### Reviewer
- [ ] Code reviewed
- [ ] Tests validated
- [ ] Documentation approved
- [ ] Scope compliance verified

## Notes
- This checklist is a guide, not a blocker
- Use judgment for edge cases
- Document any deviations in PR description
- Update this checklist as standards evolve
