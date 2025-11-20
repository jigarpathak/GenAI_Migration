# Complete GenAI Prompts Library for Azure Migration
## All Phases: Assessment, Planning, Migration, Security, Monitoring, Optimization

---

## Table of Contents

1. **Assessment Phase Prompts**
2. **Planning Phase Prompts**
3. **Migration Phase Prompts**
4. **Security Phase Prompts**
5. **Monitoring Phase Prompts**
6. **Optimization Phase Prompts**

---

## PHASE 1: ASSESSMENT PHASE PROMPTS

### Prompt 1.1: Infrastructure Inventory Analysis

```
ROLE: Senior Cloud Infrastructure Architect with 15+ years of experience in enterprise migrations

CONTEXT: 
Company: Contoso Insurance
Industry: Financial Services
Scale: 450 servers, 87 applications across 2 data centers
Compliance: PCI-DSS, SOC 2, HIPAA

TASK:
Analyze the following infrastructure inventory data and provide comprehensive assessment:

INPUT DATA:
[Paste CSV/JSON export from asset management system]

REQUIRED ANALYSIS:

1. ASSET CATEGORIZATION
   - Compute resources (physical/virtual)
   - Storage systems (SAN, NAS, DAS)
   - Network devices (switches, routers, firewalls)
   - Database servers
   - Application servers
   - Middleware platforms

2. TECHNICAL ASSESSMENT (per asset)
   - Operating system and version
   - End-of-life (EOL) status
   - Hardware specifications (CPU, RAM, Disk)
   - Current utilization metrics
   - Performance baseline
   - License type and count

3. CLOUD COMPATIBILITY SCORING (1-10 scale)
   - OS compatibility with Azure
   - Application architecture compatibility
   - Database migration complexity
   - Network requirements feasibility
   - Storage migration complexity

4. MIGRATION STRATEGY RECOMMENDATION
   - Rehost (Lift & Shift)
   - Replatform (Lift & Optimize)
   - Refactor (Re-architect)
   - Repurchase (Replace with SaaS)
   - Retire (Decommission)
   - Retain (Keep on-premises)

5. EFFORT ESTIMATION
   - Migration complexity (Low/Medium/High)
   - Estimated migration hours
   - Required team size and skills
   - Recommended timeline

6. RISK IDENTIFICATION
   - Technical risks (compatibility, performance)
   - Business risks (downtime, data loss)
   - Compliance risks (data residency, audit)
   - Mitigation strategies for each risk

7. AZURE SERVICE RECOMMENDATIONS
   - Compute: VM size/series, App Service, AKS, Container Instances
   - Storage: Managed Disks, Blob Storage, Azure Files
   - Database: SQL Database, Cosmos DB, Database for PostgreSQL
   - Network: VNet, ExpressRoute, VPN Gateway, Load Balancer

8. COST ESTIMATES
   - Current on-premises annual cost (estimated)
   - Projected Azure annual cost
   - Cost savings potential
   - Optimization opportunities

OUTPUT FORMAT:
Provide results as structured JSON with the following schema:

{
  "inventory_summary": {
    "total_assets": number,
    "asset_breakdown": {},
    "eol_status": {},
    "cloud_readiness_distribution": {}
  },
  "detailed_assessment": [
    {
      "asset_id": "string",
      "asset_name": "string",
      "category": "string",
      "os": "string",
      "specifications": {},
      "cloud_compatibility_score": number,
      "migration_strategy": "string",
      "azure_target_service": "string",
      "effort_hours": number,
      "complexity": "string",
      "risks": [],
      "estimated_cost": {
        "current_annual": number,
        "azure_annual": number,
        "savings": number
      },
      "confidence_score": number
    }
  ],
  "recommendations": {
    "priority_migrations": [],
    "quick_wins": [],
    "complex_migrations": [],
    "optimization_opportunities": []
  }
}

CONSTRAINTS:
- Provide confidence scores (0-100%) for each assessment
- Highlight assets requiring manual review (confidence < 70%)
- Flag compliance-sensitive workloads
- Identify dependencies requiring deeper analysis
- Recommend assessment tools (Azure Migrate, Service Map)

DELIVERABLES:
1. JSON assessment report
2. Executive summary (3-5 key findings)
3. Risk heat map data
4. Prioritized migration list
5. Quick wins identification
```

---

### Prompt 1.2: Application Dependency Mapping

