# Complete Step-by-Step Migration Guide
## From Base Environment Setup to Azure Migration Using GenAI

---

## 📋 Table of Contents

1. **Phase 0: Prerequisites & Planning (1 day)**
2. **Phase 1: Base Environment Setup (2 days)**
3. **Phase 2: Discovery & Assessment (3-5 days)**
4. **Phase 3: Azure Target Environment Setup (3-5 days)**
5. **Phase 4: Migration Execution (Wave-based)**
6. **Phase 5: Validation & Cutover (Per Wave)**
7. **Phase 6: Optimization & Cleanup (Ongoing)**

---

## PHASE 0: Prerequisites & Planning (Day 0)

### Step 0.1: Validate Prerequisites

**Required Access & Permissions:**

```bash
# Check Azure subscription access
az login
az account list --output table

# Verify you have required roles
az role assignment list --assignee $(az ad signed-in-user show --query id -o tsv) --output table

# Required roles:
# - Owner or Contributor on subscription
# - User Access Administrator (for RBAC assignments)
```

**Checklist:**
- [ ] Azure subscription with sufficient quota
- [ ] Azure AD Global Admin or Application Administrator
- [ ] Budget approved ($5M for Contoso example)
- [ ] Executive sponsorship confirmed
- [ ] Migration team identified (15 FTE)

**Required Tools Installation:**

```bash
# Windows (PowerShell)
# Install Chocolatey first
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Install tools
choco install python --version=3.11.0 -y
choco install azure-cli -y
choco install git -y
choco install vscode -y
choco install powershell-core -y

# Linux (Ubuntu/Debian)
sudo apt update
sudo apt install -y python3.11 python3-pip git

# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# macOS
brew install python@3.11
brew install azure-cli
brew install git
```

**Verify Installations:**

```bash
# Check Python
python3 --version  # Should be 3.11.x or higher

# Check Azure CLI
az --version  # Should be 2.50.0 or higher

# Check pip
pip3 --version

# Check Git
git --version
```

---

## PHASE 1: Base Environment Setup (Days 1-2)

### Step 1.1: Download and Extract GenAI Bundle

```bash
# Create working directory
mkdir ~/azure-migration
cd ~/azure-migration

# If you have the bundle as files, organize them
mkdir genai-migration-bundle
cd genai-migration-bundle

# Create directory structure
mkdir -p {prompts/{01-assessment,02-planning,03-migration,04-security,05-monitoring,06-optimization},scripts/{python,powershell,bash},bicep/{foundation,genai-services,workloads,parameters},config,data,outputs,docs}
```

### Step 1.2: Set Up Python Virtual Environment

```bash
# Create virtual environment
python3 -m venv venv

# Activate virtual environment
# Linux/Mac:
source venv/bin/activate

# Windows PowerShell:
.\venv\Scripts\Activate.ps1

# Windows CMD:
.\venv\Scripts\activate.bat

# Verify activation (should show (venv) prefix)
which python  # Linux/Mac
where python  # Windows
```

### Step 1.3: Install Python Dependencies

```bash
# Create requirements.txt
cat > requirements.txt << 'EOF'
# Azure SDK packages
azure-identity==1.15.0
azure-ai-openai==1.0.0
azure-mgmt-cognitiveservices==13.5.0
azure-mgmt-resource==23.0.1
azure-mgmt-compute==30.4.0
azure-mgmt-network==25.1.0
azure-mgmt-sql==4.0.0
azure-mgmt-storage==21.1.0
azure-storage-blob==12.19.0
azure-monitor-query==1.2.0

# Data processing
pandas==2.1.4
numpy==1.26.2
openpyxl==3.1.2

# Utilities
python-dotenv==1.0.0
requests==2.31.0
pyyaml==6.0.1
jinja2==3.1.2

# Visualization and reporting
matplotlib==3.8.2
seaborn==0.13.0

# Network analysis
networkx==3.2.1

# Logging
colorlog==6.8.0
EOF

# Install dependencies
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt

# Verify installation
pip list
```

