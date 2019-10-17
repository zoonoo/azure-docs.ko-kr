---
title: 가상 네트워크에 Azure SSIS 통합 런타임 조인 | Microsoft Docs
description: Azure 가상 네트워크에 Azure SSIS 통합 런타임을 조인 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 065f69cc98f05fcb19648f190a7dba4b43da1a9a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326628"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인
Azure Data Factory에서 SSIS (SQL Server Integration Services)를 사용 하는 경우 다음과 같은 시나리오에서 azure SSIS IR (통합 런타임)을 Azure virtual network에 조인 해야 합니다. 

- 자체 호스팅 IR을 프록시로 구성 하거나 관리 하지 않고 Azure-SSIS IR에서 실행 되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결 하려고 합니다. 

- Azure-SSIS IR에서 실행 되는 SSIS 패키지의 가상 네트워크 서비스 끝점에서 지원 되는 Azure 서비스 리소스에 연결 하려고 합니다.

- 가상 네트워크에서 가상 네트워크 서비스 끝점 또는 관리 되는 인스턴스를 사용 하 여 Azure SQL Database에서 SSISDB (SSIS 카탈로그 데이터베이스)를 호스트 하 고 있습니다. 

Data Factory를 사용 하면 클래식 배포 모델 또는 Azure Resource Manager 배포 모델을 통해 만든 가상 네트워크에 Azure-SSIS IR를 조인할 수 있습니다. 

> [!IMPORTANT]
> 클래식 가상 네트워크는 더 이상 사용 되지 않으므로 Azure Resource Manager 가상 네트워크를 대신 사용 하세요.  이미 클래식 가상 네트워크를 사용 하는 경우 가능한 한 빨리 Azure Resource Manager virtual network로 전환 합니다.

## <a name="access-to-on-premises-data-stores"></a>온-프레미스 데이터 저장소 액세스
SSIS 패키지가 온-프레미스 데이터 저장소에 액세스 하는 경우 온-프레미스 네트워크에 연결 된 가상 네트워크에 Azure-SSIS IR를 조인할 수 있습니다. 또는 자체 호스팅 IR을 Azure-SSIS IR에 대 한 프록시로 구성 하거나 관리할 수 있습니다. 자세한 내용은 [Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조 하세요. 

Azure-SSIS IR 가상 네트워크에 가입 하는 경우 다음과 같은 중요 한 사항을 기억해 야 합니다. 

- 가상 네트워크가 온-프레미스 네트워크에 연결 되어 있지 않으면 먼저 Azure-SSIS IR 가입할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network) 를 만듭니다. 그런 다음 해당 가상 네트워크에서 온-프레미스 네트워크로 사이트 간 [VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) 또는 [express](../expressroute/expressroute-howto-linkvnet-classic.md) 경로 연결을 구성 합니다. 

- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR와 동일한 위치에 있는 온-프레미스 네트워크에 이미 연결 되어 있는 경우 해당 가상 네트워크에 IR을 조인할 수 있습니다. 