```
ROLE: Enterprise Architect specializing in application dependency analysis and service mapping

CONTEXT:
Company: Contoso Insurance
Applications: 87 enterprise applications
Infrastructure: Distributed across 2 data centers
Integration: Complex inter-service dependencies

TASK:
Analyze application dependencies and create comprehensive dependency map for migration sequencing.

INPUT DATA:
1. Application inventory (JSON/CSV)
2. Network flow logs (last 30 days)
3. Service communication patterns
4. Database connection strings
5. API documentation
6. Middleware configurations

[Paste application data and network logs]

REQUIRED ANALYSIS:

1. DEPENDENCY IDENTIFICATION
   - Service-to-service dependencies (synchronous)
   - Message queue dependencies (asynchronous)
   - Database dependencies
   - Shared resource dependencies (file shares, config)
   - External API dependencies (3rd party)
   - Authentication/Authorization dependencies

2. DEPENDENCY CHARACTERISTICS
   For each dependency:
   - Connection type (HTTP, TCP, UDP, Message Queue)
   - Communication pattern (request/response, pub/sub, batch)
   - Frequency (requests per minute/hour/day)
   - Data volume transferred
   - Latency requirements (SLA)
   - Error handling mechanism
   - Criticality (High/Medium/Low)

3. DEPENDENCY GRAPH ANALYSIS
   - Fan-in count (services depending on this service)
   - Fan-out count (services this service depends on)
   - Critical path identification
   - Single points of failure
   - Circular dependencies detection
   - Bottleneck services
   - Chatty communication patterns

4. MIGRATION IMPACT ANALYSIS
   - Services that can be migrated independently
   - Services requiring parallel migration
   - Services with blocking dependencies
   - Recommended migration waves/groups
   - Cutover sequencing strategy

5. RISK ASSESSMENT
   - Breaking change risks
   - Integration failure risks
   - Performance degradation risks
   - Data consistency risks
   - Fallback complexity

6. AZURE INTEGRATION PATTERNS
   - API Management for service exposure
   - Service Bus for async messaging
   - Event Grid for event-driven patterns
   - Azure Functions for glue code
   - Application Gateway for routing

OUTPUT FORMAT:
{
  "dependency_graph": {
    "nodes": [
      {
        "service_id": "string",
        "service_name": "string",
        "service_type": "string",
        "criticality": "string",
        "dependencies_count": number,
        "dependent_services_count": number
      }
    ],
    "edges": [
      {
        "source_service": "string",
        "target_service": "string",
        "connection_type": "string",
        "communication_pattern": "string",
        "frequency": "string",
        "latency_requirement": "string",
        "criticality": "string"
      }
    ]
  },
  "critical_services": [
    {
      "service_name": "string",
      "criticality_score": number,
      "reason": "string",
      "dependencies_count": number,
      "dependent_services": [],
      "single_point_of_failure": boolean
    }
  ],
  "circular_dependencies": [
    {
      "services_in_loop": [],
      "severity": "string",
      "resolution_strategy": "string"
    }
  ],
  "migration_waves": [
    {
      "wave_id": number,
      "wave_name": "string",
      "services": [],
      "dependencies_satisfied": boolean,
      "parallel_migration_possible": boolean,
      "estimated_duration": "string",
      "risks": []
    }
  ],
  "integration_recommendations": {
    "api_gateway_needed": boolean,
    "message_queue_strategy": "string",
    "data_sync_approach": "string",
    "hybrid_connectivity": "string"
  }
}

CONSTRAINTS:
- Identify ALL dependencies (technical and business)
- Flag dependencies crossing security boundaries
- Highlight compliance-sensitive data flows
- Recommend Azure services for each integration pattern
- Provide fallback strategies for critical dependencies

DELIVERABLES:
1. Dependency graph (JSON + visual description)
2. Critical services list with mitigation
3. Migration wave recommendation
4. Integration pattern guide
5. Cutover runbook outline
```

---

### Prompt 1.3: Application Portfolio Assessment