### Step 1.4: Configure Azure Authentication

```bash
# Login to Azure
az login

# List available subscriptions
az account list --output table

# Set the subscription you want to use
az account set --subscription "Your-Subscription-Name-or-ID"

# Verify current subscription
az account show --output table

# Create a service principal for automation (optional but recommended)
az ad sp create-for-rbac \
  --name "migration-automation-sp" \
  --role Contributor \
  --scopes /subscriptions/YOUR_SUBSCRIPTION_ID \
  --sdk-auth > azure-credentials.json

# Save the output - you'll need it for GitHub Actions or automation
```

### Step 1.5: Create Environment Configuration

```bash
# Create .env file
cat > .env << 'EOF'
# Azure Subscription Configuration
AZURE_SUBSCRIPTION_ID=your-subscription-id-here
AZURE_TENANT_ID=your-tenant-id-here
AZURE_RESOURCE_GROUP=contoso-migration-prod-rg

# Azure OpenAI Configuration (will be populated after deployment)
AZURE_OPENAI_ENDPOINT=https://contoso-openai-prod.openai.azure.com/
AZURE_OPENAI_KEY=your-api-key-will-be-here
AZURE_OPENAI_DEPLOYMENT=gpt-4
AZURE_OPENAI_API_VERSION=2024-02-15-preview

# Migration Settings
COMPANY_PREFIX=contoso
ENVIRONMENT=prod
PRIMARY_REGION=eastus
DR_REGION=westus2

# Project Settings
PROJECT_NAME=Contoso Insurance Migration
START_DATE=2025-01-15
TOTAL_BUDGET=5000000
TEAM_SIZE=15

# Email Settings (for notifications)
NOTIFICATION_EMAIL=migration-team@contoso.com
ALERT_EMAIL=alerts@contoso.com
EOF

# Make sure .env is in .gitignore
echo ".env" >> .gitignore
echo "azure-credentials.json" >> .gitignore
echo "venv/" >> .gitignore
echo "outputs/" >> .gitignore
echo "__pycache__/" >> .gitignore
echo "*.pyc" >> .gitignore
```

### Step 1.6: Deploy Azure Foundation Infrastructure

**Create main Bicep template:**

Save this as `bicep/foundation/main.bicep`:

```bicep
targetScope = 'subscription'

param environment string = 'prod'
param location string = 'eastus'
param drLocation string = 'westus2'
param companyPrefix string = 'contoso'
param deploymentTimestamp string = utcNow()

var resourceGroupName = '${companyPrefix}-migration-${environment}-rg'
var tags = {
  Environment: environment
  Project: 'Cloud Migration'
  DeployedBy: 'Bicep'
  DeploymentDate: deploymentTimestamp
}

resource resourceGroup 'Microsoft.Resources/resourceGroups@2023-07-01' = {
  name: resourceGroupName
  location: location
  tags: tags
}

module networking 'networking.bicep' = {
  name: 'networking-deployment'
  scope: resourceGroup
  params: {
    location: location
    environment: environment
    companyPrefix: companyPrefix
  }
}

module security 'security.bicep' = {
  name: 'security-deployment'
  scope: resourceGroup
  params: {
    location: location
    environment: environment
    companyPrefix: companyPrefix
  }
}

module monitoring 'monitoring.bicep' = {
  name: 'monitoring-deployment'
  scope: resourceGroup
  params: {
    location: location
    environment: environment
    companyPrefix: companyPrefix
  }
}

output resourceGroupName string = resourceGroup.name
output vnetId string = networking.outputs.vnetId
output logAnalyticsWorkspaceId string = monitoring.outputs.logAnalyticsWorkspaceId
```

**Deploy infrastructure:**

