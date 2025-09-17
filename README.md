Subject: Request for Approval – Dedicated ADO Agent for Rocket Soft Product
Dear [Manager’s Name/Approver’s Name],
I am writing to request approval to provision a dedicated Azure DevOps (ADO) agent for the Rocket Soft (RS) product builds, instead of continuing with the current shared agent setup. After evaluating the build requirements and potential risks, we believe a dedicated agent will ensure stability, compliance, and efficiency for our product.
Reasons for Dedicated Agent
OS Library Management
Our product requires both 32-bit and 64-bit packages.
On a shared agent, library conflicts may occur if other applications depend on different versions of these packages.
Any future upgrades or new package installations will require coordination with other teams, adding overhead and increasing the risk of dependency conflicts.
License Handling
In the shared environment, with 3+ EC2 instances, we currently need to initiate the license process and re-install the license during each build.
This repeated effort adds unnecessary complexity and delays to our build process.
Build Stability (RS Recommendation)
RS has recommended that builds be run on a single agent to avoid issues.
If multiple builds are triggered simultaneously on a shared agent, there is a risk of output corruption.
With a dedicated agent, pipelines will queue naturally until the ongoing build completes, ensuring output integrity.
Advantages of a Dedicated Agent
Product-Specific Environment
The agent will be isolated to the RS product, eliminating dependency conflicts with other teams.
One-Time License Setup
Since it will run on a dedicated EC2 instance, we can leverage user-data (bootstart) scripts to automate the license process during instance startup.
License installation will then become a one-time activity, removing the need to repeat the process during each build.
Reliable Build Sequencing
With a single dedicated EC2 instance, build jobs will automatically queue until the current job finishes, aligning with RS’s recommendation and ensuring consistent, reliable outputs.
Request
Given the above points, I kindly request your approval to proceed with setting up a dedicated ADO agent for RS builds. This will significantly reduce risks, streamline the build process, and improve overall efficiency.
Please let me know if you would like me to provide additional details, cost estimates, or a proof-of-concept setup before moving forward.
Thank you for considering this request.