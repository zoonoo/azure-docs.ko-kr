---
title: Azure 서비스 태그 개요
titlesuffix: Azure Virtual Network
description: 서비스 태그에 대해 자세히 알아보세요. 서비스 태그는 보안 규칙 만들기의 복잡성을 최소화하는 데 도움이 됩니다.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 568fc880711d42941fd9aef2ea19b8ac3123793a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384248"
---
# <a name="virtual-network-service-tags"></a>가상 네트워크 서비스 태그
<a name="network-service-tags"></a>

서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙에 대한 자주 업데이트하는 복잡성을 최소화합니다.

서비스 태그를 사용하여 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 또는 Azure [방화벽](https://docs.microsoft.com/azure/firewall/service-tags)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 규칙의 적절한 *소스* 또는 *대상* 필드에 서비스 태그 이름(예: **ApiManagement)을**지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다.

서비스 태그를 사용하여 공용 끝점이 있는 Azure 서비스에 액세스하는 동안 네트워크 격리를 달성하고 일반 인터넷에서 Azure 리소스를 보호할 수 있습니다. 인바운드/아웃바운드 네트워크 보안 그룹 규칙을 만들어 **인터넷으로** 의 트래픽을 거부하고 **AzureCloud** 또는 특정 Azure 서비스의 사용 가능한 다른 서비스 태그로 트래픽을 [허용합니다.](#available-service-tags)

## <a name="available-service-tags"></a>사용 가능한 서비스 태그
다음 표에는 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 규칙에서 사용할 수 있는 모든 서비스 태그가 포함되어 있습니다.

열은 태그가 다음과 같은지 여부를 나타냅니다.

- 인바운드 또는 아웃바운드 트래픽을 포함하는 규칙에 적합합니다.
- [지역](https://azure.microsoft.com/regions) 범위를 지원합니다.
- [Azure 방화벽](https://docs.microsoft.com/azure/firewall/service-tags) 규칙에서 사용할 수 있습니다.

기본적으로 서비스 태그는 전체 클라우드의 범위를 반영합니다. 또한 일부 서비스 태그는 해당 IP 범위를 지정된 영역으로 제한하여 보다 세분화된 제어를 허용합니다. 예를 들어 서비스 태그 **저장소는** 전체 클라우드에 대한 Azure 저장소를 나타내지만 **Storage.WestUS는** 범위를 WestUS 지역의 저장소 IP 주소 범위로만 좁혀요. 다음 표는 각 서비스 태그가 이러한 지역 범위를 지원하는지 여부를 나타냅니다.  

| 태그 | 목적 | 인바운드 또는 아웃바운드를 사용할 수 있습니까? | 지역일 수 있습니까? | Azure 방화벽에서 사용할 수 있습니까? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **액션 그룹** | 작업 그룹입니다. | 인바운드 | 예 | 예 |
| **ApiManagement** | Azure API 관리 전용 배포에 대한 관리 트래픽입니다. <br/><br/>*참고:* 이 태그는 지역별 제어 평면에 대한 Azure API 관리 서비스 끝점을 나타냅니다. 이를 통해 고객은 API 관리 서비스에 구성된 API, 운영, 정책, NamedValues에 대한 관리 작업을 수행할 수 있습니다.  | 인바운드 | yes | yes |
| **애플리케이션인사이트가용성** | 애플리케이션 인사이트 가용성. | 인바운드 | 예 | 예 |
| **앱 구성** | 앱 구성. | 아웃바운드 | 예 | 예 |
| **앱 서비스**    | Azure App Service 이 태그는 웹 앱 프런트 엔드에 아웃바운드 보안 규칙에 권장됩니다. | 아웃바운드 | yes | yes |
| **AppServiceManagement** | 앱 서비스 환경 전용 배포에 대한 관리 트래픽입니다. | 모두 | 예 | yes |
| **AzureActiveDirectory** | Azure Active Directory. | 아웃바운드 | 예 | yes |
| **AzureActive 디렉터리도메인 서비스** | Azure Active Directory 도메인 서비스 전용 배포에 대한 관리 트래픽입니다. | 모두 | 예 | yes |
| **Azure고급 위협 보호** | Azure 고급 위협 보호. | 아웃바운드 | 예 | 예 |
| **Azure 백업** |Azure 백업.<br/><br/>*참고:* 이 태그는 **저장소** 및 **AzureActiveDirectory** 태그에 대한 종속성을 가집니다. | 아웃바운드 | 예 | yes |
| **AzureBotService** | Azure 봇 서비스. | 아웃바운드 | 예 | 예 |
| **AzureCloud** | 모든 [데이터 센터 공용 IP 주소](https://www.microsoft.com/download/details.aspx?id=56519). | 아웃바운드 | yes | yes |
| **Azure인지 검색** | Azure 인지 검색. <br/><br/>이 태그 또는 이 태그가 적용되는 IP 주소는 인덱서가 데이터 원본에 대한 보안 액세스를 부여하는 데 사용할 수 있습니다. 자세한 내용은 [인덱서 연결 설명서를](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) 참조하십시오. <br/><br/> *참고*: 검색 서비스의 IP는 이 서비스 태그의 IP 범위 목록에 포함되지 않으며 데이터 원본의 IP 방화벽에도 **추가해야 합니다.** | 인바운드 | 예 | 예 |
| **Azure 커넥터** | 프로브/백 엔드 연결을 위한 Azure 논리 앱 커넥터입니다. | 인바운드 | yes | yes |
| **Azure컨테이너레지스트리** | Azure 컨테이너 레지스트리입니다. | 아웃바운드 | yes | yes |
| **Azure코스모스DB** | Azure 코스모스 DB. | 아웃바운드 | yes | yes |
| **AzureDatabricks** | Azure Databricks. | 모두 | 예 | 예 |
| **AzureData탐색기관리** | Azure 데이터 탐색기 관리. | 인바운드 | 예 | 예 |
| **AzureDataLake** | Azure 데이터 레이크 스토리지 Gen1. | 아웃바운드 | 예 | yes |
| **AzureDevSpaces** | Azure 개발자 공간. | 아웃바운드 | 예 | 예 |
| **AzureEvent그리드** | Azure Event Grid. <br/><br/>*참고:* 이 태그는 미국 중남부, 미국 동부, 미국 동부 2, US 서부 2 및 미국 중부에만 있는 Azure 이벤트 그리드 끝점을 다룹니다. | 모두 | 예 | 예 |
| **AzureFrontDoor.프런트 엔드** <br/> **AzureFrontDoor.백 엔드** <br/> **AzureFrontDoor.퍼스트 파티**  | Azure 정문. | 모두 | 예 | 예 |
| **AzureInformationProtection** | Azure 정보 보호.<br/><br/>*참고:* 이 태그는 **AzureActiveDirectory,** **AzureFrontDoor.Frontend** 및 **AzureFrontDoor.FirstParty** 태그에 대한 종속성을 가집니다. | 아웃바운드 | 예 | 예 |
| **AzureIoTHub** | Azure IoT 허브. | 아웃바운드 | 예 | 예 |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*참고:* 이 태그에는 **AzureActiveDirectory** 태그에 대한 종속성이 있습니다. | 아웃바운드 | yes | yes |
| **Azure로드 밸레인저** | Azure 인프라 로드 밸러커입니다. 태그는 Azure 상태 프로브가 시작된 [호스트의 가상 IP](security-overview.md#azure-platform-considerations) 주소(168.63.129.16)로 변환됩니다. Azure 로드 밸러서 리소스에 대한 트래픽은 포함되지 않습니다. Azure 로드 밸러워를 사용하지 않는 경우 이 규칙을 재정의할 수 있습니다. | 모두 | 예 | 예 |
| **AzureMachineLearning** | Azure Machine Learning입니다. | 모두 | 예 | yes |
| **AzureMonitor** | 로그 분석, 애플리케이션 인사이트, AzMon 및 사용자 지정 메트릭(GiG 엔드포인트)<br/><br/>*참고:* 로그 분석의 경우 이 태그는 **저장소** 태그에 대한 종속성을 가집니다. | 아웃바운드 | 예 | yes |
| **AzureOpen데이터 집합** | Azure 오픈 데이터 집합입니다.<br/><br/>*참고:* 이 태그에는 **AzureFrontDoor.Frontend** 및 **저장소** 태그에 대한 종속성이 있습니다. | 아웃바운드 | 예 | 예 |
| **AzurePlatformDNS** | 기본 인프라(기본값) DNS 서비스입니다.<br/><br>이 태그를 사용하여 기본 DNS를 비활성화할 수 있습니다. 이 태그를 사용할 때는 주의해야 합니다. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)읽는 것이 좋습니다. 또한 이 태그를 사용하기 전에 테스트를 수행하는 것이 좋습니다. | 아웃바운드 | 예 | 예 |
| **Azure PlatformIMDS** | 기본 인프라 서비스인 IMDS(Azure 인스턴스 메타데이터 서비스)입니다.<br/><br/>이 태그를 사용하여 기본 IMDS를 비활성화할 수 있습니다. 이 태그를 사용할 때는 주의해야 합니다. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)읽는 것이 좋습니다. 또한 이 태그를 사용하기 전에 테스트를 수행하는 것이 좋습니다. | 아웃바운드 | 예 | 예 |
| **AzurePlatformLKM** | Windows 라이선스 또는 키 관리 서비스.<br/><br/>이 태그를 사용하여 라이선스의 기본값을 비활성화할 수 있습니다. 이 태그를 사용할 때는 주의해야 합니다. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)읽는 것이 좋습니다.  또한 이 태그를 사용하기 전에 테스트를 수행하는 것이 좋습니다. | 아웃바운드 | 예 | 예 |
| **AzureResourceManager** | Azure 리소스 관리자입니다. | 아웃바운드 | 예 | 예 |
| **AzureSignalR** | Azure 신호기. | 아웃바운드 | 예 | 예 |
| **AzureSiteRecovery** | Azure 사이트 복구.<br/><br/>*참고:* 이 태그는 **AzureActiveDirectory,** **AzureKeyVault,** **EventHub,****GuestAndHybridManagement** 및 **저장소** 태그에 대한 종속성을 가집니다. | 아웃바운드 | 예 | 예 |
| **AzureTrafficManager** | Azure 트래픽 관리자 프로브 IP 주소입니다.<br/><br/>트래픽 관리자 프로브 IP 주소에 대한 자세한 내용은 [Azure 트래픽 관리자 FAQ를](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)참조하십시오. | 인바운드 | 예 | yes |  
| **배치 노드 관리** | Azure Batch 전용 배포에 대한 관리 트래픽입니다. | 모두 | 예 | yes |
| **코그너티브 서비스 관리** | 주소는 Azure 인지 서비스에 대한 트래픽 범위입니다. | 아웃바운드 | 예 | 예 |
| **데이터 팩토리**  | Azure 데이터 팩터리 | 모두 | 예 | 예 |
| **데이터 팩토리 관리** | Azure 데이터 팩터리의 관리 트래픽입니다. | 아웃바운드 | 예 | 예 |
| **역학365For마케팅이메일** | Dynamics 365의 마케팅 이메일 서비스의 주소 범위입니다. | 아웃바운드 | yes | 예 |
| **탄성AFD** | 탄력성 Azure 정문. | 모두 | 예 | 예 |
| **이벤트 허브** | Azure 이벤트 허브입니다. | 아웃바운드 | yes | yes |
| **GatewayManager** | Azure VPN 게이트웨이 및 응용 프로그램 게이트웨이 전용 배포에 대한 관리 트래픽입니다. | 인바운드 | 예 | 예 |
| **게스트앤하이브리드매니지먼트** | Azure 자동화 및 게스트 구성. | 아웃바운드 | 예 | yes |
| **HDInsight** | Azure HDInsight. | 인바운드 | yes | 예 |
| **인터넷** | 가상 네트워크 외부에 있고 공용 인터넷을 통해 연결할 수 있는 IP 주소 공간입니다.<br/><br/>주소 범위에는 [Azure 소유의 공용 IP 주소 공간이 포함됩니다.](https://www.microsoft.com/download/details.aspx?id=41653) | 모두 | 예 | 예 |
| **LogicApps** | 논리 애플 리 케이 션. | 모두 | 예 | 예 |
| **로직앱스매니지먼트** | 논리 앱에 대한 관리 트래픽입니다. | 인바운드 | 예 | 예 |
| **마이크로소프트클라우드앱시큐리티** | Microsoft Cloud App Security | 아웃바운드 | 예 | 예 |
| **마이크로소프트 컨테이너레지스트리** | Microsoft 컨테이너 이미지에 대한 컨테이너 레지스트리입니다. <br/><br/>*참고:* 이 태그는 **AzureFrontDoor.FirstParty** 태그에 대한 종속성을 가집니다. | 아웃바운드 | yes | yes |
| **파워쿼리온라인** | 전원 쿼리 온라인. | 모두 | 예 | 예 |
| **서비스 버스** | 프리미엄 서비스 계층을 사용하는 Azure Service 버스 트래픽입니다. | 아웃바운드 | yes | yes |
| **ServiceFabric** | Azure 서비스 패브릭입니다.<br/><br/>*참고:* 이 태그는 지역별 제어 평면에 대한 서비스 패브릭 서비스 끝점을 나타냅니다. 이를 통해 고객은 VNET(예: 엔드포인트)에서 서비스 패브릭 클러스터에 대한 관리 작업을 수행할 수 있습니다. https:// westus.servicefabric.azure.com) | 모두 | 예 | 예 |
| **Sql** | Azure SQL 데이터베이스, MySQL용 Azure 데이터베이스, PostgreSQL용 Azure 데이터베이스 및 Azure SQL 데이터 웨어하우스입니다.<br/><br/>*참고:* 이 태그는 서비스를 나타내지만 서비스의 특정 인스턴스는 나타내지 않습니다. 예를 들어 태그는 특정 SQL 데이터베이스 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다. 이 태그는 SQL 관리 인스턴스에는 적용되지 않습니다. | 아웃바운드 | yes | yes |
| **Sql관리** | SQL 전용 배포에 대한 관리 트래픽입니다. | 모두 | 예 | yes |
| **스토리지** | Azure Storage. <br/><br/>*참고:* 이 태그는 서비스를 나타내지만 서비스의 특정 인스턴스는 나타내지 않습니다. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다. | 아웃바운드 | yes | yes |
| **스토리지싱크서비스** | 스토리지 동기화 서비스. | 모두 | 예 | 예 |
| **윈도우 버추얼 데스크톱** | 윈도우 가상 데스크톱. | 모두 | 예 | 예 |
| **가상 네트워크** | 가상 네트워크 주소 공간(가상 네트워크에 대해 정의된 모든 IP 주소 범위), 연결된 모든 온-프레미스 주소 공간, [피어-가상](virtual-network-peering-overview.md) 네트워크, [가상 네트워크 게이트웨이에](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)연결된 가상 네트워크, [호스트의 가상 IP 주소](security-overview.md#azure-platform-considerations)및 사용자 정의 [경로에](virtual-networks-udr-overview.md)사용되는 주소 접두사. 이 태그에는 기본 경로도 포함될 수 있습니다. | 모두 | 예 | 예 |

>[!NOTE]
>클래식 배포 모델(Azure Resource Manager 이전)에서는 이전 테이블에 나열된 태그의 하위 집합이 지원됩니다. 이러한 태그의 철자는 다음과 같습니다.
>
>| 클래식 맞춤법 | 등가 리소스 관리자 태그 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| 인터넷 | 인터넷 |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 서비스의 서비스 태그는 사용 중인 특정 클라우드의 주소 접두사를 나타냅니다. 예를 들어 Azure Public 클라우드의 **Sql** 태그 값에 해당하는 기본 IP 범위는 Azure China 클라우드의 기본 범위와 다릅니다.

> [!NOTE]
> Azure 저장소 또는 Azure SQL Database와 같은 서비스에 대한 [가상 네트워크 서비스 끝점을](virtual-network-service-endpoints-overview.md) 구현하는 경우 Azure는 서비스에 대한 가상 네트워크 서브넷에 [경로를](virtual-networks-udr-overview.md#optional-default-routes) 추가합니다. 경로의 주소 접두사는 해당 서비스 태그와 동일한 주소 접두사 또는 CIDR 범위입니다.

## <a name="service-tags-on-premises"></a>온-프레미스 서비스 태그  
온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 가져올 수 있습니다. 이 정보는 각 서비스 태그에 해당하는 IP 범위의 현재 시점 목록입니다. 다음 섹션에 설명된 대로 프로그래밍 방식으로 또는 JSON 파일 다운로드를 통해 정보를 얻을 수 있습니다.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>서비스 태그 검색 API(공개 미리 보기) 사용
IP 주소 범위 세부 정보와 함께 현재 서비스 태그 목록을 프로그래밍 방식으로 검색할 수 있습니다.

- [나머지](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure 파워쉘](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 공개 미리 보기에 있는 동안 검색 API는 JSON 다운로드에서 반환되는 정보보다 최신 정보가 아닌 정보를 반환할 수 있습니다. (다음 섹션을 참조하십시오.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>다운로드 가능한 JSON 파일을 사용하여 서비스 태그 검색 
IP 주소 범위 세부 정보와 함께 현재 서비스 태그 목록이 포함된 JSON 파일을 다운로드할 수 있습니다. 이러한 목록은 매주 업데이트되고 게시됩니다. 각 클라우드의 위치는 다음과 같습니다.

- [Azure 공용](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>이 정보의 하위 집합은 [Azure Public,](https://www.microsoft.com/download/details.aspx?id=41653) [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)및 [Azure Germany에](https://www.microsoft.com/download/details.aspx?id=54770)대한 XML 파일에 게시되었습니다. 이러한 XML 다운로드는 2020년 6월 30일까지 사용되지 않으며 해당 날짜 이후에는 더 이상 사용할 수 없습니다. 이전 섹션에서 설명한 대로 검색 API 또는 JSON 파일 다운로드를 사용 하 여 마이그레이션 해야 합니다.

### <a name="tips"></a>팁 
- JSON 파일의 *증가된 changeNumber* 값을 확인하여 한 발행물에서 다음 게시로의 업데이트를 검색할 수 있습니다. 각 하위 섹션(예: **Storage.WestUS)에는**변경 사항이 발생할 때 증가하는 자체 *changeNumber가* 있습니다. 파일 의 변경의 최상위 *수준번호는* 하위 섹션 중 어느 것이라도 변경될 때 증가합니다.
- 서비스 태그 정보를 구문 분석하는 방법(예: WestUS의 저장소에 대한 모든 주소 범위 받기)의 예는 [서비스 태그 검색 API PowerShell](https://aka.ms/discoveryapi_powershell) 설명서를 참조하십시오.

## <a name="next-steps"></a>다음 단계
- [네트워크 보안 그룹을 만드는](tutorial-filter-network-traffic.md)방법에 대해 알아봅니다.
