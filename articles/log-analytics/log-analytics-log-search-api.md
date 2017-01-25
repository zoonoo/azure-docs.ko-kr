---
title: "Log Analytics 로그 검색 REST API | Microsoft Docs"
description: "이 가이드에서는 OMS(Operations Management Suite)의 Log Analytics 검색 REST API를 사용하는 방법을 설명하는 기본 자습서를 제공하며, 명령 사용 방법을 보여 주는 예제를 제공합니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: b12f823d723b013755fc868b883faefa2072eb75
ms.openlocfilehash: 9b21fed003f96dbf7ebd72d6f46fff91acbf039e


---
# <a name="log-analytics-log-search-rest-api"></a>Log Analytics 로그 검색 REST API
이 가이드는 Log Analytics 검색 REST API를 사용하는 방법의 예제를 비롯한 기본 자습서를 제공합니다. Log Analytics는 OMS(Operations Management Suite)의 일부입니다.

> [!NOTE]
> Log Analytics는 이전에 Operational Insights라고 했기 때문에 리소스 공급자에서는 Operational Insights라고 합니다.
>
>

## <a name="overview-of-the-log-search-rest-api"></a>로그 검색 REST API의 개요
Log Analytics 검색 REST API는 RESTful이며 Azure Resource Manager API를 통해 액세스할 수 있습니다. 이 문서에서는 Azure Resource Manager API를 호출하여 단순화하는 공개 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 통해 API에 액세스하는 예제를 제공합니다. ARMClient의 사용은 Log Analytics 검색 API에 액세스하는 다양한 옵션 중 하나입니다. 또 다른 방법은 검색에 액세스하기 위한 Cmdlet을 포함하고 있는 OperationalInsights용 Azure PowerShell 모듈을 사용하는 것입니다. 이러한 도구를 사용하면 Azure Resource Manager API를 통해 OMS 작업 영역을 호출하고 작업 영역 내에서 검색 명령을 수행할 수 있습니다. API는 JSON 형식으로 검색 결과를 출력하여 다양한 프로그래밍 방식으로 검색 결과를 사용하게 됩니다.

Azure Resource Manager는 [Library for.NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) 및 [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx)를 통해 사용할 수 있습니다. 자세한 내용은 연결된 웹 페이지를 검토합니다.

