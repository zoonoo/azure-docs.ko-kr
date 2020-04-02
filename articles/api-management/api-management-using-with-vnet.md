---
title: 가상 네트워크에서 Azure API Management를 사용하는 방법
description: Azure API Management에서 가상 네트워크 연결을 설정하고 웹 서비스에 액세스하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 462a44f7766e0ec52ba7156d6de5ae5261e21376
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547360"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>가상 네트워크에서 Azure API Management를 사용하는 방법
Azure VNET(Virtual Network)을 사용하면 비인터넷 라우팅 가능 네트워크(액세스를 제어하는)에 다수의 Azure 리소스를 배치할 수 있습니다. 이러한 네트워크는 다양한 VPN 기술을 사용하여 온-프레미스 네트워크에 연결될 수 있습니다. Azure Virtual Network에 대해 자세히 알아보려면 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)부터 참조하세요.

Azure API Management가 네트워크 내의 백 엔드 서비스에 액세스할 수 있도록 VNET(가상 네트워크) 내에 배포할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다.

> [!NOTE]
> API 가져오기 문서 URL은 공개적으로 액세스할 수 있는 인터넷 주소에서 호스팅되어야 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

+ 활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET 연결 사용

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Azure Portal을 사용하여 VNET 연결 사용

1. [Azure 포털로](https://portal.azure.com) 이동하여 API 관리 인스턴스를 찾습니다. API 관리 서비스를 검색하고 **선택합니다.**

2. API 관리 인스턴스를 선택합니다.

3. **가상 네트워크**를 선택합니다.
4. API Management 인스턴스를 가상 네트워크 내부에 배포되도록 구성합니다.

    ![API Management의 가상 네트워크 메뉴][api-management-using-vnet-menu]
5. 원하는 액세스 유형을 선택합니다.

    * **꺼기**: 기본값입니다. API 관리는 가상 네트워크에 배포되지 않습니다.

    * **외부**: API 관리 게이트웨이 및 개발자 포털은 외부 로드 밸런서를 통해 공용 인터넷에서 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

        ![공용 피어링][api-management-vnet-public]

    * **내부**: API 관리 게이트웨이 및 개발자 포털은 내부 로드 밸런서를 통해 가상 네트워크 내에서만 액세스할 수 있습니다. 게이트웨이에서 가상 네트워크 내의 리소스에 액세스할 수 있습니다.

        ![프라이빗 피어링][api-management-vnet-private]

6. **외부** 또는 **내부를**선택한 경우 API 관리 서비스가 프로비전된 모든 지역 목록이 표시됩니다. **위치를**선택한 다음 가상 **네트워크** 및 **서브넷을**선택합니다. 가상 네트워크 목록은 구성하려는 지역에서 설정된 Azure 구독에서 사용할 수 있는 클래식 및 리소스 관리자 가상 네트워크로 채워집니다.

    > [!IMPORTANT]
    > Resource Manager VNET에 Azure API Management 인스턴스를 배포할 때 서비스는 Azure API Management 인스턴스를 제외한 다른 리소스가 포함되어 있는 전용 서브넷에 있어야 합니다. 다른 리소스가 포함된 Resource Manager VNET 서브넷에 Azure API Management 인스턴스를 배포하려고 하면 배포가 실패합니다.

    그런 다음, **적용**을 선택합니다. API 관리 인스턴스의 **가상 네트워크** 페이지가 새 가상 네트워크 및 서브넷 선택 으로 업데이트됩니다.

    ![VPN 선택][api-management-setup-vpn-select]

7. 상단 탐색 모음에서 **저장을**선택한 다음 **네트워크 구성 적용을**선택합니다.

> [!NOTE]
> API Management 인스턴스의 VIP 주소는 VNET이 활성화되거나 비활성화될 때마다 변경됩니다.
> VIP 주소는 API 관리를 **외부에서 내부로** **Internal**이동하거나 그 반대로 이동할 때도 변경됩니다.
>

> [!IMPORTANT]
> VNET에서 API 관리를 제거하거나 배포된 Api 관리를 변경하는 경우 이전에 사용한 VNET은 최대 6시간 동안 잠겨 있을 수 있습니다. 이 기간에는 VNET를 삭제하거나 새 리소스를 배포할 수 없게 됩니다. 이 동작은 api 버전 2018-01-01 이전을 사용하는 클라이언트에 대해 마찬가지입니다. api 버전 2019-01-01 이후를 사용하는 클라이언트는 연결된 API 관리 서비스가 삭제되는 즉시 VNET이 해제됩니다.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>PowerShell cmdlet을 사용하여 VNET 연결 사용
PowerShell cmdlet을 사용하여 VNET 연결을 사용하도록 설정할 수도 있습니다.

* **VNET 내에서 API 관리 서비스 만들기**: cmdlet [New-AzApiManagement를](/powershell/module/az.apimanagement/new-azapimanagement) 사용하여 VNET 내에서 Azure API 관리 서비스를 만듭니다.

* **VNET 내부에 기존 API 관리 서비스 배포**: cmdlet [Update-AzApiManagementRegion를](/powershell/module/az.apimanagement/update-azapimanagementregion) 사용하여 기존 Azure API 관리 서비스를 가상 네트워크 내부로 이동합니다.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>가상 네트워크 내에서 호스트되는 웹 서비스에 연결
API Management 서비스가 VNET에 연결된 후에는 공용 서비스에 액세스하는 것과 동일하게 VNET 내에서 백 엔드 서비스에 액세스할 수 있습니다. 새 API를 만들거나 기존 API를 편집할 때 **웹 서비스 URL** 필드에 웹 서비스의 로컬 IP 주소 또는 호스트 이름(DNS 서버가 VNET에 대해 구성된 경우)을 입력하면 됩니다.

![VPN에서 API 추가][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>일반적인 네트워크 구성 문제
다음은 Virtual Network로 API Management 서비스를 배포하는 동안 발생할 수 있는 일반적인 구성 오류의 목록입니다.

* **사용자 지정 DNS 서버 설치**: API Management 서비스는 여러 API 서비스에 따라 달라집니다. API Management가 사용자 지정 DNS 서버를 사용하는 VNET에서 호스트되는 경우 해당 Azure 서비스의 호스트 이름을 확인해야 합니다. 사용자 지정 DNS 설정에 대한 [이](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 지침을 따르세요. 아래의 포트 테이블 및 기타 네트워크 요구 사항을 참조하세요.

> [!IMPORTANT]
> VNET에 사용자 지정 DNS 서버를 사용하려는 경우에는 API Management 서비스를 배포하기 **전에** 설정해야 합니다. 그렇지 않으면 DNS 서버를 변경할 때마다 [네트워크 구성 작업 적용](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)을 실행하여 API Management 서비스를 업데이트해야 합니다.

* **API Management에 필요한 포트**: API Management가 배포된 인바운드 및 아웃바운드 트래픽은 [네트워크 보안 그룹][Network Security Group]을 사용하여 제어할 수 있습니다. 이러한 포트를 사용할 수 없는 경우 API Management가 정상적으로 작동하지 않고 액세스하지 못하게 될 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNET에서 API Management를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.

<a name="required-ports"> </a> API 관리 서비스 인스턴스가 VNET에서 호스팅되면 다음 테이블의 포트가 사용됩니다.

| 소스/대상 포트 | Direction          | 전송 프로토콜 |   [서비스 태그](../virtual-network/security-overview.md#service-tags) <br> 원본 / 대상   | 목적( * )                                                 | 가상 네트워크 유형 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 인바운드            | TCP                | 인터넷 / VIRTUAL_NETWORK            | API Management에 대한 클라이언트 통신                      | 외부             |
| * / 3443                     | 인바운드            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure Portal 및 Powershell용 관리 엔드포인트         | 외부 및 내부  |
| * / 443                  | 아웃바운드           | TCP                | VIRTUAL_NETWORK / 스토리지             | **Azure Storage에 대한 종속성**                             | 외부 및 내부  |
| * / 443                  | 아웃바운드           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory(해당되는](api-management-howto-aad.md) 경우)                   | 외부 및 내부  |
| * / 1433                     | 아웃바운드           | TCP                | VIRTUAL_NETWORK / SQL                 | **Azure SQL 엔드포인트에 대한 액세스**                           | 외부 및 내부  |
| * / 5671, 5672, 443          | 아웃바운드           | TCP                | VIRTUAL_NETWORK / EventHub            | [로그](api-management-howto-log-event-hubs.md) 대 이벤트 허브 정책 및 모니터링 에이전트에 대한 종속성 | 외부 및 내부  |
| * / 445                      | 아웃바운드           | TCP                | VIRTUAL_NETWORK / 스토리지             | [GIT에](api-management-configuration-repository-git.md) 대한 Azure 파일 공유에 대한 종속성                      | 외부 및 내부  |
| * / 1886                     | 아웃바운드           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 리소스 상태에 상태를 게시하는 데 필요          | 외부 및 내부  |
| * / 443                     | 아웃바운드           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | [진단 로그 및 메트릭](api-management-howto-use-azure-monitor.md) 게시                       | 외부 및 내부  |
| * / 25                       | 아웃바운드           | TCP                | VIRTUAL_NETWORK / 인터넷            | 메일을 보내기 위해 SMTP 릴레이에 연결                    | 외부 및 내부  |
| * / 587                      | 아웃바운드           | TCP                | VIRTUAL_NETWORK / 인터넷            | 메일을 보내기 위해 SMTP 릴레이에 연결                    | 외부 및 내부  |
| * / 25028                    | 아웃바운드           | TCP                | VIRTUAL_NETWORK / 인터넷            | 메일을 보내기 위해 SMTP 릴레이에 연결                    | 외부 및 내부  |
| * / 6381 - 6383              | 인바운드 및 아웃바운드 | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | 컴퓨터 간 [속도 제한](api-management-access-restriction-policies.md#LimitCallRateByKey) 정책에 대한 Redis 서비스에 액세스         | 외부 및 내부  |
| * / *                        | 인바운드            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure 인프라 부하 분산 장치                          | 외부 및 내부  |

>[!IMPORTANT]
> API Management 서비스를 성공적으로 배포하려면 *목적*이 **볼드**인 포트가 필요합니다. 하지만 다른 포트를 차단할 경우 실행 중인 서비스사를 용 및 모니터링하는 기능이 저하됩니다.

+ **TLS 기능**: TLS/SSL 인증서 체인을 구축하고 유효성 검사를 수행하려면 API 관리 서비스가 ocsp.msocsp.com, mscrl.microsoft.com 및 crl.microsoft.com 아웃바운드 네트워크 연결이 필요합니다. API Management에 업로드하는 인증서에 CA 루트의 전체 체인이 포함되어 있으면 이 종속성은 필요하지 않습니다.

+ **DNS 액세스**: DNS 서버와의 통신을 위해서는 53 포트에서 아웃바운드 액세스가 필요합니다. 사용자 지정 DNS 서버가 VPN 게이트웨이의 다른 쪽 끝에 있는 경우 API Management를 호스팅하는 서브넷에서 DNS 서버에 연결할 수 있어야 합니다.

+ **메트릭 및 상태 모니터링**: Azure Monitoring 엔드포인트에 대한 아웃바운드 네트워크 연결은 다음 도메인에서 확인합니다.

+ **지역 서비스 태그**": 스토리지, SQL 및 EventHubs 서비스 태그에 대한 아웃바운드 연결을 허용하는 NSG 규칙은 API 관리 인스턴스를 포함하는 리전(예: 미국 서부 지역의 API 관리 인스턴스에 대한 Storage.WestUS)을 포함하는 지역에 해당하는 해당 태그의 지역 버전을 사용할 수 있습니다. 다중 지역 배포에서 각 지역의 NSG는 해당 지역의 서비스 태그에 대한 트래픽을 허용해야 합니다.

    | Azure 환경 | 엔드포인트                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 공용      | <ul><li>gcs.prod.monitoring.core.windows.net **(신규)**</li><li>prod.warmpath.msftcloudes.com **(더 이상 사용되지 않을)**</li><li>shoebox2.metrics.microsoftmetrics.com **(신규)**</li><li>shoebox2.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod3.metrics.microsoftmetrics.com **(신규)**</li><li>prod3.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod3-black.prod3.metrics.microsoftmetrics.com **(신규)**</li><li>prod3-black.prod3.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod3-red.prod3.metrics.microsoftmetrics.com **(신규)**</li><li>prod3-red.prod3.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com(여기서 `East US 2`는 eastus2.warm.ingestion.msftcloudes.com임)</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com **(신규)**</li><li>shoebox2.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod3.metrics.microsoftmetrics.com **(신규)**</li><li>prod3.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com **(신규)**</li><li>shoebox2.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod3.metrics.microsoftmetrics.com **(신규)**</li><li>prod3.metrics.nsatc.net **(더 이상 사용되지 않을)**</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> dns 영역 **.nsatc.net .microsoftmetrics.com** 위의 클러스터를 **변경하는** 것은 대부분 DNS 변경입니다. 클러스터의 IP 주소는 변경되지 않습니다.

+ **SMTP 릴레이**: `smtpi-co1.msn.com`호스트 , `smtpi-ch1.msn.com`및 `smtpi-db3.msn.com` `smtpi-sin.msn.com``ies.global.microsoft.com`

+ **개발자 포털 CAPTCHA**: 호스트 `client.hip.live.com` 및 `partner.hip.live.com`에서 해결되는 개발자 포털의 CAPTCHA에 대한 아웃바운드 네트워크 연결

+ **Azure Portal 진단**: Virtual Network 내부에서 API Management 확장을 사용할 때 Azure Portal에서 진단 로그의 흐름을 사용하도록 설정하려면 포트 443에서 `dc.services.visualstudio.com`에 대한 아웃바운드 액세스가 필요합니다. 이는 확장을 사용할 때 발생할 수 있는 문제 해결에 도움이 됩니다.

+ **익스프레스 경로 또는 네트워크 가상 어플라이언스를 사용하여 온프레미스 방화벽으로 트래픽을 터널링하는**경우: 일반적인 고객 구성은 API 관리 위임서브넷의 모든 트래픽이 온-프레미스 방화벽또는 네트워크 가상 어플라이언스를 통해 흐르도록 강제하는 자체 기본 경로(0.0.0.0/0)를 정의하는 것입니다. 이 트래픽 흐름은 변함없이 Azure API Management와의 연결을 끊습니다. 그 이유는 아웃바운드 트래픽이 온-프레미스에서 막히거나 다양한 Azure 엔드포인트에서 더 이상 작동하지 않는 인식 불가능한 주소 집합으로 NAT되기 때문입니다. 이 솔루션을 사용하려면 다음 몇 가지 작업을 수행해야 합니다.

  * API 관리 서비스가 배포되는 서브넷에서 서비스 끝점을 활성화합니다. Azure Sql, Azure 저장소, Azure EventHub 및 Azure ServiceBus에 대해 [서비스 끝점을][ServiceEndpoints] 사용하도록 설정해야 합니다. API Management가 이러한 서비스에 대해 위임한 서브넷에서 직접 엔드포인트를 사용하도록 설정하면 서비스 트래픽에 대한 최적의 라우팅을 제공하는 Microsoft Azure 백본 네트워크를 사용할 수 있습니다. 강제 터널을 통해 된 Api 관리와 함께 서비스 끝점을 사용하는 경우 위의 Azure 서비스 트래픽은 강제 터널로 처리되지 않습니다. 다른 API 관리 서비스 종속성 트래픽은 강제로 터널을 통해 손실되거나 API 관리 서비스가 제대로 작동하지 않습니다.
    
  * 인터넷에서 API 관리 서비스의 관리 끝점으로 의 모든 제어 평면 트래픽은 API 관리에서 호스팅하는 특정 인바운드 IP 집합을 통해 라우팅됩니다. 트래픽이 강제로 터널을 터널로 이동하면 응답이 이러한 인바운드 소스 IP로 대칭적으로 매핑되지 않습니다. 이러한 제한을 극복하기 위해 이러한 호스트 경로의 대상을 "인터넷"으로 설정하여 트래픽을 Azure로 다시 조정하기 위해 다음과 같은 사용자 정의[경로(DUD)를][UDRs]추가해야 합니다. 제어 비행기 트래픽에 대한 인바운드 IP 세트는 [관제비행기 IP 주소로 문서화되어 있습니다.](#control-plane-ips)

  * 강제 터널이 되는 다른 API 관리 서비스 종속성의 경우 호스트 이름을 해결하고 끝점에 도달하는 방법이 있어야 합니다. 이러한 요소는 다음과 같습니다.
      - 메트릭 및 상태 모니터링
      - Azure 포털 진단
      - SMTP 릴레이
      - 개발자 포털 CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>문제 해결
* **초기 설정**: API Management 서비스를 서브넷으로 초기 배포하는 작업이 성공하지 않는 경우 먼저 동일한 서브넷에 가상 머신을 배포하는 것이 좋습니다. 그런 다음 원격 데스크톱을 가상 머신에 연결하고 사용자의 Azure 구독 아래 각 리소스에 대한 연결이 구축되었는지 확인합니다.
    * Azure Storage Blob
    * Azure SQL Database
    * Azure Storage 테이블

  > [!IMPORTANT]
  > 연결을 검증한 후에는 서브넷에 배포된 리소스를 모두 제거한 다음 API Management를 서비넷으로 배포합니다.

* **증분 업데이트**: 네트워크를 변경할 때 [NetworkStatus API를](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus)참조하여 API 관리 서비스가 종속된 중요한 리소스에 대한 액세스 권한을 잃지 않았는지 확인합니다. 연결 상태는 15분마다 업데이트되어야 합니다.

* **리소스 탐색 링크**: 리소스 관리자 스타일 Vnet 서브넷으로 배포할 경우 API Management는 리소스 탐색 링크를 만들어 서브넷을 보유합니다. 서브넷에 니미 다른 공급자의 리소스가 포함된 경우에는 배포가 **실패**합니다. 마찬가지로 API Management 서비스를 다른 서브넷으로 이동하거나 삭제할 경우에는 해당 리소스 탐색 링크가 삭제됩니다.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> 서브넷 크기 요구 사항
Azure는 각 서브넷 내의 일부 IP 주소를 예약하며, 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 Azure 서비스에 사용되는 3개 이상의 주소와 함께 프로토콜 적합성을 위해 예약됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure VNET 인프라에 사용되는 IP 주소 외에도, 서브넷의 각 API Management 인스턴스는 프리미엄 SKU 단위당 두 개의 IP 주소를 사용하거나 개발자 SKU에 대해 하나의 IP 주소를 사용합니다. 각 인스턴스는 외부 부하 분산 장치에 대해 하나의 추가 IP 주소를 예약합니다. 내부 가상 네트워크에 배포할 때 내부 로드 밸런서에 대한 추가 IP 주소가 필요합니다.

API 관리를 배포할 수 있는 서브넷의 최소 크기 보다 높은 계산을 감안할 때 세 개의 사용 가능한 IP 주소를 제공하는 /29입니다.

API 관리의 각 추가 확장 단위에는 두 개의 IP 주소가 더 필요합니다.

## <a name="routing"></a><a name="routing"> </a> 라우팅
+ 모든 서비스 엔드포인트에 대한 액세스를 제공하기 위해 부하 분산된 VIP(공용 IP 주소)가 예약됩니다.
+ 서브넷 IP 범위의 IP 주소(DIP)는 VNet 내의 리소스에 액세스하는 데 사용되고, 공용 IP 주소(VIP)는 VNet 외부 리소스에 액세스하는 데 사용됩니다.
+ 부하 분산된 공용 IP 주소는 Azure Portal의 개요/기본 정보 블레이드에서 확인할 수 있습니다.

## <a name="limitations"></a><a name="limitations"> </a>제한 사항
* API Management 인스턴스가 포함된 서브넷은 다른 Azure 리소스 종류를 포함할 수 없습니다.
* 서브넷과 API Management 서비스는 동일한 구독에 있어야 합니다.
* API Management 인스턴스가 포함된 서브넷은 구독 간에 이동할 수 없습니다.
* 내부 가상 네트워크 모드에서 구성된 다중 지역 API Management 배포의 경우 사용자는 라우팅을 소유하는 여러 지역 사이에서 부하 분산을 관리할 책임이 있습니다.
* 다른 지역에서 전역적으로 피어링된 VNET의 리소스에서 내부 모드의 API Management 서비스로 연결하는 기능은 플랫폼 제한 때문에 작동하지 않습니다. 자세한 내용은 [하나의 가상 네트워크의 리소스는 피어링된 가상 네트워크에 있는 Azure 내부 부하 분산 장치와 통신할 수 없음](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)을 참조하세요.

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> 제어 평면 IP 주소

IP 주소는 Azure **환경으로**나뉩니다. **글로벌로** 표시된 인바운드 요청 IP 주소를 허용하는 경우 **지역별** IP 주소와 함께 허용해야 합니다.

| **Azure 환경**|   **지역**|  **IP 주소**|
|-----------------|-------------------------|---------------|
| Azure 공용| 미국 중남부(글로벌)| 104.214.19.224|
| Azure 공용| 미국 중북부 (글로벌)| 52.162.110.80|
| Azure 공용| 미국 중서부| 52.253.135.58|
| Azure 공용| 한국 중부| 40.82.157.167|
| Azure 공용| 영국 서부| 51.137.136.0|
| Azure 공용| 일본 서부| 40.81.185.8|
| Azure 공용| 미국 중북부| 40.81.47.216|
| Azure 공용| 영국 남부| 51.145.56.125|
| Azure 공용| 인도 서부| 40.81.89.24|
| Azure 공용| 미국 동부| 52.224.186.99|
| Azure 공용| 서유럽| 51.145.179.78|
| Azure 공용| 일본 동부| 52.140.238.179|
| Azure 공용| 프랑스 중부| 40.66.60.111|
| Azure 공용| 캐나다 동부| 52.139.80.117|
| Azure 공용| 아랍에미리트 북부| 20.46.144.85|
| Azure 공용| 브라질 남부| 191.233.24.179|
| Azure 공용| 동남아시아| 40.90.185.46|
| Azure 공용| 남아프리카 북부| 102.133.130.197|
| Azure 공용| 캐나다 중부| 52.139.20.34|
| Azure 공용| 한국 남부| 40.80.232.185|
| Azure 공용| 인도 중부| 13.71.49.1|
| Azure 공용| 미국 서부| 13.64.39.16|
| Azure 공용| 오스트레일리아 남동부| 20.40.160.107|
| Azure 공용| 오스트레일리아 중부| 20.37.52.67|
| Azure 공용| 인도 남부| 20.44.33.246|
| Azure 공용| 미국 중부| 13.86.102.66|
| Azure 공용| 오스트레일리아 동부| 20.40.125.155|
| Azure 공용| 미국 서부 2| 51.143.127.203|
| Azure 공용| 미국 동부 2 EUAP| 52.253.229.253|
| Azure 공용| 미국 중부 EUAP| 52.253.159.160|
| Azure 공용| 미국 중남부| 20.188.77.119|
| Azure 공용| 미국 동부 2| 20.44.72.3|
| Azure 공용| 북유럽| 52.142.95.35|
| Azure 공용| 동아시아| 52.139.152.27|
| Azure 공용| 프랑스 남부| 20.39.80.2|
| Azure 공용| 스위스 웨스트| 51.107.96.8|
| Azure 공용| 오스트레일리아 중부 2| 20.39.99.81|
| Azure 공용| 아랍에미리트 중부| 20.37.81.41|
| Azure 공용| 스위스 북부| 51.107.0.91|
| Azure 공용| 남아프리카 공화국 서부| 102.133.0.79|
| Azure 공용| 독일 웨스트 센트럴| 51.116.96.0|
| Azure 공용| 독일 북부| 51.116.0.0|
| Azure 공용| 노르웨이 동부| 51.120.2.185|
| Azure 공용| 노르웨이 웨스트| 51.120.130.134|
| Azure China 21Vianet| 중국 북부 (글로벌)| 139.217.51.16|
| Azure China 21Vianet| 중국 동부 (글로벌)| 139.217.171.176|
| Azure China 21Vianet| 중국 북부| 40.125.137.220|
| Azure China 21Vianet| 중국 동부| 40.126.120.30|
| Azure China 21Vianet| 중국 북부 2| 40.73.41.178|
| Azure China 21Vianet| 중국 동부 2| 40.73.104.4|
| Azure Government| USGov 버지니아 (글로벌)| 52.127.42.160|
| Azure Government| USGov 텍사스 (글로벌)| 52.127.34.192|
| Azure Government| USGov 버지니아| 52.227.222.92|
| Azure Government| USGov 아이오와| 13.73.72.21|
| Azure Government| USGov 애리조나| 52.244.32.39|
| Azure Government| USGov 텍사스| 52.243.154.118|
| Azure Government| USDoD 센트럴| 52.182.32.132|
| Azure Government| USDoD 이스트| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>관련 콘텐츠
* [VPN Gateway를 사용하여 Virtual Network를 백 엔드에 연결](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [다양한 배포 모델에서 Virtual Network 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [API 검사기를 사용하여 Azure API Management에서 호출을 추적하는 방법](api-management-howto-api-inspector.md)
* [가상 네트워크 자주 묻는 질문](../virtual-network/virtual-networks-faq.md)
* [서비스 태그](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