```
ROLE: Application Portfolio Analyst with expertise in cloud readiness assessment

CONTEXT:
Company: Contoso Insurance
Portfolio: 87 applications
Technology Stack: Java, .NET, Python, legacy mainframe
Databases: SQL Server, Oracle, PostgreSQL, MongoDB

TASK:
Perform comprehensive application portfolio assessment for cloud migration.

INPUT DATA:
[Paste application portfolio data: name, technology stack, business owner, user count, criticality, current hosting, database, integrations]

ASSESSMENT DIMENSIONS:

1. TECHNICAL CHARACTERISTICS
   - Application architecture (monolith, microservices, SOA)
   - Technology stack and versions
   - Code quality and technical debt
   - API/interface design
   - Database type and size
   - Dependencies (libraries, frameworks)
   - Authentication mechanism
   - Deployment frequency
   - Test coverage

2. BUSINESS CHARACTERISTICS
   - Business criticality (1-10 scale)
   - User base size (internal/external)
   - Revenue impact if unavailable
   - Peak usage patterns
   - Geographic distribution
   - Compliance requirements
   - SLA requirements (availability, performance)
   - Business owner engagement

3. CLOUD READINESS ASSESSMENT (1-10 score)
   Evaluate across:
   - Application architecture modularity
   - Stateless vs stateful design
   - Horizontal scalability capability
   - Cloud-native features (12-factor compliance)
   - Externalized configuration
   - Containerization readiness
   - API-first design
   - Observability (logging, metrics, tracing)

4. MIGRATION STRATEGY (6 Rs)
   For each application, recommend ONE:
   
   **REHOST** (Lift & Shift)
   - Move to Azure VMs as-is
   - Minimal changes
   - Fast migration
   - Example: Legacy apps, time-sensitive migrations
   
   **REPLATFORM** (Lift & Optimize)
   - Move to Azure PaaS (App Service, SQL Database)
   - Minor optimizations
   - Better TCO
   - Example: Standard web apps, databases
   
   **REFACTOR** (Re-architect)
   - Redesign for cloud-native (microservices, containers)
   - Significant code changes
   - Maximum cloud benefits
   - Example: Strategic apps, high-growth apps
   
   **REPURCHASE** (Replace with SaaS)
   - Replace with commercial SaaS
   - No code migration
   - Subscription cost
   - Example: Email, CRM, HR systems
   
   **RETIRE** (Decommission)
   - No longer needed
   - End-of-life
   - Zero migration cost
   - Example: Redundant, unused apps
   
   **RETAIN** (Keep on-premises)
   - Not ready for cloud
   - Regulatory constraints
   - High migration risk
   - Example: Mainframe, specialized hardware

5. EFFORT ESTIMATION
   - Migration complexity (Low/Medium/High)
   - Estimated effort (person-days)
   - Team composition (architects, developers, DBAs, testers)
   - Timeline (weeks)
   - Skills required

6. RISK ASSESSMENT
   - Technical risks (compatibility, performance, integration)
   - Business risks (downtime, user impact, data loss)
   - Compliance risks (data residency, audit trail)
   - Vendor risks (licensing, support)
   - Mitigation strategies for each risk

7. COST ANALYSIS
   - Current TCO (annual)
     - Infrastructure (hardware, datacenter)
     - Licenses
     - Operations (staff, maintenance)
   - Estimated Azure cost (annual)
     - Compute
     - Storage
     - Networking
     - Licenses (BYOL vs new)
   - Cost comparison and savings
   - Payback period

8. AZURE SERVICE RECOMMENDATIONS
   Based on application type:
   - **Web Apps**: App Service, AKS, Container Apps
   - **APIs**: API Management, Functions, App Service
   - **Batch Jobs**: Batch, Functions, Logic Apps
   - **Databases**: SQL Database, Cosmos DB, PostgreSQL
   - **File Storage**: Blob Storage, Azure Files, NetApp Files
   - **Integration**: Service Bus, Event Grid, Logic Apps

OUTPUT FORMAT:
{
  "portfolio_summary": {
    "total_applications": number,
    "distribution_by_strategy": {
      "rehost": number,
      "replatform": number,
      "refactor": number,
      "repurchase": number,
      "retire": number,
      "retain": number
    },
    "cloud_readiness_average": number,
    "total_estimated_effort_days": number,
    "total_estimated_cost_savings_annual": number
  },
  "applications": [
    {
      "app_id": "string",
      "app_name": "string",
      "business_owner": "string",
      "technology_stack": "string",
      "architecture_type": "string",
      "database": "string",
      "user_count": number,
      "criticality_score": number,
      "cloud_readiness_score": number,
      "migration_strategy": "string",
      "migration_rationale": "string",
      "recommended_azure_services": [],
      "effort_estimation": {
        "complexity": "string",
        "effort_days": number,
        "team_size": number,
        "duration_weeks": number,
        "skills_required": []
      },
      "cost_analysis": {
        "current_annual_tco": number,
        "azure_annual_cost": number,
        "annual_savings": number,
        "payback_period_months": number
      },
      "risks": [
        {
          "risk_type": "string",
          "description": "string",
          "impact": "string",
          "probability": "string",
          "mitigation": "string"
        }
      ],
      "dependencies": [],
      "compliance_requirements": [],
      "confidence_score": number
    }
  ],
  "recommendations": {
    "quick_wins": [],
    "strategic_priorities": [],
    "deferred_migrations": [],
    "retirement_candidates": []
  }
}

CONSTRAINTS:
- Provide detailed rationale for each migration strategy
- Consider business value vs migration effort
- Factor in dependencies (from dependency mapping)
- Align with business priorities and timelines
- Identify skills gaps requiring training/hiring
- Recommend pilot candidates (low-risk, high-value)

DELIVERABLES:
1. Application assessment matrix (JSON)
2. Portfolio distribution chart data
3. Migration strategy recommendations
4. Effort and cost estimates
5. Risk register
6. Prioritized migration roadmap input
```