```bash
# Deploy using Azure CLI
az deployment sub create \
  --name "migration-foundation-$(date +%Y%m%d-%H%M%S)" \
  --location eastus \
  --template-file bicep/foundation/main.bicep \
  --parameters environment=prod location=eastus companyPrefix=contoso

# This will take 15-20 minutes
# Monitor deployment
az deployment sub show \
  --name migration-foundation-TIMESTAMP \
  --query "properties.provisioningState"
```

### Step 1.7: Deploy GenAI Services

```bash
# Deploy Azure OpenAI and supporting services
az deployment group create \
  --name "genai-services-$(date +%Y%m%d-%H%M%S)" \
  --resource-group contoso-migration-prod-rg \
  --template-file bicep/genai-services/openai.bicep \
  --parameters environment=prod location=eastus companyPrefix=contoso

# Get Azure OpenAI endpoint and key
OPENAI_ENDPOINT=$(az cognitiveservices account show \
  --name contoso-openai-prod \
  --resource-group contoso-migration-prod-rg \
  --query "properties.endpoint" -o tsv)

OPENAI_KEY=$(az cognitiveservices account keys list \
  --name contoso-openai-prod \
  --resource-group contoso-migration-prod-rg \
  --query "key1" -o tsv)

# Update .env file
sed -i "s|AZURE_OPENAI_ENDPOINT=.*|AZURE_OPENAI_ENDPOINT=$OPENAI_ENDPOINT|" .env
sed -i "s|AZURE_OPENAI_KEY=.*|AZURE_OPENAI_KEY=$OPENAI_KEY|" .env

# Store key in Key Vault
az keyvault secret set \
  --vault-name contoso-kv-prod \
  --name "azure-openai-key" \
  --value "$OPENAI_KEY"

echo "✓ GenAI services deployed successfully"
```

### Step 1.8: Verify Base Environment

```bash
# Run environment verification script
cat > scripts/bash/verify-environment.sh << 'EOF'
#!/bin/bash

echo "========================================="
echo "Environment Verification"
echo "========================================="

# Check Python
echo -n "Python: "
python3 --version || echo "❌ NOT FOUND"

# Check Azure CLI
echo -n "Azure CLI: "
az --version | head -n1 || echo "❌ NOT FOUND"

# Check Azure login
echo -n "Azure Login: "
az account show &>/dev/null && echo "✓ Logged in" || echo "❌ Not logged in"

# Check resource group
echo -n "Resource Group: "
az group show --name contoso-migration-prod-rg &>/dev/null && echo "✓ Exists" || echo "❌ Not found"

# Check Azure OpenAI
echo -n "Azure OpenAI: "
az cognitiveservices account show \
  --name contoso-openai-prod \
  --resource-group contoso-migration-prod-rg &>/dev/null && echo "✓ Deployed" || echo "❌ Not found"

# Check Python packages
echo -n "Python packages: "
python3 -c "import azure.ai.openai; import pandas; import openpyxl" 2>/dev/null && echo "✓ Installed" || echo "❌ Missing"

echo "========================================="
echo "Verification Complete"
echo "========================================="
EOF

chmod +x scripts/bash/verify-environment.sh
./scripts/bash/verify-environment.sh
```

**Expected Output:**
```
=========================================
Environment Verification
=========================================
Python: Python 3.11.0
Azure CLI: azure-cli 2.50.0
Azure Login: ✓ Logged in
Resource Group: ✓ Exists
Azure OpenAI: ✓ Deployed
Python packages: ✓ Installed
=========================================
Verification Complete
=========================================
```

---

## PHASE 2: Discovery & Assessment (Days 3-7)

### Step 2.1: Collect Current Environment Data

**On-Premises Data Collection:**

