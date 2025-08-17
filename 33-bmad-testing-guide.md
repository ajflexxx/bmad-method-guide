# BMad Testing Guide - Quality Advisory Framework v5.0

## Overview

The BMad Testing Guide introduces the revolutionary Quality Advisory Framework in version 5.0, transforming traditional QA from a blocking gatekeeper to an advisory partner. This guide explains the philosophy, tools, and practices that enable teams to maintain high quality while preserving autonomy.

## Core Philosophy: Advisory Over Blocking

### The Paradigm Shift

#### Traditional QA (v4.x and Earlier)
- **Role**: Quality Gatekeeper
- **Approach**: Pass/Fail decisions that block progress
- **Authority**: Veto power over releases
- **Result**: Bottlenecks and friction

#### Quality Advisory (v5.0)
- **Role**: Quality Advisor
- **Approach**: Risk-based recommendations
- **Authority**: Teams decide their quality bar
- **Result**: Informed decisions and team ownership

### Key Principles

1. **Inform, Don't Block**: Provide comprehensive quality data without preventing progress
2. **Risk-Based Prioritization**: Focus testing effort where risk is highest
3. **Team Autonomy**: Teams choose when to address or waive quality concerns
4. **Continuous Improvement**: Learn from decisions and outcomes
5. **Transparent Documentation**: All quality decisions are documented

## The Test Architect Role

### Meet Quinn - Your Test Architect

Quinn represents the evolution from QA Engineer to Test Architect:

```yaml
agent:
  name: Quinn
  title: Test Architect & Quality Advisor
  focus: 
    - Risk assessment and mitigation
    - Test strategy and architecture
    - Requirements traceability
    - Quality metrics and reporting
```

### Core Responsibilities

1. **Risk Assessment**: Identify and quantify project risks
2. **Test Architecture**: Design comprehensive test strategies
3. **Requirements Coverage**: Ensure all requirements have test coverage
4. **Quality Advisory**: Provide recommendations without blocking
5. **Metrics & Reporting**: Track quality trends and outcomes

## Risk-Based Testing Framework

### Risk Calculation

```
Risk Score = Probability × Impact
```

### Risk Matrix

| Probability/Impact | LOW (1) | MEDIUM (2) | HIGH (3) | CRITICAL (4) |
|-------------------|---------|------------|----------|--------------|
| **VERY_HIGH (4)** | 4       | 8          | 12       | 16           |
| **HIGH (3)**      | 3       | 6          | 9        | 12           |
| **MEDIUM (2)**    | 2       | 4          | 6        | 8            |
| **LOW (1)**       | 1       | 2          | 3        | 4            |

### Testing Depth by Risk

- **Critical Risk (12-16)**: Comprehensive testing required
  - Full unit test coverage (>90%)
  - Integration testing for all paths
  - E2E testing for critical journeys
  - Performance and security testing
  
- **High Risk (9-11)**: Thorough testing recommended
  - Good unit test coverage (>70%)
  - Integration testing for main paths
  - E2E testing for primary journeys
  
- **Medium Risk (5-8)**: Standard testing sufficient
  - Reasonable unit test coverage (>50%)
  - Basic integration testing
  - Smoke tests for E2E
  
- **Low Risk (1-4)**: Basic testing acceptable
  - Core unit tests
  - Minimal integration testing

## Quality Gate System

### Gate Statuses

1. **PASS**: All quality criteria met
2. **CONCERNS**: Quality issues identified but not blocking
3. **FAIL**: Critical issues that should be addressed
4. **WAIVED**: Issues acknowledged but explicitly waived

### Gate File Structure

Quality gates are stored in `docs/qa/gates/`:

```yaml
schema: 1
story: "1.3"
gate: CONCERNS
status_reason: "Test coverage below target but risk is low"
reviewer: "Quinn"
updated: "2025-08-16T10:00:00Z"
top_issues:
  - priority: MEDIUM
    category: TEST_COVERAGE
    issue: "Unit test coverage at 65% (target: 80%)"
    recommendation: "Add tests for edge cases"
    risk_if_unaddressed: "Potential bugs in error handling"
waiver:
  active: false
  reason: null
  approved_by: null
```

### Waiver Process

When teams choose to proceed despite concerns:

```yaml
waiver:
  active: true
  reason: "Low-risk feature, deadline critical"
  approved_by: "Product Owner"
  risk_accepted: true
  follow_up: "Address in next sprint"
```

## Test Architecture Patterns

### Test Pyramid Strategy

```
        /\           E2E Tests (5%)
       /  \          - Critical user journeys
      /    \         - Smoke tests
     /      \        
    /________\       Integration Tests (25%)
   /          \      - API contracts
  /            \     - Database operations
 /              \    - Service interactions
/________________\   Unit Tests (70%)
                     - Business logic
                     - Algorithms
                     - Validation rules
```

