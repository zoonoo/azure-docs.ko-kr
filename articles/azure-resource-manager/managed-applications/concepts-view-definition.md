---
title: 뷰 정의 개요
description: Azure 관리되는 응용 프로그램에 대한 뷰 정의를 만드는 개념을 설명합니다.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391685"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure 관리 형 응용 프로그램의 정의 아티팩트 보기

뷰 정의는 Azure 관리 되는 응용 프로그램에서 선택적 아티팩트입니다. 이를 통해 개요 페이지를 사용자 지정하고 측정항목 및 사용자 지정 리소스와 같은 보기를 더 추가할 수 있습니다.

이 문서에서는 뷰 정의 아티팩트 및 해당 기능에 대한 개요를 제공합니다.

## <a name="view-definition-artifact"></a>보기 정의 아티팩트

뷰 정의 아티팩트는 **viewDefinition.json이라는** 이름을 지정하고 관리되는 응용 프로그램 정의를 만드는 .zip 패키지의 **createUiDefinition.json** 및 **mainTemplate.json과** 동일한 수준에 배치되어야 합니다. .zip 패키지를 만들고 관리되는 응용 프로그램 정의를 게시하는 방법을 알아보려면 [Azure 관리되는 응용 프로그램 정의 게시를](publish-service-catalog-app.md) 참조하십시오.

## <a name="view-definition-schema"></a>정의 스키마 보기

