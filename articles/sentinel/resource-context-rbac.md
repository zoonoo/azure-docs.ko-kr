---
title: 리소스를 사용 하 여 Azure 센티널 데이터에 대 한 액세스 관리 | Microsoft Docs
description: 이 문서에서는 사용자가 액세스할 수 있는 리소스를 통해 Azure 센티널 데이터에 대 한 액세스를 관리할 수 있는 방법을 설명 합니다. 리소스를 통한 액세스 관리를 통해 전체 Azure 센티널 환경 없이 특정 데이터에만 액세스를 제공할 수 있습니다. 이 메서드를 리소스 컨텍스트 RBAC 라고도 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: fc1246d079760fd86513840aebbffa34d192f8ed
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044178"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>리소스를 사용 하 여 Azure 센티널 데이터에 대 한 액세스 관리

일반적으로 Azure 센티널 작업 영역에 대 한 액세스 권한이 있는 사용자는 보안 콘텐츠를 비롯 한 모든 작업 영역 데이터에도 액세스할 수 있습니다. 관리자는 [azure 역할](roles.md) 을 사용 하 여 팀의 액세스 요구 사항에 따라 azure 센티널의 특정 기능에 대 한 액세스를 구성할 수 있습니다.

그러나 Azure 센티널 작업 영역에서 특정 데이터에만 액세스 해야 하는 일부 사용자가 있을 수 있지만 전체 Azure 센티널 환경에 액세스할 수는 없습니다. 예를 들어 소유 하 고 있는 서버에 대 한 Windows 이벤트 데이터에 액세스할 수 있는 비-보안 작업 (비 SOC) 팀을 제공할 수 있습니다.

이러한 경우 Azure 센티널 작업 영역 또는 특정 Azure 센티널 기능에 대 한 액세스를 제공 하는 대신 사용자에 게 허용 되는 리소스에 따라 RBAC (역할 기반 액세스 제어)를 구성 하는 것이 좋습니다. 이 메서드는 **리소스 컨텍스트 RBAC** 를 설정 하는 것으로도 알려져 있습니다.

사용자가 Azure 센티널 작업 영역 대신 액세스할 수 있는 리소스를 통해 Azure 센티널 데이터에 액세스할 수 있는 경우 다음 방법을 사용 하 여 로그 및 통합 문서를 볼 수 있습니다.

- Azure 가상 머신과 같은 **리소스 자체를 통해**. 이 메서드를 사용 하 여 특정 리소스에 대 한 로그 및 통합 문서만 볼 수 있습니다.

- **Via Azure Monitor**. 여러 리소스 및/또는 리소스 그룹에 걸친 쿼리를 만들려는 경우이 방법을 사용 합니다. Azure Monitor에서 로그와 통합 문서를 탐색 하는 경우 하나 이상의 특정 리소스 그룹 또는 리소스에 대 한 범위를 정의 합니다.

Azure Monitor에서 리소스 컨텍스트 RBAC를 사용 하도록 설정 합니다. 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역에 대 한 액세스 관리](../azure-monitor/logs/manage-access.md)를 참조 하세요.

