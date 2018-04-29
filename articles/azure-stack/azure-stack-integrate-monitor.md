---
title: Azure 스택와 외부 모니터링 솔루션을 통합 | Microsoft Docs
description: Azure 스택 외부 모니터링 솔루션 데이터 센터에 통합 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 4188d114aa86086821b2c640d7f2d98a78bcbf4e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Azure 스택와 외부 모니터링 솔루션을 통합

Azure 스택 인프라의 외부 모니터링에 대 한 Azure 스택 소프트웨어, 물리적 컴퓨터와 실제 네트워크 스위치를 모니터링 해야 합니다. 이러한 각 영역 상태 및 경고 정보를 검색 하는 메서드를 제공 합니다.

- Azure 스택 소프트웨어 상태 및 경고를 검색 하는 REST 기반 API를 제공 합니다. (저장소 공간 다이렉트 같은 기술 소프트웨어 정의 사용 하 여 저장소 상태 및 경고의 일부인 소프트웨어 모니터링 합니다.).
- 물리적 컴퓨터 정확해 상태 및 경고 정보 베이스 보드 관리 컨트롤러 Bmc ()을 통해 사용할 수 있습니다.
- 실제 네트워크 장치 수 확인 상태 및 경고 정보 SNMP 프로토콜을 통해 사용할 수 있는 합니다.

각 Azure 스택 솔루션 하드웨어 수명 주기 호스트와 함께 제공 됩니다. 이 호스트는 제조업체 (OEM) 하드웨어 공급 업체의 물리적 서버 및 네트워크 장치에 대 한 모니터링 소프트웨어를 실행합니다. 원하는 경우에 솔루션 모니터링을 무시할 수 있으며 데이터 센터에서 기존 모니터링 솔루션을 직접 통합할 수 있습니다.

> [!IMPORTANT]
> 사용 하면 외부 모니터링 솔루션은 에이전트 없는 이어야 합니다. Azure 스택 구성 요소 안의 제 3 자 에이전트를 설치할 수 없습니다.

다음 다이어그램에서는 Azure 스택 통합 시스템, 하드웨어 수명 주기 호스트, 외부 모니터링 솔루션은 및 외부 티켓/데이터 컬렉션 시스템 간의 트래픽 흐름을 보여 줍니다.

