---
title: 보기 정의 개요
description: Azure Managed Applications에 대한 보기 정의를 만드는 개념을 설명합니다.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 55263d3c742d18cf03303f96f08fb9aa370c7af8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592058"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications의 보기 정의 아티팩트

보기 정의는 Azure Managed Applications의 선택적 아티팩트입니다. 보기 정의를 통해 개요 페이지를 사용자 지정하고 메트릭 및 사용자 지정 리소스와 같은 보기를 더 추가할 수 있습니다.

해당 문서에서는 보기 정의 아티팩트 및 해당 기능에 대한 개요를 제공합니다.

## <a name="view-definition-artifact"></a>보기 정의 아티팩트

보기 정의 아티팩트 이름을 **viewDefinition.json** 으로 지정하고, 관리형 애플리케이션 정의를 만드는 .zip 패키지에서 **createUiDefinition.json** 및 **mainTemplate.json** 과 동일한 수준에 배치해야 합니다. .zip 패키지를 만들고 관리형 애플리케이션 정의를 게시하는 방법을 알아보려면 [Azure 관리형 애플리케이션 정의 게시](publish-service-catalog-app.md)를 참조하세요.

## <a name="view-definition-schema"></a>보기 정의 스키마

**viewDefinition.json** 파일은 보기의 배열인 최상위 속성 `views` 하나만 가지고 있습니다. 각 보기는 관리형 애플리케이션 사용자 인터페이스의 목차에서 별도의 메뉴 항목으로 표시됩니다. 각 보기에는 보기의 형식을 설정하는 `kind` 속성이 있습니다. [Overview](#overview), [Metrics](#metrics), [CustomResources](#custom-resources), [Associations](#associations) 값 중 하나로 설정해야 합니다. 자세한 내용은 현재 [JSON schema for viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)을 참조하세요.

보기 정의에 대한 샘플 JSON:

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

**viewDefinition.json** 에서 해당 보기를 제공할 경우 관리형 애플리케이션의 기본 개요 페이지가 재정의됩니다.

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

|속성|필수|설명|
|---------|---------|---------|
|header|예|개요 페이지의 헤더입니다.|
|description|예|관리형 애플리케이션에 대한 설명입니다.|
|명령|예|개요 페이지의 추가 도구 모음 단추에 대한 배열입니다. [명령](#commands)을 참조하세요.|

![데모 애플리케이션을 실행하기 위한 테스트 작업 컨트롤이 포함된 관리형 애플리케이션에 대한 개요를 보여 주는 스크린샷입니다.](./media/view-definition/overview.png)

## <a name="metrics"></a>메트릭

`"kind": "Metrics"`

메트릭 보기를 사용하면 [Azure Monitor Metrics](../../azure-monitor/essentials/data-platform-metrics.md)의 관리형 애플리케이션 리소스에서 데이터를 수집하고 집계할 수 있습니다.

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

|속성|필수|설명|
|---------|---------|---------|
|displayName|예|표시된 보기의 제목입니다.|
|버전|예|보기를 렌더링하는 데 사용된 플랫폼의 버전입니다.|
|차트|예|메트릭 페이지의 차트 배열입니다.|

### <a name="chart"></a>차트

|속성|필수|설명|
|---------|---------|---------|
|displayName|예|표시된 차트의 제목입니다.|
|chartType|예|해당 차트에 사용할 시각화입니다. 기본적으로 꺾은선형 차트를 사용합니다. 지원되는 차트 종류: `Bar, Line, Area, Scatter`|
|메트릭|예|해당 차트에 그릴 메트릭의 배열입니다. Azure Portal이 지원하는 메트릭에 대한 자세한 정보는 [Azure Monitor에서 지원되는 메트릭](../../azure-monitor/essentials/metrics-supported.md)을 참조하세요.|

### <a name="metric"></a>메트릭

|속성|필수|설명|
|---------|---------|---------|
|name|예|메트릭의 이름입니다.|
|aggregationType|예|해당 메트릭에 사용할 집계 형식입니다. 지원되는 집계 형식: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|예|올바른 메트릭 공급자를 결정할 때 사용할 추가 정보입니다.|
|resourceTagFilter|예|메트릭이 표시되는 리소스 태그 배열(word `or`로 구분됨)입니다. 리소스 종류 필터의 상위에 적용됩니다.|
|resourceType|예|메트릭이 표시되는 리소스 종류입니다.|

![관리형 애플리케이션에 대해 내 메트릭 보기라는 모니터링 페이지를 보여 주는 스크린샷입니다.](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>사용자 지정 리소스

`"kind": "CustomResources"`

해당 형식의 보기를 여러 개 정의할 수 있습니다. 각 보기는 **mainTemplate.json** 에서 정의한 사용자 지정 공급자의 **고유한** 사용자 지정 리소스 종류를 나타냅니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](../custom-providers/overview.md)를 참조하세요.

해당 보기에서 사용자 지정 리소스 종류에 대한 GET, PUT, DELETE 및 POST 작업을 수행할 수 있습니다. POST 작업은 사용자 지정 리소스 종류의 컨텍스트에서 전역 사용자 지정 작업 또는 사용자 지정 작업으로서 수행할 수 있습니다.

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

|속성|필수|설명|
|---------|---------|---------|
|displayName|예|표시된 보기의 제목입니다. **viewDefinition.json** 의 각 CustomResources 보기에 대한 **고유한** 제목이어야 합니다.|
|버전|예|보기를 렌더링하는 데 사용된 플랫폼의 버전입니다.|
|resourceType|예|사용자 지정 리소스 종류입니다. 사용자 지정 공급자의 **고유한** 사용자 지정 리소스 종류여야 합니다.|
|icon|예|보기의 아이콘입니다. 예제 아이콘 목록은 [JSON 스키마](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)에 정의되어 있습니다.|
|createUIDefinition|예|사용자 지정 리소스 만들기 명령에 대한 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.|
|명령|예|CustomResources 보기의 추가 도구 모음 단추에 대한 배열입니다. [명령](#commands)을 참조하세요.|
|열|예|사용자 지정 리소스의 열 배열입니다. 정의되지 않은 경우 `name` 열이 기본적으로 표시됩니다. 열에는 `"key"` 및 `"displayName"`이 있어야 합니다. 키에 대해 보기에 표시할 속성의 키를 제공합니다. 중첩된 경우에는 `"key": "name"` 또는 `"key": "properties.property1"`과 같이 dot을 구분 기호로 사용합니다. 표시 이름에는 보기에 표시할 속성의 표시 이름을 제공합니다. 또는 `"optional"` 속성을 제공할 수도 있습니다. True로 설정하면 기본적으로 보기에서 열이 숨겨집니다.|

![스크린샷은 테스트 사용자 지정 리소스 종류라는 리소스 페이지 및 사용자 지정 컨텍스트 작업 컨트롤을 보여 줍니다.](./media/view-definition/customresources.png)

## <a name="commands"></a>명령

명령은 페이지에 표시되는 추가 도구 모음 단추의 배열입니다. 각 명령은 **mainTemplate.json** 에 정의된 Azure 사용자 지정 공급자의 POST 작업을 나타냅니다. 사용자 지정 공급자에 대한 소개는 [Azure 사용자 지정 공급자 개요](../custom-providers/overview.md)를 참조하세요.

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

|속성|필수|설명|
|---------|---------|---------|
|displayName|예|표시되는 명령 단추의 이름입니다.|
|path|예|사용자 지정 공급자 작업의 이름입니다. 작업은 **mainTemplate.json** 에서 정의해야 합니다.|
|icon|예|명령 단추의 아이콘입니다. 예제 아이콘 목록은 [JSON 스키마](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)에 정의되어 있습니다.|
|createUIDefinition|예|명령에 대한 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.|

## <a name="associations"></a>연결

`"kind": "Associations"`

해당 형식의 보기를 여러 개 정의할 수 있습니다. 해당 보기를 사용하여 **mainTemplate.json** 에서 정의한 사용자 지정 공급자를 통해 관리형 애플리케이션에 기존 리소스를 연결할 수 있습니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](../custom-providers/overview.md)를 참조하세요.

해당 보기에서는 `targetResourceType`에 따라 기존 Azure 리소스를 확장할 수 있습니다. 리소스를 선택하면 리소스에 부작용을 적용할 수 있는 **퍼블릭** 사용자 지정 공급자에 대한 온보딩 요청이 생성됩니다. 

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

|속성|필수|설명|
|---------|---------|---------|
|displayName|예|표시된 보기의 제목입니다. **viewDefinition.json** 의 각 Associations 보기에 대한 **고유한** 제목이어야 합니다.|
|버전|예|보기를 렌더링하는 데 사용된 플랫폼의 버전입니다.|
|targetResourceType|예|대상 리소스 종류입니다. 리소스 온보딩에 대해 표시되는 리소스 종류입니다.|
|createUIDefinition|예|연결 리소스 만들기 명령에 대한 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.|

## <a name="looking-for-help"></a>도움말 찾기

Azure Managed Applications에 대한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps)에서 문의하세요. 이미 비슷한 질문을 받고 답변했을 수 있으므로 게시하기 전에 먼저 확인하세요. 빠른 응답을 얻으려면 `azure-managedapps` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

- 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](overview.md)를 참조하세요.
- 사용자 지정 공급자에 대한 소개는 [Azure 사용자 지정 공급자 개요](../custom-providers/overview.md)를 참조하세요.
- Azure 사용자 지정 공급자를 사용하여 Azure Managed Application을 만들려면 [자습서: 사용자 지정 공급자 작업 및 리소스 종류를 사용하여 관리형 애플리케이션 만들기](tutorial-create-managed-app-with-custom-provider.md)를 참조하세요.