---

### Prompt 1.4: Database Assessment

```
ROLE: Database Architect specializing in cloud database migrations

CONTEXT:
Company: Contoso Insurance
Databases: 45 database instances (SQL Server, Oracle, PostgreSQL, MongoDB)
Data Volume: Multi-TB across databases
Compliance: PCI-DSS, data residency requirements

TASK:
Assess all databases for Azure migration and recommend optimal Azure database services.

INPUT DATA:
[Paste database inventory: name, type, version, size, transactions/sec, users, criticality, backup strategy, HA setup]

ASSESSMENT AREAS:

1. DATABASE INVENTORY ANALYSIS
   - Database engine and version
   - Database size (GB/TB)
   - Growth rate (monthly)
   - Transaction volume (TPS)
   - Query complexity
   - Stored procedures, triggers, functions count
   - Number of schemas/tables
   - Index strategy
   - Partitioning setup

2. COMPATIBILITY ASSESSMENT
   For each database:
   - Azure compatibility score (1-10)
   - Feature compatibility (T-SQL, PL/SQL extensions)
   - Licensing compatibility (BYOL, license-included)
   - Version support in Azure
   - Breaking changes identification
   - Deprecated features usage
   - Custom extensions/plugins

3. MIGRATION STRATEGY
   **For SQL Server:**
   - Azure SQL Database (PaaS)
   - Azure SQL Managed Instance (PaaS+)
   - SQL Server on Azure VM (IaaS)
   
   **For Oracle:**
   - Oracle on Azure VM
   - Migrate to Azure SQL Database
   - Migrate to PostgreSQL
   
   **For PostgreSQL:**
   - Azure Database for PostgreSQL (Flexible Server)
   - PostgreSQL on Azure VM
   
   **For MongoDB:**
   - Azure Cosmos DB (Mongo API)
   - MongoDB on Azure VM

4. MIGRATION APPROACH
   - Online vs Offline migration
   - Downtime tolerance
   - Data migration tool (Azure DMS, native tools)
   - Schema migration complexity
   - Data transformation required
   - Cutover strategy

5. PERFORMANCE SIZING
   - Azure service tier recommendation
   - vCore/DTU sizing
   - Storage type (Premium SSD, Standard)
   - IOPS requirements
   - Backup retention strategy
   - Geo-replication needs

6. HIGH AVAILABILITY & DR
   - Availability requirements (99.9%, 99.95%, 99.99%)
   - RPO (Recovery Point Objective)
   - RTO (Recovery Time Objective)
   - Azure HA options:
     - Availability Zones
     - Geo-replication
     - Always On Availability Groups
     - Backup strategies

7. SECURITY & COMPLIANCE
   - Encryption at rest (TDE)
   - Encryption in transit (SSL/TLS)
   - Data masking requirements
   - Auditing and compliance
   - Network isolation (Private Link)
   - Access control (Azure AD, RBAC)

8. COST ANALYSIS
   - Current database TCO (annual)
   - Azure database service cost
   - Backup storage cost
   - Data transfer cost
   - License cost (if BYOL)
   - Cost optimization opportunities

OUTPUT FORMAT:
{
  "database_summary": {
    "total_databases": number,
    "total_data_size_tb": number,
    "distribution_by_engine": {},
    "migration_complexity_distribution": {}
  },
  "databases": [
    {
      "db_id": "string",
      "db_name": "string",
      "engine": "string",
      "version": "string",
      "size_gb": number,
      "tps": number,
      "criticality": "string",
      "compatibility_score": number,
      "migration_strategy": {
        "recommended_azure_service": "string",
        "service_tier": "string",
        "rationale": "string",
        "alternative_options": []
      },
      "migration_approach": {
        "method": "string",
        "tool": "string",
        "estimated_downtime": "string",
        "complexity": "string",
        "special_considerations": []
      },
      "sizing_recommendation": {
        "compute": "string",
        "storage_gb": number,
        "iops": number,
        "backup_retention_days": number
      },
      "ha_dr_configuration": {
        "availability_zones": boolean,
        "geo_replication": boolean,
        "rpo_minutes": number,
        "rto_minutes": number
      },
      "security_configuration": {
        "tde_enabled": boolean,
        "ssl_required": boolean,
        "private_endpoint": boolean,
        "azure_ad_auth": boolean
      },
      "cost_estimate": {
        "current_annual": number,
        "azure_annual": number,
        "savings": number
      },
      "risks": [],
      "confidence_score": number
    }
  ],
  "migration_waves": [],
  "recommendations": {
    "quick_wins": [],
    "complex_migrations": [],
    "pilot_candidates": []
  }
}

CONSTRAINTS:
- Consider data residency requirements
- Factor in compliance needs (PCI-DSS, HIPAA)
- Recommend smallest viable tier (right-sizing)
- Include cost optimization strategies
- Provide migration tool recommendations
- Identify breaking changes requiring code fixes

DELIVERABLES:
1. Database assessment report (JSON)
2. Azure service recommendations
3. Migration approach per database
4. Cost comparison analysis
5. Risk and mitigation strategies
6. Migration sequencing recommendations
```

