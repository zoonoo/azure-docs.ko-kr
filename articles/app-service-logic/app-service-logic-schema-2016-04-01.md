---
title: "새 스키마 버전 2016-06-01 | Microsoft Docs"
description: "최신 버전의 논리 앱에 대한 JSON 정의 작성 방법 알아보기"
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eec9705688f88cedddedee32c562bdf714e45743


---
# <a name="new-schema-version-2016-06-01"></a>새 스키마 버전 2016-06-01
논리 앱에 대한 새 스키마 및 API 버전에 논리 앱의 안정성 및 사용 편의성을 개선하는 다양한 향상된 기능이 있습니다. 다음과 같은 3개의 주요 차이점이 있습니다.

1. 범위의 추가는 작업의 컬렉션을 포함하는 동작입니다.
2. 조건과 루프는 최고 수준의 작업입니다.
3. `runAfter` 속성을 통한 더 자세한 정보를 표시하는 실행 순서(`dependsOn`를 대체)

2015-08-01-미리 보기 스키마에서 2016-06-01 스키마로 논리 앱을 업그레이드하는 내용은 [아래 업그레이드 섹션을 확인합니다.](#upgrading-to-2016-06-01-schema)

## <a name="1-scopes"></a>1. 범위
이 스키마에서 가장 큰 변화 중 하나는 범위와 기능을 중첩 작업에 서로 추가하는 것입니다.  일련의 작업을 함께 그룹화하거나 서로에게서 작업을 중첩해야 할 경우에 유용합니다(예: 조건이 다른 조건을 포함할 수 있음).  범위 구문에 대한 자세한 내용은 [여기](app-service-logic-loops-and-scopes.md)에 있지만 간단한 범위 예제는 아래에서 찾을 수 있습니다.

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. 조건 및 루프 변경
이전 버전의 스키마에서 조건과 루프는 단일 작업과 관련된 매개 변수였습니다.  이러한 제한 사항은 이 스키마에 적용되지 않고 이제 조건과 루프가 작업의 유형으로 표시됩니다.  [이 문서에서](app-service-logic-loops-and-scopes.md)자세한 정보를 찾을 수 있으며 조건 작업의 간단한 예는 아래와 같습니다.

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. RunAfter 속성
새 `runAfter` 속성은 `dependsOn`를 교체하여 실행 순서가 더 정확하도록 도움을 줍니다.  `dependsOn` 은 "작업을 실행하고 성공했습니다"와 동의어이지만 이전 작업을 성공하거나 실패 또는 건너뛴 경우 여러 번 동작을 실행해야 합니다.  `runAfter` 는 해당 유연성을 허용합니다.  이후에 실행할 모든 작업의 이름을 지정하는 개체이며 트리거하도록 허용 가능한 상태의 배열을 정의합니다.  예를 들어 A단계가 성공하고 B단계가 성공하거나 실패한 후에 실행하려는 경우 다음 `runAfter` 속성을 생성합니다.

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>2016-06-01 스키마로 업그레이드
새롭게 2016-06-01 스키마로 업그레이드하려면 몇 가지 단계가 필요합니다.  스키마의 변경 내용에 대한 자세한 내용은 [이 문서에서](app-service-logic-schema-2016-04-01.md)찾을 수 있습니다.  업그레이드 프로세스에는 업그레이드 스크립트를 실행하고 새 논리 앱으로 저장하며 필요한 경우 잠재적으로 기존 논리 앱을 덮어쓰는 작업이 포함됩니다.

1. 현재 논리 앱을 엽니다.
2. 도구 모음에서 **스키마 업데이트** 단추를 클릭합니다.
   
    ![][1]
   
    업그레이드된 정의가 반환됩니다.  필요한 경우 리소스 정의를 복사하고 붙여 넣을 수 있지만 **다른 이름으로 저장** 단추를 사용하여 모든 연결 참조가 업그레이드된 논리 앱에서 유효하도록 하는 것이 **가장 좋습니다**.
3. 업그레이드 블레이드의 도구 모음에서 **다른 이름으로 저장** 단추를 클릭합니다.
4. 이름 및 논리 앱 상태를 입력하고 **만들기** 를 클릭하여 업그레이드 논리 앱을 배포합니다.
5. 업그레이드된 논리 앱이 예상대로 작동하는지 확인합니다.
   
   > [!NOTE]
   > 수동 또는 요청 트리거를 사용하는 경우 콜백 URL이 새 논리 앱에서 변경됩니다.  새 URL을 사용하여 종단 간에 작동하는지를 확인하고 기존 논리 앱에 복제하여 이전 URL을 유지할 수 있습니다.
   > 
   > 
6. *선택 사항* 도구 모음에서 **복제** 단추를 사용하여(위의 그림에서 **스키마 업데이트** 아이콘에 인접함) 새 스키마 버전으로 이전 논리 앱을 덮어씁니다.  동일한 리소스 ID를 유지하거나 논리 앱의 트리거 URL을 요청하려는 경우에만 필요합니다.

### <a name="upgrade-tool-notes"></a>업그레이드 도구 정보
#### <a name="condition-mapping"></a>조건 매핑
도구는 업그레이드된 정의의 범위에서 true 및 false 분기 작업을 함께 그룹화하는 최고의 방법입니다.  특히 디자이너 패턴 `@equals(actions('a').status, 'Skipped')`은 `else` 작업으로 표시해야 합니다.  그러나 이 도구가 인식할 수 없는 패턴을 검색하는 경우 true와 false 분기 모두에 잠재적으로 별도의 조건을 만들게 됩니다.  작업은 필요한 경우 게시 업그레이드를 다시 매핑하는 것일 수 있습니다.

#### <a name="foreach-with-condition"></a>ForEach 조건
필터 작업을 사용하여 새 스키마에서 항목당 조건이 있는 foreach 루프의 이전 패턴을 복제할 수 있습니다.  업그레이드에서 자동으로 발생해야 합니다.  조건은 (조건과 일치하는 항목의 배열을 반환하는) foreach 루프 이전에 필터 동작이 되고 해당 배열은 foreach 작업으로 전달됩니다.   [이 문서에서](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>리소스 태그
리소스 태그가 업그레이드에서 제거되기 때문에 업그레이드된 워크플로에 다시 설정해야 합니다.

## <a name="other-changes"></a>기타 변경 내용
### <a name="manual-trigger-renamed-to-request-trigger"></a>요청 트리거로 이름을 바꾼 수동 트리거
`manual` 형식이 사용되지 않고 `http` 종류를 사용하여 이름이 `request`로 변경되었습니다.  트리거가 구축하는 데 사용되는 패턴의 종류와 일치합니다.

### <a name="new-filter-action"></a>새 '필터' 작업
큰 배열을 사용하고 더 작은 항목 집합으로 필터링해야 하는 경우 새 '필터' 형식을 사용할 수 있습니다.  배열 및 조건을 허용하고 각 항목에 대한 조건을 평가하며 조건을 충족 하는 항목의 배열을 반환합니다.

### <a name="foreach-and-until-action-restrictions"></a>forEach 및 until 작업 제한
foreach 및 until 루프는 한 번의 작업으로 제한됩니다.

### <a name="trackedproperties-on-actions"></a>동작의 TrackedProperties
작업에는 `runAfter` 및 `type`의 형제인 `trackedProperties`이라는 추가 속성이 있을 수 있습니다.  특정 작업 입력 또는 출력을 지정하는 개체는 워크플로의 일부로 내보낸 Azure 진단 원격 분석에 포함됩니다.  예:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
* [논리 앱 워크플로 정의 사용](app-service-logic-author-definitions.md)
* [논리 앱 배포 템플릿 만들기](app-service-logic-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png



<!--HONumber=Nov16_HO3-->


