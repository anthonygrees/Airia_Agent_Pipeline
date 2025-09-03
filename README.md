# Airia Agent Promotion GitHub Action

This GitHub Action promotes an Airia agent definition to Airia using the `pipelineDefinition.json` file in your repository.
  
After promotion of you Airia Agent, the GitHub Action then runs Airia Evaluations to automatically test the new Agent.
  
## Setup

### Required Secrets

Add the following secrets to your GitHub repository:

1. Go to your repository → Settings → Secrets and variables → Actions
2. Add the following repository secrets:
   - **Name**: `AIRIA_API_TOKEN`
   - **Value**: Your Airia API authentication token
   - **Name**: `AIRIA_PROJECT_ID`
   - **Value**: Your Airia project identifier
   - **Name**: `AIRIA_EVALUATION_MODEL_ID`
   - **Value**: Model ID to use for agent evaluation testing

### Optional Secrets

   - **Name**: `AIRIA_API_ENDPOINT`
   - **Value**: Custom API endpoint URL (defaults to `https://prodaus.api.airia.ai/v1/PipelineImport/definition`)
   - **Name**: `AIRIA_EVALUATION_ENDPOINT`
   - **Value**: Custom evaluation endpoint URL (defaults to `https://prodaus.api.airia.ai/v1/AgentEvaluation`)

### Required Files

- `pipelineDefinition.json` must exist in the repository root

## Usage

1. Navigate to your repository on GitHub
2. Go to the "Actions" tab
3. Select "Promote Agent Definition" workflow
4. Click "Run workflow"
5. Configure the optional parameters:
   - **Conflict Resolution Strategy**: Choose how to handle conflicts (default: `SkipConflictingEntities`)
   - **Default Project Behavior**: Choose default behavior (default: `Library`)
   - **Run Evaluation**: Enable/disable automated agent evaluation after promotion (default: `true`)

## Workflow Parameters

| Parameter | Description | Required | Default | Options |
|-----------|-------------|----------|---------|---------|
| `conflict_resolution_strategy` | How to handle conflicts | No | `SkipConflictingEntities` | `SkipConflictingEntities`, `OverwriteConflictingEntities` |
| `default_project_behavior` | Default project behavior | No | `Library` | `Library`, `Project` |
| `run_evaluation` | Run automated evaluation after promotion | No | `true` | `true`, `false` |

## What it does

1. **Pre-flight checks**: Verifies that `pipelineDefinition.json` exists
2. **Correlation tracking**: Generates a unique correlation ID for tracking
3. **Agent promotion**: 
   - Reads the pipeline definition from the JSON file
   - Sends a POST request to the Airia API
   - Extracts the agent version ID from the promotion response
4. **Automated evaluation** (if enabled):
   - Creates an automated evaluation request for the promoted agent
   - Uses the configured model ID for testing
   - Provides evaluation tracking information
5. **Reporting**: Reports success or failure with detailed information for both promotion and evaluation

## Agent Evaluation Testing

When enabled (default), the workflow will automatically trigger an evaluation of your promoted agent using Airia's evaluation API. This provides:

- **Automated testing**: Evaluates your agent's performance immediately after promotion
- **Quality assurance**: Helps catch issues early in your deployment pipeline
- **Performance tracking**: Monitor evaluation results in your Airia dashboard
- **Non-blocking**: Evaluation failures won't fail the overall workflow (promotion success is maintained)

The evaluation uses the model specified in `AIRIA_EVALUATION_MODEL_ID` and can be monitored via the returned evaluation ID.

## Troubleshooting

### General Issues
- Ensure `AIRIA_API_TOKEN` and `AIRIA_PROJECT_ID` are set as repository secrets
- Verify `pipelineDefinition.json` is valid JSON and exists in the repository root
- Check the workflow logs for detailed error messages
- If using custom API endpoints, ensure `AIRIA_API_ENDPOINT` and `AIRIA_EVALUATION_ENDPOINT` secrets are properly configured

### Evaluation Issues
- Ensure `AIRIA_EVALUATION_MODEL_ID` is set as a repository secret
- Verify the model ID exists and is accessible in your Airia project
- Evaluation failures don't affect agent promotion success
- Check evaluation progress in your Airia dashboard using the provided evaluation ID
