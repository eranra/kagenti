# Skills

## Overview

**Skills** are reusable capabilities managed by the Kagenti framework that expand what agents can accomplish. They encapsulate domain expertise for high-value activities such as decision support, operational analysis, compliance checks, and workflow automation. By combining structured knowledge with executable logic, skills enable agents to perform and execute operations in a consistent, context-aware manner—applying provided knowledge directly to real tasks without rebuilding logic each time.

Skills are a key component of the Kagenti workload runtime, working alongside Agents and Tools in the platform architecture. While agents provide the reasoning and orchestration layer, and tools offer specific integrations, skills deliver specialized domain knowledge and workflows that can be invoked on-demand.

## Enabling Skills

Skills are currently a feature flag in Kagenti and must be explicitly enabled before use. The method for enabling skills depends on your deployment approach:

### Using Kind and OpenShift Setup Script

When using the `scripts/kind/setup-kagenti.sh` script, skills can be enabled by setting the environment variable before running the script:

```bash
# Enable skills with the setup script
export KAGENTI_FEATURE_FLAG_SKILLS=true
./scripts/kind/setup-kagenti.sh --with-backend --with-ui
```

**Note**: The `--with-backend` and `--with-ui` flags are required to deploy the Kagenti backend and UI components where the skills feature is used.

### Using Ansible Installer

When using the Ansible installer, enable skills by modifying your values file (e.g., `deployments/envs/.secret_values.yaml` or a custom values file):

```yaml
charts:
  kagenti:
    values:
      featureFlags:
        skills: true
```

Then run the installer:

```bash
cd deployments/ansible
./run-install.sh
```

### Using Helm

When installing or upgrading Kagenti with Helm, enable skills by setting the feature flag in your values:

```bash
# Using --set flag
helm upgrade --install kagenti ./charts/kagenti/ \
  -n kagenti-system --create-namespace \
  --set featureFlags.skills=true


### Verifying Skills Are Enabled

After enabling the feature flag and restarting/upgrading your deployment:

1. **Check the UI**: Navigate to the Kagenti UI at `http://kagenti-ui.localtest.me:8080` (or your configured domain)
2. **Look for Skills Section**: The Skills management interface should now be visible in the navigation menu
3. **Verify Backend**: Check the backend logs to confirm skills routes are registered:
   ```bash
   kubectl logs -n kagenti-system -l app=kagenti-backend | grep "skills routes registered"
   ```

Once enabled, the Skills management interface will be accessible through the UI, allowing you to configure and deploy skills for your agents.

## Key Features

- **Reusable Capabilities**: Pre-built expertise for common development and operational tasks
- **Framework Integration**: Seamlessly integrated into the Kagenti platform
- **UI Configuration**: Easy setup and management through the Kagenti UI
- **Agent Enhancement**: Extend agent capabilities without custom code

## Common Use Cases

Skills are designed to handle specialized tasks that combine domain knowledge with executable business logic:

- **API Orchestration**: Coordinate multi-step API workflows based on business rules (e.g., validate customer data, call payment gateway, update inventory, send notifications)
- **Decision Automation**: Execute conditional logic for approval workflows, routing decisions, or resource allocation based on predefined criteria
- **Data Pipeline Execution**: Orchestrate data transformations, validations, and integrations across multiple systems with error handling and retry logic
- **Compliance Enforcement**: Apply regulatory rules and policies through executable checks, automated remediation, and audit trail generation
- **Event-Driven Actions**: React to system events with context-aware business logic (e.g., trigger escalations, initiate rollbacks, adjust configurations)
- **Integration Workflows**: Execute multi-service operations with knowledge of when to call specific APIs, handle authentication, manage state, and coordinate responses

## Configuring Skills

### Prerequisites

Before configuring skills, ensure you have:

1. A running Kagenti installation (see [Installation Guide](./install.md))
2. Access to the Kagenti UI
3. Appropriate permissions to manage skills

### Accessing Skills in the UI

1. **Navigate to the Kagenti UI**
   ```bash
   open http://kagenti-ui.localtest.me:8080
   ```

2. **Login** with your credentials (use `show-services.sh` to retrieve credentials if needed)

3. **Access the Skills Section**
   - From the main dashboard, navigate to the Skills management interface
   - Here you can view available skills, their status, and configuration options

### Skill Invocation

Agents can discover and invoke skills through the platform's skill registry:

```python
# Example: Agent invoking a code review skill
skill_result = await agent.invoke_skill(
    skill_name="code-review",
    parameters={
        "repository": "kagenti/kagenti",
        "pull_request": 123,
        "review_depth": "comprehensive"
    }
)
```

### Skill Discovery

Agents can query available skills:

```python
# List available skills
available_skills = await agent.list_skills()

# Get skill details
skill_info = await agent.get_skill_info("code-review")
```


### Troubleshooting and Additional Help

For additional support:

- Check the [Troubleshooting Guide](./troubleshooting.md)
- Review [Component Details](./components.md)