```bash
# Create data collection script for Windows servers
cat > scripts/powershell/Collect-ServerInventory.ps1 << 'EOF'
# Run this on each on-premises server or use SCCM/SCOM

$inventory = @{
    ServerName = $env:COMPUTERNAME
    OS = (Get-WmiObject Win32_OperatingSystem).Caption
    OSVersion = (Get-WmiObject Win32_OperatingSystem).Version
    Manufacturer = (Get-WmiObject Win32_ComputerSystem).Manufacturer
    Model = (Get-WmiObject Win32_ComputerSystem).Model
    CPU_Cores = (Get-WmiObject Win32_Processor).NumberOfCores
    RAM_GB = [math]::Round((Get-WmiObject Win32_ComputerSystem).TotalPhysicalMemory / 1GB, 2)
    Disks = (Get-WmiObject Win32_LogicalDisk -Filter "DriveType=3" | 
             Select-Object DeviceID, 
                          @{Name="Size_GB";Expression={[math]::Round($_.Size/1GB,2)}},
                          @{Name="FreeSpace_GB";Expression={[math]::Round($_.FreeSpace/1GB,2)}})
    NetworkAdapters = (Get-WmiObject Win32_NetworkAdapter -Filter "NetConnectionStatus=2" | 
                       Select-Object Name, MACAddress)
    InstalledSoftware = (Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
                         Select-Object DisplayName, DisplayVersion, Publisher)
    LastBootTime = (Get-WmiObject Win32_OperatingSystem).LastBootUpTime
}

# Export to JSON
$inventory | ConvertTo-Json -Depth 10 | Out-File ".\$env:COMPUTERNAME-inventory.json"

Write-Host "Inventory collected: $env:COMPUTERNAME-inventory.json"
EOF

# For Linux servers
cat > scripts/bash/collect-server-inventory.sh << 'EOF'
#!/bin/bash

SERVER_NAME=$(hostname)
OUTPUT_FILE="${SERVER_NAME}-inventory.json"

cat > "$OUTPUT_FILE" << INVENTORY
{
  "ServerName": "$(hostname)",
  "OS": "$(lsb_release -d | cut -f2)",
  "OSVersion": "$(uname -r)",
  "Manufacturer": "$(sudo dmidecode -s system-manufacturer 2>/dev/null || echo 'Unknown')",
  "Model": "$(sudo dmidecode -s system-product-name 2>/dev/null || echo 'Unknown')",
  "CPU_Cores": $(nproc),
  "RAM_GB": $(free -g | awk '/^Mem:/{print $2}'),
  "Disks": $(lsblk -J | jq -c '.blockdevices'),
  "NetworkAdapters": $(ip -j addr | jq -c '.'),
  "LastBootTime": "$(uptime -s)"
}
INVENTORY

echo "Inventory collected: $OUTPUT_FILE"
EOF

chmod +x scripts/bash/collect-server-inventory.sh
```

**Consolidate Inventory Data:**

```bash
# Create consolidated inventory CSV
cat > data/infrastructure-inventory.csv << 'EOF'
ServerName,OS,OSVersion,Manufacturer,Model,RAM_GB,DiskSize_GB,CPU_Cores,Location,Environment,Criticality
SRV-NYC-001,Windows Server 2019,10.0.17763,Dell,PowerEdge R740,64,500,16,New York,Production,High
SRV-NYC-002,Windows Server 2016,10.0.14393,HP,ProLiant DL380,32,250,8,New York,Production,Medium
SRV-NYC-003,Windows Server 2019,10.0.17763,Dell,PowerEdge R740,64,500,16,New York,Production,High
SRV-MIA-001,Linux Ubuntu 20.04,5.4.0,Dell,PowerEdge R640,128,1000,24,Miami,Production,High
SRV-MIA-002,Windows Server 2019,10.0.17763,Dell,PowerEdge R740,64,500,16,Miami,Production,High
SRV-CHI-001,Windows Server 2012 R2,6.3.9600,HP,ProLiant DL380,16,200,4,Chicago,Development,Low
SRV-CHI-002,Linux CentOS 7,3.10.0,Dell,PowerEdge R630,32,400,8,Chicago,Development,Low
EOF

# Create application portfolio
cat > data/application-portfolio.csv << 'EOF'
AppID,AppName,TechnologyStack,BusinessOwner,UserCount,Criticality,CurrentHosting,Database,DatabaseSize_GB
APP001,Claims Processing,ASP.NET Core 6.0,John Doe,2500,High,SRV-NYC-001,SQL Server 2019,500
APP002,Policy Management,Java Spring Boot 2.7,Jane Smith,1800,High,SRV-NYC-002,PostgreSQL 14,300
APP003,Customer Portal,React + Node.js,Bob Johnson,5000,High,SRV-MIA-001,MongoDB 5.0,200
APP004,Internal Tools,Python Django 4.0,Alice Brown,150,Low,SRV-CHI-001,SQLite,5
APP005,Reporting System,Power BI,Mike Davis,500,Medium,SRV-NYC-003,SQL Server 2019,100
EOF

# Create database inventory
cat > data/databases.csv << 'EOF'
DBName,Engine,Version,Size_GB,TPS,Users,Criticality,BackupStrategy,HASetup
PolicyDB,SQL Server,2019,500,1500,50,High,Full daily + Hourly log,Always On AG
ClaimsDB,SQL Server,2019,1200,2000,100,High,Full daily + Hourly log,Always On AG
CustomersDB,PostgreSQL,14,300,800,30,Medium,Full daily,Streaming replication
ReportsDB,SQL Server,2016,100,200,10,Low,Weekly full,None
InternalDB,SQLite,3.36,5,50,5,Low,Daily file copy,None
EOF
```

