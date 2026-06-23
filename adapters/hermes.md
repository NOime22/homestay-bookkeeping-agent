# Hermes Adapter Notes

Hermes is the current development host, not the product identity.

Use Hermes to:
- load this package as explicit agent assets
- debug card callbacks
- verify candidate extraction behavior
- inspect gateway logs
- run local tests

Do not assume hidden Hermes memory will exist in other runtimes.
All durable rules needed by the independent agent should live in this package.