### Test Level Selection

#### Unit Tests
**When to Use**:
- Pure functions
- Business logic
- Data transformations
- Validation rules
- Algorithms

**Example**:
```javascript
// Good unit test candidate
function calculateDiscount(price, discountPercent) {
  return price * (1 - discountPercent / 100);
}
```

#### Integration Tests
**When to Use**:
- API endpoints
- Database operations
- Service communication
- External integrations

**Example**:
```javascript
// Good integration test candidate
async function createUser(userData) {
  const user = await db.users.create(userData);
  await emailService.sendWelcome(user.email);
  return user;
}
```

#### E2E Tests
**When to Use**:
- Critical user journeys
- Cross-system workflows
- Payment flows
- Authentication flows

**Example Scenarios**:
- User registration → email verification → login
- Product search → add to cart → checkout → payment

## Requirements Traceability

### Mapping Requirements to Tests

Every requirement should map to one or more tests:

```yaml
requirement_matrix:
  story: "2.1 - User Authentication"
  requirements:
    - id: "AC1"
      description: "Users can login with email/password"
      test_mappings:
        - type: unit
          file: "auth.test.js"
          test: "validates email format"
          coverage: PARTIAL
        - type: integration
          file: "auth-api.test.js"
          test: "POST /login returns token"
          coverage: FULL
        - type: e2e
          file: "login-flow.e2e.js"
          test: "complete login journey"
          coverage: FULL
      overall_coverage: FULL
```

### Coverage Levels

- **FULL**: All scenarios tested
- **PARTIAL**: Main scenarios tested, edge cases missing
- **MINIMAL**: Basic happy path only
- **NONE**: No test coverage
- **NOT_TESTABLE**: Cannot be automated (e.g., visual design)

### Given-When-Then Documentation

Used for documenting test scenarios (NOT for writing test code):

```yaml
test_scenario:
  given: "A registered user with valid credentials"
  when: "They submit the login form"
  then: "They receive an auth token and are redirected"
  covers: ["AC1", "AC2"]
```

## Non-Functional Requirements (NFR) Testing

### Performance Requirements

```yaml
performance_requirements:
  api_response_time:
    target: "< 200ms p95"
    test_approach: "Load testing with k6"
    measurement: "CloudWatch metrics"
  page_load_time:
    target: "< 3s on 3G"
    test_approach: "Lighthouse CI"
    measurement: "Web Vitals"
```

### Security Requirements

```yaml
security_requirements:
  authentication:
    requirement: "JWT with 15min expiry"
    test_approach: "Security test suite"
    tools: ["OWASP ZAP", "Custom scripts"]
  data_protection:
    requirement: "PII encrypted at rest"
    test_approach: "Database audit"
    verification: "Compliance scan"
```

### Reliability Requirements

```yaml
reliability_requirements:
  uptime:
    target: "99.9%"
    monitoring: "Synthetic monitoring"
    alerts: "PagerDuty integration"
  error_rate:
    target: "< 0.1%"
    monitoring: "Application insights"
    dashboard: "Grafana"
```

## Testing Workflow Integration

### Planning Phase

```yaml
planning_integration:
  story_creation:
    - Define acceptance criteria
    - Identify test scenarios
    - Optional: Early risk assessment
  architecture_review:
    - Test Architect consultation
    - Identify testing challenges
    - Define test strategy
```

### Development Phase

```yaml
development_integration:
  before_coding:
    - Review test scenarios
    - Set up test structure
    - Define test data needs
  during_coding:
    - Write tests alongside code
    - Continuous test execution
    - Update test coverage
  code_review:
    - Test coverage check
    - Test quality review
    - Edge case identification
```

### Review Phase

```yaml
review_integration:
  test_architect_review:
    - Requirements traceability check
    - Risk assessment
    - Test coverage analysis
    - Quality gate creation
  team_decision:
    - Review quality gate
    - Decide on issues
    - Document waivers if needed
```

## Quality Metrics and Reporting

### Key Metrics

1. **Test Coverage**: Percentage of code covered by tests
2. **Requirement Coverage**: Percentage of requirements with tests
3. **Defect Density**: Bugs per 1000 lines of code
4. **Test Pass Rate**: Percentage of passing tests
5. **Risk Coverage**: Percentage of high-risk areas tested

### Quality Dashboard

```yaml
quality_dashboard:
  current_sprint:
    test_coverage: 75%
    requirement_coverage: 90%
    open_defects: 3
    test_pass_rate: 98%
    high_risk_coverage: 100%
  trends:
    coverage_trend: "improving"
    defect_trend: "decreasing"
    quality_gates:
      passed: 8
      concerns: 3
      failed: 1
      waived: 2
```

