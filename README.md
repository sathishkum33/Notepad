version: "3.0"

nlu:
- intent: check_hostname
  examples: |
    - Is {hostname} present in the inventory?
    - Check if {hostname} is in the inventory
    - Do we have {hostname} in stock?
    - Is {hostname} available?

- regex:hostname
  examples: |
    - [hostname](hostname)
