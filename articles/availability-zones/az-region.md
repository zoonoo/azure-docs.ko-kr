---
title: 가용성 영역을 지원하는 Azure 서비스
description: Azure에서 가용성과 복원력이 높은 애플리케이션을 만들려면 가용성 영역에서 리소스를 실행하는 데 사용할 수 있는 별도의 실제 위치를 제공합니다.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 2a2e4ac57eec866d9857f564d6c76ad4a775d223
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874611"
---
# <a name="azure-services-that-support-availability-zones"></a>가용성 영역을 지원하는 Azure 서비스

Microsoft Azure 글로벌 인프라는 고객에 게 가장 높은 수준의 중복성 및 복원 력을 제공 하기 위해 모든 계층에서 설계 되 고 생성 됩니다. Azure 인프라는 지역, 지역 및 가용성 영역으로 구성 되어 있으며,이를 통해 실패의 폭발 반지름을 제한 하 여 고객 응용 프로그램 및 데이터에 잠재적 영향을 제한 합니다. Azure 가용성 영역 구문은 데이터 센터 오류 로부터 보호 하 고 고객에 게 증가 하는 HA (고가용성)를 제공 하기 위해 소프트웨어 및 네트워킹 솔루션을 제공 하기 위해 개발 되었습니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립적인 전원, 냉각 및 네트워킹을 포함 하는 하나 이상의 데이터 센터로 구성 됩니다. 지역 내에서 가용성 영역의 물리적인 분리는 대규모 시간 초과, 주요 스톰 및 슈퍼 스톰, 사이트 액세스, 안전 통로, 확장 유틸리티 작동 시간 및 리소스 가용성을 방해할 수 있는 기타 이벤트와 같은 영역 장애 로부터 응용 프로그램 및 데이터에 미치는 영향을 제한 합니다. 가용성 영역와 연결 된 데이터 센터는 한 영역이 손상 되 면 해당 지역의 다른 가용성 영역에서 서비스, 용량 및 가용성을 지원 하도록 설계 되었습니다.

모든 Azure 관리 서비스는 지역 수준 오류에서 탄력적으로 대처할 수 있도록 설계 되었습니다. 오류가 발생 하는 경우 지역 내에서 하나 이상의 가용성 영역 오류는 전체 지역 오류와 비교 하 여 더 작은 오류 반지름이 있습니다. Azure는 지역 내에서 관리 서비스의 영역 수준 오류 로부터 복구할 수 있습니다. Azure는 지역 내에서 한 번에 한 영역 내에서 중요 한 유지 관리를 수행 하 여 지역 내에서 가용성 영역에 배포 된 고객 리소스에 영향을 주는 오류를 방지 합니다.


![3 개의 영역이 있는 Azure 지역의 개념 보기](./media/az-region/azure-region-availability-zones.png)


가용성 영역를 지 원하는 Azure **서비스는** **영역, 영역 중복** 및 **비 지역** 서비스의 세 가지 범주로 나뉩니다. 고객 작업은 이러한 아키텍처 시나리오를 활용 하 여 응용 프로그램 성능 및 내구성에 맞게 분류할 수 있습니다.

- 영역 **서비스** – 리소스를 자체 선택 된 특정 가용성 영역에 배포 하 여 보다 엄격한 대기 시간 또는 성능 요구 사항을 달성할 수 있습니다.  복원 력은 지역 내 하나 이상의 영역에 응용 프로그램 및 데이터를 복제 하 여 자체 설계 됩니다.  리소스는 특정 영역에 고정 될 수 있습니다. 예를 들어 가상 머신, 관리 디스크 또는 표준 IP 주소는 특정 영역에 고정 될 수 있으며,이 경우 하나 이상의 리소스 인스턴스를 영역에 분산 하 여 복원 력을 높일 수 있습니다.

- **영역 중복 서비스** – Azure platform은 영역 간에 리소스와 데이터를 복제 합니다.  Azure는 지역 내에서 인스턴스를 자동으로 복제 하 고 배포 하므로 Microsoft는 고가용성의 제공을 관리 합니다.  예를 들어, ZRS는 영역 오류가 데이터의 HA에 영향을 주지 않도록 3 개의 영역으로 데이터를 복제 합니다. 

- **비-지역 서비스** – 서비스는 항상 Azure 지역에서 사용할 수 있으며 영역 전체 중단 뿐만 아니라 지역 전체 중단에도 복원 가능 합니다. 


