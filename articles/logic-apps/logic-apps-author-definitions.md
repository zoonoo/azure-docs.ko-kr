---
title: 논리 앱 JSON 워크플로 정의 만들기, 편집 또는 확장
description: Azure Logic Apps에서 논리 앱의 JSON 워크플로 정의를 작성, 편집 및 확장 하는 방법
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979387"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Azure Logic Apps에서 논리 앱 워크플로 정의에 대 한 JSON 만들기, 편집 또는 확장

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 자동화된 워크플로를 사용하여 엔터프라이즈 통합 솔루션을 만들 때 기본 논리 앱 정의는 해당 설명 및 유효성 검사에 대해 간단하고 선언적 JSON(JavaScript Object Notation)과 함께 [WDL(워크플로 정의 언어) 스키마](../logic-apps/logic-apps-workflow-definition-language.md)를 사용합니다. 이러한 형식 덕분에 코드에 대해 잘 몰라도 논리 앱 정의를 더 쉽게 읽고 이해할 수 있습니다.
논리 앱의 만들기 및 배포를 자동화하려는 경우 논리 앱 정의를 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md) 내 [Azure 리소스](../azure-resource-manager/management/overview.md)로 포함할 수 있습니다.
그런 다음, [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 또는 [Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/)를 사용하여 논리 앱을 생성, 관리 및 배포할 수 있습니다.

JSON에서 논리 앱 정의를 사용하려면 Azure Portal 또는 Visual Studio에서 작업하는 경우 코드 보기 편집기를 열거나 원하는 편집기에로 정의를 복사합니다.
논리 앱을 처음 사용하는 경우 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 검토하세요.

> [!NOTE]
> 논리 앱 정의에서 매개 변수 및 여러 트리거를 정의하는 것처럼 일부 Azure Logic Apps 기능은 Logic Apps 디자이너가 아닌 JSON에서만 사용할 수 있습니다.
> 따라서 이러한 작업의 경우 코드 보기 또는 다른 편집기에서 작업해야 합니다.

## <a name="edit-json---azure-portal"></a>JSON 편집 - Azure Portal

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.
검색 상자에서 “논리 앱”을 찾은 다음, 결과에서 사용자의 논리 앱을 선택합니다.

3. 논리 앱 메뉴의 **개발 도구**에서 **Logic Apps 코드 보기**를 선택합니다.

   코드 보기 편집기가 열리고 논리 앱 정의가 JSON 형식으로 표시됩니다.

## <a name="edit-json---visual-studio"></a>JSON 편집 - Visual Studio

Visual Studio에서 논리 앱 정의를 작업하려면 [필요한 도구가 설치되었는지](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) 확인해야 합니다.
Visual Studio를 사용하여 논리 앱을 만들려면 [빠른 시작: Azure Logic Apps - Visual Studio를 사용하여 작업 및 프로세스 자동화](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)를 검토합니다.

Visual Studio에서 생성되어 직접 Azure Portal에서 또는 Visual Studio에서 Azure Resource Manager 프로젝트로 배포된 논리 앱을 열 수 있습니다.

1. Visual Studio 솔루션 또는 논리 앱이 포함된 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) 프로젝트를 엽니다.

2. 기본적으로 [Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)에 표시되는 **LogicApp.json**이라 명명된 논리 앱의 정의를 찾아서 엽니다.
배포를 위해 이 템플릿을 다른 환경으로 사용 및 사용자 지정할 수 있습니다.

3. 논리 앱 정의 및 템플릿에 대한 바로 가기 메뉴를 엽니다.
**Logic Apps 디자이너를 사용하여 열기**를 선택합니다.

   ![Visual Studio 솔루션에서 논리 앱 열기](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

4. 디자이너의 맨 아래에서 **코드 보기**를 선택합니다.

   코드 보기 편집기가 열리고 논리 앱 정의가 JSON 형식으로 표시됩니다.

5. 디자이너 보기로 돌아가려면 코드 보기 편집기의 맨 아래에서 **디자인**을 선택합니다.

## <a name="parameters"></a>매개 변수

배포 수명 주기는 일반적으로 개발, 테스트, 스테이징 및 프로덕션에 대해 서로 다른 환경을 포함 합니다. 하드 코딩 하지 않고 논리 앱 전체에서 다시 사용 하거나 배포 요구 사항에 따라 달라 지는 값이 있는 경우 논리 앱 배포를 자동화할 수 있도록 워크플로 정의에 대 한 [Azure Resource Manager 템플릿을](../azure-resource-manager/management/overview.md) 만들 수 있습니다.

이러한 값에 대 한 매개 변수를 매개 *변수화*하거나 정의 하 고 사용 하는 일반적인 단계를 수행 합니다. 그런 다음 해당 값을 템플릿에 전달 하는 별도의 매개 변수 파일에 값을 제공할 수 있습니다. 이렇게 하면 논리 앱을 업데이트 하 고 다시 배포 하지 않고도 이러한 값을 보다 쉽게 변경할 수 있습니다. 자세한 내용은 [개요: Azure Resource Manager 템플릿을 사용 하 여 논리 앱에 대 한 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)를 참조 하세요.

1. 템플릿에서 각각 배포 및 런타임에 사용할 값을 허용 하기 위한 템플릿 매개 변수 및 워크플로 정의 매개 변수를 정의 합니다.

   템플릿 매개 변수는 워크플로 정의 외부에 있는 매개 변수 섹션에서 정의 되며 워크플로 정의 매개 변수는 워크플로 정의 내에 있는 매개 변수 섹션에서 정의 됩니다.

1. 하드 코딩 된 값을 이러한 매개 변수를 참조 하는 식으로 바꿉니다. 템플릿 식에서는 워크플로 정의 식과 다른 구문을 사용 합니다.

   런타임에 평가 되는 워크플로 정의 식 내에서 배포 시 평가 되는 템플릿 식을 사용 하지 않고 코드를 복잡 하 게 하지 않도록 합니다. 워크플로 정의 외부에서 템플릿 식만 사용 합니다. 워크플로 정의 내에서 워크플로 정의 식만 사용 합니다.

   워크플로 정의 매개 변수에 대 한 값을 지정할 때 워크플로 정의 외부에 있지만 논리 앱에 대 한 리소스 정의 내에 있는 매개 변수 섹션을 사용 하 여 템플릿 매개 변수를 참조할 수 있습니다. 이렇게 하면 템플릿 매개 변수 값을 워크플로 정의 매개 변수에 전달할 수 있습니다.

1. 매개 변수의 값을 별도의 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md) 에 저장 하 고 해당 파일을 배포에 포함 합니다.

