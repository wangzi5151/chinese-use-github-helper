# GitHub App Development Guide

## What is GitHub App?

GitHub App is an application integrated with GitHub API, can access various GitHub features.

## GitHub App vs OAuth App

| Feature | GitHub App | OAuth App |
|---------|-----------|-----------|
| Permission Management | Fine-grained | Broad |
| Installation Method | Organization/User install | Per-user authorization |
| API Access | As App identity | As user identity |
| Recommended | ✅ | ❌ |

## Create GitHub App

### 1. Basic Information

```
1. Visit https://github.com/settings/apps/new
2. Fill in:
   - GitHub App name: your-app-name
   - Homepage URL: https://your-domain.com
   - Webhook URL: https://your-domain.com/webhook
   - Webhook secret: Generate and save
```

### 2. Permission Configuration

```yaml
Repository permissions:
  - Contents: Read & Write
  - Issues: Read & Write
  - Pull requests: Read & Write
  - Actions: Read only

Organization permissions:
  - Members: Read only

Subscribe to events:
  - push
  - pull_request
  - issues
  - issue_comment
```

### 3. Install App

```bash
# Get installation access token
curl -X POST \
  -H "Authorization: Bearer $(jwt)" \
  -H "Accept: application/vnd.github.v3+json" \
  "https://api.github.com/app/installations/{installation_id}/access_tokens"
```

## Develop GitHub App

### Using Octokit

```javascript
const { App } = require("@octokit/app");
const { Octokit } = require("@octokit/rest");

// Initialize App
const app = new App({
  appId: process.env.APP_ID,
  privateKey: process.env.PRIVATE_KEY,
  webhooks: {
    secret: process.env.WEBHOOK_SECRET,
  },
});

// Handle webhook
app.webhooks.on("push", async ({ payload }) => {
  console.log(`Push to ${payload.repository.name}`);
  console.log(`Branch: ${payload.ref}`);
  console.log(`Commits: ${payload.commits.length}`);
});

// Handle pull_request
app.webhooks.on("pull_request.opened", async ({ payload }) => {
  const octokit = await app.getInstallationOctokit(
    payload.installation.id
  );
  
  // Add comment
  await octokit.rest.issues.createComment({
    owner: payload.repository.owner.login,
    repo: payload.repository.name,
    issue_number: payload.pull_request.number,
    body: "Thank you for your PR! We've received it and will review soon.",
  });
});

// Start server
const port = process.env.PORT || 3000;
app.webhooks.listen(port, () => {
  console.log(`Server listening on port ${port}`);
});
```

### Using Probot

```javascript
// index.js
const { Probot, ProbotOctokit } = require("probot");

module.exports = (app) => {
  app.on("issues.opened", async (context) => {
    const issueComment = context.issue({
      body: "Thank you for submitting an Issue! We'll process it soon.",
    });
    await context.octokit.issues.createComment(issueComment);
  });

  app.on("pull_request.opened", async (context) => {
    // Auto-add labels
    await context.octokit.issues.addLabels(
      context.issue({
        labels: ["needs-review"],
      })
    );
  });
};
```

## Usage Scenarios

### 1. Automated Code Review

```javascript
app.webhooks.on("pull_request.opened", async ({ payload }) => {
  const octokit = await app.getInstallationOctokit(
    payload.installation.id
  );
  
  // Get PR files
  const files = await octokit.rest.pulls.listFiles({
    owner: payload.repository.owner.login,
    repo: payload.repository.name,
    pull_number: payload.pull_request.number,
  });
  
  // Check file size
  const largeFiles = files.data.filter((file) => file.changes > 100);
  
  if (largeFiles.length > 0) {
    await octokit.rest.issues.createComment({
      owner: payload.repository.owner.login,
      repo: payload.repository.name,
      issue_number: payload.pull_request.number,
      body: `⚠️ The following files have too many changes:\n${largeFiles
        .map((f) => `- ${f.filename}: ${f.changes} changes`)
        .join("\n")}`,
    });
  }
});
```

### 2. Automatic Label Management

```javascript
app.webhooks.on("pull_request.opened", async ({ payload }) => {
  const octokit = await app.getInstallationOctokit(
    payload.installation.id
  );
  
  const labels = [];
  
  // Add labels based on file paths
  const files = await octokit.rest.pulls.listFiles({
    owner: payload.repository.owner.login,
    repo: payload.repository.name,
    pull_number: payload.pull_request.number,
  });
  
  const paths = files.data.map((f) => f.filename);
  
  if (paths.some((p) => p.startsWith("src/"))) {
    labels.push("source-code");
  }
  
  if (paths.some((p) => p.startsWith("docs/"))) {
    labels.push("documentation");
  }
  
  if (paths.some((p) => p.includes("test"))) {
    labels.push("tests");
  }
  
  if (labels.length > 0) {
    await octokit.rest.issues.addLabels({
      owner: payload.repository.owner.login,
      repo: payload.repository.name,
      issue_number: payload.pull_request.number,
      labels,
    });
  }
});
```

### 3. Deployment Notifications

```javascript
app.webhooks.on("deployment", async ({ payload }) => {
  const octokit = await app.getInstallationOctokit(
    payload.installation.id
  );
  
  // Update deployment status
  await octokit.rest.repos.createDeploymentStatus({
    owner: payload.repository.owner.login,
    repo: payload.repository.name,
    deployment_id: payload.deployment.id,
    state: "in_progress",
    description: "Starting deployment...",
  });
  
  // Execute deployment logic
  try {
    await deploy(payload.deployment.ref);
    
    await octokit.rest.repos.createDeploymentStatus({
      owner: payload.repository.owner.login,
      repo: payload.repository.name,
      deployment_id: payload.deployment.id,
      state: "success",
      description: "Deployment successful!",
    });
  } catch (error) {
    await octokit.rest.repos.createDeploymentStatus({
      owner: payload.repository.owner.login,
      repo: payload.repository.name,
      deployment_id: payload.deployment.id,
      state: "failure",
      description: `Deployment failed: ${error.message}`,
    });
  }
});
```

## Deployment

### Using Vercel

```json
{
  "version": 2,
  "builds": [
    {
      "src": "index.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/webhook",
      "dest": "index.js"
    }
  ]
}
```

### Using Docker

```dockerfile
FROM node:20-slim

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

## Best Practices

1. **Minimum Permissions**: Only request necessary permissions
2. **Verify Signatures**: Verify webhook signatures
3. **Handle Rate Limits**: Implement retry mechanism
4. **Error Handling**: Handle errors gracefully
5. **Logging**: Log important operations

## Related Resources

- [GitHub App Documentation](https://docs.github.com/en/apps/creating-github-apps)
- [Octokit Documentation](https://octokit.github.io/)
- [Probot Documentation](https://probot.github.io/)