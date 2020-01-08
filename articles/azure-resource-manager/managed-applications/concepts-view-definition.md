---
title: 뷰 정의 개요
description: Azure Managed Applications에 대 한 뷰 정의를 만드는 개념을 설명 합니다.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650696"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications에서 뷰 정의 아티팩트

뷰 정의는 Azure Managed Applications의 선택적 아티팩트입니다. 이를 통해 개요 페이지를 사용자 지정 하 고 메트릭 및 사용자 지정 리소스와 같은 보기를 더 추가할 수 있습니다.

이 문서에서는 뷰 정의 아티팩트 및 해당 기능에 대 한 개요를 제공 합니다.

## <a name="view-definition-artifact"></a>보기 정의 아티팩트

뷰 정의 아티팩트 이름을 **Viewdefinition. json** 으로 지정 하 고 관리 되는 응용 프로그램 정의를 만드는 .zip 패키지의 **createuidefinition. json** 및 **maintemplate.json** 와 동일한 수준에 배치 해야 합니다. .Zip 패키지를 만들고 관리 되는 응용 프로그램 정의를 게시 하는 방법을 알아보려면 [Azure 관리 되는 응용 프로그램 정의 게시](publish-managed-app-definition-quickstart.md) 를 참조 하세요.

## <a name="view-definition-schema"></a>정의 스키마 보기

