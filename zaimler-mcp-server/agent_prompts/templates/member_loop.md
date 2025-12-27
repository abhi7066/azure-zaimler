# Member Processing Loop (Controller Responsibility)
 
## Looping Rule
When multiple member_ids are provided, the controller agent must:
 
- Process members sequentially, one at a time.
- Fully complete all worker-agent steps for one member before moving to the next.
- Reset context between members.
- Never reuse data, intermediate results, or reasoning from a previous member.
 
## Execution Pattern (Logical)
For each member_id in member_id_list:
1. Invoke Coverage Risk Worker
2. Invoke Medical Risk Worker
3. Invoke Medication Adherence Risk Worker
4. Aggregate results
5. Emit final output for that member
 
## Isolation Guarantee
- Each member must be treated as an independent execution unit.
- Failures for one member must not affect other members.
- Outputs must be clearly associated with the corresponding member_id.
 
## Output Handling
- Return results as an array of per-member outputs when multiple members are processed.
- Preserve the defined output schema for each member.