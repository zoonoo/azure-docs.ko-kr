---
title: REST API를 사용 하 여 Azure 센티널에서 구하기 및 라이브 스트림 쿼리 관리 Microsoft Docs
description: 이 문서에서는 Azure 센티널 구하기 기능을 사용 하 여 Log Analytics ' REST API를 활용 하 여 구하기 및 라이브 스트림 쿼리를 관리 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: ca60b9350171cee55462c9df28915c811e1cfd25
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94377709"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>REST API를 사용 하 여 Azure 센티널에서 구하기 및 라이브 스트림 쿼리 관리

Azure Monitor Log Analytics에 포함 되는 Azure 센티널은 Log Analytics ' REST API를 사용 하 여 구하기 및 라이브 스트림 쿼리를 관리할 수 있습니다. 이 문서에서는 REST API를 사용 하 여 구하기 쿼리를 만들고 관리 하는 방법을 보여 줍니다.  이러한 방식으로 만들어진 쿼리는 Azure 센티널 UI에 표시 됩니다.

[저장 된 검색 API](https://docs.microsoft.com/rest/api/loganalytics/savedsearches)에 대 한 자세한 내용은 결정적인 REST API 참조를 참조 하세요.

## <a name="api-examples"></a>API 예제

다음 예에서는 이러한 자리 표시자를 다음 표에 지정 된 대체 항목으로 바꿉니다.

| 자리표시자 | 다음 항목으로 교체 |
|-|-|
| **구독** | 구하기 또는 라이브 스트림 쿼리를 적용할 구독의 이름입니다. |
| **ResourceGroupName** | 구하기 또는 라이브 스트림 쿼리를 적용 하는 리소스 그룹의 이름입니다. |
| **{savedSearchId}** | 각 사냥 쿼리의 고유 id (GUID)입니다. |
| **WorkspaceName** | 쿼리의 대상인 Log Analytics 작업 영역의 이름입니다. |
| **DisplayName** | 쿼리에 대해 선택한 표시 이름입니다. |
| **한** | 구하기 또는 라이브 스트림 쿼리에 대 한 설명입니다. |
| **공학적** | 쿼리에 적용 되는 관련 MITRE AT&T&접시 헤드 전술입니다. |
| **쿼리입니다** | 쿼리에 대 한 쿼리 식입니다. |
|  

### <a name="example-1"></a>예 1

이 예에서는 지정 된 Azure 센티널 작업 영역에 대 한 구하기 쿼리를 만들거나 업데이트 하는 방법을 보여 줍니다.  라이브 스트림 쿼리의 경우 **요청 본문** 에서 " *category": "* 라이브 스트림 queries"를 "Category": " *queries"* 로 바꿉니다. 

#### <a name="request-header"></a>요청 헤더

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>예제 2

이 예에서는 지정 된 Azure 센티널 작업 영역에 대 한 구하기 또는 라이브 스트림 쿼리를 삭제 하는 방법을 보여 줍니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>예제 3

이 예에서는 지정 된 작업 영역에 대 한 검색 또는 라이브 스트림 쿼리를 검색 하는 방법을 보여 줍니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Log Analytics API를 사용 하 여 Azure 센티널에서 구하기 및 라이브 스트림 쿼리를 관리 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [노트북을 사용 하 여 자동화 된 구하기 캠페인 실행](notebooks.md)
