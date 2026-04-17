# Zero-Cost AI Orchestration Playbook
## Integrated System: Budget Guard + Multi-Agent Coordination + Resilience

**Status**: Ready for deployment  
**Cost**: $0 (local Ollama + Budget Guard proxy)  
**Agents Supported**: Unlimited (via file-based coordination)  
**Uptime Target**: 99.9% (self-healing architecture)

---

## Quick Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                     USER INTERFACE LAYER                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐ │
│  │ Windsurf/   │  │ VS Code     │  │ Azure VM (optional)     │ │
│  │ Kilo        │  │ Extension   │  │ Cloud Agent             │ │
│  └──────┬──────┘  └──────┬──────┘  └──────────┬──────────────┘ │
└─────────┼────────────────┼────────────────────┼────────────────┘
          │                │                    │
          ▼                ▼                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                    BUDGET GUARD PROXY (Port 4001)               │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Token Limit: 300,000/day                                │  │
│  │  Current Usage: ~34,000 tokens                           │  │
│  │  Endpoint: http://localhost:4001/v1                    │  │
│  │  Model: llama3.1:8b-instruct-q4_0                        │  │
│  └────────────────────┬─────────────────────────────────────┘  │
└───────────────────────┼──────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────────┐
│                    OLLAMA (Port 11434)                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  GPU Accelerated (RTX 5060)                                │  │
│  │  Models: llama3.1, gemma3, deepseek-r1, etc.             │  │
│  │  OpenAI-compatible API                                   │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────────┐
│              MULTI-AGENT COORDINATION HUB (WebSocket)           │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────────────────┐   │
│  │ Agent A  │ │ Agent B  │ │ Agent C  │ │ 48-Layer Memory    │   │
│  │ (Local)  │ │ (VSCode) │ │ (Phone)  │ │ Persistence        │   │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └─────────┬──────────┘   │
│       │          │          │                   │               │
│       └──────────┴──────────┘                   │               │
│              │                                    │               │
│              ▼                                    ▼               │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  File-Based Coordination (MESSAGE_*.md)                  │    │
│  │  - Cross-platform communication                          │    │
│  │  - Automatic backup & recovery                           │    │
│  │  - Memory leak prevention (1hr rotation)                 │    │
│  └──────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 1. Pre-Deployment Checklist

### 1.1 Verify Budget Guard Status
```powershell
# Check if Budget Guard is running
curl.exe http://localhost:4001/health

# Expected: {"status":"healthy","dailyTokens":34000,"limit":300000}
```

### 1.2 Verify Ollama Models
```powershell
# List available models
curl.exe http://localhost:11434/api/tags

# Required: llama3.1:8b-instruct-q4_0
# Optional: gemma3:1b, deepseek-r1:8b, etc.
```

### 1.3 Verify Resilience Features
```powershell
# Check WebSocket server
curl.exe http://localhost:3001/health

# Check collaboration hub
curl.exe http://localhost:3002/status
```

---

## 2. Agent Deployment Modes

### Mode A: Local Agent (Single Machine)
```yaml
# config/local-agent.yaml
agent_type: "local"
budget_guard_endpoint: "http://localhost:4001/v1"
ollama_endpoint: "http://localhost:11434"
coordination_port: 3001
memory_layers: 48
persistent_storage: "./data/local-agent/"
```

**Start Command:**
```powershell
cd C:\Dev\mev-swarm-temp
node start-local-agent.js --config=config/local-agent.yaml
```

### Mode B: Multi-Agent Swarm (Local Network)
```yaml
# config/multi-agent.yaml
agents:
  - name: "Desktop-Claude"
    type: "primary"
    port: 3001
    layers: [0-23]  # Perceptual to Working
    
  - name: "VSCode-Kilo"
    type: "secondary"
    port: 3002
    layers: [24-39] # Long-term to Associative
    
  - name: "Background-Optimizer"
    type: "background"
    port: 3003
    layers: [40-47] # Transcendent

shared_memory: "./data/shared/"
coordination_protocol: "file-based"
```

**Start Command:**
```powershell
node start-swarm.js --config=config/multi-agent.yaml
```