> [!NOTE]
> 데이터가 Syslog, CEF, AAD 데이터 또는 사용자 지정 수집기에 의해 수집 된 데이터와 같은 Azure 리소스가 아닌 경우 데이터를 식별 하 고 액세스를 사용 하도록 설정 하는 데 사용 되는 리소스 ID를 수동으로 구성 해야 합니다.
>
> 자세한 내용은 [리소스 컨텍스트 RBAC 명시적 구성](#explicitly-configure-resource-context-rbac)을 참조 하세요.
>
## <a name="scenarios-for-resource-context-rbac"></a>리소스 컨텍스트 RBAC에 대 한 시나리오

다음 표에서는 리소스 컨텍스트 RBAC가 가장 유용한 시나리오를 중점적으로 설명 합니다. SOC 팀과 비 SOC 팀 간의 액세스 요구 사항에 대 한 차이점을 확인 합니다.

| 요구 사항 유형 |SOC 팀  |비 SOC 팀  |
|---------|---------|---------|
|**권한**     | 전체 작업 영역        |   특정 리소스만      |
|**데이터 액세스**     |  작업 영역의 모든 데이터       | 팀에서 액세스할 수 있는 리소스에 대 한 데이터만        |
|**환경**     |  전체 Azure 센티널 환경 (사용자에 게 할당 된 [기능 권한](roles.md) 으로 제한 될 수 있음)       |  쿼리 및 통합 문서만 로그       |
|     |         |         |

위의 표에서 설명 하는 비 SOC 팀에 대해 팀의 액세스 요구 사항이 비슷한 경우에는 리소스 컨텍스트 RBAC가 조직에 적합 한 솔루션이 될 수 있습니다.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>리소스 컨텍스트 RBAC 구현에 대 한 대체 방법

조직에 필요한 사용 권한에 따라 리소스 컨텍스트 RBAC를 사용 하 여 전체 솔루션을 제공 하지 않을 수 있습니다.

다음 목록에서는 데이터 액세스에 대 한 다른 솔루션이 요구 사항에 더 적합할 수 있는 시나리오를 설명 합니다.

|시나리오  |해결 방법  |
|---------|---------|
|**자회사에는 전체 Azure 센티널 환경이 필요한 SOC 팀이** 있습니다.     |  이 경우 다중 작업 영역 아키텍처를 사용 하 여 데이터 권한을 구분 합니다. <br><br>자세한 내용은 다음을 참조하세요. <br>- [작업 영역 및 테 넌 트에서 Azure 센티널 확장](extend-sentinel-across-workspaces-tenants.md)<br>    - [여러 작업 영역에서 한 번에 인시던트 사용](multiple-workspace-view.md)          |
|**특정 유형의 이벤트에 대 한 액세스를 제공** 하려고 합니다.     |  예를 들어 Windows 관리자에 게 모든 시스템의 Windows 보안 이벤트에 대 한 액세스 권한을 제공 합니다. <br><br>이러한 경우 [테이블 수준 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) 를 사용 하 여 각 테이블에 대 한 사용 권한을 정의 합니다.       |
| **리소스를 기준으로 하지 않거나 이벤트에 있는 필드의 하위 집합에 대 한 액세스를 보다 세분화 된 수준으로 제한**   |   예를 들어 사용자의 자회사에 따라 Office 365 로그에 대 한 액세스를 제한할 수 있습니다. <br><br>이 경우 [Power BI 대시보드 및 보고서](../azure-monitor/visualize/powerbi.md)와의 기본 제공 통합을 사용 하 여 데이터에 대 한 액세스를 제공 합니다.      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>리소스 컨텍스트 RBAC를 명시적으로 구성

리소스 컨텍스트 RBAC를 구성 하려는 경우에는 다음 단계를 사용 합니다. 그러나 데이터는 Azure 리소스가 아닙니다.

예를 들어 azure 리소스가 아닌 Azure 센티널 작업 영역의 데이터에는 Syslog, CEF 또는 AAD 데이터 또는 사용자 지정 수집기에서 수집한 데이터가 포함 됩니다.

**리소스 컨텍스트 RBAC를 명시적으로 구성 하려면 다음을 수행 합니다**.

1. Azure Monitor에서 [리소스 컨텍스트 RBAC를 사용 하도록 설정](../azure-monitor/logs/manage-access.md) 했는지 확인 합니다. 

1. 전체 Azure 센티널 환경을 사용 하지 않고 리소스에 액세스 해야 하는 각 사용자 팀에 대 한 [리소스 그룹을 만듭니다](../azure-resource-manager/management/manage-resource-groups-portal.md) .

    각 팀 멤버에 대 한 [로그 판독기 권한을](../azure-monitor/logs/manage-access.md#resource-permissions) 할당 합니다.

1. 만든 리소스 팀 그룹에 리소스를 할당 하 고 관련 리소스 Id로 이벤트에 태그를 지정 합니다.

    Azure 리소스가 Azure 센티널에 데이터를 보낼 때 로그 레코드는 자동으로 데이터 원본의 리소스 ID로 태그가 지정 됩니다.

    > [!TIP]
    > 목적을 위해 만든 특정 리소스 그룹에서 액세스 권한을 부여 하는 리소스를 그룹화 하는 것이 좋습니다.
    >
    > 액세스할 수 없는 경우 팀에 액세스 하려는 리소스에 대 한 로그 판독기 권한이 있는지 확인 합니다.
    >

    리소스 Id에 대 한 자세한 내용은 다음을 참조 하세요.

    - [로그 전달을 사용 하는 리소스 Id](#resource-ids-with-log-forwarding)
    - [Logstash 태 시 컬렉션을 사용 하는 리소스 Id](#resource-ids-with-logstash-collection)
    - [Log Analytics API 컬렉션을 사용 하는 리소스 Id](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>로그 전달을 사용 하는 리소스 Id

[CEF (일반 이벤트 형식)](connect-common-event-format.md) 또는 [Syslog](connect-syslog.md)를 사용 하 여 이벤트를 수집 하는 경우 로그 전달은 여러 원본 시스템에서 이벤트를 수집 하는 데 사용 됩니다.

예를 들어 CEF 또는 Syslog 전달 VM이 Syslog 이벤트를 보내는 소스를 수신 대기 하 고이를 Azure 센티널에 전달 하는 경우 전달 되는 모든 이벤트에 로그 전달 VM 리소스 ID가 할당 됩니다.

여러 팀이 있는 경우 별도의 각 팀에 대 한 이벤트를 처리 하는 별도의 로그 전달 Vm이 있는지 확인 합니다.

예를 들어 Vm을 분리 하면 팀 A에 속한 Syslog 이벤트가 수집기 VM A를 사용 하 여 수집 됩니다.

> [!TIP]
> - 온-프레미스 VM 또는 다른 클라우드 VM (예: AWS)을 로그 전달자로 사용 하는 경우 [Azure Arc](../azure-arc/servers/overview.md)를 구현 하 여 리소스 ID가 있는지 확인 합니다.
> - 로그 전달 VM 환경의 크기를 조정 하려면 CEF 및 Sylog 로그를 수집 하는 [vm 확장 집합](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) 을 만드는 것이 좋습니다.


### <a name="resource-ids-with-logstash-collection"></a>Logstash 태 시 컬렉션을 사용 하는 리소스 Id

Azure 센티널 [Logstash 출력 플러그 인](connect-logstash.md)을 사용 하 여 데이터를 수집 하는 경우 **azure_resource_id** 필드를 사용 하 여 출력에 리소스 id를 포함 하도록 사용자 지정 수집기를 구성 합니다.

리소스 컨텍스트 RBAC를 사용 하 고 API를 통해 수집 되는 이벤트를 특정 사용자가 사용할 수 있도록 하려면 [사용자에 대해 만든](#explicitly-configure-resource-context-rbac)리소스 그룹의 리소스 ID를 사용 합니다.

예를 들어 다음 코드는 샘플 Logstash 태 구성 파일을 보여 줍니다.

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> 여러 섹션을 추가 하 여 여러 `output` 이벤트에 적용 된 태그를 구분할 수 있습니다.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Log Analytics API 컬렉션을 사용 하는 리소스 Id

[Log Analytics 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 사용 하 여 수집 하는 경우 HTTP [*AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) request 헤더를 사용 하 여 리소스 ID를 사용 하 여 이벤트에 할당할 수 있습니다.

리소스 컨텍스트 RBAC를 사용 하 고 API를 통해 수집 되는 이벤트를 특정 사용자가 사용할 수 있도록 하려면 [사용자에 대해 만든](#explicitly-configure-resource-context-rbac)리소스 그룹의 리소스 ID를 사용 합니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 센티널의 권한](roles.md)을 참조 하세요.