**Viewdefinition. json** 파일에는 뷰의 배열인 최상위 `views` 속성이 하나만 있습니다. 각 보기는 관리 되는 응용 프로그램 사용자 인터페이스에 목차에서 별도의 메뉴 항목으로 표시 됩니다. 각 뷰에는 뷰의 유형을 설정 하는 `kind` 속성이 있습니다. [개요](#overview), [메트릭](#metrics), [customresources](#custom-resources), [association](#associations)값 중 하나로 설정 해야 합니다. 자세한 내용은 [viewDefinition에 대 한 현재 json 스키마 (json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#))를 참조 하세요.

뷰 정의에 대 한 샘플 JSON:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>개요

`"kind": "Overview"`

**Viewdefinition. json**에이 뷰를 제공 하면 관리 되는 응용 프로그램의 기본 개요 페이지가 재정의 됩니다.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|속성|필수|Description|
|---------|---------|---------|
|머리글|아닙니다.|개요 페이지의 머리글입니다.|
|description|아닙니다.|관리 되는 응용 프로그램에 대 한 설명입니다.|
|명령|아닙니다.|개요 페이지의 추가 도구 모음 단추에 대 한 배열입니다. [명령](#commands)을 참조 하십시오.|

![개요](./media/view-definition/overview.png)

## <a name="metrics"></a>메트릭

`"kind": "Metrics"`

메트릭 보기를 사용 하면 [Azure Monitor 메트릭의](../../azure-monitor/platform/data-platform-metrics.md)관리 되는 응용 프로그램 리소스에서 데이터를 수집 하 고 집계할 수 있습니다.

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|속성|필수|Description|
|---------|---------|---------|
|displayName|아닙니다.|표시 된 뷰의 제목입니다.|
|버전|아닙니다.|뷰를 렌더링 하는 데 사용 되는 플랫폼의 버전입니다.|
|차트|예|메트릭 페이지의 차트 배열입니다.|

### <a name="chart"></a>차트

|속성|필수|Description|
|---------|---------|---------|
|displayName|예|표시 된 차트의 제목입니다.|
|chartType|아닙니다.|이 차트에 사용할 시각화입니다. 기본적으로 꺾은선형 차트를 사용 합니다. 지원 되는 차트 종류: `Bar, Line, Area, Scatter`.|
|메트릭|예|이 차트에 그릴 메트릭의 배열입니다. Azure Portal에서 지원 되는 메트릭에 대 한 자세한 내용은 [Azure Monitor에서 지원 되는 메트릭](../../azure-monitor/platform/metrics-supported.md) 을 참조 하세요.|

### <a name="metric"></a>메트릭

|속성|필수|Description|
|---------|---------|---------|
|name|예|메트릭의 이름입니다.|
|aggregationType|예|이 메트릭에 사용할 집계 유형입니다. 지원 되는 집계 유형: `none, sum, min, max, avg, unique, percentile, count`|
|네임스페이스|아닙니다.|올바른 메트릭 공급자를 결정할 때 사용할 추가 정보입니다.|
|resourceTagFilter|아닙니다.|메트릭이 표시 되는 리소스 태그 배열을 `or` 단어로 구분 됩니다. 리소스 종류 필터의 위에 적용 됩니다.|
|resourceType|예|메트릭이 표시 되는 리소스 종류입니다.|

![메트릭](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>사용자 지정 리소스

`"kind": "CustomResources"`

이 형식의 뷰를 여러 개 정의할 수 있습니다. 각 보기는 **maintemplate.json**에서 정의한 사용자 지정 공급자의 **고유한** 사용자 지정 리소스 형식을 나타냅니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](../custom-providers/overview.md)를 참조하세요.

이 보기에서 사용자 지정 리소스 종류에 대 한 GET, PUT, DELETE 및 POST 작업을 수행할 수 있습니다. POST 작업은 사용자 지정 리소스 형식의 컨텍스트에서 전역 사용자 지정 작업 또는 사용자 지정 작업 일 수 있습니다.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|속성|필수|Description|
|---------|---------|---------|
|displayName|예|표시 된 뷰의 제목입니다. 제목은 Viewdefinition의 각 CustomResources 뷰에 대해 **고유** 해야 합니다 **. json**.|
|버전|아닙니다.|뷰를 렌더링 하는 데 사용 되는 플랫폼의 버전입니다.|
|resourceType|예|사용자 지정 리소스 형식입니다. 사용자 지정 공급자의 **고유한** 사용자 지정 리소스 형식 이어야 합니다.|
|icon|아닙니다.|뷰의 아이콘입니다. 예제 아이콘 목록은 [JSON 스키마](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)에 정의 되어 있습니다.|
|createUIDefinition|아닙니다.|사용자 지정 리소스 만들기 명령에 대 한 UI 정의 스키마를 만듭니다. UI 정의를 만드는 방법에 대 한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md) 하기를 참조 하세요.|
|명령|아닙니다.|CustomResources 뷰의 추가 도구 모음 단추에 대 한 배열입니다. [명령](#commands)을 참조 하십시오.|
|열|아닙니다.|사용자 지정 리소스의 열 배열입니다. 정의 되지 않은 경우 기본적으로 `name` 열이 표시 됩니다. 열에 `"key"` 및 `"displayName"`있어야 합니다. 키에 대해 뷰에 표시할 속성의 키를 제공 합니다. 중첩 된 경우 점을 구분 기호로 사용 합니다 (예: `"key": "name"` 또는 `"key": "properties.property1"`). 표시 이름에는 뷰에 표시할 속성의 표시 이름을 제공 합니다. `"optional"` 속성을 제공할 수도 있습니다. True로 설정 하면 기본적으로 뷰에서 열이 숨겨집니다.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>명령

명령은 페이지에 표시 되는 추가 도구 모음 단추의 배열입니다. 각 명령은 **maintemplate.json**에 정의 된 Azure 사용자 지정 공급자의 게시 작업을 나타냅니다. 사용자 지정 공급자에 대 한 소개는 [Azure 사용자 지정 공급자 개요](../custom-providers/overview.md)를 참조 하세요.

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|속성|필수|Description|
|---------|---------|---------|
|displayName|예|명령 단추의 표시 이름입니다.|
|경로|예|사용자 지정 공급자 작업 이름입니다. 작업은 **maintemplate.json**에서 정의 해야 합니다.|
|icon|아닙니다.|명령 단추의 아이콘입니다. 예제 아이콘 목록은 [JSON 스키마](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)에 정의 되어 있습니다.|
|createUIDefinition|아닙니다.|명령에 대 한 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.|

## <a name="associations"></a>연결

`"kind": "Associations"`

이 형식의 뷰를 여러 개 정의할 수 있습니다. 이 보기에서는 **maintemplate.json**에서 정의한 사용자 지정 공급자를 통해 기존 리소스를 관리 되는 응용 프로그램에 연결할 수 있습니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](../custom-providers/overview.md)를 참조하세요.

이 보기에서 `targetResourceType`기반으로 기존 Azure 리소스를 확장할 수 있습니다. 리소스를 선택 하면 리소스에 부작용을 적용할 수 있는 **공용** 사용자 지정 공급자에 대 한 온 보 딩 요청이 생성 됩니다. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|속성|필수|Description|
|---------|---------|---------|
|displayName|예|표시 된 뷰의 제목입니다. 제목은 Viewdefinition의 각 연결 뷰에 대해 **고유** 해야 합니다 **. json**.|
|버전|아닙니다.|뷰를 렌더링 하는 데 사용 되는 플랫폼의 버전입니다.|
|targetResourceType|예|대상 리소스 종류입니다. 리소스 온 보 딩에 대해 표시 되는 리소스 종류입니다.|
|createUIDefinition|아닙니다.|연결 리소스 만들기 명령에 대 한 UI 정의 스키마를 만듭니다. UI 정의를 만드는 방법에 대 한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md) 하기를 참조 하세요.|

## <a name="looking-for-help"></a>도움말 찾기

Azure Managed Applications에 대한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps)에서 문의하세요. 이미 비슷한 질문을 받고 답변했을 수 있으므로 게시하기 전에 먼저 확인하세요. 빠른 응답을 얻으려면 `azure-managedapps` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

- 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](overview.md)를 참조하세요.
- 사용자 지정 공급자에 대 한 소개는 [Azure 사용자 지정 공급자 개요](../custom-providers/overview.md)를 참조 하세요.
- Azure 사용자 지정 공급자를 사용 하 여 Azure 관리 되는 응용 프로그램을 만들려면 [자습서: 사용자 지정 공급자 작업 및 리소스 종류를 사용 하 여 관리 되는 응용 프로그램 만들기](tutorial-create-managed-app-with-custom-provider.md)