### Step 2.2: Run GenAI-Powered Assessment

**Infrastructure Assessment:**

```bash
# Ensure virtual environment is activated
source venv/bin/activate  # Linux/Mac
# or
.\venv\Scripts\Activate.ps1  # Windows

# Run infrastructure assessment
python3 << 'PYTHON_SCRIPT'
import os
import json
import pandas as pd
from azure.ai.openai import AzureOpenAI
from datetime import datetime

# Initialize Azure OpenAI client
client = AzureOpenAI(
    api_version="2024-02-15-preview",
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    api_key=os.getenv("AZURE_OPENAI_KEY")
)

# Load inventory
inventory_df = pd.read_csv('data/infrastructure-inventory.csv')
inventory_json = inventory_df.to_json(orient='records', indent=2)

# GenAI Prompt for infrastructure assessment
prompt = f"""
Role: Senior Cloud Infrastructure Architect with 15+ years of Azure migration experience

Task: Analyze this infrastructure inventory and provide comprehensive Azure migration assessment.

INFRASTRUCTURE DATA:
{inventory_json}

Provide analysis with:
1. Asset categorization and cloud readiness (1-10 score per server)
2. Recommended Azure services (VM size, App Service, AKS, etc.)
3. Migration strategy (Rehost/Replatform/Refactor/Retire)
4. Effort estimation (hours per server)
5. Cost comparison (current vs Azure annual)
6. Risks and mitigation strategies
7. Migration sequence recommendation

Output as JSON with this structure:
{{
  "inventory_summary": {{"total_assets": 0, "cloud_ready": 0, "needs_review": 0}},
  "detailed_assessment": [
    {{
      "server_name": "string",
      "cloud_readiness_score": 0-10,
      "azure_target": "string",
      "migration_strategy": "string",
      "effort_hours": 0,
      "current_annual_cost": 0,
      "azure_annual_cost": 0,
      "risks": [],
      "confidence": 0-100
    }}
  ],
  "recommendations": {{
    "quick_wins": [],
    "complex_migrations": [],
    "retirement_candidates": []
  }}
}}
"""

print("🔄 Calling Azure OpenAI for infrastructure assessment...")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a senior cloud infrastructure architect."},
        {"role": "user", "content": prompt}
    ],
    temperature=0.5,
    max_tokens=4000,
    response_format={"type": "json_object"}
)

# Parse and save results
assessment = json.loads(response.choices[0].message.content)
timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
output_file = f'outputs/infrastructure-assessment-{timestamp}.json'

os.makedirs('outputs', exist_ok=True)
with open(output_file, 'w') as f:
    json.dump(assessment, f, indent=2)

print(f"✓ Infrastructure assessment complete: {output_file}")
print(f"\nSummary:")
print(f"  Total assets: {assessment['inventory_summary']['total_assets']}")
print(f"  Cloud ready: {assessment['inventory_summary']['cloud_ready']}")
print(f"  Needs review: {assessment['inventory_summary']['needs_review']}")
PYTHON_SCRIPT
```

