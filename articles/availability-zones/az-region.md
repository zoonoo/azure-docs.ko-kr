---
title: Azure에서 가용성 영역를 지 원하는 지역
description: Azure에서 가용성과 복원력이 높은 애플리케이션을 만들려면 가용성 영역에서 리소스를 실행하는 데 사용할 수 있는 별도의 실제 위치를 제공합니다.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/07/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 888365e04566f18e5a73454fea4c96c321f5ce52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858390"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Azure에서 가용성 영역를 지 원하는 지역

가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 기능입니다. 가용성 영역에 대 한 자세한 내용은 [Azure에서 지역 및 가용성 영역](az-overview.md)을 참조 하세요.

이 섹션에서는 가용성 영역을 지 원하는 Azure 서비스와 지역을 나열 합니다.

각 지역에서 사용할 수 있는 서비스는 가용성에 대 한 예정 된 로드맵과 함께 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)에서 찾을 수 있습니다.


## <a name="americas"></a>아메리카

| **제품** | **미국 중부** | **미국 동부** | **미국 동부 2** | **미국 서부 2** | **캐나다 중부** |
|--|--|--|--|--|--|
| **컴퓨팅** |  |  |  |  |  |
| [ILB (App Service 환경)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS(Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **스토리지** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [프리미엄 파일 저장소](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **네트워킹** |  |  |  |  |  |
| [Application Gateway V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express 경로](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [표준 IP 주소](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **데이터베이스** |  |  |  |  |  |
| [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **분석** |  |  |  |  |  |
| [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **통합** |  |  |  |  |  |
| [Event Grid](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **보안** |  |  |  |  |  |
| [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>유럽

| **제품** | **프랑스 중부** | **북유럽** | **영국 남부** | **서유럽** |
|--|--|--|--|--|
| **컴퓨팅** |  |  |  |  |
| [ILB (App Service 환경)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS(Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **스토리지** |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [프리미엄 파일 저장소](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **네트워킹** |  |  |  |  |
| [Application Gateway V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express 경로](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [표준 IP 주소](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **데이터베이스** |  |  |  |  |
| [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **분석** |  |  |  |  |
| [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **통합** |  |  |  |  |
| [Event Grid](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **보안** |  |  |  |  |
| [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>아시아 태평양



| **제품** | **일본 동부** | **동남 아시아** | **오스트레일리아 동부** |
|--|--|--|--|
| **컴퓨팅** |  |  |  |
| [ILB (App Service 환경)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS(Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **스토리지** |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [프리미엄 파일 저장소](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **네트워킹** |  |  |  |
| [Application Gateway V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express 경로](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [표준 IP 주소](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **데이터베이스** |  |  |  |
| [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **분석** |  |  |  |
| [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **통합** |  |  |  |
| [Event Grid](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **보안** |  |  |  |
| [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: |  |





## <a name="other"></a>기타

또한 Azure는 다음 지역에서 가용성 영역 지원을 제공 합니다. • US Gov 버지니아 • 남아프리카 공화국 북부 • 미국 중 북부

이러한 세 지역에서 가용성 영역 지원에 대 한 자세한 내용은 Microsoft 영업 담당자나 고객 담당자에 게 문의 하세요.


## <a name="next-steps"></a>다음 단계

- [Azure의 지역 및 가용성 영역](az-overview.md)