> [!NOTE]
> `|measure count()` 또는 `distinct`와 같은 집계 명령을 사용하는 경우 검색을 호출할 때마다 최대 500,000개의 레코드를 반환할 수 있습니다. 집계 명령을 포함하지 않는 검색의 경우 최대 5,000개의 레코드를 반환합니다.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>기본 Log Analytics 검색 REST API 자습서
### <a name="to-use-armclient"></a>ARMClient를 사용하려면
1. Windows용 오픈 소스 패키지 관리자인 [Chocolatey](https://chocolatey.org/)를 설치합니다. 관리자 권한으로 명령 프롬프트 창을 열고 다음 명령을 실행합니다.

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. 다음 명령을 실행하여 ARMClient를 설치합니다.

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>ARMClient를 사용하여 검색을 수행하려면
1. Microsoft 계정이나 회사 또는 학교 계정을 사용하여 로그인합니다.

    ```
    armclient login
    ```

    성공적으로 로그인하여 지정된 계정에 연결된 모든 구독을 나열합니다.

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. 작업 관리 도구 모음 작업 영역을 가져옵니다.

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Get 호출이 성공하면 구독에 연결된 모든 작업 영역이 출력됩니다.

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 검색 변수를 만듭니다.

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. 새 검색 변수를 사용하여 검색 합니다.

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Log Analytics 검색 REST API 참조 예제
다음 예제에서는 검색 API를 사용하는 방법을 보여줍니다.

### <a name="search---actionread"></a>검색-동작/읽기
**샘플 Url:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**요청:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
다음 테이블에서 사용할 수 있는 속성을 설명합니다.

| **속성** | **설명** |
| --- | --- |
| top |반환할 결과의 최대 수입니다. |
| highlight |일치하는 필드를 강조 표시하는 데 사용된 pre 및 post 매개 변수를 포함합니다 |
| pre |일치하는 필드에 지정된 문자열로 접두사를 추가합니다. |
| post |일치하는 필드에 지정된 문자열을 추가합니다. |
| 쿼리 |결과를 집하고 반환하는 데 사용한 검색 쿼리입니다. |
| 시작 |결과를 찾으려는 시간 창의 시작 부분입니다. |
| end |결과를 찾으려는 시간 창의 끝 부분입니다. |

**응답:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>검색/{ID}-동작/읽기
**저장된 검색의 내용을 요청:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> 검색이 '보류중' 상태와 함께 반환되면 업데이트된 결과를 폴링하는 것은 API를 통해 수행할 수 있습니다. 6분 후 캐시에서 검색 결과가 삭제되고 HTTP Gone이 반환됩니다. 초기 검색 요청이 '성공' 상태를 즉시 반환하는 경우 쿼리되면 이 API를 발생시키는 캐시에 추가되지 않아서 HTTP Gone을 반환합니다. HTTP 200 결과의 내용은 업데이트된 값이 포함된 초기 검색 요청과 같은 형식입니다.
>
>

### <a name="saved-searches"></a>저장된 검색
**저장된 검색의 목록 요청입니다.**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

지원되는 메서드: GET PUT DELETE

지원되는 컬렉션 메서드: GET

다음 테이블에서 사용할 수 있는 속성을 설명합니다.

| 속성 | 설명 |
| --- | --- |
| Id |고유 식별자 |
| ETag |**패치에 필요합니다**. 각 쓰기에 대해 서버에서 업데이트되었습니다. 값은 현재 저장 된 값 또는 업데이트할 ‘*’과 같아야 합니다. 오래되거나 잘못된 값에 대해 409가 반환되었습니다. |
| properties.query |**필수입니다**. 검색 쿼리. |
| properties.displayName |**필수입니다**. 쿼리의 사용자 정의된 표시 이름입니다. |
| properties.category |**필수입니다**. 쿼리의 사용자 정의된 범주입니다. |

> [!NOTE]
> Log Analytics 검색 API는 작업 영역에서 저장된 검색에 대해 폴링될 때 사용자가 만든 저장된 검색을 현재 반환합니다. API는 솔루션에서 제공되는 저장된 검색을 반환하지 않습니다.
>
>

### <a name="create-saved-searches"></a>저장된 검색 만들기
**요청:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>저장된 검색 삭제
**요청:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>저장된 검색 업데이트
 **요청:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>메타데이터-JSON만
작업 영역에 수집된 데이터에 대한 모든 로그 형식에 대해 필드를 참조하는 방법은 다음과 같습니다. 예를 들어 원하는 이벤트 유형에 컴퓨터라는 필드가 있는지 알고 싶은 경우 이 쿼리가 확인하는 한 가지 방법입니다.

**필드에 대한 요청:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**응답:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

다음 테이블에서 사용할 수 있는 속성을 설명합니다.

| **속성** | **설명** |
| --- | --- |
| name |필드 이름. |
| displayName |필드의 표시 이름입니다. |
| type |필드 값의 형식입니다. |
| facetable |현재 'indexed', 'store' 및 'facet' 속성의 조합입니다. |
| display |현재 'display' 속성입니다. 필드를 검색에서 볼 수 있는 경우 true입니다. |
| ownerType |등록된 IP에 속하는 형식에만 줄어듭니다. |

## <a name="optional-parameters"></a>선택적 매개 변수
다음 정보는 사용 가능한 선택적 매개 변수를 설명합니다.

### <a name="highlighting"></a>강조 표시
“Highlight” 매개변수는 해당 응답의 표식 집합을 포함하는 검색 하위 시스템을 요청할 때 사용할 수도 있는 선택적 매개 변수입니다.

이러한 표식은 검색 쿼리에 제공된 용어와 일치하는 시작 및 종료 강조 표시된 텍스트를 나타냅니다.
강조 표시된 용어를 래핑할 검색에서 사용되는 시작 및 종료 표식을 지정할 수 있습니다.

**예제 검색 쿼리**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**샘플 결과:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

위의 결과에 접두사가 있고 추가된 오류 메시지가 포함됩니다.

## <a name="computer-groups"></a>컴퓨터 그룹
컴퓨터 그룹은 컴퓨터 집합을 반환하는 특수한 저장된 검색입니다.  다른 쿼리에서 컴퓨터 그룹을 사용하여 결과를 해당 그룹의 컴퓨터로 제한할 수 있습니다.  컴퓨터 그룹은 값이 Computer인 Group 태그가 있는 저장된 검색으로 구현됩니다.

다음은 컴퓨터 그룹에 대한 샘플 응답입니다.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>컴퓨터 그룹 검색
컴퓨터 그룹을 검색하려면 그룹 ID와 함께 Get 메서드를 사용합니다.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>컴퓨터 그룹 만들기 또는 업데이트
컴퓨터 그룹을 만들려면 고유 저장 검색 ID와 함께 Put 메서드를 사용합니다. 기존 컴퓨터 그룹 ID를 사용하는 경우 해당 ID가 수정됩니다. Log Analytics 포털에서 컴퓨터 그룹을 만들면 해당 그룹 및 이름에서 ID가 만들어집니다.

그룹 정의에 사용된 쿼리가 제대로 작동하려면 해당 그룹에 대한 컴퓨터 집합을 반환해야 합니다.  올바른 데이터가 반환되도록 하려면 `| Distinct Computer`로 쿼리를 종료하는 것이 좋습니다.

저장된 검색에 대한 정의에는 컴퓨터 그룹으로 분류할 검색에 대해 값이 Computer인 Group 태그가 포함되어야 합니다.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>컴퓨터 그룹 삭제
컴퓨터 그룹을 삭제하려면 그룹 ID와 함께 Delete 메서드를 사용합니다.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>다음 단계
* 조건에 대한 사용자 지정 필드를 사용하여 쿼리를 빌드하기 위해 [검색 로그](log-analytics-log-searches.md) 에 대해 알아봅니다.



<!--HONumber=Dec16_HO1-->