### Mode C: Azure Cloud Deployment
```yaml
# config/azure-deployment.yaml
agent_type: "cloud"
azure_vm: "187.77.3.56"
port_forwarding:
  - local: 8001 → remote: 3001
  - local: 8002 → remote: 3002
  - local: 8003 → remote: 3003

resilience:
  auto_reconnect: true
  backup_interval: "5m"
  health_check_interval: "30s"
```

**Deploy Command:**
```powershell
.\scripts\deploy-to-azure.ps1 -Config config/azure-deployment.yaml
```

---

## 3. Operational Procedures

### 3.1 Daily Monitoring (5 minutes)
```powershell
# 1. Check Budget Guard token usage
curl.exe http://localhost:4001/health | ConvertFrom-Json

# 2. Check agent health
Get-Content .\logs\agent-health.log -Tail 20

# 3. Check memory usage
node scripts/check-memory.js

# 4. Verify CPS scores (if deployed)
node agents-public/cps_test.js --quick
```

### 3.2 Weekly Maintenance (15 minutes)
```powershell
# 1. Backup all agent states
node scripts/backup-all-agents.js

# 2. Prune old messages (>1 hour)
node scripts/prune-messages.js --older-than=1h

# 3. Verify backup integrity
node scripts/verify-backups.js

# 4. Check for drift (CPS monitoring)
node scripts/drift-detection.js --report
```

### 3.3 Emergency Recovery (if agent crashes)
```powershell
# 1. Identify crashed agent
node scripts/find-crashed-agents.js

# 2. Attempt automatic recovery
node scripts/recover-agent.js --agent=Desktop-Claude

# 3. Verify recovery success
node scripts/verify-recovery.js --agent=Desktop-Claude

# 4. If recovery fails, restore from backup
node scripts/restore-from-backup.js --agent=Desktop-Claude --backup=latest
```

---

## 4. Integration with Existing Systems

### 4.1 Elasticsearch Integration
```javascript
// config/elasticsearch-connector.js
const esClient = require('./lib/elasticsearch-client');
const agentCoordinator = require('./lib/agent-coordinator');

class ElasticsearchOptimizer {
  async optimize() {
    // Get recommendations from 48-layer memory
    const recommendations = await agentCoordinator.query({
      type: 'optimization',
      target: 'elasticsearch',
      layers: [40, 41, 42, 43] // Transcendent layers for pattern recognition
    });
    
    // Apply recommendations with constitutional constraints
    for (const rec of recommendations) {
      if (this.validateAgainstConstraints(rec)) {
        await esClient.applyOptimization(rec);
      }
    }
  }
  
  validateAgainstConstraints(recommendation) {
    // Check against constitutional constraints
    return recommendation.riskLevel <= 0.2 && // Max 20% portfolio risk
           recommendation.costImpact >= 0;     // No negative cost
  }
}
```

### 4.2 VS Code Extension Integration
```javascript
// vscode-extension/integration.js
const { BudgetGuardClient } = require('../shared/budget-guard-client');
const { AgentCoordinator } = require('../shared/coordinator');

class KiloIntegration {
  constructor() {
    this.budgetGuard = new BudgetGuardClient('http://localhost:4001/v1');
    this.coordinator = new AgentCoordinator();
  }
  
  async processRequest(userInput) {
    // 1. Check budget
    const budgetStatus = await this.budgetGuard.checkBudget();
    if (budgetStatus.tokensRemaining < 1000) {
      return { error: 'Daily token budget exceeded' };
    }
    
    // 2. Query appropriate agent layer
    const layer = this.determineLayer(userInput);
    const response = await this.coordinator.queryLayer(layer, userInput);
    
    // 3. Update token usage
    await this.budgetGuard.recordUsage(response.tokensUsed);
    
    return response;
  }
  
  determineLayer(input) {
    if (input.complexity < 0.3) return [0, 7];   // Perceptual
    if (input.complexity < 0.6) return [8, 15];  // Short-term
    if (input.complexity < 0.8) return [16, 23]; // Working
    return [40, 47]; // Transcendent for complex patterns
  }
}
```

---

## 5. Cost Tracking & Budget Management

### 5.1 Daily Cost Dashboard
```powershell
# Generate daily cost report
node scripts/generate-cost-report.js --date=today

# Output example:
# ╔══════════════════════════════════════════════════════════════╗
# ║  Daily Cost Report - 2026-03-24                              ║
# ╠══════════════════════════════════════════════════════════════╣
# ║  Token Usage:        34,253 / 300,000 (11.4%)               ║
# ║  Estimated Cost:    $0.00 (local Ollama)                   ║
# ║  API Calls:         11                                    ║
# ║  Avg Response Time:  2.3s                                   ║
# ║  Status:           ✓ Within budget                        ║
# ╚══════════════════════════════════════════════════════════════╝
```

