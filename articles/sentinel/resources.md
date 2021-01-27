---
title: Azure 센티널로 작업할 때 유용한 리소스 | Microsoft Docs
description: 이 문서에서는 Azure 센티널로 작업할 때 유용한 리소스 목록을 제공 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 11c5c77623fcc693210d5a42bf94c968e884fec3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920023"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Azure 센티널 작업에 유용한 리소스



이 문서에는 Azure 센티널 사용에 대 한 자세한 정보를 얻는 데 도움이 되는 리소스가 나열 되어 있습니다.

- **Azure Logic Apps 커넥터**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>감사 및 보고
Azure 센티널의 감사 로그는 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md)에서 유지 관리 됩니다.

다음과 같은 지원 되는 작업을 감사할 수 있습니다.

|작업 이름|    리소스 유형|
|----|----|
|통합 문서 만들기 또는 업데이트  |Microsoft Insights/통합 문서|
|통합 문서 삭제    |Microsoft Insights/통합 문서|
|워크플로 설정   |Microsoft.Logic/workflows|
|워크플로 삭제    |Microsoft.Logic/workflows|
|저장 된 검색 만들기    |OperationalInsights/작업 영역/savedSearches|
|저장 된 검색 삭제    |OperationalInsights/작업 영역/savedSearches|
|경고 규칙 업데이트 |Microsoft SecurityInsights/alertRules|
|경고 규칙 삭제 |Microsoft SecurityInsights/alertRules|
|경고 규칙 응답 작업 업데이트 |Microsoft SecurityInsights/alertRules/작업|
|경고 규칙 응답 작업 삭제 |Microsoft SecurityInsights/alertRules/작업|
|책갈피 업데이트   |Microsoft SecurityInsights/책갈피|
|책갈피 삭제   |Microsoft SecurityInsights/책갈피|
|업데이트 사례   |Microsoft SecurityInsights/사례|
|업데이트 사례 조사  |Microsoft SecurityInsights/사례/조사|
|사례 설명 만들기   |Microsoft SecurityInsights/사례/설명|
|데이터 커넥터 업데이트 |Microsoft SecurityInsights/dataConnectors|
|데이터 커넥터 삭제 |Microsoft SecurityInsights/dataConnectors|
|업데이트 설정    |Microsoft SecurityInsights/설정|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Azure 센티널에서 감사 및 보고 데이터 보기

Azure 활동 로그에서 Azure 센티널로 스트리밍 하 여이 데이터를 볼 수 있습니다. 그런 다음 연구 및 분석을 수행할 수 있습니다.

1. [Azure 활동](connect-azure-activity.md) 데이터 원본에 연결 합니다. 이 작업을 수행한 후에 감사 이벤트는 AzureActivity 라는 **로그** 화면에서 새 테이블로 스트리밍됩니다.

1. 그런 다음 다른 테이블과 같이 KQL를 사용 하 여 데이터를 쿼리 합니다.

    예를 들어 특정 분석 규칙을 편집 하는 마지막 사용자를 확인 하려면 다음 쿼리를 사용 합니다 (를 확인 하려는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 규칙의 규칙 ID로 대체).

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>공급 업체 설명서

| **공급업체**  | **Azure 센티널에서 인시던트 사용** | **링크**|
|----|----|----|
| GitHub| 커뮤니티 페이지에 액세스 하는 데 사용 됩니다.| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF 구성| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto 쿼리 언어 과정| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>블로그 및 포럼

사용자의 의견을 환영 합니다.

- Azure 센티널의 [TechCommunity 공간](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) 에 **질문을 게시** 합니다. 

- [사용자 음성](https://feedback.azure.com/forums/920458-azure-sentinel) 프로그램 **을 통해 향상 된 기능에 대 한 제안을 보냅니다** .

- Azure 센티널 블로그 게시물을 **보고 의견을 보내** 드립니다.

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)

Azure 보안 및 규정 준수에 대 한 자세한 내용은 [Microsoft Azure 보안 및 규정 준수 블로그](https://techcommunity.microsoft.com/t5/microsoft-security-and/bg-p/MicrosoftSecurityandCompliance)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증 받기](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [고객 사용 사례 스토리 읽기](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

