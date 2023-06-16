# data/nlu.yml

version: "2.0"

nlu:
- intent: deployment_request
  examples: |
    - I want to deploy my application [MyApp] to [production] using Artifactory [http://artifactory.com].
    - Can you assist with deploying [MyApp] to [development] environment? The Artifactory URL is [http://artifactory.com].
    - I need help with deploying my application [MyApp]. It should be deployed to [staging]. The Artifactory URL is [http://artifactory.com].
    - How can I deploy my application [MyApp]? I want to deploy it to [production]. Artifactory URL is [http://artifactory.com].
    - Please guide me through the deployment process for [MyApp]. It should be deployed to [production]. The Artifactory URL is [http://artifactory.com].