### 5.2 Budget Alerts
```yaml
# config/budget-alerts.yaml
alert_thresholds:
  warning: 200000   # 66% of daily limit
  critical: 250000  # 83% of daily limit
  emergency: 290000 # 97% of daily limit

actions:
  warning:
    - log_alert: true
    - notify_dashboard: true
  critical:
    - log_alert: true
    - reduce_agent_complexity: true
    - notify_user: true
  emergency:
    - log_alert: true
    - pause_non_critical_agents: true
    - send_notification: "Daily budget nearly exhausted"
```

---

## 6. Performance Optimization

### 6.1 GPU Acceleration Verification
```powershell
# Check GPU utilization
nvidia-smi

# Expected output should show:
# - GPU: RTX 5060
# - Memory: ~4-8GB used
# - Utilization: 50-90% during inference
```

### 6.2 Model Selection Strategy
| Task Type | Recommended Model | GPU Memory | Speed |
|-----------|-------------------|------------|-------|
| Simple Q&A | gemma3:1b | 1GB | Fast |
| Code generation | llama3.1:8b | 6GB | Medium |
| Complex reasoning | deepseek-r1:8b | 8GB | Slow |
| Pattern recognition | gpt-oss:20b | 14GB | Very Slow |

### 6.3 Caching Strategy
```javascript
// lib/smart-cache.js
class LayerCache {
  constructor() {
    this.cache = new Map();
    this.ttl = 3600000; // 1 hour
  }
  
  async get(query, layer) {
    const key = `${query.hash()}:${layer}`;
    const cached = this.cache.get(key);
    
    if (cached && Date.now() - cached.timestamp < this.ttl) {
      return cached.value;
    }
    
    const result = await this.compute(query, layer);
    this.cache.set(key, { value: result, timestamp: Date.now() });
    return result;
  }
}
```

---

## 7. Troubleshooting Guide

### Problem: Budget Guard returns 429 (rate limit)
**Solution:**
```powershell
# 1. Check current usage
curl.exe http://localhost:4001/health

# 2. If near limit, wait or increase limit
$env:DAILY_TOKEN_LIMIT=500000
node budgetGuard.mjs

# 3. Or reduce agent complexity temporarily
node scripts/reduce-complexity.js --factor=0.5
```

### Problem: Ollama model not found
**Solution:**
```powershell
# Pull the model
ollama pull llama3.1:8b-instruct-q4_0

# Verify
ollama list
```

### Problem: WebSocket connection drops repeatedly
**Solution:**
```powershell
# Check WebSocket reconfiguration
node scripts/websocket-diagnostic.js

# Restart with exponential backoff
node scripts/restart-websocket.js --backoff=exponential
```

### Problem: Memory leak (agent using too much RAM)
**Solution:**
```powershell
# Force garbage collection
node scripts/force-gc.js

# Prune old messages immediately
node scripts/prune-messages.js --immediate

# Restart agent with memory limits
node start-agent.js --max-memory=4GB
```

---

## 8. Monitoring Dashboard (Optional)

### Quick Setup
```powershell
# Install monitoring dashboard
npm install -g pm2
pm2 start dashboard/server.js

# Access dashboard
Start-Process http://localhost:8080
```

### Dashboard Features:
- Real-time token usage graph
- Agent health status (all 48 layers)
- WebSocket connection status
- Memory usage by layer
- CPS score trends
- Drift detection alerts

---

## 9. Next Steps

1. **Deploy Budget Guard** (if not running): `node budgetGuard.mjs`
2. **Start Ollama** (if not running): `ollama serve`
3. **Choose deployment mode** (A, B, or C above)
4. **Run health checks**: `node scripts/health-check.js`
5. **Begin operations**: Start with Mode A, scale to B or C as needed

---

**Support:** ai@deliberateensemble.works  
**Repository:** https://github.com/vortsghost2025/Deliberate-AI-Ensemble  
**License:** CC0 1.0 Universal (Public Domain)

*Built with WE. For the commons.*
