# Execution

A Knowledge Graph library or service transforms or operates on a Knowledge Graph
Spec via execution.

Note: The Knowledge Graph Spec do not require any specific serialization format
or transport mechanism. Message serialization and transport mechanisms should be
chosen by the implementing library or service.

## Executing Links

### Handling Node ID Errors

A _node ID error_ is an error raised from a particular link during execution.
These errors should be reported at runtime.

If a node ID (either source or target) is not found, a runtime error should be
reported.
