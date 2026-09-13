# Feature Flags

## What are Feature Flags?

Feature Flags is a technology that controls feature enable/disable at runtime, allowing you to progressively release new features.

## Common Tools

| Tool | Features | Free Tier |
|------|----------|-----------|
| LaunchDarkly | Powerful features | None |
| Split.io | Experiment-driven | Yes |
| Flagsmith | Open source self-hosted | Unlimited |
| Unleash | Open source | Unlimited |
| GitHub Feature Flags | GitHub native | Yes |

## Using Flagsmith

### Configuration

```yaml
# .github/workflows/feature-flags.yml
name: Feature Flags

on:
  push:
    branches: [main]

jobs:
  update-flags:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Update feature flags
      run: |
        curl -X POST https://api.flagsmith.com/api/v1/flags/ \
          -H "Authorization: Bearer ${{ secrets.FLAGSMITH_TOKEN }}" \
          -H "Content-Type: application/json" \
          -d '{
            "feature": {
              "name": "new_dashboard",
              "description": "New dashboard UI"
            },
            "default_value": "false"
          }'
```

### Code Integration

```javascript
// Node.js
const flagsmith = require('flagsmith-node');

const flagsmithClient = new flagsmith.Flagsmith({
  environmentKey: 'your-env-key',
  identity: 'user-123',
});

// Check feature flag
const showNewDashboard = await flagsmithClient.getValue('new_dashboard', false);

if (showNewDashboard) {
  // Show new dashboard
  renderNewDashboard();
} else {
  // Show old dashboard
  renderOldDashboard();
}
```

## Using LaunchDarkly

### Configuration

```javascript
// Initialize
const ldClient = require('launchdarkly-node-server-sdk');

const client = ldClient.init('your-sdk-key');

await client.waitForInitialization();

// Check feature flag
const showNewFeature = await client.variation('new-feature-flag', user, false);

if (showNewFeature) {
  // Show new feature
}
```

## Using GitHub Feature Flags

```yaml
# .github/workflows/feature-flag.yml
name: Feature Flag

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Deploy with feature flag
      run: |
        # Decide deployment strategy based on feature flag
        if [[ "${{ github.event.head_commit.message }}" == *"[canary]"* ]]; then
          echo "Deploying canary..."
          # Canary deployment
        else
          echo "Deploying full..."
          # Full deployment
        fi
```

## Progressive Release

### Canary Release

```yaml
# .github/workflows/canary.yml
name: Canary Deployment

on:
  push:
    branches: [main]

jobs:
  canary:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Deploy canary
      run: |
        # Deploy to 10% of users
        kubectl set image deployment/my-app \
          my-app=ghcr.io/your-org/your-app:${{ github.sha }}
        
        # Wait and monitor
        sleep 300
        
        # Check error rate
        ERROR_RATE=$(curl -s https://prometheus.example.com/api/v1/query?query=rate(http_requests_total{status=~"5.."}[5m]))
        
        if (( $(echo "$ERROR_RATE > 0.01" | bc -l) )); then
          echo "Error rate too high, rolling back..."
          kubectl rollout undo deployment/my-app
        else
          echo "Canary looks good, proceeding with full deployment..."
          # Full deployment
        fi
```

### A/B Testing

```javascript
// Use in React component
import { useFeatureFlag } from 'flagsmith-react';

function Dashboard() {
  const showNewUI = useFeatureFlag('new-dashboard-ui');
  
  if (showNewUI) {
    return <NewDashboardUI />;
  }
  
  return <OldDashboardUI />;
}
```

## Best Practices

1. **Progressive Release**: Test small first, then gradually expand
2. **Monitor Metrics**: Monitor error rate and performance during release
3. **Quick Rollback**: Ability to quickly disable feature when issues occur
4. **Clean Old Code**: Remove flag-related code after feature stabilizes
5. **Document**: Record each flag's purpose and expiration time

## Related Resources

- [Feature Flags Documentation](https://docs.launchdarkly.com)
- [Flagsmith Documentation](https://docs.flagsmith.com)
- [Unleash Documentation](https://docs.getunleash.io)