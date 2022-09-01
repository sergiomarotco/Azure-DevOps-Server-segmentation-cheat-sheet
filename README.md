# Azure-DevOps-Server-segmentation-cheat-sheet

# Introduction
## What is this?
This project was created to publish the best practices for segmentation of the CI/CD based on the Azure DevOps Serve (on-premise). In general, the schemes in this project are suitable for any company.
## About Azure DevOps Server
**Azure DevOps Server** combines the following core products:
- [Azure Repos](https://azure.microsoft.com/services/devops/repos/) - source code storage;
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) - CI/CD service;
- [Azure Artifacts](https://azure.microsoft.com/services/devops/artifacts/) - packages storage, but often companies use another service, for example, [Jfrog Artifactory](https://jfrog.com/artifactory/).

The best practice would be to place CI/CD service on one network and the source code repository, package repository on other networks, something like this:

![base segmentation](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.Separate%20CiCd.jpg)

In the process of protecting [software supply chains](https://en.wikipedia.org/wiki/Software_supply_chain), according to various frameworks, for example, [SLSA](https://github.com/slsa-framework/slsa), it is necessary to protect these services. One way is to segment and limit access only on certain network ports to all components of the software supply chain.
# Mapping Threats from SLSA to Network Diagram
## Source integrity threats
### (A) Submit unauthorized change
![Azure DevOps Server segmentation](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.jpg)<br/>
