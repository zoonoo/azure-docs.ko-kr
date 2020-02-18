---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 6f0bc57bf73ae1bb44f022e399c941418ccafc03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169905"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[모든 Azure 가상 네트워크 게이트웨이 연결에 사용자 지정 IPsec/IKE 정책을 적용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_CustomIpSecPolicies_Audit.json) |이 정책은 모든 Azure 가상 네트워크 게이트웨이 연결이 사용자 지정 Ipsec(인터넷 프로토콜 보안)/IKE(Internet Key Exchange) 정책을 사용함을 보장합니다. 지원되는 알고리즘 및 키 수준 - https://aka.ms/AA62kb0 |감사, 사용 안 함 |1.0.0 |
|[App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 App Service를 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[Azure VPN Gateway에서는 '기본' SKU를 사용할 수 없음](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VPNGateways_BasicSKU_Audit.json) |이 정책은 VPN Gateway가 '기본' SKU를 사용하지 않음을 보장합니다. |감사, 사용 안 함 |1.0.0 |
|[Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Cosmos DB를 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[가상 네트워크를 만들 때 네트워크 감시자 배포](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |이 정책은 가상 네트워크를 사용하여 지역에 네트워크 감시자 리소스를 만듭니다. 네트워크 감시자 인스턴스를 배포하는 데 사용되는 networkWatcherRG라는 리소스 그룹이 있는지 확인해야 합니다. |DeployIfNotExists |1.0.0 |
|[이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 이벤트 허브를 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[게이트웨이 서브넷은 네트워크 보안 그룹을 사용하여 구성해서는 안 됨](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |이 정책은 게이트웨이 서브넷이 네트워크 보안 그룹으로 구성된 경우 거부합니다. 네트워크 보안 그룹을 게이트웨이 서브넷에 할당하면 게이트웨이가 작동을 중지합니다. |deny |1.0.0 |
|[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Key Vault를 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[네트워크 인터페이스가 IP 전달을 사용하지 않아야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkIPForwardingNic_Deny.json) |이 정책은 IP 전달을 사용하도록 설정된 네트워크 인터페이스를 거부합니다. IP 전달 설정은 Azure에서 네트워크 인터페이스에 대한 원본과 대상을 확인하지 않도록 합니다. 네트워크 보안 팀에서 검토해야 합니다. |deny |1.0.0 |
|[네트워크 인터페이스에 공용 IP를 사용할 수 없음](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkPublicIPNic_Deny.json) |이 정책은 공용 IP로 구성된 네트워크 인터페이스를 거부합니다. 공용 IP 주소를 사용하면 인터넷 리소스에서 Azure 리소스로 인바운드 방식으로 통신하고, Azure 리소스에서 인터넷으로 아웃바운드 방식으로 통신할 수 있습니다. 네트워크 보안 팀에서 검토해야 합니다. |deny |1.0.0 |
|[Network Watcher를 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다. |auditIfNotExists |1.0.0 |
|[인터넷에서 RDP 액세스를 차단해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |이 정책은 인터넷에서 RDP 액세스를 허용하는 모든 네트워크 보안 규칙을 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[Service Bus는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Service Bus를 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 SQL Server를 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
|[인터넷에서 SSH 액세스를 차단해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |이 정책은 인터넷에서 SSH 액세스를 허용하는 모든 네트워크 보안 규칙을 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[스토리지 계정은 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 스토리지 계정을 감사합니다. |감사, 사용 안 함 |1.0.0 |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |1.0.0 |
|[가상 네트워크는 지정된 가상 네트워크 게이트웨이를 사용해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Network/VirtualNetwork_ApprovedVirtualNetworkGateway_AuditIfNotExists.json) |이 정책은 기본 경로가 지정된 가상 네트워크 게이트웨이를 가리키지 않는 경우 모든 가상 네트워크를 감사합니다. |AuditIfNotExists, 사용 안 함 |1.0.0 |
