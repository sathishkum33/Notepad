version: "3.0"
stories:
- story: Check Hostname and Fetch Information
  steps:
  - intent: check_hostname
    entities:
    - hostname: "example.com"
  - action: action_check_hostname
  - action: action_fetch_information