Azure에서 포괄적인 비즈니스 연속성을 구현하려면 Azure 지역 쌍과 가용성 영역의 조합을 사용하여 애플리케이션 아키텍처를 빌드하십시오. 고가용성에 대한 Azure 지역 내의 가용성 영역을 사용하여 애플리케이션 및 데이터를 동기적으로 복제하고 재해 복구 보호에 대한 Azure 지역에서 비동기적으로 복제할 수 있습니다. 자세히 알아보려면 [가용성 영역를 사용 하 여 고가용성을 위한 솔루션 빌드](/azure/architecture/high-availability/building-solutions-for-high-availability)를 참조 하세요. 

## <a name="azure-services-supporting-availability-zones"></a>가용성 영역를 지 원하는 Azure 서비스

 - 이전 세대 가상 머신은 나열 되지 않습니다. 자세한 내용은 [이전 세대의 가상 머신 크기](../virtual-machines/sizes-previous-gen.md)를 참조하세요.
 - [Azure의 지역 및 가용성 영역](az-overview.md)에 설명 된 대로 일부 서비스는 비 지역입니다. 이러한 서비스는 특정 Azure 지역에 종속 되지 않으므로 영역 전체 중단 및 지역 전체 중단에 대 한 복원 력이 있습니다.  지역이 아닌 서비스 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)에서 찾을 수 있습니다.


## <a name="azure-regions-with-availability-zones"></a>가용성 영역 Azure 지역


| 아메리카           | 유럽         | 독일              | 아프리카              | 아시아 태평양   |
|--------------------|----------------|----------------------|---------------------|----------------|
|                    |                |                      |                     |                |
| 캐나다 중부     | 프랑스 중부 | 독일 중서부 | 남아프리카 공화국 북부 * | 일본 동부     |
| 미국 중부         | 북유럽   |                      |                     | 동남 아시아 |
| 미국 동부            | 영국 남부       |                      |                     | 오스트레일리아 동부 |
| 미국 동부 2          | 서유럽    |                      |                     |                |
| 미국 중 남부 |                |                      |                     |                |
| US Gov 버지니아 * |                |                      |                     |                |
| 미국 서 부 2        |                |                      |                     |                |


이러한 지역에서 가용성 영역 및 사용 가능한 서비스 지원에 대 한 자세한 내용은 Microsoft 영업 담당자나 고객 담당자에 게 문의 하세요. 가용성 영역를 지원할 예정 된 지역에 대해서는 [Azure 지역](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)을 참조 하세요.


## <a name="azure-services-supporting-availability-zones"></a>가용성 영역를 지 원하는 Azure 서비스

- 이전 세대 가상 컴퓨터는 아래에 나열 되어 있지 않습니다. 자세한 내용은 [이전 세대의 가상 컴퓨터 크기](../virtual-machines/sizes-previous-gen.md)를 참조 하세요.

- 일부 서비스는 비 지역입니다. 자세한 내용은 [Azure에서 지역 및 가용성 영역](az-overview.md) 을 참조 하세요. 이러한 서비스는 특정 Azure 지역에 종속 되지 않으므로 영역 전체 중단 및 지역 전체 중단에 탄력적으로 대처할 수 있습니다.  지역이 아닌 서비스 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)에서 찾을 수 있습니다.


### <a name="zone-resilient-services"></a>영역 복원 서비스 

: globe_with_meridians: 비 지역 서비스-서비스는 항상 Azure 지역에서 사용할 수 있으며 지역 전체의 작동 중단 뿐만 아니라 영역 전체 중단에 대해 복원 가능 합니다.

: large_blue_diamond: 영역 전체의 작동 중단에 대 한 복원 력 

**기본 서비스**

