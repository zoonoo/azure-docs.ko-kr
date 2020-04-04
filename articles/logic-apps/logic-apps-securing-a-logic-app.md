---
title: 액세스 및 데이터 보안
description: 입력, 출력, 요청 기반 트리거, 실행 기록, 관리 작업 및 Azure Logic Apps의 다른 리소스에 대한 액세스에 대한 보안 액세스
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 4fc4960eb3af8a3d3c9902c9b24505bb5610b709
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657169"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure 논리 앱의 보안 액세스 및 데이터

Azure Logic Apps에서 액세스를 제어하고 데이터를 보호하려면 다음 영역에서 보안을 설정할 수 있습니다.

* [요청 기반 트리거에 대한 액세스](#secure-triggers)
* [논리 앱 작업에 대한 액세스](#secure-operations)
* [기록 입력 및 출력 실행에 대한 액세스](#secure-run-history)
* [매개 변수 입력에 대한 액세스](#secure-action-parameters)
* [논리 앱에서 호출된 서비스 및 시스템에 대한 액세스](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>요청 기반 트리거에 대한 액세스

논리 앱에서 요청 [또는](../connectors/connectors-native-reqres.md) [Webhook](../connectors/connectors-native-webhook.md) 트리거와 같은 수신 호출 또는 요청을 받는 요청 기반 트리거를 사용하는 경우 권한이 부여된 클라이언트만 논리 앱을 호출할 수 있도록 액세스를 제한할 수 있습니다. 논리 앱에서 수신된 모든 요청은 이전에 SSL(보안 소켓 계층), 프로토콜이라고 하는 전송 계층 보안(TLS)으로 암호화되고 보호됩니다.

다음은 이 트리거 유형에 대한 액세스를 보호하는 데 도움이 되는 옵션입니다.

* [공유 액세스 서명 생성](#sas)
* [인바운드 IP 주소 제한](#restrict-inbound-ip-addresses)
* [Azure Active Directory OAuth 또는 기타 보안 추가](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>공유 액세스 서명 생성(SAS)

논리 앱의 모든 요청 끝점에는 다음 형식다음에 오는 끝점의 URL에 [SAS(공유 액세스 서명)가](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) 있습니다.

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

각 URL에는 `sp` `sv`이 `sig` 테이블에 설명된 대로 의 에서 와 서 및 쿼리 매개 변수가 포함 됩니다.

| 쿼리 매개 변수 | 설명 |
|-----------------|-------------|
| `sp` | 허용되는 HTTP 메서드에 대한 사용 권한을 지정합니다. |
| `sv` | 서명을 생성하는 데 사용할 SAS 버전을 지정합니다. |
| `sig` | 트리거에 대한 액세스를 인증하는 데 사용할 서명을 지정합니다. 이 서명은 모든 URL 경로 및 속성에 비밀 액세스 키가 있는 SHA256 알고리즘을 사용하여 생성됩니다. 노출되거나 게시되지 않은 이 키는 암호화된 채 로직 앱과 함께 저장됩니다. 논리 앱은 암호 키로 만들어진 유효한 서명을 포함하는 트리거에 권한을 부여합니다. |
|||

SAS를 통해 액세스 보안에 대한 자세한 내용은 이 항목의 다음 섹션을 참조하십시오.

* [액세스 키 다시 생성](#access-keys)
* [만료 콜백 URL 만들기](#expiring-urls)
* [주 또는 보조 키를 사용하여 URL 만들기](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>액세스 키 다시 생성

언제든지 새 보안 액세스 키를 생성하려면 Azure REST API 또는 Azure 포털을 사용합니다. 이전 키를 사용하는 이전에 생성된 모든 URL은 무효화되며 더 이상 논리 앱을 트리거할 수 있는 권한이 없습니다. 재생성 후에 검색하는 URL은 새 액세스 키로 서명됩니다.

1. Azure [포털에서](https://portal.azure.com)재생할 키가 있는 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **액세스 키**를 선택합니다.

1. 다시 생성할 키를 선택하고 프로세스를 완료합니다.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>만료 콜백 URL 만들기

요청 기반 트리거의 끝점 URL을 다른 당사자와 공유하는 경우 특정 키를 사용하고 만료 날짜가 있는 콜백 URL을 생성할 수 있습니다. 이렇게 하면 키를 원활하게 롤링하거나 특정 기간에 따라 논리 앱을 트리거하는 액세스를 제한할 수 있습니다. URL의 만료 날짜를 지정하려면 [논리 앱 REST API를](https://docs.microsoft.com/rest/api/logic/workflowtriggers)사용합니다.

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

본문에 JSON `NotAfter`날짜 문자열을 사용 하 여 속성을 포함 합니다. 이 속성은 `NotAfter` 날짜 및 시간까지만 유효한 콜백 URL을 반환합니다.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>기본 또는 보조 비밀 키로 URL 만들기

요청 기반 트리거에 대한 콜백 URL을 생성하거나 나열할 때 URL 서명에 사용할 키를 지정할 수 있습니다. 특정 키로 서명된 URL을 생성하려면 [Logic Apps REST API를](https://docs.microsoft.com/rest/api/logic/workflowtriggers)사용합니다.

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

본문에서는 속성 `KeyType`을 `Primary` 또는 `Secondary`로서 포함합니다. 이 속성은 지정된 보안 키에 의해 서명 된 URL을 반환 합니다.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>인바운드 IP 주소 제한

SAS(공유 액세스 서명)와 함께 논리 앱을 호출할 수 있는 클라이언트를 구체적으로 제한할 수 있습니다. 예를 들어 Azure API Management를 사용하여 요청 끝점을 관리하는 경우 논리 앱이 API 관리 인스턴스의 IP 주소에서만 요청을 수락하도록 제한할 수 있습니다.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Azure 포털에서 인바운드 IP 범위 제한

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세스 제어 구성에서** > **인바운드 IP 주소를 허용하고** **특정 IP 범위를 선택합니다.**

1. **트리거에 대한 IP 범위**에서 트리거가 허용하는 IP 주소 범위를 지정합니다.

   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다.

로직 앱이 **허용된 인바운드 IP 주소** 목록에서 중첩된 논리 앱으로만 트리거하도록 하려면 **다른 논리 앱만**선택합니다. 이 옵션은 논리 앱 리소스에 빈 배열을 씁니다. 이렇게 하면 논리 앱 서비스(상위 논리 앱)의 호출만 중첩논리 앱을 트리거할 수 있습니다.

> [!NOTE]
> IP 주소에 관계 없이 Azure REST API 또는 API Management를 통해 `/triggers/<trigger-name>/run`을 사용하여 요청 기반 트리거가 있는 논리 앱을 계속 실행할 수 있습니다. 그러나 이 시나리오에서는 여전히 Azure REST API에 대한 인증이 필요합니다. 모든 이벤트는 Azure 감사 로그에 나타납니다. 그에 따라 액세스 제어 정책을 설정해야 합니다.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿에서 인바운드 IP 범위 제한

Resource [Manager 템플릿을 사용하여 논리 앱에 대한 배포를 자동화하는](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)경우 논리 `accessControl` 앱의 `triggers` 리소스 정의섹션과 함께 IP 범위를 지정할 수 있습니다.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory OAuth 또는 기타 보안 추가

논리 앱에 권한 부여 프로토콜을 추가하려면 [Azure API 관리](../api-management/api-management-key-concepts.md) 서비스를 사용하는 것이 좋습니다. 이 서비스를 사용하면 논리 앱을 API로 노출하고 모든 엔드포인트에 대해 풍부한 모니터링, 보안, 정책 및 설명서를 제공합니다. API 관리는 논리 앱에 대한 공용 또는 개인 끝점을 노출할 수 있습니다. 이 끝점에 대한 액세스를 승인하려면 [Azure Active Directory OAuth,](#azure-active-directory-oauth-authentication) [클라이언트 인증서](#client-certificate-authentication)또는 해당 끝점에 대한 액세스 권한을 부여하기 위한 기타 보안 표준을 사용할 수 있습니다. API Management는 요청을 받으면 논리 앱에 요청을 보내고 필요한 변환 또는 제한을 수행합니다. API 관리만 논리 앱을 트리거할 수 있도록 논리 앱의 인바운드 IP 범위 설정을 사용할 수 있습니다.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>논리 앱 작업에 대한 액세스

특정 사용자 나 그룹만 논리 앱 관리, 편집 및 보기와 같은 특정 작업을 실행하도록 허용할 수 있습니다. 해당 권한을 제어하려면 Azure 구독의 구성원에 사용자 지정또는 기본 제공 역할을 할당할 수 있도록 [AZURE 역할 기반 액세스 제어(RBAC)를](../role-based-access-control/role-assignments-portal.md) 사용합니다.

* [논리 앱 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor): 논리 앱을 관리할 수 있지만 논리 앱에 대한 액세스를 변경할 수는 없습니다.

* [논리 앱 연산자](../role-based-access-control/built-in-roles.md#logic-app-operator): 논리 앱을 읽고, 활성화하고, 비활성화할 수 있지만 편집하거나 업데이트할 수는 없습니다.

다른 사람이 논리 앱을 변경하거나 삭제하지 못하게 하려면 [Azure 리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 사용할 수 있습니다. 이 기능은 다른 사용자가 프로덕션 리소스를 변경하거나 삭제하지 못하도록 합니다.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>기록 데이터 실행에 대한 액세스

논리 앱을 실행하는 동안 전송 계층 보안(TLS) 및 [미 사용 중](../security/fundamentals/encryption-atrest.md)에서 전송 중에 모든 데이터가 [암호화됩니다.](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 논리 앱 실행이 완료되면 각 작업에 대한 상태, 기간, 입력 및 출력과 함께 실행된 단계를 포함하여 해당 실행의 기록을 볼 수 있습니다. 이 풍부한 세부 정보를 통해 논리 앱이 실행된 방법과 발생하는 문제 해결을 시작할 수 있는 위치에 대한 통찰력을 제공합니다.

논리 앱의 실행 기록을 볼 때 Logic Apps는 액세스를 인증한 다음 각 실행에 대한 요청 및 응답에 대한 입력 및 출력에 대한 링크를 제공합니다. 그러나 암호, 암호, 키 또는 기타 중요한 정보를 처리하는 작업의 경우 다른 사용자가 해당 데이터를 보고 액세스하지 못하도록 해야 합니다. 예를 들어 로직 앱에서 HTTP 작업을 인증할 때 사용할 [Azure Key Vault의](../key-vault/key-vault-overview.md) 비밀을 받는 경우 해당 비밀을 보기에서 숨기려고 합니다.

논리 앱의 실행 기록에서 입력 및 출력에 대한 액세스를 제어하려면 다음 옵션이 있습니다.

* [IP 주소 범위별로 액세스를 제한합니다.](#restrict-ip)

  이 옵션을 사용하면 특정 IP 주소 범위의 요청에 따라 기록 실행에 대한 액세스를 보호할 수 있습니다.

* [난독 화를 사용하여 실행 기록에서 데이터를 숨깁니다.](#obfuscate)

  많은 트리거 및 작업에서 논리 앱의 실행 기록에서 입력, 출력 또는 둘 다를 숨길 수 있습니다.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP 주소 범위별 액세스 제한

특정 IP 주소 범위의 요청만 해당 데이터를 볼 수 있도록 논리 앱의 실행 기록에서 입력 및 출력에 대한 액세스를 제한할 수 있습니다. 예를 들어 모든 사용자가 입력 및 출력에 액세스하지 못하도록 차단하려면 `0.0.0.0-0.0.0.0`와 같은 IP 주소 범위를 지정합니다. 관리자 권한이 있는 사용자만 이 제한을 제거할 수 있으므로 논리 앱의 데이터에 "적시에" 액세스할 수 있습니다. Azure 포털을 사용하거나 논리 앱 배포에 사용하는 Azure 리소스 관리자 템플릿에서 제한할 IP 범위를 지정할 수 있습니다.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure 포털에서 IP 범위 제한

1. Azure Portal의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세스 제어 구성에서** > **인바운드 IP 주소를 허용하고** **특정 IP 범위를 선택합니다.**

1. **콘텐츠의 IP 범위**에서 입력 및 출력의 콘텐츠에 액세스할 수 있는 IP 주소 범위를 지정합니다. 

   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다.

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿에서 IP 범위 제한

Resource [Manager 템플릿을 사용하여 논리 앱에 대한 배포를 자동화하는](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)경우 논리 `accessControl` 앱의 `contents` 리소스 정의섹션과 함께 IP 범위를 지정할 수 있습니다.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>난독화를 사용하여 실행 기록에서 데이터 숨기기

대부분의 트리거 및 작업에는 논리 앱의 실행 기록에서 입력, 출력 또는 둘 다를 숨기는 설정이 있습니다. 다음은 이 데이터를 보호하는 데 도움이 되는 이러한 설정을 사용할 때 [검토해야 할](#obfuscation-considerations) 몇 가지 고려 사항입니다.

#### <a name="hide-inputs-and-outputs-in-the-designer"></a>디자이너의 입력 및 출력 숨기기

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

   ![논리 앱 디자이너에서 논리 앱 열기](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 중요한 데이터를 숨길 트리거 또는 작업에서 타원 **(...**) 버튼을 선택한 다음 **설정을**선택합니다.

   ![트리거 또는 작업 설정 열기](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. **보안 입력,** 보안 **출력**또는 둘 다 켜십시오. 완료되면 **완료**를 선택합니다.

   !["안전한 입력" 또는 "안전한 출력" 켜기](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   이제 작업 또는 트리거에 제목 표시줄에 잠금 아이콘이 표시됩니다.

   ![작업 또는 트리거 제목 표시줄에 잠금 아이콘이 표시됩니다.](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   이전 작업의 보안 출력을 나타내는 토큰에도 잠금 아이콘이 표시됩니다. 예를 들어 동적 콘텐츠 목록에서 이러한 출력을 선택하여 작업에서 사용할 경우 해당 토큰에는 잠금 아이콘이 표시됩니다.

   ![보안 출력을 위한 토큰 선택](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 논리 앱을 실행한 후 해당 실행의 기록을 볼 수 있습니다.

   1. 논리 앱의 **개요** 창에서 보려는 실행을 선택합니다.

   1. 논리 **앱 실행** 창에서 검토할 작업을 확장합니다.

      입력과 출력을 모두 가리도록 선택한 경우 해당 값이 숨김으로 표시됩니다.

      ![실행 기록에 숨겨진 입력 및 출력](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="hide-inputs-and-outputs-in-code-view"></a>코드 뷰에서 입력 및 출력 숨기기

기본 트리거 또는 작업 정의에서 다음 `runtimeConfiguration.secureData.properties` 값 중 하나 또는 둘 다로 배열을 추가하거나 업데이트합니다.

* `"inputs"`: 실행 기록의 입력을 보호합니다.
* `"outputs"`: 실행 기록에서 출력을 보호합니다.

다음은 이 데이터를 보호하는 데 도움이 되는 이러한 설정을 사용할 때 [검토해야 할](#obfuscation-considerations) 몇 가지 고려 사항입니다.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>입력 및 출력을 숨길 때 고려해야 할 사항

* 트리거 또는 작업의 입력 또는 출력을 가리면 Logic Apps는 보안 데이터를 Azure Log Analytics로 전송하지 않습니다. 또한 [추적된 속성을](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) 해당 트리거또는 모니터링을 위한 작업에 추가할 수 없습니다.

* [워크플로 기록을 처리하기 위한 Logic Apps API는](https://docs.microsoft.com/rest/api/logic/) 보안 된 출력을 반환 하지 않습니다.

* 입력을 가리거나 출력을 명시적으로 가리는 작업에서 출력을 숨기려면 해당 작업에서 **보안 출력을** 수동으로 켭니다.

* 실행 기록이 해당 데이터를 모호하게 할 것으로 예상되는 다운스트림 작업에서 **보안 입력** 또는 **보안 출력을** 켜야 합니다.

  **보안 출력 설정**

  트리거 또는 동작에서 **보안 출력을** 수동으로 켜면 Logic Apps는 실행 기록에서 이러한 출력을 보호합니다. 다운스트림 작업이 이러한 보안 출력을 입력으로 명시적으로 사용하는 경우 Logic Apps는 실행 기록에서 이 작업의 입력을 숨기지만 작업의 **보안 입력** 설정을 *사용하지 는 않습니다.*

  ![대부분의 작업에 대한 입력 및 다운스트림 영향으로 보호된 출력](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  컴포지션, JSON 및 응답 작업에는 보안 **입력** 설정만 있습니다. 설정이 설정되면 이러한 작업의 출력도 숨겨집니다. 이러한 작업이 업스트림 보안 출력을 입력으로 명시적으로 사용하는 경우 Logic Apps는 이러한 작업의 입력 및 출력을 숨기지만 이러한 작업의 **보안 입력** 설정을 사용하지 *는 않습니다.* 다운스트림 작업이 컴포지션, 구문 분석 JSON 또는 응답 작업의 숨겨진 출력을 입력으로 명시적으로 사용하는 경우 Logic Apps는 *이 다운스트림 작업의 입력 또는 출력을 숨기지 않습니다.*

  ![특정 작업에 다운스트림 에 영향을 미치는 입력으로 안전한 출력](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **보안 입력 설정**

  트리거 또는 동작에서 **보안 입력을** 수동으로 켜면 Logic Apps는 실행 기록에서 이러한 입력을 보호합니다. 다운스트림 작업이 해당 트리거 또는 동작의 가시 출력을 입력으로 명시적으로 사용하는 경우 Logic Apps는 실행 기록에서 이 다운스트림 작업의 입력을 숨기지만 이 작업에서 **Secure 입력을** *사용하도록 설정하지 않으며* 이 작업의 출력을 숨기지 않습니다.

  ![대부분의 작업에 대한 보안 입력 및 다운스트림 영향](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  컴포지션, 구문 분석 JSON 및 응답 작업이 보안 입력이 있는 트리거 또는 동작에서 보이는 출력을 명시적으로 사용하는 경우 Logic Apps는 이러한 작업의 입력 및 출력을 숨기지만 이러한 작업의 **보안 입력** 설정을 사용하지 *는 않습니다.* 다운스트림 작업이 컴포지션, 구문 분석 JSON 또는 응답 작업의 숨겨진 출력을 입력으로 명시적으로 사용하는 경우 Logic Apps는 *이 다운스트림 작업의 입력 또는 출력을 숨기지 않습니다.*

  ![특정 작업에 대한 보안 입력 및 다운스트림 영향](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>매개 변수 입력에 대한 액세스

여러 환경에 배포하는 경우 해당 환경에 따라 달라지는 워크플로 정의의 값을 매개변수화하는 것이 좋습니다. 이렇게 하면 [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/overview.md) 사용하여 논리 앱을 배포하고, 보안 매개 변수를 정의하여 중요한 데이터를 보호하고, [매개 변수 파일을](../azure-resource-manager/templates/parameter-files.md)사용하여 [템플릿의 매개 변수를](../azure-resource-manager/templates/template-parameters.md) 통해 해당 데이터를 별도의 입력으로 전달하여 하드 코딩된 데이터를 방지할 수 있습니다.

예를 들어 [Azure Active Directory OAuth를](#azure-active-directory-oauth-authentication)사용하여 HTTP 작업을 인증하는 경우 인증에 사용되는 클라이언트 ID 및 클라이언트 암호를 허용하는 매개 변수를 정의하고 모호하게 할 수 있습니다. 논리 앱에서 이러한 매개 변수를 `parameters` 정의하려면 논리 앱의 워크플로 정의 섹션과 배포를 위한 리소스 관리자 템플릿을 사용합니다. 논리 앱을 편집하거나 실행 기록을 볼 때 표시하지 않으려는 매개 변수 값을 `securestring` 숨기려면 `secureobject` 또는 입력을 사용하여 매개 변수를 정의하고 필요에 따라 인코딩을 사용합니다. 이 형식의 매개 변수는 리소스 정의와 함께 반환되지 않으며 배포 후 리소스를 볼 때 액세스할 수 없습니다. 런타임 중에 이러한 매개 변수 `@parameters('<parameter-name>')` 값에 액세스하려면 워크플로 정의 내에서 식을 사용합니다. 이 식은 런타임시에만 평가되며 [워크플로 정의 언어에](../logic-apps/logic-apps-workflow-definition-language.md)의해 설명됩니다.

> [!NOTE]
> 요청 헤더 또는 본문에서 매개 변수를 사용하는 경우 논리 앱의 실행 기록 및 나가는 HTTP 요청을 볼 때 해당 매개 변수가 표시될 수 있습니다. 콘텐츠 액세스 정책을 적절하게 설정해야 합니다. [난독화를](#obfuscate) 사용하여 실행 기록의 입력 및 출력을 숨길 수도 있습니다. 권한 부여 헤더는 입력 또는 출력을 통해 볼 수 없습니다. 따라서 권한 부여 헤더에 비밀이 사용되면 해당 비밀을 검색할 수 없습니다.

자세한 내용은 이 항목의 다음 섹션을 참조하십시오.

* [워크플로 정의에서 매개 변수 보호](#secure-parameters-workflow)
* [난독화를 사용하여 실행 기록에서 데이터 숨기기](#obfuscate)

Resource [Manager 템플릿을 사용하여 논리 앱에 대한 배포를 자동화하는](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)경우 및 `secureobject` 형식을 사용하여 배포 `securestring` 시 평가되는 보안 템플릿 매개 [변수를](../azure-resource-manager/templates/template-parameters.md)정의할 수 있습니다. 템플릿 매개 변수를 정의하려면 워크플로 `parameters` 정의 `parameters` 섹션과 별개로 다른 템플릿의 최상위 섹션을 사용합니다. 템플릿 매개 변수에 대한 값을 제공하려면 별도의 [매개 변수 파일을](../azure-resource-manager/templates/parameter-files.md)사용합니다.

예를 들어 암호를 사용하는 경우 배포 시 [Azure Key Vault에서](../key-vault/key-vault-overview.md) 해당 암호를 검색하는 보안 템플릿 매개 변수를 정의하고 사용할 수 있습니다. 그런 다음 매개 변수 파일에서 키 자격 증명 모음 및 비밀을 참조할 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [Azure 키 볼트를 사용하여 배포시 중요한 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)
* [이 항목의 후반부에서 Azure 리소스 관리자 템플릿의 보안 매개 변수](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>워크플로 정의에서 매개 변수 보호

논리 앱의 워크플로 정의에서 중요한 정보를 보호하려면 논리 앱을 저장한 후 이 정보가 표시되지 않도록 보안 매개 변수를 사용합니다. 예를 들어 HTTP 작업이 사용자 이름과 암호를 사용하는 기본 인증이 필요하다고 가정합니다. 워크플로 정의에서 `parameters` 섹션은 형식을 `basicAuthPasswordParam` `basicAuthUsernameParam` 사용하여 및 매개 `securestring` 변수를 정의합니다. 그런 다음 작업 정의는 섹션에서 `authentication` 이러한 매개 변수를 참조합니다.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿의 안전한 매개 변수

논리 앱의 [리소스 관리자 템플릿에는](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) 여러 `parameters` 섹션이 있습니다. 암호, 키, 암호 및 기타 중요한 정보를 보호하려면 또는 `securestring` `secureobject` 형식을 사용하여 템플릿 수준 및 워크플로 정의 수준에서 보안 매개 변수를 정의합니다. 그런 다음 이러한 값을 [Azure Key Vault에](../key-vault/key-vault-overview.md) 저장하고 [매개 변수 파일을](../azure-resource-manager/templates/parameter-files.md) 사용하여 키 자격 증명 모음 및 비밀을 참조할 수 있습니다. 그런 다음 템플릿은 배포 시 해당 정보를 검색합니다. 자세한 내용은 Azure Key Vault 를 [사용하여 배포시 중요한 값 전달을](../azure-resource-manager/templates/key-vault-parameter.md)참조하십시오.

다음은 이 `parameters` 섹션에 대한 자세한 정보입니다.

* 템플릿의 최상위 수준에서 `parameters` 섹션은 *템플릿이 배포할*때 사용하는 값에 대한 매개 변수를 정의합니다. 예를 들어 이러한 값에는 특정 배포 환경에 대한 연결 문자열이 포함될 수 있습니다. 그런 다음 이러한 값을 별도의 [매개 변수 파일에](../azure-resource-manager/templates/parameter-files.md)저장할 수 있으므로 이러한 값을 더 쉽게 변경할 수 있습니다.

* 논리 앱의 리소스 정의 내에서워크플로 정의 `parameters` 외부에서 는 워크플로 정의의 매개 변수에 대한 값을 지정합니다. 이 섹션에서는 템플릿의 매개 변수를 참조하는 템플릿 식을 사용하여 이러한 값을 할당할 수 있습니다. 이러한 식은 배포 시 평가됩니다.

* 워크플로 정의 내에서 `parameters` 섹션은 로직 앱이 런타임에 사용하는 매개 변수를 정의합니다. 그런 다음 런타임에 평가되는 워크플로 정의 식을 사용하여 논리 앱의 워크플로 내에서 이러한 매개 변수를 참조할 수 있습니다.

이 예제 템플릿은 형식을 사용하는 여러 `securestring` 보안 매개 변수 정의가 있습니다.

| 매개 변수 이름 | 설명 |
|----------------|-------------|
| `TemplatePasswordParam` | 암호를 수락한 다음 워크플로 정의의 `basicAuthPasswordParam` 매개 변수로 전달되는 템플릿 매개 변수 |
| `TemplateUsernameParam` | 그런 다음 워크플로 정의의 `basicAuthUserNameParam` 매개 변수로 전달되는 사용자 이름을 허용하는 템플릿 매개 변수 |
| `basicAuthPasswordParam` | HTTP 작업에서 기본 인증에 대 한 암호를 허용 하는 워크플로 정의 매개 변수 |
| `basicAuthUserNameParam` | HTTP 작업에서 기본 인증에 대한 사용자 이름을 허용하는 워크플로 정의 매개 변수 |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>논리 앱에서 호출된 서비스 및 시스템에 대한 액세스

다음은 논리 앱에서 호출 또는 요청을 수신하는 끝점을 보호하는 데 도움이 되는 몇 가지 방법입니다.

* 아웃바운드 요청에 인증을 추가합니다.

  HTTP, HTTP + Swagger 또는 Webhook과 같이 아웃바운드 호출을 수행하는 HTTP 기반 트리거 또는 작업으로 작업할 때 논리 앱에서 전송되는 요청에 인증을 추가할 수 있습니다. 예를 들어 다음과 같은 인증 유형을 사용할 수 있습니다.

  * [기본 인증](#basic-authentication)

  * [클라이언트 인증서 인증](#client-certificate-authentication)

  * [액티브 디렉토리 OAuth 인증](#azure-active-directory-oauth-authentication)

  * [관리 ID 인증](#managed-identity-authentication)
  
  자세한 내용은 이 항목의 [후반부아웃바운드 호출에 대한 인증 추가를](#add-authentication-outbound) 참조하십시오.

* 논리 앱 IP 주소에서 액세스를 제한합니다.

  논리 앱에서 끝점에 대한 모든 호출은 논리 앱의 지역을 기반으로 하는 특정 지정된 IP 주소에서 시작됩니다. 이러한 IP 주소의 요청만 수락하는 필터링을 추가할 수 있습니다. 이러한 IP 주소를 얻으려면 [Azure 논리 앱에 대한 제한 및 구성을](logic-apps-limits-and-config.md#configuration)참조하십시오.

* 온-프레미스 시스템에 대한 연결에 대한 보안을 향상시킵니다.

  Azure Logic Apps는 보다 안전하고 신뢰할 수 있는 온-프레미스 통신을 제공하기 위해 이러한 서비스와의 통합을 제공합니다.

  * 온-프레미스 데이터 게이트웨이

    Azure Logic Apps의 많은 관리형 커넥터는 파일 시스템, SQL, SharePoint 및 DB2와 같은 온-프레미스 시스템에 대한 보안 연결을 용이하게 합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널에서 온-프레미스 원본의 데이터를 보냅니다. 모든 트래픽은 게이트웨이 에이전트에서 안전한 아웃바운드 트래픽으로 시작됩니다. [온-프레미스 데이터 게이트웨이 작동 방식](logic-apps-gateway-install.md#gateway-cloud-service)을 알아보세요.

  * Azure API 관리를 통한 연결

    [Azure API Management](../api-management/api-management-key-concepts.md)는 보안 프록시를 위한 사이트 간 가상 사설망 및 ExpressRoute 통합과 온-프레미스 시스템에 대한 통신 등의 온-프레미스 연결 옵션을 제공합니다. 로직 앱 디자이너의 로직 앱 워크플로에서 온-프레미스 시스템에 빠르게 액세스할 수 있는 API 관리에 의해 노출되는 API를 선택할 수 있습니다.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>아웃바운드 호출에 인증 추가

HTTP 및 HTTPS 끝점은 다양한 종류의 인증을 지원합니다. 이러한 끝점에 액세스하는 아웃바운드 호출 또는 요청을 수행하는 데 사용하는 트리거 또는 작업에 따라 다양한 인증 유형 중에서 선택할 수 있습니다. 논리 앱에서 처리하는 중요한 정보를 보호하려면 보안 매개 변수를 사용하고 필요에 따라 데이터를 인코딩합니다. 매개 변수 사용 및 보안에 대한 자세한 내용은 [매개 변수 입력에 대한 액세스를](#secure-action-parameters)참조하십시오.

> [!NOTE]
> 논리 앱 디자이너에서 **인증** 형식을 지정할 수 있는 일부 트리거 및 작업에 는 인증 속성이 숨질 수 있습니다. 이러한 경우 속성이 트리거 또는 작업에서 **새 매개 변수 추가** 목록을 열고 **인증을**선택합니다. 자세한 내용은 [관리되는 ID로 액세스 인증](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)을 참조하십시오.

| 인증 유형 | 지원 요소 |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API 관리, Azure 앱 서비스, HTTP, HTTP + Swagger, HTTP 웹후크 |
| [클라이언트 인증서](#client-certificate-authentication) | Azure API 관리, Azure 앱 서비스, HTTP, HTTP + Swagger, HTTP 웹후크 |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API 관리, Azure 앱 서비스, Azure 기능, HTTP, HTTP + Swagger, HTTP 웹후크 |
| [원시](#raw-authentication) | Azure API 관리, Azure 앱 서비스, Azure 기능, HTTP, HTTP + Swagger, HTTP 웹후크 |
| [관리되는 ID](#managed-identity-authentication) | Azure API 관리, Azure 앱 서비스, Azure 기능, HTTP, HTTP + Swagger, HTTP 웹후크 |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>기본 인증

[기본](../active-directory-b2c/secure-rest-api.md) 옵션을 사용할 수 있는 경우 다음 속성 값을 지정합니다.

| 속성 (디자이너) | Property(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | Basic | 사용할 인증 유형 |
| **사용자 이름** | `username` | 예 | <*사용자 이름*>| 대상 서비스 엔드포인트에 대한 액세스를 인증하는 사용자 이름입니다. |
| **암호** | `password` | 예 | <*암호*> | 대상 서비스 엔드포인트에 대한 액세스를 인증하는 암호입니다. |
||||||

[보안 매개 변수를](#secure-action-parameters) 사용하여 중요한 정보를 처리하고 보호하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿)](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)표현식을 사용하여 런타임에 이러한 매개 변수 값에 액세스할 수 있습니다. 이 예제 HTTP 작업 정의는 `type` `Basic` 인증을 다음과 같이 지정하고 [parameters() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>클라이언트 인증서 인증

클라이언트 [인증서](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) 옵션을 사용할 수 있는 경우 다음 속성 값을 지정합니다.

| 속성 (디자이너) | Property(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | **클라이언트 인증서** <br>또는 <br>`ClientCertificate` | TLS/SSL 클라이언트 인증서에 사용할 인증 유형입니다. 자체 서명된 인증서가 지원되지만 TLS/SSL에 대한 자체 서명된 인증서는 지원되지 않습니다. |
| **Pfx** | `pfx` | 예 | <*인코딩된 pfx 파일 콘텐츠*> | PFX(개인 정보 교환) 파일의 base64로 인코딩된 콘텐츠 <p><p>PFX 파일을 base64 인코딩 형식으로 변환하려면 다음 단계를 수행하여 PowerShell을 사용할 수 있습니다. <p>1. 인증서 내용을 변수에 저장합니다. <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. `ToBase64String()` 함수를 사용하여 인증서 내용을 변환하고 해당 콘텐츠를 텍스트 파일에 저장합니다. <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **암호** | `password`| 예 | <*암호 - 대 pfx 파일*> | PFX 파일에 액세스하기 위한 암호 |
|||||

[보안 매개 변수를](#secure-action-parameters) 사용하여 중요한 정보를 처리하고 보호하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿)](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)표현식을 사용하여 런타임에 이러한 매개 변수 값에 액세스할 수 있습니다. 이 예제 HTTP 작업 정의는 `type` `ClientCertificate` 인증을 다음과 같이 지정하고 [parameters() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

클라이언트 인증서 인증을 사용하여 서비스 보안에 대한 자세한 내용은 다음 항목을 참조하십시오.

* [Azure API 관리에서 클라이언트 인증서 인증을 사용하여 API에 대한 보안 향상](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Azure API 관리에서 클라이언트 인증서 인증을 사용하여 백 엔드 서비스에 대한 보안 향상](../api-management/api-management-howto-mutual-certificates.md)
* [클라이언트 인증서를 사용하여 RESTfuL 서비스에 대한 보안 향상](../active-directory-b2c/secure-rest-api.md)
* [애플리케이션 인증을 위한 인증서 자격 증명](../active-directory/develop/active-directory-certificate-credentials.md)
* [Azure 앱 서비스의 코드에서 TLS/SSL 인증서 사용](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth 인증

Active [Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) 옵션을 사용할 수 있는 경우 다음 속성 값을 지정합니다.

| 속성 (디자이너) | Property(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | **Active Directory OAuth** <br>또는 <br>`ActiveDirectoryOAuth` | 사용할 인증 유형입니다. 논리 앱은 현재 [OAuth 2.0 프로토콜을](../active-directory/develop/v2-overview.md)따릅니다. |
| **Authority** | `authority` | 예 | <*권한용 URL 토큰 발급자*> | 인증 토큰을 제공하는 기관의 URL입니다. 이 값은 기본적으로 `https://login.windows.net`입니다. |
| **테넌트** | `tenant` | 예 | <*테넌트 ID*> | Azure AD 테넌트의 테넌트 ID |
| **관객** | `audience` | 예 | <*권한 부여할 리소스*> | 권한 부여에 사용할 리소스(예: `https://management.core.windows.net/`) |
| **클라이언트 ID** | `clientId` | 예 | <*클라이언트 ID*> | 권한 부여를 요청하는 앱에 대한 클라이언트 ID |
| **자격 증명 유형** | `credentialType` | 예 | 인증서 <br>또는 <br>비밀 | 클라이언트가 권한 부여를 요청하는 데 사용하는 자격 증명 유형입니다. 이 속성 및 값논리 앱의 기본 정의에 나타나지 않지만 선택한 자격 증명 유형에 대해 나타나는 속성을 결정합니다. |
| **비밀** | `secret` | 예, 하지만 "비밀" 자격 증명 유형에 대해서만 | <*클라이언트 비밀*> | 권한 부여를 요청하는 클라이언트 비밀 |
| **Pfx** | `pfx` | 예. 하지만 "인증서" 자격 증명 유형에 대해서만 | <*인코딩된 pfx 파일 콘텐츠*> | PFX(개인 정보 교환) 파일의 base64로 인코딩된 콘텐츠 |
| **암호** | `password` | 예. 하지만 "인증서" 자격 증명 유형에 대해서만 | <*암호 - 대 pfx 파일*> | PFX 파일에 액세스하기 위한 암호 |
|||||

[보안 매개 변수를](#secure-action-parameters) 사용하여 중요한 정보를 처리하고 보호하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿)](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)표현식을 사용하여 런타임에 이러한 매개 변수 값에 액세스할 수 있습니다. 이 예제 HTTP 작업 정의는 `type` `ActiveDirectoryOAuth`인증을 자격 증명 `Secret`유형으로 지정하고 [parameters() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>원시 인증

**Raw** 옵션을 사용할 수 있는 경우 [OAuth 2.0 프로토콜을](https://oauth.net/2/)따르지 않는 [인증 체계를](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) 사용해야 하는 경우 이 인증 유형을 사용할 수 있습니다. 이 형식을 사용하면 나가는 요청과 함께 보내는 권한 부여 헤더 값을 수동으로 만들고 트리거 또는 작업에서 해당 헤더 값을 지정합니다.

예를 들어 [OAuth 1.0 프로토콜을](https://tools.ietf.org/html/rfc5849)따르는 HTTPS 요청에 대한 샘플 헤더는 다음과 같습니다.

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

원시 인증을 지원하는 트리거 또는 작업에서 다음 속성 값을 지정합니다.

| 속성 (디자이너) | Property(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | Raw | 사용할 인증 유형 |
| **값** | `value` | 예 | <*권한 부여-헤더-값*> | 인증에 사용할 권한 부여 헤더 값 |
||||||

[보안 매개 변수를](#secure-action-parameters) 사용하여 중요한 정보를 처리하고 보호하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿)](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)표현식을 사용하여 런타임에 이러한 매개 변수 값에 액세스할 수 있습니다. 이 예제 HTTP 작업 정의는 `type` `Raw`인증을 로 지정하고 [parameters() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>관리 ID 인증

[관리되는 ID](../active-directory/managed-identities-azure-resources/overview.md) 옵션을 사용할 수 있는 경우 논리 앱은 로그인하지 않고 다른 Azure Active Directory(Azure AD) 테넌트에 있는 리소스에 대한 액세스를 인증하기 위해 시스템 할당 된 ID 또는 수동으로 만든 *단일* 사용자 할당 ID를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다. [Azure AD 인증에 대해 관리되는 ID를 지원하는 Azure 서비스에 대해](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)자세히 알아봅니다.

1. 논리 앱에서 관리되는 ID를 사용하기 전에 Azure Logic Apps 에서 [관리되는 ID를 사용하여 Azure 리소스에 대한 액세스 인증단계를](../logic-apps/create-managed-service-identity.md)따릅니다. 이러한 단계를 수행하면 논리 앱에서 관리되는 ID를 활성화하고 대상 Azure 리소스에 대한 해당 ID의 액세스를 설정합니다.

1. Azure 함수가 관리되는 ID를 사용하려면 먼저 [Azure 함수에 대한 인증을 사용하도록 설정합니다.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

1. 관리되는 ID를 사용하려는 트리거 또는 작업에서 다음 속성 값을 지정합니다.

   | 속성 (디자이너) | Property(JSON) | 필수 | 값 | Description |
   |---------------------|-----------------|----------|-------|-------------|
   | **인증** | `type` | 예 | **관리 ID** <br>또는 <br>`ManagedServiceIdentity` | 사용할 인증 유형 |
   | **관리 ID** | `identity` | 예 | * **시스템 할당된 관리되는 ID** <br>또는 <br>`SystemAssigned` <p><p>* *<사용자 할당 된 ID 이름*> | 사용할 관리되는 ID |
   | **관객** | `audience` | 예 | <*대상 리소스 ID*> | 액세스하려는 대상 리소스의 리소스 ID입니다. <p>예를 들어 `https://storage.azure.com/` 인증에 대한 액세스 토큰을 모든 저장소 계정에 대해 유효하게 만듭니다. 그러나 특정 저장소 계정에 `https://fabrikamstorageaccount.blob.core.windows.net` 대한 루트 서비스 URL을 지정할 수도 있습니다. <p>**참고:** **Audience** 속성은 일부 트리거 또는 동작에 숨겨져 있을 수 있습니다. 이 속성을 표시하려면 트리거 또는 작업에서 **새 매개 변수 추가** 목록을 열고 **잠재고객**을 선택합니다. <p><p>**중요**: 이 대상 리소스 ID가 필요한 후행 슬래시를 포함하여 Azure AD가 기대하는 값과 *정확히 일치하는지* 확인합니다. 따라서 모든 `https://storage.azure.com/` Azure Blob Storage 계정에 대한 리소스 ID에는 후행 슬래시가 필요합니다. 그러나 특정 저장소 계정에 대 한 리소스 ID 후행 슬래시를 필요로 하지 않습니다. 이러한 리소스 아이디를 찾으려면 [Azure AD를 지원하는 Azure 서비스를](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)참조하십시오. |
   |||||

   [보안 매개 변수를](#secure-action-parameters) 사용하여 중요한 정보를 처리하고 보호하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿)](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)표현식을 사용하여 런타임에 이러한 매개 변수 값에 액세스할 수 있습니다. 이 예제 HTTP 작업 정의는 `type` `ManagedServiceIdentity` 인증을 다음과 같이 지정하고 [parameters() 함수를](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 사용하여 매개 변수 값을 가져옵니다.

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>다음 단계

* [Azure 논리 앱에 대한 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [논리 앱 모니터링](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [논리 앱 오류 및 문제 진단](../logic-apps/logic-apps-diagnosing-failures.md)  
* [논리 앱 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