---

## PHASE 2: PLANNING PHASE PROMPTS

### Prompt 2.1: Migration Roadmap Generation

```
ROLE: Senior Migration Program Manager with 20+ years of enterprise transformation experience

CONTEXT:
Company: Contoso Insurance
Portfolio: 87 applications, 450 servers, 45 databases
Timeline: 12 months
Budget: $5M
Team: 15 FTE

INPUT DATA:
1. Application portfolio assessment results
2. Dependency mapping analysis
3. Database assessment
4. Business priorities
5. Resource constraints

[Paste assessment results]

TASK:
Generate comprehensive 4-wave migration roadmap with detailed execution plan.

ROADMAP STRUCTURE:

**WAVE 1: Foundation & Infrastructure (Weeks 1-4)**
Focus: Azure landing zone, networking, security baseline

Deliverables:
- Azure subscription setup (Hub-Spoke topology)
- ExpressRoute or VPN connectivity
- Azure AD integration & RBAC
- Network Security Groups & firewalls
- Backup infrastructure (Recovery Services Vault)
- Monitoring foundation (Log Analytics, Application Insights)
- DevOps pipelines (Azure DevOps or GitHub Actions)
- Security baseline (Azure Policy, Security Center)

Success Criteria:
- Network connectivity validated (< 10ms latency)
- Azure AD sync working (100% users synced)
- Backup policies active
- Monitoring dashboards operational
- Security baseline compliant

Go/No-Go Decision Point: Network performance, security compliance validated

**WAVE 2: Non-Critical Applications (Weeks 5-12)**
Focus: Low-risk applications, build confidence, establish patterns

Target Applications:
- Development/test environments
- Internal tools (low user count)
- Stateless web applications
- Non-production databases

Parallel Migrations: 3 applications simultaneously

Deliverables:
- 30-35 applications migrated
- CI/CD pipelines established per app
- Monitoring and alerting configured
- Runbook documentation per app
- Team training completed

Success Criteria:
- Application availability > 99.9%
- Performance within ±10% of on-prem
- Zero critical incidents
- Positive user feedback
- Cost tracking on target

Go/No-Go Decision Point: Application stability, team readiness for critical apps

**WAVE 3: Critical Applications (Weeks 13-20)**
Focus: Business-critical applications with careful planning

Target Applications:
- Claims processing system
- Policy management system
- Customer portal
- Core databases (SQL Always On)

Parallel Migrations: 2 applications (reduced for safety)

Special Considerations:
- Pre-migration load testing
- Cutover rehearsals (3x minimum)
- 24/7 war room during cutover
- Rollback plan tested
- Business stakeholder approval per app

Deliverables:
- 25-28 critical applications migrated
- DR configuration validated
- Performance baselines documented
- Security audits completed
- Compliance validation

Success Criteria:
- Zero customer-facing incidents
- SLA targets met or exceeded
- Performance improvements demonstrated
- Cost savings realized
- Business sign-off obtained

Go/No-Go Decision Point: Business approval, DR validation, performance testing

**WAVE 4: Optimization & Cleanup (Weeks 21-24)**
Focus: Cost optimization, decommissioning, documentation

Activities:
- On-premises infrastructure decommissioning
- Cost optimization (right-sizing, reserved instances)
- Performance tuning
- Security hardening
- Knowledge transfer
- Final documentation
- Lessons learned documentation

Deliverables:
- On-prem datacenter decommissioned
- Cost optimization implemented (target: 30% reduction)
- Final architecture documentation
- Operational runbooks complete
- Training materials delivered
- Project closure report

Success Criteria:
- Cost targets achieved
- All apps performing optimally
- Operations team fully trained
- Documentation complete
- Stakeholder satisfaction > 90%

OUTPUT FORMAT:
{
  "roadmap": {
    "total_duration_weeks": 24,
    "total_budget": "$5,000,000",
    "start_date": "2025-01-15",
    "end_date": "2025-07-15",
    "waves": [
      {
        "wave_id": 1,
        "wave_name": "string",
        "start_date": "string",
        "end_date": "string",
        "duration_weeks": number,
        "applications": [],
        "focus_areas": [],
        "deliverables": [],
        "success_criteria": [],
        "go_no_go_criteria": [],
        "budget_allocation": number,
        "team_size": number,
        "risks": []
      }
    ],
    "resource_plan": {
      "total_fte": 15,
      "roles": [
        {
          "role": "string",
          "count": number,
          "skills_required": [],
          "allocation_percentage": number
        }
      ]
    },
    "budget_breakdown": {
      "infrastructure": number,
      "labor": number,
      "tools_licenses": number,
      "training": number,
      "contingency": number
    },
    "critical_path": [],
    "dependencies": [],
    "assumptions": []
  },
  "gantt_chart_data": [],
  "risk_register": []
}

CONSTRAINTS:
- Maximum 3 parallel migrations in Wave 2
- Maximum 2 parallel migrations in Wave 3
- Minimum 1 week between critical app migrations
- Include 15% contingency in timeline and budget
- Factor in training, documentation time
- Schedule quarterly reviews with stakeholders

DELIVERABLES:
1. Detailed migration roadmap (JSON + Excel Gantt)
2. Resource allocation plan
3. Budget allocation schedule
4. Risk register with mitigation
5. Communication plan
6. Success metrics dashboard
```