**Application Assessment:**

```bash
python3 << 'PYTHON_SCRIPT'
import os
import json
import pandas as pd
from azure.ai.openai import AzureOpenAI
from datetime import datetime

client = AzureOpenAI(
    api_version="2024-02-15-preview",
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    api_key=os.getenv("AZURE_OPENAI_KEY")
)

# Load application portfolio
apps_df = pd.read_csv('data/application-portfolio.csv')
apps_json = apps_df.to_json(orient='records', indent=2)

prompt = f"""
Role: Application Portfolio Analyst with cloud readiness expertise

Task: Assess application portfolio for Azure migration.

APPLICATION PORTFOLIO:
{apps_json}

For each application provide:
1. Cloud readiness score (1-10)
2. Recommended Azure service (App Service, AKS, Functions, etc.)
3. Migration strategy (Rehost/Replatform/Refactor/Repurchase/Retire/Retain)
4. Effort estimation (person-days)
5. Cost analysis (current vs Azure)
6. Risks and dependencies
7. Priority ranking

Output as JSON:
{{
  "portfolio_summary": {{
    "total_applications": 0,
    "by_strategy": {{"rehost": 0, "replatform": 0, "refactor": 0}},
    "total_effort_days": 0
  }},
  "applications": [
    {{
      "app_name": "string",
      "cloud_readiness": 0-10,
      "azure_service": "string",
      "migration_strategy": "string",
      "effort_days": 0,
      "team_size": 0,
      "cost_current": 0,
      "cost_azure": 0,
      "priority": "High/Medium/Low",
      "risks": []
    }}
  ]
}}
"""

print("🔄 Calling Azure OpenAI for application assessment...")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are an application portfolio analyst."},
        {"role": "user", "content": prompt}
    ],
    temperature=0.5,
    max_tokens=4000,
    response_format={"type": "json_object"}
)

assessment = json.loads(response.choices[0].message.content)
timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
output_file = f'outputs/application-assessment-{timestamp}.json'

with open(output_file, 'w') as f:
    json.dump(assessment, f, indent=2)

print(f"✓ Application assessment complete: {output_file}")
print(f"\nSummary:")
print(f"  Total applications: {assessment['portfolio_summary']['total_applications']}")
print(f"  Migration strategies: {assessment['portfolio_summary']['by_strategy']}")
PYTHON_SCRIPT
```

**Database Assessment:**

```bash
python3 << 'PYTHON_SCRIPT'
import os
import json
import pandas as pd
from azure.ai.openai import AzureOpenAI
from datetime import datetime

client = AzureOpenAI(
    api_version="2024-02-15-preview",
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    api_key=os.getenv("AZURE_OPENAI_KEY")
)

# Load database inventory
dbs_df = pd.read_csv('data/databases.csv')
dbs_json = dbs_df.to_json(orient='records', indent=2)

prompt = f"""
Role: Database Architect specializing in Azure database migrations

Task: Assess databases for Azure migration.

DATABASE INVENTORY:
{dbs_json}

For each database provide:
1. Azure compatibility score (1-10)
2. Recommended Azure service (SQL Database, Managed Instance, PostgreSQL, Cosmos DB)
3. Service tier and sizing
4. Migration approach (online/offline, tools)
5. Downtime estimate
6. Cost comparison
7. HA/DR configuration

Output as JSON:
{{
  "database_summary": {{
    "total_databases": 0,
    "total_size_gb": 0,
    "by_engine": {{"sql_server": 0, "postgresql": 0}}
  }},
  "databases": [
    {{
      "db_name": "string",
      "compatibility_score": 0-10,
      "azure_service": "string",
      "service_tier": "string",
      "migration_tool": "string",
      "downtime_hours": 0,
      "cost_current": 0,
      "cost_azure": 0,
      "ha_dr_config": "string"
    }}
  ]
}}
"""

print("🔄 Calling Azure OpenAI for database assessment...")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a database architect."},
        {"role": "user", "content": prompt}
    ],
    temperature=0.5,
    max_tokens=4000,
    response_format={"type": "json_object"}
)

assessment = json.loads(response.choices[0].message.content)
timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
output_file = f'outputs/database-assessment-{timestamp}.json'

with open(output_file, 'w') as f:
    json.dump(assessment, f, indent=2)

print(f"✓ Database assessment complete: {output_file}")
print(f"\nSummary:")
print(f"  Total databases: {assessment['database_summary']['total_databases']}")
print(f"  Total size: {assessment['database_summary']['total_size_gb']} GB")
PYTHON_SCRIPT
```

