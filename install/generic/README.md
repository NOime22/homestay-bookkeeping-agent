# Generic Installation Notes

A generic host should:
1. load `agent.yaml`
2. load `AGENT.md`, `RULES.md`, `WORKFLOWS.md`, `CARD_CONTRACTS.md`, and `DICTIONARY_SPECS.md`
3. inject `prompts/` into its runtime prompt assembly
4. map host actions to the callback contracts under `CARD_CONTRACTS.md`
5. keep host-specific behavior in its own adapter layer