![Azure 스택, 모니터링 및 티켓 솔루션 간의 트래픽을 보여 주는 다이어그램입니다.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

이 문서에서는 Azure 스택 Nagios 및 System Center Operations Manager와 같은 외부 모니터링 솔루션으로 통합 하는 방법을 설명 합니다. PowerShell을 사용 하 여 또는 REST API 호출을 통해 경고에 프로그래밍 방식으로 작업 하는 방법을 포함 됩니다.

## <a name="integrate-with-operations-manager"></a>Operations Manager와 통합

Azure 스택의 외부 모니터링에 대 한 Operations Manager를 사용할 수 있습니다. Microsoft Azure 스택 용 System Center 관리 팩을 사용 하면 단일 Operations Manager 인스턴스를 사용 하는 여러 Azure 스택 배포를 모니터링할 수 있습니다. 관리 팩 상태 리소스 공급자 및 업데이트 리소스 공급자 REST Api를 사용 하 여 Azure 스택와 통신할 수 있습니다. 모니터링 하드웨어 수명 주기 호스트에서 실행 되는 소프트웨어 OEM 무시 하려는 경우 물리적 서버를 모니터링 하는 공급 업체 관리 팩을 설치할 수 있습니다. 또한 네트워크 스위치를 모니터링할 Operations Manager 네트워크 장치 검색을 사용할 수 있습니다.

Azure 스택 용 관리 팩에는 다음과 같은 기능을 제공합니다.

- 여러 Azure 스택 배포를 관리할 수 있습니다.
- Azure Active Directory (Azure AD) 및 Active Directory Federation Services (AD FS)에 대 한 지원은
- 검색 하 고 경고 종결
- 상태 및 용량 대시보드
- 패치 및 업데이트 (& U) 중인 경우 진행률에 대 한 자동 유지 관리 모드 검색 포함
- 작업 배포 및 지역에 대해 강제 업데이트를 포함합니다.
- 영역에 사용자 지정 정보를 추가할 수 있습니다.
- 알림 지원 및 보고

Microsoft Azure 스택 및 연결 된 용 System Center 관리 팩을 다운로드할 수 있습니다 [사용자 가이드](https://www.microsoft.com/en-us/download/details.aspx?id=55184), 또는 Operations Manager에서 직접 합니다.

티켓 솔루션에 대 한 Operations Manager System Center Service Manager를 통합할 수 있습니다. 통합된 제품 커넥터에서 Service Manager에서 서비스 요청을 해결 한 후 Azure 스택 및 Operations Manager에서 경고를 닫을 수 있습니다 하는 양방향 통신을 수 있습니다.

다음 다이어그램에서는 기존 System Center 배포와 Azure 스택의 통합을 보여 줍니다. System Center Orchestrator 또는 서비스 관리 자동화 (SMA) Azure 스택에서 작업을 실행 하려면 서비스 관리자를 추가 자동화할 수 있습니다.

![SMA OM, 서비스 관리자와 통합을 보여 주는 다이어그램입니다.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Nagios와 통합

관대 한 무료 소프트웨어 라이선스 – MIT (매사추세츠 공과대)에 따라 사용할 수 있는 파트너 Cloudbase 솔루션에서 함께 플러그 인 모니터링 Nagios 개발 되었습니다.

플러그 인 Python에서 기록 되 고 상태 리소스 공급자 REST API를 활용 합니다. 검색 한 Azure 스택의 경고를 종결 하는 기본 기능을 제공 합니다. 마찬가지로 System Center 관리 팩 것을 사용 하면 여러 Azure 스택 배포를 추가 하 고 알림을 보낼 수 있습니다.

Nagios 엔터프라이즈 및 Nagios 코어 플러그 인 작동합니다. [여기](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)에서 다운로드할 수 있습니다. 다운로드 사이트에 설치 및 구성 세부 정보가 포함 되어 있습니다.

### <a name="plugin-parameters"></a>플러그 인 매개 변수

매개 변수가 플러그 인 파일 "Azurestack_plugin.py"를 구성 합니다.

| 매개 변수 | 설명 | 예 |
|---------|---------|---------|
| *arm_endpoint* | Azure 리소스 관리자 (관리자) 끝점 |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure 리소스 관리자 (관리자) 끝점  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 관리자 구독 ID | 관리자 포털 또는 PowerShell을 통해 검색 |
| *User_name* | 연산자 구독 사용자 이름 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | 연산자 구독 암호 | mypassword |
| *Client_id* | 클라이언트 | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure 스택 영역 이름 | local |
|  |  |

* PowerShell GUID 제공 되는 유니버설입니다. 각 배포에 대해 사용할 수 있습니다.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>PowerShell을 사용 하 여 상태 모니터 및 경고를

Operations Manager, Nagios, 또는 Nagios 기반 솔루션을 사용 하지 않는 경우에 Azure 스택와 통합 하는 솔루션을 모니터링 하는 다양 한 범위의 사용 하도록 설정 하려면 PowerShell을 사용할 수 있습니다.
 
1. PowerShell을 사용 하려면 확인 했는지 [PowerShell 설치 및 구성](azure-stack-powershell-configure-quickstart.md) Azure 스택 연산자 환경에 대 한 합니다. 리소스 관리자 (관리자) 끝점에 도달할 수 있는 로컬 컴퓨터에서 PowerShell을 설치 (https://adminmanagement. [ 지역]입니다. [External_FQDN])입니다.

2. Azure 스택 운영자 Azure 스택 환경에 연결 하려면 다음 명령을 실행 합니다.

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. 설치한 디렉터리로 변경는 [Azure 스택 도구](https://github.com/Azure/AzureStack-Tools) c:\azurestack-tools-master 예를 들어 PowerShell 설치 과정의 일환으로 합니다. 그런, 인프라 디렉터리로 변경 하 고 인프라 모듈을 가져오려면 다음 명령을 실행 합니다.

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. 다음 예제와 같은 명령을 사용 하 여 경고를 작성 하려면:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>상태 모니터 및 경고 하는 REST API를 사용 하 여

경고를 받으려면 경고를 닫고 리소스 공급자의 상태 가져오기 REST API 호출을 사용할 수 있습니다.

### <a name="get-alert"></a>경으십시오

**요청**

요청은 기본 공급자 구독에 대 한 모든 활성 작업과 닫힌 경고를 가져옵니다. 요청 본문이 없습니다.


|방법  |요청 URI  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**인수**

|인수  |설명  |
|---------|---------|
|armendpoint     |  Azure 스택 환경의 형식의 Azure 리소스 관리자 끝점 https://adminmanagement.{RegionName}.{External FQDN}. 예를 들어 외부 FQDN *azurestack.external* 지역 이름은 *로컬*, 리소스 관리자 끝점은 https://adminmanagement.local.azurestack.external합니다.       |
|subid     |   호출 하는 사용자의 구독 ID입니다. 이 API는 쿼리를 기본 공급자 구독 수 있는 권한을 가진 사용자와만 사용할 수 있습니다.      |
|RegionName     |    Azure 스택 배포의 지역 이름입니다.     |
|api-version     |  이 요청에 사용 되는 프로토콜의 버전입니다. 2016-05-01을 사용 해야 합니다.      |
|     |         |

**응답**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**응답 세부 정보**


|  인수  |설명  |
|---------|---------|
|*id*     |      경고의 고유 ID입니다.   |
|*name*     |     경고의 내부 이름입니다.   |
|*type*     |     리소스 정의 합니다.    |
|*위치*     |       지역 이름입니다.     |
|*태그*     |   리소스 태그입니다.     |
|*closedtimestamp*    |  경고가 종결 된 UTC 시간입니다.    |
|*createdtimestamp*     |     경고를 만들 때 UTC 시간입니다.   |
|*description*     |    경고에 대한 설명입니다.     |
|*faultid*     | 영향을 받는 구성 요소입니다.        |
|*alertid*     |  경고의 고유 ID입니다.       |
|*faulttypeid*     |  잘못 된 구성 요소의 고유 형식입니다.       |
|*lastupdatedtimestamp*     |   경고 정보를 마지막으로 수정한 UTC 시간입니다.    |
|*healthstate*     | 전체 상태입니다.        |
|*name*     |   특정 경고의 이름입니다.      |
|*fabricname*     |    등록 된 패브릭 이름 잘못 된 구성 요소입니다.   |
|*description*     |  등록 된 패브릭 구성 요소에 대 한 설명입니다.   |
|*servicetype*     |   등록 된 패브릭 서비스의 형식입니다.   |
|*remediation*     |   업데이트 관리 단계가 것이 좋습니다.    |
|*type*     |   경고 유형입니다.    |
|*resourceRegistrationid*    |     등록 된 리소스의 ID입니다.    |
|*resourceProviderRegistrationID*   |    영향을 받는 구성 요소는 등록 된 리소스 공급자의 ID입니다.  |
|*serviceregistrationid*     |    등록 된 서비스의 ID입니다.   |
|*severity*     |     경고 심각도입니다.  |
|*state*     |    경고 상태입니다.   |
|*title*     |    경고 제목입니다.   |
|*impactedresourceid*     |     영향을 받는 리소스의 ID입니다.    |
|*ImpactedresourceDisplayName*     |     영향을 받는 리소스의 이름입니다.  |
|*closedByUserAlias*     |   경고를 종결 하는 사용자입니다.      |

### <a name="close-alert"></a>경고 닫기

**요청**

요청 고유 ID 하 여 경고를 닫습니다.

|방법    |요청 URI  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**인수**


|인수  |설명  |
|---------|---------|
|*armendpoint*     |   Azure 스택 환경의 형식에서 리소스 관리자 끝점 https://adminmanagement.{RegionName}.{External FQDN}. 예를 들어 외부 FQDN *azurestack.external* 지역 이름은 *로컬*, 리소스 관리자 끝점은 https://adminmanagement.local.azurestack.external합니다.      |
|*subid*     |    호출 하는 사용자의 구독 ID입니다. 이 API는 쿼리를 기본 공급자 구독 수 있는 권한을 가진 사용자와만 사용할 수 있습니다.     |
|*RegionName*     |   Azure 스택 배포의 지역 이름입니다.      |
|*api-version*     |    이 요청에 사용 되는 프로토콜의 버전입니다. 2016-05-01을 사용 해야 합니다.     |
|*alertid*     |    경고의 고유 ID입니다.     |

**본문**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**응답**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**응답 세부 정보**


|  인수  |설명  |
|---------|---------|
|*id*     |      경고의 고유 ID입니다.   |
|*name*     |     경고의 내부 이름입니다.   |
|*type*     |     리소스 정의 합니다.    |
|*위치*     |       지역 이름입니다.     |
|*태그*     |   리소스 태그입니다.     |
|*closedtimestamp*    |  경고가 종결 된 UTC 시간입니다.    |
|*createdtimestamp*     |     경고를 만들 때 UTC 시간입니다.   |
|*설명*     |    경고에 대한 설명입니다.     |
|*faultid*     | 영향을 받는 구성 요소입니다.        |
|*alertid*     |  경고의 고유 ID입니다.       |
|*faulttypeid*     |  잘못 된 구성 요소의 고유 형식입니다.       |
|*lastupdatedtimestamp*     |   경고 정보를 마지막으로 수정한 UTC 시간입니다.    |
|*healthstate*     | 전체 상태입니다.        |
|*name*     |   특정 경고의 이름입니다.      |
|*fabricname*     |    등록 된 패브릭 이름 잘못 된 구성 요소입니다.   |
|*description*     |  등록 된 패브릭 구성 요소에 대 한 설명입니다.   |
|*servicetype*     |   등록 된 패브릭 서비스의 형식입니다.   |
|*remediation*     |   업데이트 관리 단계가 것이 좋습니다.    |
|*type*     |   경고 유형입니다.    |
|*resourceRegistrationid*    |     등록 된 리소스의 ID입니다.    |
|*resourceProviderRegistrationID*   |    영향을 받는 구성 요소는 등록 된 리소스 공급자의 ID입니다.  |
|*serviceregistrationid*     |    등록 된 서비스의 ID입니다.   |
|*severity*     |     경고 심각도입니다.  |
|*state*     |    경고 상태입니다.   |
|*title*     |    경고 제목입니다.   |
|*impactedresourceid*     |     영향을 받는 리소스의 ID입니다.    |
|*ImpactedresourceDisplayName*     |     영향을 받는 리소스의 이름입니다.  |
|*closedByUserAlias*     |   경고를 종결 하는 사용자입니다.      |

### <a name="get-resource-provider-health"></a>리소스 공급자 상태 가져오기

**요청**

요청 모든 등록 된 리소스 공급자에 대 한 상태를 가져옵니다.


|방법  |요청 URI  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**인수**


|인수  |설명  |
|---------|---------|
|*armendpoint*     |    Azure 스택 환경의 형식에서 리소스 관리자 끝점 https://adminmanagement.{RegionName}.{External FQDN}. 예를 들어 경우 외부 FQDN은 azurestack.external와 지역 이름 로컬 리소스 관리자 끝점 이면 https://adminmanagement.local.azurestack.external합니다.     |
|*subid*     |     호출 하는 사용자의 구독 ID입니다. 이 API는 쿼리를 기본 공급자 구독 수 있는 권한을 가진 사용자와만 사용할 수 있습니다.    |
|*RegionName*     |     Azure 스택 배포의 지역 이름입니다.    |
|*api-version*     |   이 요청에 사용 되는 프로토콜의 버전입니다. 2016-05-01을 사용 해야 합니다.      |


**응답**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**응답 세부 정보**


|인수  |설명  |
|---------|---------|
|*Id*     |   경고의 고유 ID입니다.      |
|*name*     |  경고의 내부 이름입니다.       |
|*type*     |  리소스 정의 합니다.       |
|*위치*     |  지역 이름입니다.       |
|*태그*     |     리소스 태그입니다.    |
|*registrationId*     |   리소스 공급자에 대 한 고유 등록 합니다.      |
|*displayName*     |리소스 공급자 표시 이름입니다.        |
|*namespace*     |   리소스 공급자 API 네임 스페이스를 구현합니다.       |
|*routePrefix*     |    리소스 공급자와 상호 작용 하는 URI입니다.     |
|*serviceLocation*     |   이 리소스 공급자가 등록 된 지역입니다.      |
|*infraURI*     |   인프라 역할로 표시 하는 리소스 공급자의 URI입니다.      |
|*alertSummary*     |   리소스 공급자와 관련 된 위험 및 경고 경고의 요약입니다.      |
|*healthState*     |    리소스 공급자의 상태입니다.     |


### <a name="get-resource-health"></a>리소스 상태 가져오기

요청 등록 된 특정 리소스 공급자에 대 한 상태를 가져옵니다.

**요청**

|방법  |요청 URI  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**인수**

|인수  |설명  |
|---------|---------|
|*armendpoint*     |    Azure 스택 환경의 형식에서 리소스 관리자 끝점 https://adminmanagement.{RegionName}.{External FQDN}. 예를 들어 경우 외부 FQDN은 azurestack.external와 지역 이름 로컬 리소스 관리자 끝점 이면 https://adminmanagement.local.azurestack.external합니다.     |
|*subid*     |호출 하는 사용자의 구독 ID입니다. 이 API는 쿼리를 기본 공급자 구독 수 있는 권한을 가진 사용자와만 사용할 수 있습니다.         |
|*RegionName*     |  Azure 스택 배포의 지역 이름입니다.       |
|*api-version*     |  이 요청에 사용 되는 프로토콜의 버전입니다. 2016-05-01을 사용 해야 합니다.       |
|*RegistrationID* |특정 리소스 공급자에 대 한 등록 ID입니다. |

**응답**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**응답 세부 정보**

|인수  |설명  |
|---------|---------|
|*Id*     |   경고의 고유 ID입니다.      |
|*name*     |  경고의 내부 이름입니다.       |
|*type*     |  리소스 정의 합니다.       |
|*위치*     |  지역 이름입니다.       |
|*태그*     |     리소스 태그입니다.    |
|*registrationId*     |   리소스 공급자에 대 한 고유 등록 합니다.      |
|*resourceType*     |리소스의 형식입니다.        |
|*resourceName*     |   리소스 이름입니다.   |
|*usageMetrics*     |    리소스에 대 한 사용 현황 메트릭입니다.     |
|*resourceLocation*     |   영역의 이름 배포 하는 경우.      |
|*resourceURI*     |   리소스에 대 한 URI입니다.   |
|*alertSummary*     |   중요 한 요약 및 경고 알림, 상태입니다.     |

## <a name="learn-more"></a>자세한 정보

기본 제공 상태 모니터링에 대 한 정보를 참조 하십시오. [모니터링 상태 및 경고를 Azure 스택](azure-stack-monitor-health.md)합니다.


## <a name="next-steps"></a>다음 단계

[보안 통합](azure-stack-integrate-security.md)
