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
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585274"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Azure 센티널 작업에 유용한 리소스



이 문서에는 Azure 센티널 사용에 대 한 자세한 정보를 얻는 데 도움이 되는 리소스가 나열 되어 있습니다.

Azure Logic Apps 커넥터: <https://docs.microsoft.com/connectors/>


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
|설정 업데이트    |Microsoft SecurityInsights/설정|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Azure 센티널에서 감사 및 보고 데이터 보기

Azure 활동 로그에서 Azure 센티널로 스트리밍 하 여이 데이터를 볼 수 있습니다. 그런 다음 연구 및 분석을 수행할 수 있습니다.

1. [Azure 활동](connect-azure-activity.md) 데이터 원본에 연결 합니다. 이 작업을 수행한 후에 감사 이벤트는 AzureActivity 라는 **로그** 화면에서 새 테이블로 스트리밍됩니다.
2. 그런 다음 다른 테이블과 같이 KQL를 사용 하 여 데이터를 쿼리 합니다.



## <a name="vendor-documentation"></a>공급 업체 설명서

| **공급업체**  | **Azure 센티널에서 인시던트 사용** | **링크**|
|----|----|----|
| GitHub| 커뮤니티 페이지에 액세스 하는 데 사용 됩니다.| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF 구성| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto 쿼리 언어 과정| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>블로그 및 포럼

Azure 센티널의 [TechCommunity 공간](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) 에 질문을 게시 합니다.

[TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 및 [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)에서 Azure 센티널 블로그 게시물을 확인 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure 센티널로 작업 하는 경우 유용한 리소스 목록을 얻었습니다. Azure 보안 및 규정 준수에 대 한 추가 정보는 [Microsoft Azure 보안 및 규정 준수 블로그](https://blogs.msdn.com/b/azuresecurity/)에서 확인할 수 있습니다.