### Reporting Cadence

- **Daily**: Test execution results
- **Sprint**: Quality gate summaries
- **Release**: Comprehensive quality report
- **Quarterly**: Quality trends and insights

## Best Practices

### For Development Teams

1. **Engage Early**: Involve Test Architect in planning
2. **Test as You Go**: Don't leave testing until the end
3. **Focus on Risk**: Prioritize testing high-risk areas
4. **Document Decisions**: Record why you waive concerns
5. **Learn from Issues**: Use production issues to improve tests

### For Test Architects

1. **Be Advisory**: Recommend, don't mandate
2. **Provide Context**: Explain risks clearly
3. **Offer Alternatives**: Multiple ways to address issues
4. **Track Outcomes**: Monitor waived issues in production
5. **Continuous Improvement**: Refine risk assessments

### For Product Owners

1. **Understand Risks**: Read quality gates carefully
2. **Make Informed Decisions**: Balance quality vs. speed
3. **Document Rationale**: Record why you accept risks
4. **Plan Follow-ups**: Schedule debt reduction
5. **Learn from Data**: Use metrics to guide decisions

## Common Scenarios

### Scenario 1: Tight Deadline, Quality Concerns

```yaml
situation:
  deadline: "Critical customer demo tomorrow"
  quality_gate: CONCERNS
  issues:
    - "Missing edge case tests"
    - "Performance not validated"
    
decision_framework:
  assess_risk:
    - What's the probability of issues?
    - What's the impact if issues occur?
  options:
    - Waive and monitor closely
    - Delay demo for testing
    - Partial release with known limitations
  recommended: "Waive with close monitoring and follow-up sprint"
```

### Scenario 2: High-Risk Feature

```yaml
situation:
  feature: "Payment processing"
  risk_score: 16 (CRITICAL)
  
test_strategy:
  mandatory:
    - Comprehensive unit tests
    - Full integration test suite
    - Security testing
    - Performance testing
    - E2E payment flows
  optional:
    - Chaos engineering
    - Penetration testing
```

### Scenario 3: Legacy Code Refactoring

```yaml
situation:
  context: "Refactoring 5-year-old module"
  existing_tests: "None"
  
approach:
  phase_1: "Add characterization tests"
  phase_2: "Refactor with safety net"
  phase_3: "Improve test coverage"
  risk_mitigation: "Incremental releases with monitoring"
```

## Tooling Recommendations

### Test Frameworks

```yaml
unit_testing:
  javascript: ["Jest", "Mocha", "Vitest"]
  python: ["pytest", "unittest"]
  java: ["JUnit", "TestNG"]
  
integration_testing:
  api: ["Postman", "REST Assured", "Supertest"]
  database: ["DbUnit", "TestContainers"]
  
e2e_testing:
  web: ["Cypress", "Playwright", "Selenium"]
  mobile: ["Appium", "Detox"]
```

### Quality Tools

```yaml
coverage_tools:
  javascript: ["NYC", "C8", "Jest Coverage"]
  python: ["Coverage.py", "pytest-cov"]
  
static_analysis:
  javascript: ["ESLint", "SonarJS"]
  python: ["Pylint", "Flake8"]
  
performance:
  load_testing: ["k6", "JMeter", "Gatling"]
  monitoring: ["Datadog", "New Relic", "AppDynamics"]
```

## Migration from v4.x

### Key Changes

1. **QA Agent → Test Architect**: Role and responsibility shift
2. **Blocking → Advisory**: Quality gates don't block
3. **New Tasks**: Five new testing-related tasks
4. **Risk-Based**: Testing depth based on risk assessment
5. **Team Autonomy**: Teams decide quality bar

### Migration Steps

1. **Update Agent References**: Replace QA with Test Architect
2. **Adopt Advisory Mindset**: Train teams on new philosophy
3. **Implement Risk Assessment**: Start using risk-profile task
4. **Create Quality Gates**: Use new qa-gate task
5. **Track Decisions**: Document all waivers and outcomes

## Summary

The BMad v5.0 Testing Guide represents a fundamental shift in quality assurance philosophy. By moving from blocking gates to advisory recommendations, teams gain autonomy while maintaining quality through informed decision-making. The Test Architect provides expert guidance, risk assessment, and comprehensive testing strategies, but ultimately teams decide their quality bar based on context and constraints.

Key takeaways:
- Quality is everyone's responsibility
- Risk drives testing depth
- Advisory gates preserve team autonomy
- Documentation ensures accountability
- Continuous improvement through metrics

This approach balances quality with delivery speed, enabling teams to make pragmatic decisions while maintaining transparency and accountability.