**viewDefinition.json** 파일에는 뷰 배열인 최상위 속성이 `views` 하나만 있습니다. 각 보기는 관리되는 응용 프로그램 사용자 인터페이스에 목치표의 별도의 메뉴 항목으로 표시됩니다. 각 뷰에는 `kind` 뷰 유형을 설정하는 속성이 있습니다. [개요,](#overview) [메트릭,](#metrics) [사용자 지정 리소스,](#custom-resources)연결 중 하나로 설정해야 [합니다.](#associations) 자세한 내용은 [viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)에 대한 현재 JSON 스키마를 참조하십시오.

보기 정의를 위한 샘플 JSON:

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

**viewDefinition.json에서**이 보기를 제공 하면 관리 되는 응용 프로그램에서 기본 개요 페이지를 재정의 합니다.

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
|머리글|예|개요 페이지의 헤더입니다.|
|description|예|관리되는 응용 프로그램에 대한 설명입니다.|
|명령|예|개요 페이지의 추가 도구 모음 단추 배열은 [명령을](#commands)참조하십시오.|

![개요](./media/view-definition/overview.png)

## <a name="metrics"></a>메트릭

`"kind": "Metrics"`

메트릭 보기를 사용하면 Azure Monitor 메트릭에서 관리되는 응용 프로그램 리소스에서 데이터를 수집하고 집계할 수 [있습니다.](../../azure-monitor/platform/data-platform-metrics.md)

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
|displayName|예|뷰의 표시된 제목입니다.|
|버전|예|뷰를 렌더링하는 데 사용되는 플랫폼 버전입니다.|
|차트|예|메트릭 페이지의 차트 배열입니다.|

### <a name="chart"></a>차트

|속성|필수|Description|
|---------|---------|---------|
|displayName|예|차트의 표시된 제목입니다.|
|차트유형|예|이 차트에 사용할 시각화입니다. 기본적으로 선부 차트를 사용합니다. 지원되는 차트 `Bar, Line, Area, Scatter`유형: .|
|메트릭|예|이 차트에서 플롯할 메트릭의 배열입니다. Azure 포털에서 지원되는 메트릭에 대해 자세히 알아보려면 [Azure 모니터를 사용하여 지원되는 메트릭을](../../azure-monitor/platform/metrics-supported.md) 참조하십시오.|

### <a name="metric"></a>메트릭

|속성|필수|Description|
|---------|---------|---------|
|name|예|메트릭의 이름입니다.|
|집계 유형|예|이 메트릭에 사용할 집계 유형입니다. 지원되는 집계 유형:`none, sum, min, max, avg, unique, percentile, count`|
|네임스페이스|예|올바른 메트릭 공급자를 결정할 때 사용할 추가 정보입니다.|
|리소스 태그 필터|예|메트릭이 표시될 리소스 태그 배열(단어로 `or` 구분). 리소스 유형 필터 위에 적용됩니다.|
|resourceType|예|메트릭이 표시될 리소스 유형입니다.|

![메트릭](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>사용자 지정 리소스

`"kind": "CustomResources"`

이 유형의 여러 뷰를 정의할 수 있습니다. 각 보기는 **mainTemplate.json**에서 정의한 사용자 지정 공급자의 **고유한** 사용자 지정 리소스 유형을 나타냅니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](../custom-providers/overview.md)를 참조하세요.

이 보기에서 사용자 지정 리소스 유형에 대해 GET, PUT, DELETE 및 POST 작업을 수행할 수 있습니다. POST 작업은 사용자 지정 리소스 유형의 컨텍스트에서 전역 사용자 지정 작업 또는 사용자 지정 작업일 수 있습니다.

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
|displayName|예|뷰의 표시된 제목입니다. 제목은 **viewDefinition.json**의 각 사용자 지정 리소스 보기에 대해 **고유해야** 합니다.|
|버전|예|뷰를 렌더링하는 데 사용되는 플랫폼 버전입니다.|
|resourceType|예|사용자 지정 리소스 유형입니다. 사용자 지정 공급자의 **고유한** 사용자 지정 리소스 유형이어야 합니다.|
|icon|예|뷰의 아이콘입니다. 예제 아이콘 목록은 [JSON 스키마에](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)정의되어 있습니다.|
|createUI정의|예|사용자 지정 리소스 명령을 만들기 위해 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition를 시작하기](create-uidefinition-overview.md) 를 참조하십시오.|
|명령|예|CustomResources 보기의 추가 도구 모음 단추 배열은 [명령을](#commands)참조하십시오.|
|열|예|사용자 지정 리소스의 열 배열입니다. 정의되지 않은 `name` 경우 열이 기본적으로 표시됩니다. 열에는 및 `"key"` `"displayName"`가 있어야 합니다. 키의 경우 뷰에 표시할 속성의 키를 제공합니다. 중첩된 경우 예를 들어 점을 구분 `"key": "name"` 기호로 사용하거나 `"key": "properties.property1"`. 표시 이름의 경우 뷰에 표시할 속성의 표시 이름을 제공합니다. 숙소를 `"optional"` 제공할 수도 있습니다. true로 설정하면 기본적으로 열이 뷰에 숨김이 있습니다.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>명령

명령은 페이지에 표시되는 추가 도구 모음 단추의 배열입니다. 각 명령은 **mainTemplate.json에**정의된 Azure 사용자 지정 공급자의 POST 작업을 나타냅니다. 사용자 지정 공급자에 대한 소개는 [Azure 사용자 지정 공급자 개요를](../custom-providers/overview.md)참조하십시오.

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
|displayName|예|명령 단추의 표시된 이름입니다.|
|경로|예|사용자 지정 공급자 작업 이름입니다. 작업은 **mainTemplate.json**.|
|icon|예|명령 단추의 아이콘입니다. 예제 아이콘 목록은 [JSON 스키마에](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)정의되어 있습니다.|
|createUI정의|예|명령에 대한 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.|

## <a name="associations"></a>연결

`"kind": "Associations"`

이 유형의 여러 뷰를 정의할 수 있습니다. 이 보기를 사용하면 **mainTemplate.json**에 정의한 사용자 지정 공급자를 통해 기존 리소스를 관리되는 응용 프로그램에 연결할 수 있습니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](../custom-providers/overview.md)를 참조하세요.

이 보기에서는 을 기반으로 기존 Azure `targetResourceType`리소스를 확장할 수 있습니다. 리소스를 선택하면 **공용** 사용자 지정 공급자에 대한 온보딩 요청을 생성하여 리소스에 부작용을 적용할 수 있습니다. 

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
|displayName|예|뷰의 표시된 제목입니다. 제목은 **ViewDefinition.json**에서 각 연결 보기에 대해 **고유해야** 합니다.|
|버전|예|뷰를 렌더링하는 데 사용되는 플랫폼 버전입니다.|
|targetResourceType|예|대상 리소스 유형입니다. 리소스 온보딩에 대해 표시되는 리소스 유형입니다.|
|createUI정의|예|연결 리소스 명령을 만들기 위해 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition를 시작하기](create-uidefinition-overview.md) 를 참조하십시오.|

## <a name="looking-for-help"></a>도움말 찾기

Azure Managed Applications에 대한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps)에서 문의하세요. 이미 비슷한 질문을 받고 답변했을 수 있으므로 게시하기 전에 먼저 확인하세요. 빠른 응답을 얻으려면 `azure-managedapps` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

- 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](overview.md)를 참조하세요.
- 사용자 지정 공급자에 대한 소개는 [Azure 사용자 지정 공급자 개요를](../custom-providers/overview.md)참조하십시오.
- Azure 사용자 지정 공급자를 사용하여 Azure 관리 응용 프로그램을 만드는 경우 [자습서: 사용자 지정 공급자 작업 및 리소스 형식을 사용하여 관리되는 응용 프로그램 만들기](tutorial-create-managed-app-with-custom-provider.md) 를 참조하십시오.
