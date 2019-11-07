---
title: Azure 서비스 태그 개요
titlesuffix: Azure Virtual Network
description: 서비스 태그에 대해 알아봅니다. 서비스 태그는 보안 규칙 생성의 복잡성을 최소화 하는 데 도움이 됩니다.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 0eb15d1b9b56522b9caa1bb10890eb2b485714e8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587525"
---
# <a name="virtual-network-service-tags"></a>가상 네트워크 서비스 태그 
<a name="network-service-tags"></a>

서비스 태그는 지정 된 Azure 서비스에서 IP 주소 접두사 그룹을 나타냅니다. 네트워크 보안 규칙에 대 한 빈번한 업데이트의 복잡성을 최소화 하는 데 도움이 됩니다. 서비스 태그를 사용 하 여 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 또는 [Azure 방화벽](https://docs.microsoft.com/azure/firewall/service-tags)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 *원본* 또는 *대상* 필드에서 서비스 태그 이름 (예: **microsoft.apimanagement**)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그가 들어 있는 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다. 

## <a name="available-service-tags"></a>사용 가능한 서비스 태그
다음 표에는 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 규칙에서 사용할 수 있는 모든 서비스 태그가 포함 되어 있습니다.

열은 태그가 다음과 같은지 여부를 나타냅니다.

- 인바운드 또는 아웃 바운드 트래픽을 처리 하는 규칙에 적합
- [지역](https://azure.microsoft.com/regions) 범위 지원 
- [Azure 방화벽](https://docs.microsoft.com/azure/firewall/service-tags) 규칙에서 사용할 수 있습니다.

기본적으로 서비스 태그는 전체 클라우드의 범위를 반영 합니다.  또한 일부 서비스 태그는 해당 IP 범위를 지정 된 지역으로 제한 하 여 보다 세부적인 제어를 허용 합니다.  예를 들어 서비스 태그 **저장소** 는 전체 클라우드의 Azure Storage를 나타내므로 WestUS는 WestUS 지역의 저장소 IP 주소 범위로만 축소 합니다 **.**  아래 각 서비스 태그의 설명은 이러한 지역 범위를 지원 하는지 여부를 나타냅니다.  



| 태그 | 목적 | 인바운드 또는 아웃 바운드를 사용할 수 있나요? | 지역별 일 수 있나요? | Azure 방화벽과 함께 사용할 수 있나요? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | APIM 전용 배포에 대 한 관리 트래픽 | 둘 다 | 아니요 | 예 |
| **AppService**    | App Service 서비스입니다. 이 태그는 WebApp 프런트 엔드에 대 한 아웃 바운드 보안 규칙에 권장 됩니다. | 아웃바운드 | 예 | 예 |
| **AppServiceManagement** | 전용 배포 App Service Environment에 대 한 관리 트래픽 | 둘 다 | 아니요 | 예 |
| **AzureActiveDirectory** | Azure Active Directory 서비스입니다. | 아웃바운드 | 아니요 | 예 |
| **AzureActiveDirectoryDomainServices** | 전용 배포 Azure Active Directory Domain Services에 대 한 관리 트래픽 | 둘 다 | 아니요 | 예 |
| **AzureBackup** |Azure Backup 서비스입니다.<br/><br/>*참고:* 이 태그는 **Storage** 및 **AzureActiveDirectory** 태그에 종속 됩니다. | 아웃바운드 | 아니요 | 예 |
| **AzureCloud** | 모든 [데이터 센터 공용 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653)입니다. | 아웃바운드 | 예 | 예 |
| **AzureConnectors** | 프로브/백 엔드 연결용 커넥터를 Logic Apps 합니다. | 인바운드 | 예 | 예 |
| **AzureContainerRegistry** | Azure Container Registry 서비스입니다. | 아웃바운드 | 예 | 예 |
| **Microsoft.azurecosmosdb** | Azure Cosmos 데이터베이스 서비스입니다. | 아웃바운드 | 예 | 예 |
| **AzureDataLake** | Azure Data Lake 서비스입니다. | 아웃바운드 | 아니요 | 예 |
| **AzureKeyVault** | Azure KeyVault 서비스.<br/><br/>*참고:* 이 태그는 **AzureActiveDirectory** 태그에 종속 됩니다. | 아웃바운드 | 예 | 예 |
| **AzureLoadBalancer** | Azure의 인프라 부하 분산 장치. 태그는 Azure의 상태 프로브가 시작되는 [호스트의 가상 IP 주소](security-overview.md#azure-platform-considerations)(168.63.129.16)로 변환됩니다. Azure Load Balancer를 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다. | 둘 다 | 아니요 | 아니요 |
| **AzureMachineLearning** | Azure Machine Learning 서비스입니다. | 아웃바운드 | 아니요 | 예 |
| **AzureMonitor** | Log Analytics, App Insights, AzMon 및 사용자 지정 메트릭 (4Gb 끝점)을 지정 합니다.<br/><br/>*참고:* Log Analytics의 경우이 태그는 **저장소** 태그에 종속 됩니다. | 아웃바운드 | 아니요 | 예 |
| **AzurePlatformDNS** | 기본 인프라 (기본) DNS 서비스입니다.<br/><br>이 태그를 사용 하 여 기본 DNS를 사용 하지 않도록 설정할 수 있습니다. 이 태그를 사용 하는 경우 주의 하세요. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) 참조 하는 것이 좋습니다. 이 태그를 사용 하기 전에 테스트를 수행 하는 것이 좋습니다. | 아웃바운드 | 아니요 | 아니요 |
| **AzurePlatformIMDS** | 기본 인프라 서비스인 IMDS<br/><br/>이 태그를 사용 하 여 기본 IMDS를 사용 하지 않도록 설정할 수 있습니다.  이 태그를 사용 하는 경우 주의 하세요. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) 참조 하는 것이 좋습니다. 이 태그를 사용 하기 전에 테스트를 수행 하는 것이 좋습니다. | 아웃바운드 | 아니요 | 아니요 |
| **AzurePlatformLKM** | Windows 라이선스 또는 키 관리 서비스입니다.<br/><br/>이 태그를 사용 하 여 라이선스에 대 한 기본값을 사용 하지 않도록 설정할 수 있습니다. 이 태그를 사용 하는 경우 주의 하세요.  [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) 참조 하는 것이 좋습니다. 이 태그를 사용 하기 전에 테스트를 수행 하는 것이 좋습니다. | 아웃바운드 | 아니요 | 아니요 |
| **AzureTrafficManaged** | Azure Traffic Manager 프로브 IP 주소.<br/><br/>Traffic Manager 프로브 IP 주소에 대한 자세한 내용은 [Azure Traffic Manager FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)에서 찾을 수 있습니다. | 인바운드 | 아니요 | 예 |  
| **BatchNodeManagement** | 전용 배포 Azure Batch에 대 한 관리 트래픽 | 둘 다 | 아니요 | 예 |
| **CognitiveServicesManagement** | 트래픽에 대 한 주소 범위 Cognitive Services | 아웃바운드 | 아니요 | 아니요 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 마케팅 메일 서비스의 주소 범위입니다. | 아웃바운드 | 예 | 아니요 |
| **EventHub** | Azure EventHub 서비스. | 아웃바운드 | 예 | 예 |
| **게이트웨이 관리자** | VPN/앱 게이트웨이 전용 배포에 대 한 관리 트래픽 | 인바운드 | 아니요 | 아니요 |
| **인터넷** | 가상 네트워크 외부에 있고 공용 인터넷에서 연결할 수 있는 IP 주소 공간입니다.<br/><br/>주소 범위에는 [Azure에서 소유하는 공용 IP 주소 공간](https://www.microsoft.com/download/details.aspx?id=41653)이 포함됩니다. | 둘 다 | 아니요 | 아니요 |
| **MicrosoftContainerRegistry** | Microsoft Container Registry 서비스입니다. | 아웃바운드 | 예 | 예 |
| **Service Bus** | 프리미엄 서비스 계층을 사용 하 여 서비스를 Azure Service Bus 합니다. | 아웃바운드 | 예 | 예 |
| **ServiceFabric** | Service Fabric 서비스입니다. | 아웃바운드 | 아니요 | 아니요 |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure SQL Data Warehouse 서비스입니다.<br/><br/>*참고:* 이 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 SQL 데이터베이스 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다. | 아웃바운드 | 예 | 예 |
| **SqlManagement** | SQL 전용 배포에 대 한 관리 트래픽 | 둘 다 | 아니요 | 예 |
| **스토리지** | Azure Storage 서비스입니다. <br/><br/>*참고:* 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다. | 아웃바운드 | 예 | 예 |
| **VirtualNetwork** | 가상 네트워크 주소 공간 (가상 네트워크에 대해 정의 된 모든 IP 주소 범위), 연결 된 모든 온-프레미스 주소 공간, [피어 링](virtual-network-peering-overview.md) 가상 네트워크 또는 가상 네트워크 [게이트웨이에](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)연결 된 가상 네트워크, 가상 [IP ](security-overview.md#azure-platform-considerations) [사용자 정의 경로](virtual-networks-udr-overview.md)에 사용 되는 호스트 및 주소 접두사의 주소입니다. 이 태그에는 기본 경로가 포함 될 수도 있습니다. | 둘 다 | 아니요 | 아니요 |

>[!NOTE]
>*클래식* (사전 Azure Resource Manager) 환경에서 작업 하는 경우 위의 태그의 선택 집합이 지원 됩니다.  대체 철자를 사용 합니다.

| 클래식 맞춤법 | 해당 리소스 관리자 태그 |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| 인터넷 | 인터넷 |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 서비스의 서비스 태그는 사용되는 특정 클라우드의 주소 접두사를 나타냅니다. 예를 들어 **Sql** 태그 값에 해당 하는 기본 IP 범위는 azure 공용 클라우드와 azure 중국 클라우드 간에 차이가 있습니다.

> [!NOTE]
> Azure Storage 또는 Azure SQL Database와 같은 서비스에 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 구현하는 경우 Azure는 서비스의 가상 네트워크 서브넷에 [경로](virtual-networks-udr-overview.md#optional-default-routes)를 추가합니다. 경로의 주소 접두사는 해당하는 서비스 태그와 동일한 주소 접두사 또는 CIDR 범위입니다.



## <a name="service-tags-in-on-premises"></a>온-프레미스의 서비스 태그  
온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 얻을 수 있습니다.  이 정보는 각 서비스 태그에 해당 하는 IP 범위의 현재 지정 시간 목록입니다.  이 정보는 다음과 같이 프로그래밍 방식으로 또는 JSON 파일 다운로드를 통해 얻을 수 있습니다.

### <a name="service-tag-discovery-api-public-preview"></a>서비스 태그 검색 API (공개 미리 보기)
IP 주소 범위 세부 정보를 사용 하 여 서비스 태그의 현재 목록을 프로그래밍 방식으로 검색할 수 있습니다.

- [REST (영문)](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 공개 미리 보기로 제공 되는 동안 검색 API는 JSON 다운로드로 최신이 아닌 정보를 반환할 수 있습니다 (아래 참조).


### <a name="discover-service-tags-using-downloadable-json-files"></a>다운로드 가능한 JSON 파일을 사용 하 여 서비스 태그 검색 
IP 주소 범위 세부 정보를 사용 하 여 서비스 태그의 현재 목록이 포함 된 JSON 파일을 다운로드할 수 있습니다. 매주 업데이트 되 고 게시 됩니다.  각 클라우드의 위치는 다음과 같습니다.

- [Azure 공용](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>이 정보의 하위 집합은 이전에 [Azure 공용](https://www.microsoft.com/download/details.aspx?id=41653), [Azure 중국](https://www.microsoft.com/download/details.aspx?id=42064) 및 [azure 독일](https://www.microsoft.com/download/details.aspx?id=54770)용 XML 파일에 게시 되었습니다. 이러한 XML 다운로드는 2020 년 6 월 30 일부 터 더 이상 사용 되지 않으며 해당 날짜 이후에는 더 이상 사용할 수 없습니다. 위에 설명 된 대로 검색 API 또는 JSON 파일 다운로드를 사용 하 여로 마이그레이션 하세요.

### <a name="tips"></a>팁 
- JSON 파일 내에서 증가 된 *changeNumber* 값을 통해 한 번의 게시에서 업데이트를 검색할 수 있습니다. 각 하위 섹션 (예: **WestUS**)에는 변경 사항이 발생할 때마다 증가 하는 고유한 *changeNumber* 가 있습니다.  하위 섹션이 변경 되 면 파일 *changeNumber* 의 최상위 수준이 증가 합니다.
- 서비스 태그 정보를 구문 분석 하는 방법에 대 한 예제는 (예: WestUS의 저장소에 대 한 모든 주소 범위 가져오기) [서비스 태그 검색 API PowerShell](https://aka.ms/discoveryapi_powershell) 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [네트워크 보안 그룹 만들기](tutorial-filter-network-traffic.md)에 대해 알아보세요.