---

### Prompt 2.2: Risk Assessment & Mitigation

```
ROLE: Enterprise Risk Management Specialist with cloud migration expertise

CONTEXT:
Company: Contoso Insurance (highly regulated)
Migration Scope: 87 applications, $5M budget, 12 months
Criticality: Several revenue-critical systems

INPUT DATA:
1. Application portfolio assessment
2. Dependency analysis
3. Migration roadmap
4. Business constraints

[Paste relevant data]

TASK:
Identify, assess, and create mitigation strategies for all migration risks.

RISK CATEGORIES:

1. **TECHNICAL RISKS**

**Integration Risks:**
- Risk: APIs break during migration
- Impact: Service unavailability, data inconsistency
- Probability: Medium (30%)
- Impact Score: 8/10
- Mitigation:
  1. Pre-migration integration testing
  2. API versioning strategy
  3. Parallel run for 1 week
  4. Rollback plan ready

**Performance Risks:**
- Risk: Application slower in cloud
- Impact: Poor user experience, SLA breach
- Probability: Medium (40%)
- Impact Score: 7/10
- Mitigation:
  1. Load testing at 150% capacity
  2. Performance baseline documentation
  3. Caching layer implementation
  4. CDN for static content

**Data Migration Risks:**
- Risk: Data loss or corruption during migration
- Impact: Business disruption, compliance violation
- Probability: Low (10%)
- Impact Score: 10/10
- Mitigation:
  1. Pre-migration data validation
  2. Post-migration reconciliation
  3. Point-in-time backup before migration
  4. CDC (Change Data Capture) for sync

2. **BUSINESS RISKS**

**Service Disruption Risk:**
- Risk: Claims processing down during migration
- Impact: $500K/hour revenue loss
- Probability: Medium (35%)
- Impact Score: 10/10
- Mitigation:
  1. Blue-green deployment
  2. 4-hour maximum downtime window
  3. Rollback tested 3 times
  4. 24/7 war room coverage
  5. Business communication plan

**Customer Impact Risk:**
- Risk: Customer-facing portal unavailable
- Impact: Customer churn, reputation damage
- Probability: Low (15%)
- Impact Score: 9/10
- Mitigation:
  1. Customer communication (2 weeks notice)
  2. Migration during low-traffic window
  3. Status page for transparency
  4. Support team on standby

3. **OPERATIONAL RISKS**

**Team Capability Gap:**
- Risk: Team lacks Azure expertise
- Impact: Project delays, errors
- Probability: High (60%)
- Impact Score: 6/10
- Mitigation:
  1. Azure training program (2 weeks)
  2. Hire 2 Azure consultants
  3. Microsoft FastTrack engagement
  4. Pair programming with experts

**Tool Inadequacy:**
- Risk: Migration tools fail at scale
- Impact: Timeline delays
- Probability: Medium (30%)
- Impact Score: 5/10
- Mitigation:
  1. Pilot migration to test tools
  2. Backup manual process
  3. Vendor support agreement
  4. Alternative tool evaluation

4. **FINANCIAL RISKS**

**Budget Overrun:**
- Risk: Costs exceed $5M budget
- Impact: Project halt, scope reduction
- Probability: Medium (40%)
- Impact Score: 8/10
- Mitigation:
  1. Weekly cost tracking
  2. 15% contingency reserved
  3. Phased budget release
  4. Cost optimization reviews

**Hidden Costs:**
- Risk: Unforeseen egress, license costs
- Impact: Budget strain
- Probability: High (50%)
- Impact Score: 6/10
- Mitigation:
  1. Detailed cost modeling upfront
  2. Reserved instances (1-year)
  3. Hybrid benefit licensing
  4. Cost anomaly alerts

5. **COMPLIANCE & SECURITY RISKS**

**Data Residency Violation:**
- Risk: Data stored in wrong region
- Impact: Regulatory penalties, compliance breach
- Probability: Low (10%)
- Impact Score: 10/10
- Mitigation:
  1. Azure Policy enforcement (region restriction)
  2. Data classification and tagging
  3. Pre-migration compliance review
  4. Regular compliance audits

**Security Breach During Migration:**
- Risk: Data exposed during transit
- Impact: Data breach, regulatory penalties
- Probability: Low (5%)
- Impact Score: 10/10
- Mitigation:
  1. Encryption in transit (TLS 1.2+)
  2. Private connectivity (ExpressRoute)
  3. Security monitoring during migration
  4. Incident response plan ready

OUTPUT FORMAT:
{
  "risk_register": {
    "total_risks_identified": 25,
    "high_risks": 8,
    "medium_risks": 12,
    "low_risks": 5,
    "risks": [
      {
        "risk_id": "string",
        "risk_name": "string",
        "category": "string",
        "description": "string",
        "impact_score": number,
        "probability": "string",
        "risk_score": number,
        "business_impact": "string",
        "technical_impact": "string",
        "mitigation_strategies": [],
        "contingency_plan": "string",
        "owner": "string",
        "status": "string",
        "monitoring_approach": "string",
        "early_warning_indicators": []
      }
    ]
  },
  "risk_heat_map_data": [],
  "top_10_risks": [],
  "mitigation_action_items": [],
  "contingency_budget_required": "$750,000"
}

CONSTRAINTS:
- Identify risks across ALL dimensions
- Provide 3+ mitigation strategies per risk
- Assign clear ownership
- Include early warning indicators
- Link risks to specific migration waves
- Provide Go/No-Go criteria based on risk

DELIVERABLES:
1. Comprehensive risk register (JSON + Excel)
2. Risk heat map
3. Mitigation action plan
4. Contingency plans per wave
5. Risk monitoring dashboard
6. Weekly risk review template
```

---

*Note: Due to length constraints, I'll continue with Migration, Security, Monitoring, and Optimization phase prompts in the next artifact. This document covers Assessment and Planning phases comprehensively.*

**[Document continues in next file...]**
