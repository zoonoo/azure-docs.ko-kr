---
title: Azure Managed Applications의 뷰 정의의 개요 | Microsoft Docs
description: Azure Managed Applications에 대 한 보기 정의 만드는 개념을 설명 합니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478746"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure Managed Applications의 뷰 정의 아티팩트

뷰 정의 Azure Managed Applications의 선택적 아티팩트입니다. 개요 페이지를 사용자 지정 하 여 메트릭 및 사용자 지정 리소스와 같은 자세한 뷰를 추가할 수 있습니다.

이 문서는 개요 뷰 정의 아티팩트 및 해당 기능을 제공합니다.

## <a name="view-definition-artifact"></a>보기 정의 아티팩트

뷰 정의 아티팩트 이름은 **viewDefinition.json** 와 같은 수준에 배치 하 고 **createUiDefinition.json** 하 고 **mainTemplate.json** .zip의 관리 되는 응용 프로그램 정의 만드는 패키지 합니다. .Zip 패키지를 만들기 및 관리 되는 응용 프로그램 정의 게시 하는 방법에 알아보려면 참조 [Azure Managed Application 정의 게시 합니다.](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>뷰 정의 스키마

합니다 **viewDefinition.json** 파일에 하나의 최상위 `views` 뷰의 배열 속성입니다. 각 보기는 콘텐츠의 테이블에 별도 메뉴 항목으로 관리 되는 응용 프로그램 사용자 인터페이스에 표시 됩니다. 각 보기에는 `kind` 보기의 유형을 설정 하는 속성입니다. 다음 값 중 하나로 설정 되어야 합니다. [개요](#overview)하십시오 [메트릭을](#metrics)를 [CustomResources](#custom-resources)합니다. 자세한 내용은 현재 참조 [viewDefinition.json에 대 한 JSON 스키마](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)합니다.

뷰 정의 대 한 샘플 JSON:

```json
{
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
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
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
    ]
}

```

## <a name="overview"></a>개요

`"kind": "Overview"`

이 뷰를 제공 하는 경우 **viewDefinition.json**, 관리 되는 응용 프로그램의 기본 개요 페이지에 우선 합니다.

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

|자산|필수|설명|
|---------|---------|---------|
|머리글|아닙니다.|개요 페이지의 헤더입니다.|
|description|아닙니다.|관리 되는 응용 프로그램의 설명입니다.|
|명령|아닙니다.|개요 페이지의 추가 도구 모음 단추 배열을 참조 [명령](#commands)입니다.|

## <a name="metrics"></a>메트릭

`"kind": "Metrics"`

메트릭 보기를 사용 하면 수집 하 고 관리 되는 응용 프로그램 리소스의 데이터를 집계 [Azure Monitor 메트릭](../azure-monitor/platform/data-platform-metrics.md)합니다.

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

|자산|필수|설명|
|---------|---------|---------|
|displayName|아닙니다.|보기의 표시 된 제목입니다.|
|버전|아닙니다.|뷰를 렌더링 하는 데 사용 되는 플랫폼의 버전입니다.|
|차트|예|메트릭 페이지의 차트의 배열입니다.|

### <a name="chart"></a>차트

|자산|필수|설명|
|---------|---------|---------|
|displayName|예|차트의 표시 된 제목입니다.|
|chartType|아닙니다.|이 차트에 사용할 시각화 합니다. 기본적으로 꺾은선형 차트를 사용합니다. 지원 되는 차트 유형: `Bar, Line, Area, Scatter`합니다.|
|metrics|예|이 차트에 넣을 메트릭의 배열입니다. Azure portal에서 지원 되는 메트릭에 대 한 자세한 내용은를 참조 하세요. [지원 되는 Azure Monitor 메트릭](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>메트릭

|자산|필수|설명|
|---------|---------|---------|
|name|예|메트릭의 이름입니다.|
|aggregationType|예|이 메트릭에 대해 사용 하도록 집계 유형입니다. 지원 되는 집계 형식: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|아닙니다.|올바른 메트릭 공급자를 결정할 때 사용할 추가 정보입니다.|
|resourceTagFilter|아닙니다.|리소스 태그 배열 (사용 하 여 구분 됩니다 `or` word) 메트릭을 표시 됩니다. 리소스 유형 필터를 기반으로 적용 됩니다.|
|resourceType|예|메트릭에 표시할 리소스 형식입니다.|

## <a name="custom-resources"></a>사용자 지정 리소스

`"kind": "CustomResources"`

이 형식의 여러 뷰를 정의할 수 있습니다. 각 뷰를 나타내는 **고유** 에서 정의한 사용자 지정 공급자에서 사용자 지정 리소스 종류 **mainTemplate.json**합니다. 사용자 지정 공급 기업에 대한 소개는 [Azure 사용자 지정 공급 기업 미리 보기 개요](custom-providers-overview.md)를 참조하세요.

수행할 수 있습니다이 뷰에서 GET, PUT, 삭제 하 고 사용자 지정 리소스 형식에 대 한 작업을 게시 합니다. POST 작업은 전역 사용자 지정 작업 또는 사용자 지정 리소스 형식의 사용자 지정 작업 컨텍스트에서 수 있습니다.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
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

|자산|필수|설명|
|---------|---------|---------|
|displayName|예|보기의 표시 된 제목입니다. 제목이 있어야 **고유한** 의 각 CustomResources 뷰에 대 한 프로그램 **viewDefinition.json**합니다.|
|버전|아닙니다.|뷰를 렌더링 하는 데 사용 되는 플랫폼의 버전입니다.|
|resourceType|예|사용자 지정 리소스 형식입니다. 이어야 합니다는 **고유** 사용자 지정 공급자의 사용자 지정 리소스 형식입니다.|
|createUIDefinition|아닙니다.|UI 정의 만들기에 대 한 스키마 사용자 지정 리소스 명령을 만듭니다. UI 정의 만들기 소개를 참조 하세요. [CreateUiDefinition 시작](create-uidefinition-overview.md)|
|명령|아닙니다.|추가 도구 모음 단추 CustomResources 뷰의 배열을 참조 [명령](#commands)입니다.|
|열|아닙니다.|사용자 지정 리소스에 있는 열의 배열입니다. 정의 되지 않은 경우는 `name` 열 기본적으로 표시 됩니다. 열이 있어야 합니다 `"key"` 고 `"displayName"`입니다. 키에 대 한 보기에 표시할 속성의 키를 제공 합니다. 중첩 된 경우 점 구분 기호로 사용, 예를 들어 `"key": "name"` 또는 `"key": "properties.property1"`합니다. 표시 이름에 대 한 보기에 표시할 속성의 표시 이름을 제공 합니다. 제공할 수도 있습니다는 `"optional"` 속성입니다. 때 열을 true로 설정은 기본적으로 보기에서 숨겨집니다.|

## <a name="commands"></a>명령

페이지에 표시 되는 추가 도구 모음 단추의 배열인 명령입니다. 각 명령에 정의 된 Azure 사용자 지정 공급자에서 POST 작업을 나타내는 **mainTemplate.json**합니다. 사용자 지정 공급자에 대 한 소개를 참조 하세요 [Azure 사용자 지정 공급자 개요](custom-providers-overview.md)합니다.

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

|자산|필수|설명|
|---------|---------|---------|
|displayName|예|명령 단추 표시 이름입니다.|
|경로|예|사용자 지정 공급자 동작 이름입니다. 작업에 정의 되어 있어야 **mainTemplate.json**합니다.|
|아이콘|아닙니다.|명령 단추 아이콘입니다. 지원 되는 아이콘 목록에 정의 된 [JSON 스키마](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)합니다.|
|createUIDefinition|아닙니다.|명령에 대 한 UI 정의 스키마를 만듭니다. UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

- 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](overview.md)를 참조하세요.
- 사용자 지정 공급자에 대 한 소개를 참조 하세요 [Azure 사용자 지정 공급자 개요](custom-providers-overview.md)합니다.
