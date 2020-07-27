---
title: 특정 API 커넥터에 대 한 연결 차단
description: Azure Logic Apps에서 API 연결 만들기 및 사용 제한
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: cccc45f182f3ae826440df8bc163080b82226c9f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172087"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Azure Logic Apps 커넥터에서 만든 연결 차단

조직에서 Azure Logic Apps의 커넥터를 사용 하 여 제한 된 리소스 또는 승인 되지 않은 리소스에 대 한 연결을 허용 하지 않는 경우 논리 앱 워크플로에서 이러한 연결을 만들고 사용 하는 기능을 차단할 수 있습니다. [Azure Policy](../governance/policy/overview.md)를 사용 하 여 차단 하려는 커넥터에 대 한 연결을 만들거나 사용 하지 못하도록 하는 [정책을](../governance/policy/overview.md#policy-definition) 정의 하 고 적용할 수 있습니다. 예를 들어, 보안상의 이유로 특정 소셜 미디어 플랫폼 또는 다른 서비스와 시스템에 대 한 연결을 차단할 수 있습니다.

이 항목에서는 Azure Portal를 사용 하 여 특정 연결을 차단 하는 정책을 설정 하는 방법을 보여 주지만 Azure REST API, Azure PowerShell, Azure CLI 및 Azure Resource Manager 템플릿을 통해 다른 방법으로 정책 정의를 만들 수 있습니다. 자세한 내용은 [자습서: 규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 시작 하기 전에 [무료 Azure 계정을 만듭니다](https://azure.microsoft.com/free/) .

* 차단 하려는 커넥터의 참조 ID입니다. 자세한 내용은 [커넥터 참조 ID 찾기](#connector-reference-ID)를 참조 하세요.

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>커넥터 참조 ID 찾기

차단할 연결을 포함 하는 논리 앱이 이미 있는 경우 [Azure Portal 단계](#connector-ID-portal)를 수행 합니다. 액세스 권한이 없는 경우 다음 단계를 수행하세요.

1. [Logic Apps 커넥터 목록을](/connectors/connector-reference/connector-reference-logicapps-connectors)방문 합니다.

1. 차단할 커넥터에 대 한 참조 페이지를 찾습니다.

   예를 들어, 사용 되지 않는 go Agram 커넥터를 차단 하려는 경우이 페이지로 이동 합니다.

   `https://docs.microsoft.com/connectors/instagram/`

1. 페이지의 URL에서 슬래시 ()를 사용 하지 않고 끝에 커넥터 참조 ID를 복사 하 여 저장 `/` 합니다 (예:) `instagram` .

   나중에 정책 정의를 만들 때이 ID를 정의의 condition 문에서 사용 합니다. 예를 들면 다음과 같습니다.

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴에서 논리 앱 **코드 보기** 를 선택 하 여 논리 앱의 JSON 정의를 볼 수 있습니다.

   !["논리 앱 코드 보기"를 열어 커넥터 ID 찾기](./media/block-connections-connectors/code-view-connector-id.png)

1. `parameters` `$connections` `{connection-name}` 논리 앱의 각 연결에 대 한 개체를 포함 하 고 해당 연결에 대 한 정보를 지정 하는 개체를 포함 하는 개체를 찾습니다.

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   예를 들어,이 커넥터에 대 한 개체를 찾습니다 `instagram` .

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. 차단 하려는 연결의 경우 `id` 다음 형식에 따라 속성 및 값을 찾습니다. 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   예를 들어, 다음은 `id` 인스턴스 연결에 대 한 속성 및 값입니다.

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. `id`속성 값에서 커넥터 참조 ID를 복사 하 고 끝에 저장 합니다 (예:) `instagram` .

   나중에 정책 정의를 만들 때이 ID를 정의의 condition 문에서 사용 합니다. 예를 들면 다음과 같습니다.

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>연결 만들기를 차단 하는 정책 만들기

논리 앱에서 연결 만들기를 모두 차단 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 포털 검색 상자에를 입력 하 `policy` 고 **정책**을 선택 합니다.

   ![Azure Portal에서 "정책"을 찾아 선택 합니다.](./media/block-connections-connectors/find-select-azure-policy.png)

1. **정책** 메뉴의 **제작**에서 **정의**  >  **+ 정책 정의**를 선택 합니다.

   !["정의" > "+ 정책 정의"를 선택 합니다.](./media/block-connections-connectors/add-new-policy-definition.png)

1. **정책 정의**에서 예제에 설명 된 속성에 따라 정책 정의에 대 한 정보를 제공 합니다.

   ![정책 정의 속성](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **정의 위치** | 예 | <*Azure-subscription-name*> | 정책 정의에 사용할 Azure 구독입니다. <p><p>1. 구독을 찾으려면 줄임표 (**...**) 단추를 선택 합니다. <br>2. **구독** 목록에서 구독을 찾아 선택 합니다. <br>3. 완료 되 면 **선택**을 선택 합니다. |
   | **Name** | 예 | <*정책-정의-이름*> | 정책 정의에 사용할 이름입니다. |
   | **설명** | 예 | <*정책-정의-이름*> | 정책 정의에 대 한 설명입니다. |
   | **범주** | 예 | **논리 앱** | 정책 정의에 대 한 기존 범주나 새 범주의 이름 |
   | **정책 적용** | 예 | **Enabled** | 이 설정은 작업을 저장할 때 정책 정의를 사용할지 여부를 지정 합니다. |
   ||||

1. **정책 규칙**에서 JSON 편집 상자는 정책 정의 템플릿으로 미리 채워집니다. 이 템플릿을 아래 표에 설명 된 속성을 기반으로 하는 [정책 정의](../governance/policy/concepts/definition-structure.md) 로 바꾸고 다음 구문을 따라 바꿉니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 속성 | Value | Description |
   |----------|-------|-------------|
   | `mode` | `All` | 정책이 평가 하는 리소스 종류를 결정 하는 모드입니다. <p><p>이 시나리오에서는 `mode` `All` 정책을 Azure 리소스 그룹, 구독 및 모든 리소스 유형에 적용 하는로 설정 합니다. <p><p>자세한 내용은 [정책 정의 구조-모드](../governance/policy/concepts/definition-structure.md#mode)를 참조 하세요. |
   | `if` | `{condition-to-evaluate}` | 정책 규칙을 적용할 시기를 결정 하는 조건입니다. <p><p>이 시나리오에서는 `{condition-to-evaluate}` `api.id` `Microsoft.Web/connections/api.id` `*managedApis/{connector-name}` 와일드 카드 (*) 값을 지정 하는에 일치 하는 값이 있는지 여부를 확인 합니다. <p><p>자세한 내용은 [정책 정의 구조-정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조 하세요. |
   | `field` | `Microsoft.Web/connections/api.id` | `field`조건과 비교할 값입니다. <p><p>이 시나리오에서는 `field` [*별칭*](../governance/policy/concepts/definition-structure.md#aliases)을 사용 하 여 `Microsoft.Web/connections/api.id` 커넥터 속성의 값에 액세스 `api.id` 합니다. |
   | `like` | `*managedApis/{connector-name}` | 값을 비교 하는 데 사용할 논리 연산자 및 값입니다. `field` <p><p>이 시나리오에서 `like` 연산자와 와일드 카드 (*) 문자는 모두 지역에 관계 없이 작동 하는지 확인 하 고, 문자열은 `*managedApis/{connector-name}` 차단 하려는 커넥터의 ID와 일치 하는 값입니다 `{connector-name}` . <p><p>예를 들어 소셜 미디어 플랫폼 또는 데이터베이스에 대 한 연결 만들기를 차단 하려는 경우를 가정해 보겠습니다. <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server 또는 Azure SQL:`sql` <p><p>이러한 커넥터 Id를 찾으려면이 항목의 앞부분에 나오는 [커넥터 참조 ID 찾기](#connector-reference-ID) 를 참조 하세요. |
   | `then` | `{effect-to-apply}` | 조건이 충족 될 때 적용할 효과입니다. `if` <p><p>이 시나리오에서는 `{effect-to-apply}` 정책을 준수 하지 않는 요청이 나 작업을 차단 하 고 실패 하는 것입니다. <p><p>자세한 내용은 [정책 정의 구조-정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조 하세요. |
   | `effect` | `deny` | 는 `effect` 지정 된 연결을 만드는 요청을 차단 하는 것입니다. <p><p>자세한 내용은 [Azure Policy 효과 이해-거부](../governance/policy/concepts/effects.md#deny)를 참조 하세요. |
   ||||

   예를 들어, 사용자가이 커넥터를 사용 하 여 연결 만들기를 차단 하려는 경우를 가정해 보겠습니다. 사용할 수 있는 정책 정의는 다음과 같습니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   **정책 규칙** 상자가 표시 되는 방법은 다음과 같습니다.

   ![정책 정의에 대 한 규칙](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   여러 커넥터의 경우 다음과 같은 조건을 추가할 수 있습니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. 완료되면 **저장**을 선택합니다. 정책 정의를 저장 한 후 Azure Policy는 정책 정의에 더 많은 속성 값을 생성 하 고 추가 합니다.

1. 그런 다음 정책을 적용 하려는 정책 정의를 할당 하려면 [정책 할당을 만듭니다](#create-policy-assignment).

Azure 정책 정의에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [정책 구조 정의](../governance/policy/concepts/definition-structure.md)
* [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
* [Azure Logic Apps에 대한 Azure Policy 기본 제공 정책 정의](../logic-apps/policy-samples.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>연결을 사용 하 여 차단 하는 정책 만들기

논리 앱 내에서 연결을 만들면 해당 연결은 별도의 Azure 리소스로 존재 합니다. 논리 앱만 삭제 하면 연결이 자동으로 삭제 되지 않고 삭제 될 때까지 계속 존재 합니다. 연결이 이미 존재 하거나 논리 앱 외부에서 사용할 연결을 만들어야 하는 시나리오가 있을 수 있습니다. 제한 된 연결이 나 승인 되지 않은 연결을 포함 하는 논리 앱을 저장할 수 없도록 하는 정책을 만들어 논리 앱에서 기존 연결을 사용 하는 기능을 차단할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 포털 검색 상자에를 입력 하 `policy` 고 **정책**을 선택 합니다.

   ![Azure Portal에서 "정책"을 찾아 선택 합니다.](./media/block-connections-connectors/find-select-azure-policy.png)

1. **정책** 메뉴의 **제작**에서 **정의**  >  **+ 정책 정의**를 선택 합니다.

   !["정의" > "+ 정책 정의"를 선택 합니다.](./media/block-connections-connectors/add-new-policy-definition.png)

1. **정책 정의**에서 예제에 설명 된 속성을 기반으로 정책 정의에 대 한 정보를 제공 하 고, 예를 들어, 다음과 같이 명령이 설정 된 agram을 사용 하 여 계속 합니다.

   ![정책 정의 속성](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **정의 위치** | 예 | <*Azure-subscription-name*> | 정책 정의에 사용할 Azure 구독입니다. <p><p>1. 구독을 찾으려면 줄임표 (**...**) 단추를 선택 합니다. <br>2. **구독** 목록에서 구독을 찾아 선택 합니다. <br>3. 완료 되 면 **선택**을 선택 합니다. |
   | **Name** | 예 | <*정책-정의-이름*> | 정책 정의에 사용할 이름입니다. |
   | **설명** | 예 | <*정책-정의-이름*> | 정책 정의에 대 한 설명입니다. |
   | **범주** | 예 | **논리 앱** | 정책 정의에 대 한 기존 범주나 새 범주의 이름 |
   | **정책 적용** | 예 | **Enabled** | 이 설정은 작업을 저장할 때 정책 정의를 사용할지 여부를 지정 합니다. |
   ||||

1. **정책 규칙**에서 JSON 편집 상자는 정책 정의 템플릿으로 미리 채워집니다. 이 템플릿을 아래 표에 설명 된 속성을 기반으로 하는 [정책 정의](../governance/policy/concepts/definition-structure.md) 로 바꾸고 다음 구문을 따라 바꿉니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | 속성 | Value | Description |
   |----------|-------|-------------|
   | `mode` | `All` | 정책이 평가 하는 리소스 종류를 결정 하는 모드입니다. <p><p>이 시나리오에서는 `mode` `All` 정책을 Azure 리소스 그룹, 구독 및 모든 리소스 유형에 적용 하는로 설정 합니다. <p><p>자세한 내용은 [정책 정의 구조-모드](../governance/policy/concepts/definition-structure.md#mode)를 참조 하세요. |
   | `if` | `{condition-to-evaluate}` | 정책 규칙을 적용할 시기를 결정 하는 조건입니다. <p><p>이 시나리오에서는의 `{condition-to-evaluate}` 문자열 출력에 문자열이 포함 되어 있는지 여부를 확인 합니다 `[string(field('Microsoft.Logic/workflows/parameters'))]` `{connector-name}` . <p><p>자세한 내용은 [정책 정의 구조-정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조 하세요. |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | 조건과 비교할 값입니다. <p><p>이 시나리오에서는 `value` `[string(field('Microsoft.Logic/workflows/parameters'))]` `$connectors` 개체 내의 개체를 문자열로 변환 하는의 문자열 출력입니다 `Microsoft.Logic/workflows/parameters` . |
   | `contains` | `{connector-name}` | 속성과 비교 하는 데 사용할 논리 연산자 및 값입니다. `value` <p><p>이 시나리오에서 연산자는 `contains` 가 표시 되는 위치에 관계 없이 작동 하도록 합니다 `{connector-name}` . 여기서 문자열는 `{connector-name}` 제한 하거나 차단 하려는 커넥터의 ID입니다. <p><p>예를 들어 소셜 미디어 플랫폼 또는 데이터베이스에 대 한 연결 사용을 차단 하려는 경우를 가정해 보겠습니다. <p><p>Twitter`twitter` <br>Instagram`instagram` <br>Facebook`facebook` <br>Pinterest`pinterest` <br>-SQL Server 또는 Azure SQL:`sql` <p><p>이러한 커넥터 Id를 찾으려면이 항목의 앞부분에 나오는 [커넥터 참조 ID 찾기](#connector-reference-ID) 를 참조 하세요. |
   | `then` | `{effect-to-apply}` | 조건이 충족 될 때 적용할 효과입니다. `if` <p><p>이 시나리오에서은 `{effect-to-apply}` 요청을 차단 하 고 실패 하는 것입니다 .는 정책을 준수 하지 않습니다. <p><p>자세한 내용은 [정책 정의 구조-정책 규칙](../governance/policy/concepts/definition-structure.md#policy-rule)을 참조 하세요. |
   | `effect` | `deny` | 는 `effect` 지정 된 `deny` 연결을 사용 하는 논리 앱을 저장 하는 요청을 또는 차단 합니다. <p><p>자세한 내용은 [Azure Policy 효과 이해-거부](../governance/policy/concepts/effects.md#deny)를 참조 하세요. |
   ||||

   예를 들어, 인스턴스를 사용 하는 논리 앱의 저장을 차단 하려는 경우를 예로 들어 보겠습니다. 사용할 수 있는 정책 정의는 다음과 같습니다.

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   **정책 규칙** 상자가 표시 되는 방법은 다음과 같습니다.

   ![정책 정의에 대 한 규칙](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. 완료되면 **저장**을 선택합니다. 정책 정의를 저장 한 후 Azure Policy는 정책 정의에 더 많은 속성 값을 생성 하 고 추가 합니다.

1. 그런 다음 정책을 적용 하려는 정책 정의를 할당 하려면 [정책 할당을 만듭니다](#create-policy-assignment).

Azure 정책 정의에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [정책 구조 정의](../governance/policy/concepts/definition-structure.md)
* [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
* [Azure Logic Apps에 대한 Azure Policy 기본 제공 정책 정의](../logic-apps/policy-samples.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>정책 할당 만들기

그런 다음 정책을 적용 하려는 정책 정의 (예: 단일 리소스 그룹, 여러 리소스 그룹, Azure Active Directory (Azure AD) 테 넌 트 또는 Azure 구독)를 할당 해야 합니다. 이 작업의 경우 다음 단계에 따라 정책 할당을 만듭니다.

1. 로그 아웃 한 경우 [Azure Portal](https://portal.azure.com)다시 로그인 합니다. 포털 검색 상자에를 입력 하 `policy` 고 **정책**을 선택 합니다.

   ![Azure Portal에서 "정책"을 찾아 선택 합니다.](./media/block-connections-connectors/find-select-azure-policy.png)

1. **정책** 메뉴의 **제작**에서 **할당**  >  **정책 할당**을 선택 합니다.

   !["할당" > "할당"을 선택 합니다.](./media/block-connections-connectors/add-new-policy-assignment.png)

1. **기본**에서 정책 할당에 대 한 다음 정보를 제공 합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **범위** | 예 | 정책 할당을 적용 하려는 리소스입니다. <p><p>1. **범위** 상자 옆의 줄임표 (**...**) 단추를 선택 합니다. <br>2. **구독** 목록에서 Azure 구독을 선택 합니다. <br>3. 필요에 따라 **리소스 그룹** 목록에서 리소스 그룹을 선택 합니다. <br>4. 완료 되 면 **선택**을 선택 합니다. |
   | **제외 항목** | 예 | 정책 할당에서 제외할 모든 Azure 리소스입니다. <p><p>1. **제외** 상자 옆의 줄임표 (**...**) 단추를 선택 합니다. <br>2. **리소스** 목록에서 **선택한 범위에 추가**> 리소스를 선택 합니다. <br>3. 완료 되 면 **저장**을 선택 합니다. |
   | **정책 정의** | 예 | 할당 하 고 적용 하려는 정책 정의의 이름입니다. 이 예에서는 "실행 중인 Agram 연결 차단" 예제를 사용 하 여 계속 진행 합니다. <p><p>1. **정책 정의** 상자 옆에 있는 줄임표 (**...**) 단추를 선택 합니다. <br>2. **형식** 필터 또는 **검색** 상자를 사용 하 여 정책 정의를 찾아 선택 합니다. <br>3. 완료 되 면 **선택**을 선택 합니다. |
   | **할당 이름** | 예 | 정책 정의와 다른 경우 정책 할당에 사용할 이름입니다. |
   | **할당 ID** | 예 | 정책 할당에 대해 자동으로 생성 된 ID입니다. |
   | **설명** | 예 | 정책 할당에 대 한 설명입니다. |
   | **정책 적용** | 예 | 정책 할당을 사용 하거나 사용 하지 않도록 설정 하는 설정 |
   | **할당자** | 예 | 정책 할당을 만들고 적용 한 사용자의 이름입니다. |
   ||||

   예를 들어, 다음을 사용 하 여 Azure 리소스 그룹에 정책을 할당 합니다. 예:

   ![정책 할당 속성](./media/block-connections-connectors/policy-assignment-basics.png)

1. 완료되면 **검토 + 만들기**를 선택합니다.

   정책을 만든 후 정책을 적용 하려면 최대 15 분 정도 기다려야 할 수 있습니다. 변경 내용의 지연 효과도 달라질 수도 있습니다.

1. 정책이 적용 된 후 [정책을 테스트할](#test-policy)수 있습니다.

자세한 내용은 [빠른 시작: 규정 비준수 리소스를 식별 하는 정책 할당 만들기](../governance/policy/assign-policy-portal.md)를 참조 하세요.

<a name="test-policy"></a>

## <a name="test-the-policy"></a>정책 테스트

정책을 시도 하려면 논리 앱 디자이너에서 현재 제한 된 커넥터를 사용 하 여 연결 만들기를 시작 합니다. 사용 중인 Agram 예를 사용 하 여 작업을 수행 하면 논리 앱에서 연결을 만들지 못해이 오류가 발생 합니다.

![적용 된 정책으로 인 한 연결 실패](./media/block-connections-connectors/connection-failure-message.png)

메시지에는 다음 정보가 포함 됩니다.

| Description | 콘텐츠 |
|-------------|---------|
| 실패 이유 | `"Resource 'instagram' was disallowed by policy."` |
| 할당 이름 | `"Block Instagram connections"` |
| 할당 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| 정책 정의 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>다음 단계

* [Azure Policy](../governance/policy/overview.md) 에 대 한 자세한 정보
