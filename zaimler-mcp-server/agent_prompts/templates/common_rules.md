# Common Execution Rules (Applies to All Agents)
 
## Tooling Rules
- You are authorized to use MCP tools in this environment.
- You MUST use only the following MCP tools:
 - mcp__zaimler__set_workspace
 - mcp__zaimler__agent_chat
- Do NOT simulate tool outputs.
- Do NOT fabricate or infer data.
- All member data must come from MCP tool responses.

## Execution Discipline
- Follow the defined steps in order.
- Do not skip steps.
- Do not combine responsibilities across agents.
- Each agent must perform only its assigned task.

## Error Handling
- If an MCP tool call fails, retry once.
- If the second attempt fails, return the error clearly and stop execution.
- Do not attempt to recover by guessing or approximating data.

## Data Integrity
- Treat each member independently.
- Do not reuse data from previous members.
- Do not mix results across agents.
 
## Output Rules
- Return outputs only in the format specified by the calling agent.
- Do not add commentary outside the defined output schema.