### Step 2.3: Generate Assessment Summary

```bash
# Create consolidated assessment report
python3 << 'PYTHON_SCRIPT'
import os
import json
import glob
from datetime import datetime

print("📊 Generating consolidated assessment report...")

# Find latest assessment files
infra_file = max(glob.glob('outputs/infrastructure-assessment-*.json'))
app_file = max(glob.glob('outputs/application-assessment-*.json'))
db_file = max(glob.glob('outputs/database-assessment-*.json'))

# Load assessments
with open(infra_file) as f:
    infra = json.load(f)
with open(app_file) as f:
    apps = json.load(f)
with open(db_file) as f:
    dbs = json.load(f)

# Generate executive summary
summary = f"""
MIGRATION ASSESSMENT EXECUTIVE SUMMARY
Generated: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}

========================================
INFRASTRUCTURE
========================================
Total Servers: {infra['inventory_summary']['total_assets']}
Cloud Ready: {infra['inventory_summary']['cloud_ready']}
Needs Review: {infra['inventory_summary']['needs_review']}

Quick Wins: {len(infra['recommendations']['quick_wins'])}
Complex Migrations: {len(infra['recommendations']['complex_migrations'])}
Retirement Candidates: {len(infra['recommendations']['retirement_candidates'])}

========================================
APPLICATIONS
========================================
Total Applications: {apps['portfolio_summary']['total_applications']}
Migration Strategies:
  - Rehost: {apps['portfolio_summary']['by_strategy'].get('rehost', 0)}
  - Replatform: {apps['portfolio_summary']['by_strategy'].get('replatform', 0)}
  - Refactor: {apps['portfolio_summary']['by_strategy'].get('refactor', 0)}
Total Effort: {apps['portfolio_summary']['total_effort_days']} person-days

========================================
DATABASES
========================================
Total Databases: {dbs['database_summary']['total_databases']}
Total Size: {dbs['database_summary']['total_size_gb']} GB

========================================
RECOMMENDATIONS
========================================
1. Start with quick wins ({len(infra['recommendations']['quick_wins'])} servers)
2. Address complex migrations early in planning
3. Consider retirement for {len(infra['recommendations']['retirement_candidates'])} assets
4. Plan {apps['portfolio_summary']['total_effort_days']} person-days for application migrations

========================================
NEXT STEPS
========================================
1. Review detailed assessment files
2. Generate migration roadmap
3. Obtain stakeholder approval
4. Begin Azure target environment setup
"""

# Save summary
output_file = f"outputs/executive-summary-{datetime.now().strftime('%Y%m%d_%H%M%S')}.txt"
with open(output_file, 'w') as f:
    f.write(summary)

print(summary)
print(f"\n✓ Executive summary saved: {output_file}")
PYTHON_SCRIPT
```

---

**[Continued in next file due to length - this covers Phases 0-2. Remaining phases 3-6 will detail Azure setup, migration execution, validation, and optimization]**