|     제품                                                    | 복원력             |
|-----------------------------------------------------------------|:----------------------------:|
|     저장소 계정                                           | : large_blue_diamond:  |
|     Application Gateway (V2)                                  | : large_blue_diamond:  |
|     Azure Backup                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage Gen 2                             | : large_blue_diamond:  |
|     Azure Express 경로                                       | : large_blue_diamond:  |
|     Azure 공용 IP                                           | : large_blue_diamond:  |
|     Azure SQL Database (일반 용도 계층)                 | : large_blue_diamond:  |
|     Azure SQL Database (Premium & 중요 비즈니스용 계층)     | : large_blue_diamond:  |
|     Disk Storage                                                | : large_blue_diamond:  |
|     Event Hubs                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Service Bus                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     저장소: 핫/쿨 Blob Storage 계층                      | : large_blue_diamond:  |
|     저장소: Managed Disks                                    | : large_blue_diamond:  |
|     Virtual Machines 확장 집합                               | : large_blue_diamond:  |
|     Virtual Machines                                          | : large_blue_diamond:  |
|     Virtual Machines: Av2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Bs-Series                               | : large_blue_diamond:  |
|     Virtual Machines: DSv2-Series                             | : large_blue_diamond:  |
|     Virtual Machines: DSv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Dv2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Dv3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: ESv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Ev3-Series                              | : large_blue_diamond:  |
|     Virtual Network                                           | : large_blue_diamond:  |
|     VPN Gateway                                                 | : large_blue_diamond:  |


**기본 서비스**

| 제품                                        | 복원력 |
|-------------------------------------------------|:------------:|
| App Service Environment                        |      : large_blue_diamond:  |
| Azure Active Directory Domain Services          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Azure Cache for Redis                           |      : large_blue_diamond:  |
| Azure Cognitive Services: Text Analytics        |      : large_blue_diamond:  |
| Azure Data Explorer                             |      : large_blue_diamond:  |
| Azure Database for MySQL – 유연한 서버      |      : large_blue_diamond:  |
| Azure Database for PostgreSQL – 유연한 서버 |      : large_blue_diamond:  |
| Azure DDoS Protection                           |      : large_blue_diamond:  |
| Azure Firewall                                  |      : large_blue_diamond:  |
| Azure Firewall Manager                          |      : large_blue_diamond:  |
| AKS(Azure Kubernetes Service)                  |      : large_blue_diamond:  |
| Azure Private Link                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Container Registry                              |      : large_blue_diamond:  |
| Event Grid                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| 프리미엄 Blob Storage                            |      : large_blue_diamond:  |
| Virtual Machines: Ddsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Ddv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dsv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dv4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Edsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Edv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Esv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Ev4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Fsv2-Series                   |      : large_blue_diamond:  |
| Virtual Machines: M 시리즈                      |      : large_blue_diamond:  |
| 가상 WAN                                     |      : large_blue_diamond:  |


**비 지역별**

|     제품                                  |     복원력    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Azure Bot Service                        |     : globe_with_meridians:             |
|     IoT 용 Azure Defender                  |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Azure Lighthouse                        |     : globe_with_meridians:             |
|     Azure Managed Applications              |     : globe_with_meridians:             |
|     Azure Maps                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Azure 리소스 그래프                    |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack Edge                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Microsoft Azure에 대 한 고객 Lockbox    |     : globe_with_meridians:             |
|     Microsoft Azure 피어 링 서비스         |     : globe_with_meridians:             |
|     Microsoft Azure portal                  |     : globe_with_meridians:             |
|     Security Center                         |     : globe_with_meridians:             |
|     Traffic Manager                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>가용성 영역 Vm에 대 한 가격 책정

가용성 영역에 배포된 가상 머신에 대한 추가 비용은 없습니다. 자세한 내용은 [대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조 하세요.


## <a name="get-started-with-availability-zones"></a>가용성 영역 시작하기

- [가상 머신 만들기](../virtual-machines/windows/create-portal-availability-zone.md)
- [PowerShell을 사용하여 Managed Disk 추가](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [영역 중복 가상 머신 확장 집합 만들기](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [영역 중복 프런트엔드를 통해 표준 Load Balancer를 사용하여 영역에서 VM 부하 분산](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [영역 중복 프런트엔드를 통해 표준 Load Balancer를 사용하여 영역 내에 VM 부하 분산](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [영역 중복 스토리지](../storage/common/storage-redundancy.md)
- [SQL Database 범용 계층](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Event Hubs 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Service Bus 지리적 재해 복구](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [영역 중복 가상 네트워크 게이트웨이 만들기](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Azure Cosmos DB에 대 한 영역 중복 영역 추가](../cosmos-db/high-availability.md#availability-zone-support)
- [Redis 가용성 영역에 대 한 Azure 캐시 시작](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Azure Active Directory Domain Services 인스턴스 만들기](../active-directory-domain-services/tutorial-create-instance.md)
- [가용성 영역를 사용 하는 AKS (Azure Kubernetes Service) 클러스터 만들기](../aks/availability-zones.md)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure의 지역 및 가용성 영역](az-overview.md)