- 클래식 가상 네트워크가 Azure-SSIS IR의 다른 위치에 있는 온-프레미스 네트워크에 이미 연결 되어 있는 경우 Azure-SSIS IR 가입할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network) 를 만들 수 있습니다. 그런 다음, [클래식-Azure Resource Manager 가상 네트워크](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 연결을 구성합니다. 
 
- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR의 다른 위치에 있는 온-프레미스 네트워크에 이미 연결 되어 있는 경우 먼저 Azure-SSIS IR 가입할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md##create-a-virtual-network) 를 만들 수 있습니다. 그런 다음 Azure Resource Manager Azure Resource Manager 가상 네트워크 연결을 구성 합니다. 

## <a name="access-to-azure-services"></a>Azure 서비스에 대한 액세스
SSIS 패키지가 [virtual network 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 을 사용 하 여 지원 되는 Azure 서비스 리소스에 액세스 하 고 이러한 리소스를 Azure-SSIS IR으로 보호 하려는 경우 가상 네트워크를 사용 하 여 구성 된 가상 네트워크 서브넷에 Azure-SSIS IR를 조인할 수 있습니다. 서비스 끝점. 한편 Azure 서비스 리소스에 가상 네트워크 규칙을 추가 하 여 동일한 서브넷에서의 액세스를 허용 합니다.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL Database에서 SSIS 카탈로그 호스팅
가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database에서 SSIS 카탈로그를 호스트하는 경우 Azure-SSIS IR을 동일한 가상 네트워크 및 서브넷에 연결해야 합니다.

Azure-SSIS IR를 관리 되는 인스턴스와 동일한 가상 네트워크에 조인 하려면 Azure-SSIS IR이 관리 되는 인스턴스와 다른 서브넷에 있어야 합니다. Azure-SSIS IR를 관리 되는 인스턴스가 아닌 다른 가상 네트워크에 가입 하려면 가상 네트워크 피어 링 (동일한 지역으로 제한 됨) 또는 가상 네트워크에서 가상 네트워크로의 연결을 권장 합니다. 자세한 내용은 [응용 프로그램을 Azure SQL Database 관리 되는 인스턴스에 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조 하세요.

모든 경우에 가상 네트워크는 Azure Resource Manager 배포 모델을 통해서만 배포할 수 있습니다.

다음 섹션에 자세한 내용이 제공됩니다. 

## <a name="virtual-network-configuration"></a>가상 네트워크 구성

다음 요구 사항을 충족 하도록 가상 네트워크를 설정 합니다. 

-   @No__t-0이 Azure-SSIS IR를 호스팅하는 가상 네트워크 서브넷의 구독에서 등록 된 공급자 인지 확인 합니다. 클래식 가상 네트워크를 사용 하는 경우에는 해당 가상 네트워크의 클래식 가상 머신 참가자 역할에 `MicrosoftAzureBatch`도 가입 합니다. 

-   필요한 권한이 있는지 확인하세요. 자세한 내용은 [설정 사용 권한](#perms)을 참조 하세요.

-   Azure-SSIS IR을 호스팅할 적절한 서브넷을 선택합니다. 자세한 내용은 [서브넷 선택](#subnet)을 참조 하세요. 

-   가상 네트워크에서 자체 DNS (Domain Name System) 서버를 사용 하는 경우 [dns 서버 설정](#dns_server)을 참조 하세요. 

-   서브넷에서 NSG (네트워크 보안 그룹)를 사용 하는 경우 [Nsg 설정](#nsg)을 참조 하세요. 

-   Azure Express 경로 또는 UDR (사용자 정의 경로)을 사용 하는 경우 [Azure express 경로 또는 Udr 사용](#route)을 참조 하세요. 

-   가상 네트워크의 리소스 그룹이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인 합니다. 자세한 내용은 [리소스 그룹 설정](#resource-group)을 참조 하세요. 

-   [Azure-SSIS IR에 대 한 사용자 지정 설정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)에 설명 된 대로 Azure-SSIS IR를 사용자 지정 하는 경우 Azure-SSIS IR 노드가 미리 정의 된 172.16.0.0에서 172.31.255.255으로 개인 IP 주소를 가져옵니다. 따라서 가상 또는 온-프레미스 네트워크의 개인 IP 주소 범위가이 범위와 충돌 하지 않는지 확인 합니다.

이 다이어그램은 Azure-SSIS IR에 필요한 연결을 보여 줍니다.

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>권한 설정

Azure-SSIS IR를 만드는 사용자에 게는 다음 사용 권한이 있어야 합니다.

- SSIS IR를 Azure Resource Manager 가상 네트워크에 연결하는 경우 다음 두 가지 옵션이 있습니다.

  - 기본 제공 네트워크 참가자 역할을 사용 합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.

  - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 

- SSIS IR을 클래식 가상 네트워크에 가입 하는 경우 기본 제공 클래식 가상 머신 참가자 역할을 사용 하는 것이 좋습니다. 그렇지 않은 경우 가상 네트워크 연결 권한이 포함된 사용자 지정 역할을 정의해야 합니다.

### <a name="subnet"></a> 서브넷 선택

서브넷을 선택 하면 다음을 수행 합니다. 

-   Azure-SSIS IR을 배포 하려면 게이트웨이 서브넷을 선택 하지 마세요. 가상 네트워크 게이트웨이 전용입니다. 

-   선택한 서브넷에 Azure-SSIS IR 사용할 수 있는 충분 한 주소 공간이 있는지 확인 합니다. IR 노드 번호의 두 배 이상에 대해 사용 가능한 IP 주소를 유지 합니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 프로토콜 규칙을 위해 예약 되 고 3 개 이상의 주소가 Azure 서비스에 사용 됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   다른 Azure 서비스 (예: SQL Database 관리 되는 인스턴스, App Service 등)에서 독점적으로 사용 되는 서브넷을 사용 하지 마세요. 

### <a name="dns_server"></a>DNS 서버 설정 
Azure-SSIS IR에 연결 된 가상 네트워크에서 자체 DNS 서버를 사용 해야 하는 경우 글로벌 Azure 호스트 이름 (예: `<your storage account>.blob.core.windows.net` 이라는 Azure Storage blob)을 확인할 수 있는지 확인 합니다. 

다음 단계를 사용하는 것이 좋습니다. 

-   Azure DNS에 요청을 전달 하도록 사용자 지정 DNS를 구성 합니다. 사용자의 DNS 서버에서 Azure 재귀적 확인자 (168.63.129.16)의 IP 주소에 확인 되지 않은 DNS 레코드를 전달할 수 있습니다. 

-   사용자 지정 DNS를 가상 네트워크에 대 한 기본 DNS 서버로 설정 합니다. Azure DNS를 보조 DNS 서버로 설정 합니다. 자체 DNS 서버를 사용할 수 없는 경우 Azure 재귀적 확인자 (168.63.129.16)의 IP 주소를 보조 DNS 서버로 등록 합니다. 

자세한 내용은 [자체 DNS 서버를 사용 하는 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조 하세요. 

### <a name="nsg"></a>NSG 설정
Azure-SSIS IR에서 사용 하는 서브넷에 대 한 NSG를 구현 해야 하는 경우 다음 포트를 통해 인바운드 및 아웃 바운드 트래픽을 허용 합니다. 

| 방향 | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 | 의견 |
|---|---|---|---|---|---|---|
| 인바운드 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (IR을 리소스 관리자 가상 네트워크에 연결 하는 경우) <br/><br/>10100, 20100, 30100(IR을 클래식 가상 네트워크에 조인하는 경우)| Data Factory 서비스는 이러한 포트를 사용 하 여 가상 네트워크에 있는 Azure-SSIS IR의 노드와 통신 합니다. <br/><br/> 서브넷 수준 NSG를 만들지 여부에 관계 없이는 Azure-SSIS IR를 호스트 하는 가상 컴퓨터에 연결 된 Nic (네트워크 인터페이스 카드) 수준에서 NSG를 항상 구성 Data Factory 합니다. 지정된 포트에서 Data Factory IP 주소의 인바운드 트래픽만 해당 NIC 수준 NSG에서 허용됩니다. 서브넷 수준에서 인터넷 트래픽에 대해 이러한 포트를 여는 경우에도 IP 주소가 Data Factory 되지 않은 IP 주소의 트래픽은 NIC 수준에서 차단 됩니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | AzureCloud | 443 | 가상 네트워크의 Azure-SSIS IR 노드는이 포트를 사용 하 여 Azure Storage 및 Azure Event Hubs와 같은 Azure 서비스에 액세스 합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | 인터넷 | 80 | 가상 네트워크의 Azure-SSIS IR 노드는이 포트를 사용 하 여 인터넷에서 인증서 해지 목록을 다운로드 합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | 가상 네트워크의 Azure-SSIS IR 노드는 이러한 포트를 사용 하 여 SQL Database 서버에서 호스팅하는 SSISDB에 액세스 합니다. SQL Database 서버 연결 정책이 **리디렉션**대신 **프록시** 로 설정 된 경우에는 포트 1433만 필요 합니다. 이 아웃 바운드 보안 규칙은 가상 네트워크의 관리 되는 인스턴스에서 호스팅하는 SSISDB에 적용 되지 않습니다. |
||||||||

### <a name="route"></a>Azure Express 경로 또는 UDR 사용
[Azure express](https://azure.microsoft.com/services/expressroute/) 경로 회로를 가상 네트워크 인프라에 연결 하 여 온-프레미스 네트워크를 azure로 확장할 때 일반적인 구성에서는 강제 터널링 (BGP 경로, 0.0.0.0/0을 가상 네트워크에 보급)을 사용 합니다. 이 터널링은 검사 및 로깅을 위해 가상 네트워크 흐름에서 온-프레미스 네트워크 기기로 아웃 바운드 인터넷 트래픽을 강제로 적용 합니다. 
 
또는 검사 및 로깅을 위해 NVA (네트워크 가상 어플라이언스)를 방화벽이 나 Azure 방화벽으로 호스트 하는 다른 서브넷에 Azure-SSIS IR를 호스팅하는 서브넷에서 아웃 바운드 인터넷 트래픽을 강제로 적용 하도록 [Udrs](../virtual-network/virtual-networks-udr-overview.md) 를 정의할 수 있습니다. 

두 경우 모두, 트래픽 경로는 종속 Azure Data Factory 서비스 (특히, Azure Batch management services)에서 가상 네트워크의 Azure-SSIS IR에 필요한 인바운드 연결을 중단 합니다. 이 문제를 방지 하려면 Azure-SSIS IR를 포함 하는 서브넷에 하나 이상의 UDRs를 정의 합니다. 

Azure Express 경로 시나리오에서 Azure-SSIS IR를 호스트 하는 서브넷에 다음 홉 유형이 **인터넷** 인 0.0.0.0/0 경로를 적용할 수 있습니다. 또는 NVA 시나리오에서 기존 0.0.0.0/0 경로를 다음 홉 유형에 서 **인터넷** 에 대 한 **가상 어플라이언스** 로 수정할 수 있습니다.

![경로 추가](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

해당 서브넷에서 아웃 바운드 인터넷 트래픽을 검사 하는 기능을 잃지 않으려면 특정 UDRs을 정의 하 여 Azure Batch 관리 서비스와 다음 홉 유형이 **인터넷**인 Azure-SSIS IR 간에만 트래픽을 라우팅할 수 있습니다.

예를 들어 Azure-SSIS IR `UK South`에 있는 경우 서비스 태그 [ip 범위 다운로드 링크](https://www.microsoft.com/en-us/download/details.aspx?id=56519) 또는 [서비스 태그 검색 API](https://aka.ms/discoveryapi)를 통해 서비스 @no__t 태그의 ip 범위 목록을 가져옵니다. 그런 다음, 다음 홉 유형이 **인터넷**인 관련 IP 범위 경로의 다음 udrs를 적용 합니다.

![Azure Batch UDR 설정](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 이 방법에는 추가 유지 관리 비용이 발생 합니다. 정기적으로 IP 범위를 확인 하 고 Azure-SSIS IR 중단을 방지 하기 위해 UDR에 새 IP 범위를 추가 합니다. 매월 IP 범위를 확인 하는 것이 좋습니다. 새 IP가 서비스 태그에 표시 되 면 IP는 다른 달에 적용 됩니다. 

### <a name="resource-group"></a>리소스 그룹 설정
Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 이러한 리소스는 다음과 같습니다.
   -   이름 *\<Guid >-azurebatch-cloudserviceloadbalancer*를 사용 하는 Azure 부하 분산 장치
   -   이름이 *\<Guid >-azurebatch-cloudservicepublicip*인 AZURE 공용 IP 주소입니다.
   -   네트워크 작업 보안 그룹 *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

이러한 리소스는 IR이 시작 될 때 생성 됩니다. IR이 중지 되 면 삭제 됩니다. IR 중지를 방지 하려면 다른 리소스에서 이러한 네트워크 리소스를 다시 사용 하지 마십시오. 

가상 네트워크가 속한 리소스 그룹 또는 구독에 대 한 리소스 잠금이 없는지 확인 합니다. 읽기 전용 잠금 또는 삭제 잠금을 구성 하는 경우 IR을 시작 및 중지 하는 작업이 실패할 수 있습니다. 그렇지 않으면 IR이 응답 하지 않을 수 있습니다. 

가상 네트워크가 속한 리소스 그룹 또는 구독에서 다음 리소스를 만들지 못하도록 하는 Azure 정책이 없는지 확인 합니다. 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 인바운드 연결에 대 한 Azure-SSIS IR에 노출 되는 공용 IP 주소를 어떻게 보호할 수 있나요? 공용 IP 주소를 제거할 수 있나요?
 
    현재 Azure-SSIS IR 가상 네트워크에 연결 되 면 공용 IP 주소가 자동으로 생성 됩니다. Azure Batch 관리 서비스만 인바운드-Azure-SSIS IR에 연결 하도록 허용 하는 NIC 수준 NSG가 있습니다. 인바운드 보호를 위해 서브넷 수준 NSG를 지정할 수도 있습니다.

    공용 IP 주소를 노출 하지 않으려면 시나리오에 적용 되는 경우 [자체 호스팅 IR을 가상 네트워크 대신 Azure-SSIS IR의 프록시로 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) 하는 것이 좋습니다.
 
- Azure-SSIS IR의 고정 IP 주소를 데이터 원본에 대 한 방화벽의 허용 목록에 추가할 수 있나요?
 
    - 데이터 원본이 온-프레미스에 있는 경우 가상 네트워크를 온-프레미스 네트워크에 연결 하 고 Azure-SSIS IR를 가상 네트워크 서브넷에 연결 하면 해당 서브넷의 IP 범위를 허용 목록에 추가할 수 있습니다.
    - 데이터 원본이 virtual network 서비스 끝점에서 지원 되는 Azure 서비스인 경우 가상 네트워크에서 가상 네트워크 서비스 지점을 구성 하 고 해당 가상 네트워크 서브넷에 Azure-SSIS IR을 조인할 수 있습니다. 그런 다음 IP 범위 대신 Azure 서비스의 가상 네트워크 규칙을 사용 하 여 액세스를 허용할 수 있습니다.
    - 데이터 원본이 다른 종류의 클라우드 데이터 원본인 경우 UDR을 사용 하 여 Azure-SSIS IR에서 NVA로 또는 고정 공용 IP 주소를 사용 하 여 Azure 방화벽으로 아웃 바운드 트래픽을 라우팅할 수 있습니다. NVA 또는 Azure 방화벽의 공용 IP 주소를 허용 목록에 추가할 수 있습니다.
    - 이전 답변이 사용자의 요구를 충족 하지 않는 경우 [에는 자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)하 여 데이터 원본 액세스를 제공 하는 것이 좋습니다. 그런 다음 가상 네트워크에 Azure-SSIS IR를 조인 하는 대신 자체 호스팅 IR을 호스트 하는 컴퓨터의 IP 주소를 허용 목록에 추가할 수 있습니다.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal(데이터 팩터리 UI)
이 섹션에서는 Azure Portal 및 Data Factory UI를 사용 하 여 기존 Azure-SSIS IR를 가상 네트워크 (클래식 또는 Azure Resource Manager)에 조인 하는 방법을 보여 줍니다. 

Azure-SSIS IR 가상 네트워크에 연결 하기 전에 가상 네트워크를 올바르게 구성 해야 합니다. 가상 네트워크의 유형 (클래식 또는 Azure Resource Manager)에 적용 되는 섹션의 단계를 따릅니다. 그런 다음 세 번째 섹션의 단계에 따라 가상 네트워크에 Azure-SSIS IR을 연결 합니다. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 가상 네트워크 구성

포털을 사용 하 여 Azure-SSIS IR에 연결 하기 전에 Azure Resource Manager 가상 네트워크를 구성 합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다. 

1. [Azure portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스**를 선택합니다. **가상 네트워크**를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크** 페이지에서 **속성**을 선택합니다. 

1. **리소스 ID**에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷**을 선택 합니다. 사용 가능한 주소 수가 Azure-SSIS IR의 노드 보다 큰지 확인 합니다. 

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록 합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대 한 구독이 등록 됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   a. Azure Portal의 왼쪽 메뉴에서 **구독**을 선택 합니다. 

   b. 구독을 선택합니다. 

   다. 왼쪽에서 **리소스 공급자**를 선택 하 고, **Microsoft Batch** 가 등록 된 공급자 인지 확인 합니다. 

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="configure-a-classic-virtual-network"></a>클래식 가상 네트워크 구성
Azure-SSIS IR에 연결 하기 전에 포털을 사용 하 여 클래식 가상 네트워크를 구성 합니다. 

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다. 

1. [Azure portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스**를 선택합니다. **가상 네트워크(클래식)** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크(클래식)** 페이지에서 **속성**을 선택합니다. 

   ![클래식 가상 네트워크 리소스 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. **RESOURCE ID**에 대한 복사 단추를 선택하여 클래식 네트워크의 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷**을 선택 합니다. 사용 가능한 주소 수가 Azure-SSIS IR의 노드 보다 큰지 확인 합니다. 

   ![가상 네트워크에서 사용 가능한 주소 수](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. **MicrosoftAzureBatch**를 가상 네트워크의 **클래식 가상 머신 참가자** 역할에 조인합니다. 

    a. 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 하 고 **역할 할당** 탭을 선택 합니다. 

    !["액세스 제어" 및 "추가" 단추](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. **역할 할당 추가**를 선택합니다.

    다. **역할 할당 추가** 페이지에서 **역할**에 대해 **클래식 가상 머신 참가자**를 선택 합니다. **선택** 상자에 **ddbf3205-c6bd-46ae-8127-60eb93363864 붙여넣고**를 붙여넣고 검색 결과 목록에서 **Microsoft Azure Batch** 을 선택 합니다. 

    !["역할 할당 추가" 페이지의 검색 결과](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. **저장** 을 선택 하 여 설정을 저장 하 고 페이지를 닫습니다. 

    ![액세스 설정 저장](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    ㅁ. 참가자 목록에 **Microsoft Azure Batch**가 보이는지 확인합니다. 

    ![Azure Batch 액세스 확인](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록 합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대 한 구독이 등록 됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   a. Azure Portal의 왼쪽 메뉴에서 **구독**을 선택 합니다. 

   b. 구독을 선택합니다. 

   다. 왼쪽에서 **리소스 공급자**를 선택 하 고, **Microsoft Batch** 가 등록 된 공급자 인지 확인 합니다. 

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR을 가상 네트워크에 조인

Azure Resource Manager 가상 네트워크 또는 클래식 가상 네트워크를 구성한 후 Azure-SSIS IR 가상 네트워크에 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **데이터 팩터리**를 선택 합니다. 메뉴에 **데이터 팩터리** 가 표시 되지 않으면 **추가 서비스**를 선택한 다음 **인텔리전스 + 분석** 섹션에서 **데이터 팩터리**를 선택 합니다. 

   ![데이터 팩터리 목록](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 목록에서 Azure-SSIS IR를 사용 하 여 데이터 팩터리를 선택 합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. **작성자 및 배포** 타일을 선택합니다. 별도의 탭에 Data Factory UI가 표시됩니다. 

   ![데이터 팩터리 홈페이지](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결**을 선택한 다음, **통합 런타임** 탭으로 전환합니다. 

   !["통합 런타임" 탭](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR 실행 중인 경우 **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR의 **중지** 단추를 선택 합니다. IR을 중지할 때까지 IR을 편집할 수 없습니다. 

   ![IR 중지](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR에 대 한 **편집** 단추를 선택 합니다. 

   ![통합 런타임 편집](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. **Integration Runtime 설정** 패널에서 **다음** 단추를 선택 하 여 **일반 설정** 및 **SQL 설정** 페이지를 진행 합니다. 

1. **고급 설정** 페이지에서 다음을 수행 합니다. 

   a. **VNet 선택**옆의 확인란을 선택 합니다. 

   b. **구독**에 대해 Azure 구독을 선택합니다. 구독에서 기존 가상 네트워크를 선택할 수 있습니다. 
  
   다. **VNet 이름**에서는 해당 가상 네트워크를 선택합니다. 

   d. **서브넷 이름**에서는 해당 가상 네트워크의 서브넷을 선택합니다. 

   ㅁ. 자체 호스팅 IR을 Azure-SSIS IR에 대 한 프록시로 구성 하거나 관리 하려는 경우에는 **자체 호스팅 설정** 확인란을 선택 합니다. 자세한 내용은 [Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조 하세요.

   f. **VNet 유효성 검사** 단추를 선택 합니다. 유효성 검사에 성공 하면 **다음** 단추를 선택 합니다. 

   ![IR 설치를 위한 고급 설정](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. **요약** 페이지에서 Azure-SSIS IR에 대 한 모든 설정을 검토 합니다. 그런 다음 **업데이트** 단추를 선택 합니다.

1. Azure-SSIS IR의 **작업** 열에서 **시작** 단추를 선택 하 여 Azure-SSIS IR를 시작 합니다. 가상 네트워크에 연결 하는 Azure-SSIS IR를 시작 하는 데 약 20 ~ 30 분이 소요 됩니다. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>가상 네트워크 구성
Azure-SSIS IR를 가상 네트워크에 연결 하려면 먼저 가상 네트워크를 구성 해야 합니다. 가상 네트워크에 가입 하기 위해 Azure-SSIS IR에 대 한 가상 네트워크 권한 및 설정을 자동으로 구성 하려면 다음 스크립트를 추가 합니다.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR을 만들어 가상 네트워크에 조인
Azure-SSIS IR을 만드는 동시에 가상 네트워크에 조인할 수 있습니다. 전체 스크립트 및 지침은 [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)를 참조 하세요.

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>기존 Azure-SSIS IR을 가상 네트워크에 조인
[Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md) 문서에서는 Azure-SSIS IR를 만들어 동일한 스크립트의 가상 네트워크에 조인 하는 방법을 보여 줍니다. Azure-SSIS IR 이미 있는 경우 다음 단계에 따라 가상 네트워크에 가입 합니다. 
1. Azure-SSIS IR을 중지합니다. 
1. 가상 네트워크에 조인하도록 Azure-SSIS IR을 구성합니다. 
1. Azure-SSIS IR을 시작합니다. 

### <a name="define-the-variables"></a>변수를 정의합니다.
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR 중지
가상 네트워크에 연결 하려면 먼저 Azure-SSIS IR를 중지 해야 합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR이 조인하기 위한 가상 네트워크 설정을 구성합니다.

Azure SSIS가 가입할 가상 네트워크에 대 한 설정을 구성 하려면 다음 스크립트를 사용 합니다. 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR 구성
가상 네트워크에 가입 하도록 Azure-SSIS IR를 구성 하려면 `Set-AzDataFactoryV2IntegrationRuntime` 명령을 실행 합니다. 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR 시작
Azure-SSIS IR를 시작 하려면 다음 명령을 실행 합니다. 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

이 명령을 완료하는 데 20~30분이 걸립니다.

## <a name="next-steps"></a>다음 단계
Azure-SSIS IR에 대 한 자세한 내용은 다음 문서를 참조 하세요. 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR를 포함 하 여 IRs에 대 한 일반적인 개념 정보를 제공 합니다. 
- [자습서: SSIS 패키지를 Azure에 배포](tutorial-create-azure-ssis-runtime-portal.md)합니다. 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공 합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR를 만듭니다](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장 합니다. 가상 네트워크의 가상 네트워크 서비스 끝점 또는 관리 되는 인스턴스와 함께 Azure SQL Database를 사용 하 여 SSIS 카탈로그를 호스트 하는 방법에 대 한 지침을 제공 합니다. Azure-SSIS IR를 가상 네트워크에 연결 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR에 대 한 정보를 가져오는 방법을 보여 줍니다. 반환 된 정보에 대 한 상태 설명을 제공 합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR를 중지, 시작 또는 삭제 하는 방법을 보여 줍니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.
