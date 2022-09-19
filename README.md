# Azure DevOps Server segmentation cheat sheet (in developing)
# Introduction
## What is this?
This project was created to publish the best practices for segmentation of the CI/CD based on the Azure DevOps Server (on-premise). In general, the schemes in this project are suitable for any company.
## Schematic symbols
Elements used in network diagrams:<br/>
![Schematic symbols](https://github.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/blob/main/Assets/Network_Segmentation_Cheat_Sheet_Schematic_symbols.drawio.png)<br/>
Crossing the border of the rectangle means crossing the firewall.
## About Azure DevOps Server
[Azure DevOps Server](https://azure.microsoft.com/services/devops/server/) combines the following core products:
- [Azure Repos](https://azure.microsoft.com/services/devops/repos/) - source code storage;
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) - CI/CD service;
- [Azure Artifacts](https://azure.microsoft.com/services/devops/artifacts/) - packages storage, but often companies use another service, for example, [Jfrog Artifactory](https://jfrog.com/artifactory/).

# Segmentation cheat sheet
## Base segmentation
The best practice would be to place CI/CD service on one network and the source code repository, package repository on other networks, something like this:
![base segmentation](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.Separate%20CiCd.jpg)
## Segmentation build agents
The software build process is carried out using build agents. It is inappropriate to place build agents on the same server as the main Azure DevOps Server. The scheme below shows the placement of the build agents.

Delivery of software to the operating environment is performed by the same agents. When the build and delivery agent needs access to the Internet or other external networks, for example, to publish applications to stores or other activities, the build agent must be placed in the DMZ. The placement of the assembly agent in this case is performed in order to implement a [three-level network architecture](https://github.com/sergiomarotco/OWASP-Network-segmentation-cheat-sheet#three-layer-network-architecture).

Final scheme:

![Segmentation build agents](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.BuildAgents.jpg)

## Contractors access
Organizations often use contractors to help develop software. This speeds up development, however, it adds some risks associated with compromising the infrastructure of the development system or source code. Consider an example of insecure direct access by a contractor from the Internet to a source code repository:

![Contractors access from Internet](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.Contractors.jpg)

In this example, let's only consider access to change the source code of a repository.
As you can see, direct access from the Internet creates a risk of access by an attacker disguised as a contractor.
Further, the attacker, noticing that the pipelines in the company are described in the form of a YAML file in the same repository, the attacker will try to modify them so that they perform malicious actions. The "Everything as code" principle should be implemented safely, it should not be that untrusted persons can change the code that can harm the company's infrastructure or such that do not require review by an employee of the organization.

![Attacker disguised as a contractor](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.Contractors.Attack.jpg)

To test applications on your test environment, the contractor will need access to the test environment. To build the application locally on computers on the contractor's local network, they will need to have access to the dependencies stored in your artifact repository (Artifactory in our example). Let's try to show all these interactions, and in order to reduce the attack surface, the contractor must be able to access not directly from the Internet, but through a virtual private network.
Additionally, the best option is to provide access through intermediate controlled workstations (VDI).

![Controlled contractor access](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.Contractors.VPN.jpg)

# Mapping Threats from SLSA to Network Diagram
In the process of protecting [software supply chains](https://en.wikipedia.org/wiki/Software_supply_chain), according to various frameworks, for example, [SLSA](https://github.com/slsa-framework/slsa), it is necessary to protect these services. One way is to segment and limit access only on certain network ports to all components of the software supply chain.
## Source integrity threats
### (A) Submit unauthorized change
![Azure DevOps Server segmentation](https://raw.githubusercontent.com/sergiomarotco/Azure-DevOps-Server-segmentation-cheat-sheet/main/Assets/Azure-DevOps-Server-segmentation-cheat-sheet.%20SLSA.%20A.jpg)<br/>
