# Claude Code + Zaimler MCP Integration Guide

Complete guide for using Claude Code with the Zaimler MCP server.

## Prerequisites

1. **Claude Code installed** - [Install from code.claude.com](https://code.claude.com/docs/en/overview)
2. **Docker running** - `docker ps` should work
3. **Zaimler credentials** - API key and tenant ID in `.env` file

## Quick Start

### Step 1: Install Claude Code

```bash
# macOS/Linux/WSL
curl -fsSL https://claude.ai/install.sh | bash

# Or with Homebrew
brew install --cask claude-code

# Verify installation
claude --version
```

### Step 2: Prepare Your Environment (Skip if using docker image)

```bash
# Navigate to the project directory
cd zaimler-mcp-server

# Verify your .env file has the correct credentials. Use .env.example to set up your .env file.
cat .env
```

Your `.env` should look like this (with your actual values):

```bash
ZAIMLER_API_KEY=your-actual-api-key
ZAIMLER_TENANT_ID=your-actual-tenant-id
ZAIMLER_API_BASE=https://prod.apollo.zaimler.ai/
TEMPLATES_PATH=/data/templates.json
```

### Step 3: Build Docker Image (Skip if using pulled docker image)

```bash
# Build the Docker image (one-time setup)
docker-compose build

# Verify image was created
docker images | grep zaimler-mcp
```

Expected output:

```
zaimler-mcp    latest    xxxxx    X minutes ago    354MB
```

**Note:** You do NOT need to run `docker-compose up`. Claude Code will start/stop containers automatically.

### Step 4: Add MCP Server to Claude Code (Skip if done already)

```bash
# Navigate to your project directory (important!)
cd zaimler-mcp-server

# Add the Zaimler MCP server using Claude Code CLI
claude mcp add zaimler -- docker run --rm -i -v zaimler_templates:/data --env-file .env zaimler-mcp:latest
```

Expected output:

```
Added stdio MCP server zaimler with command: docker run ...
File modified: /Users/mansirana/Desktop/projects/zaimler-mcp-server/.claude.json
```

**Important:** Run this command from the `zaimler-mcp-server` directory so the config is saved in the project-specific `.claude.json` file.

### Step 5: Verify MCP Server Connection

```bash
# List configured MCP servers
claude mcp list
```

Expected output:

```
Checking MCP server health...
zaimler: docker run ... - ✓ Connected
```

### Step 6: Start Using Claude Code!

```bash
# Start Claude Code from your project directory
cd zaimler-mcp-server
claude
```

**How it works:** When you ask Claude Code to use a Zaimler tool:

1. Claude Code automatically starts a Docker container (`docker run --rm -i ...`)
2. Executes the MCP tool inside the container
3. Returns the results to you
4. Container stops and removes itself (`--rm` flag)

The container is **self-contained** and **ephemeral** - no background processes needed!

## Testing Commands

### Test 0: Verify MCP Connection

First, check that Claude Code sees the MCP server:

```
/mcp
```

Expected output should show:

```
zaimler - ✓ Connected
  Tools: set_workspace, agent_chat, save_template, execute_template
```

### Test 1: Check Available Tools

In Claude Code, type:

```
What tools are available from the zaimler MCP server?
```

Expected response: Claude should list the 4 Zaimler tools:

- `set_workspace`
- `agent_chat`
- `save_template`
- `execute_template`

### Test 2: List Workspaces

```
Use the zaimler set_workspace tool to show me all available workspaces
```

Expected: List of your Zaimler workspaces

### Test 3: Set Workspace

```
Switch to the Insurance workspace using the zaimler set_workspace tool
```

Expected: Confirmation that workspace was switched

### Test 4: Query Data

```
Use zaimler chat to find all claims filed by Williams
```

Expected:

- Agent's response with claim data
- Extracted Cypher query
- Reasoning traces

### Test 5: Save Template

```
Take the Cypher query from the previous result and save it as a template named "claims_by_lastname"
```

Expected:

- Parameterized query showing `$param1` instead of 'Williams'
- Confirmation template was saved
- Important: Verify the template. You can also ask Claude to correct and save if needed.

### Test 6: Execute Template

```
Execute the "claims_by_lastname" template to fetch claims for these last names: Taylor, Thomas, Smith, Miller, Brown
```

Expected:

- Query results for Taylor, Thomas, Smith, Miller, Brown
- Row count

## Complete Workflow Test

Run this full workflow to test everything:

```bash
claude
```

Then paste this:

```
Help me test the Zaimler MCP server:

1. First, show me all available Zaimler workspaces
2. Switch to the "Insurance" workspace
3. Query for all claims filed by Williams
4. Save the extracted Cypher query as a template named "test_claims_query"
5. Execute that template with param1 set to "Smith"
6. Show me a summary of what we accomplished
```

## How It Works

**Self-Contained Execution:**

- Each time Claude Code calls a Zaimler tool, it runs: `docker run --rm -i ...`
- The `--rm` flag means the container is automatically removed after execution
- Templates persist because they're stored in the `zaimler_templates` Docker volume
- No background containers needed - everything is on-demand

## Advanced Usage

### Scripted Queries

```bash
# Query and save to file
echo "list all high-value claims" | claude -p "Use Zaimler agent_chat for this query and save results as JSON"

# Automated template execution
claude -p "Execute the claims_by_lastname template with param1=Anderson and email me the results"
```

### Example Prompts

### Data Exploration

```
Use Zaimler to show me the schema of the Claims entity

Query Zaimler for all policies created in the last 30 days

Find the top 10 customers by claim count
```

### Template Management

```
Show me all saved Zaimler templates

Create a template for finding policies by status

Update the claims_by_lastname template to also filter by date
```