## <a name="process-strings-with-functions"></a>함수로 문자열 처리

Logic Apps에는 문자열 작업을 위한 다양한 함수가 있습니다.
예를 들어 주문에 포함된 회사 이름을 다른 시스템에 전달한다고 가정하겠습니다.
하지만 문자 인코딩의 적절한 처리에 대한 확신이 없습니다.
문자열에 base64 인코딩을 수행할 수 있지만, URL의 이스케이프를 방지하기 위해 여러 문자를 바꿀 수 있습니다. 또한 앞쪽의 5자는 사용하지 않기 때문에 회사 이름의 하위 문자열만 필요합니다.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

아래 단계에서는 이 예제가 내부에서 외부로 작업하면서 이 문자열을 처리하는 방법을 설명합니다.

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. 총 문자 수를 얻기 위해 회사 이름의 [`length()`](../logic-apps/logic-apps-workflow-definition-language.md)를 구합니다.

2. 짧은 문자열을 구하려면 `5`를 뺍니다.

3. 이제 [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)을 구합니다.
인덱스 `5`에서 시작하여 문자열의 나머지로 이동합니다.

4. 이 하위 문자열을 [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) 문자열로 변환합니다.

5. 이제 모든 `+` 문자를 `-` 문자로 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)합니다.

6. 마지막으로 모든 `/` 문자를 `_` 문자로 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)합니다.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>목록 항목을 속성 값에 매핑한 다음, 맵을 매개 변수로 사용

속성 값을 기반으로 다른 결과를 얻으려면, 각 속성 값을 결과와 일치시키는 맵을 만든 다음, 이 맵을 매개 변수로 사용합니다.

예를 들어,이 워크플로는 일부 범주를 매개 변수로 정의하고 이 범주를 특정 URL과 일치시키는 맵을 정의합니다.
우선, 워크플로에서 문서의 목록을 가져옵니다. 그런 다음, 워크플로에서 맵을 사용하여 각 문서의 범주와 일치하는 URL을 찾습니다.

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) 함수는 해당 범주가 알려진 정의된 범주와 일치하는지 확인합니다.

*   일치하는 범주를 확인한 후에는 대괄호`parameters[...]`를 사용하여 맵에서 항목을 끌어옵니다.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Date 함수로 데이터 가져오기

*트리거*를 기본적으로 지원하지 않는 데이터 소스에서 데이터를 가져오려면, 시간과 날짜를 대신 처리하기 위해 Date 함수를 사용합니다.
예를 들어 이 식은 내부에서 외부로 작업하면서 워크플로 단계가 얼마나 소요되는지를 구합니다.

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. `order` 작업에서 `startTime`을 추출합니다.
2. `utcNow()`로 현재 시간을 구합니다.
3. 1초를 뺍니다.

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   `minutes` 또는 `hours`와 같은 다른 시간 단위를 사용할 수 있습니다.

3. 이제 두 값을 비교할 수 있습니다.

   첫 번째 값이 두 번째 값보다 작은 경우에는 명령이 처음으로 배치된 후 1초 이상의 시간이 경과된 것입니다.

날짜 형식을 지정하기 위해 문자열 포맷터를 사용할 수 있습니다. 예를 들어 RFC1123을 얻으려면 [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)를 사용합니다.
[날짜 형식 지정](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 자세히 알아보세요.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Azure Logic Apps에 대한 워크플로 정의 언어 스키마](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 자세히 알아보기
* [Azure Logic Apps의 워크플로 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md)에 대해 자세히 알아보기
