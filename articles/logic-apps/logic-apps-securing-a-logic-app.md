---
title: 안전 하 게 액세스 및 데이터 Azure Logic Apps
description: 매개 변수 입력, HTTP 요청 트리거, 실행 기록, 논리 앱 작업 및 다른 서비스에 대 한 연결 보호 Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b1331865224d34f731dbd388cebdaec8b7c9fe7f
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264652"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure Logic Apps에서 액세스 및 데이터 보호

Azure Logic Apps에서 데이터 액세스 및 보호를 제어 하기 위해 다음 영역에서 보안을 설정할 수 있습니다.

* [HTTP 요청 트리거에 대 한 액세스](#secure-triggers)
* [논리 앱 작업에 대 한 액세스](#secure-operations)
* [실행 기록 입력 및 출력에 대 한 액세스](#secure-run-history)
* [매개 변수 입력에 액세스](#secure-action-parameters)
* [논리 앱에서 호출 되는 서비스 및 시스템에 액세스](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>HTTP 요청 트리거에 대 한 액세스

논리 앱에서 [요청](../connectors/connectors-native-reqres.md) 또는 [WEBHOOK](../connectors/connectors-native-webhook.md) 트리거와 같은 HTTP 요청 기반 트리거를 사용 하는 경우 인증 된 클라이언트만 논리 앱을 시작할 수 있도록 액세스를 제한할 수 있습니다. 논리 앱에서 받은 모든 요청은 SSL(Secure Sockets Layer) 프로토콜을 사용하여 암호화되고 보안이 유지됩니다. 

이 트리거 형식에 대 한 액세스를 보호할 수 있는 방법은 다음과 같습니다.

* [공유 액세스 서명 생성](#sas)
* [받는 IP 주소 제한](#restrict-incoming-ip-addresses)
* [Azure Active Directory, OAuth 또는 기타 보안 추가](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>SAS (공유 액세스 서명) 생성

논리 앱의 모든 요청 끝점에는 끝점의 URL에 [SAS (공유 액세스 서명)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) 가 있으며,이는 다음 형식을 따릅니다.

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

각 URL에는 다음 표에 설명 된 대로 `sp`, `sv` 및 @no__t 쿼리 매개 변수가 포함 되어 있습니다.

| 쿼리 매개 변수 | 설명 |
|-----------------|-------------|
| `sp` | 사용할 수 있는 HTTP 메서드에 대 한 사용 권한을 지정 합니다. |
| `sv` | 서명을 생성 하는 데 사용할 SAS 버전을 지정 합니다. |
| `sig` | 트리거에 대 한 액세스를 인증 하는 데 사용할 서명을 지정 합니다. 이 서명은 모든 URL 경로 및 속성에 대 한 암호 액세스 키와 함께 SHA256 알고리즘을 사용 하 여 생성 됩니다. 노출 되거나 게시 되지 않은 경우이 키는 암호화 된 상태로 유지 되 고 논리 앱에 저장 됩니다. 논리 앱은 암호 키로 만들어진 유효한 서명을 포함하는 트리거에 권한을 부여합니다. |
|||

공유 액세스 서명을 사용 하 여 액세스를 보호 하는 방법에 대 한 자세한 내용은이 항목의 다음 섹션을 참조 하십시오.

* [액세스 키 다시 생성](#access-keys)
* [만료 콜백 URL 만들기](#expiring-urls)
* [주 또는 보조 키를 사용하여 URL 만들기](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>액세스 키 다시 생성

언제 든 지 새 보안 액세스 키를 생성 하려면 Azure REST API 또는 Azure Portal를 사용 합니다. 이전 키를 사용 하는 이전에 생성 된 모든 Url은 무효화 되 고 더 이상 논리 앱을 트리거할 수 있는 권한이 없습니다. 다시 생성 후에 검색 하는 Url은 새 액세스 키로 서명 됩니다.

1. Azure Portal에서 다시 생성하려는 키가 있는 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **액세스 키**를 선택합니다.

1. 다시 생성 하려는 키를 선택 하 고 프로세스를 완료 합니다.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>만료 콜백 Url 만들기

HTTP 요청 기반 트리거의 끝점 URL을 다른 당사자와 공유 하는 경우 특정 키를 사용 하 고 만료 날짜가 있는 콜백 Url을 생성할 수 있습니다. 이렇게 하면 키를 원활 하 게 롤링 하거나 특정 시간 범위에 따라 논리 앱을 트리거하는 액세스를 제한할 수 있습니다. URL의 만료 날짜를 지정 하려면 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)를 사용 합니다. 예를 들면 다음과 같습니다.

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

본문에서 JSON 날짜 문자열을 사용 하 여 `NotAfter`property를 포함 합니다. 이 속성은 `NotAfter` 날짜 및 시간까지만 유효한 콜백 URL을 반환합니다.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>기본 또는 보조 비밀 키로 URL 만들기

HTTP 요청 기반 트리거에 대 한 콜백 Url을 생성 하거나 나열 하는 경우 URL에 서명 하는 데 사용할 키를 지정할 수 있습니다. 특정 키로 서명 된 URL을 생성 하려면 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)를 사용 합니다. 예를 들면 다음과 같습니다.

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

본문에서는 속성 `KeyType`을 `Primary` 또는 `Secondary`로서 포함합니다. 이 속성은 지정된 보안 키로 서명된 URL을 반환합니다.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>받는 IP 주소 제한

공유 액세스 서명과 함께 논리 앱을 호출할 수 있는 특정 클라이언트를 제한 하는 것이 좋습니다. 예를 들어 Azure API Management를 사용 하 여 요청 끝점을 관리 하는 경우 API Management 인스턴스의 IP 주소 에서만 요청을 수락 하도록 논리 앱을 제한할 수 있습니다.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Azure Portal에서 들어오는 IP 범위 제한

1. Azure Portal의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세서 제어 구성** > **허용된 인바운드 IP 주소**에서 **특정 IP 범위**를 선택합니다.

1. **트리거에 대한 IP 범위**에서 트리거가 허용하는 IP 주소 범위를 지정합니다.

   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다.

논리 앱이 중첩 된 논리 앱 으로만 트리거하도록 하려면 **허용 된 인바운드 IP 주소** 목록에서 **다른 Logic Apps만**선택 합니다. 이 옵션은 논리 앱 리소스에 빈 배열을 씁니다. 이렇게 하면 Logic Apps 서비스 (부모 논리 앱)의 호출만 중첩 된 논리 앱을 트리거할 수 있습니다.

> [!NOTE]
> IP 주소에 관계 없이 Azure REST API 또는 API Management를 통해 `/triggers/<trigger-name>/run`을 사용 하 여 HTTP 요청 기반 트리거를 포함 하는 논리 앱을 계속 실행할 수 있습니다. 그러나이 시나리오에서는 여전히 Azure REST API에 대 한 인증이 필요 합니다. 모든 이벤트는 Azure 감사 로그에 표시 됩니다. 액세스 제어 정책을 적절 하 게 설정 해야 합니다.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 들어오는 IP 범위 제한

[Azure Resource Manager 템플릿을](../logic-apps/logic-apps-create-deploy-template.md)사용 하 여 논리 앱 배포를 자동화 하는 경우 논리 앱의 리소스 정의에서 `triggers` 섹션과 `accessControl` 섹션을 사용 하 여 IP 범위를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth 또는 기타 보안 추가

논리 앱에 다른 권한 부여 프로토콜을 추가하려면 [Azure API Management](../api-management/api-management-key-concepts.md)를 사용하는 것이 좋습니다. 이 서비스는 논리 앱을 API로 노출 하 고 모든 끝점에 대 한 풍부한 모니터링, 보안, 정책 및 설명서를 제공 하는 기능을 제공 합니다. API Management는 논리 앱에 대한 공용 또는 프라이빗 엔드포인트를 노출할 수 있으며 이러한 엔드포인트는 Azure Active Directory, OAuth, 인증서 또는 기타 보안 표준을 사용할 수 있습니다. API Management는 요청을 받으면 논리 앱에 요청을 보내고 필요한 변환 또는 제한을 수행합니다. API Management에서만 논리 앱을 트리거하도록 하려면 논리 앱의 들어오는 IP 범위 설정을 사용할 수 있습니다.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>논리 앱 작업에 대 한 액세스

특정 사용자나 그룹만 논리 앱 관리, 편집 및 보기와 같은 특정 작업을 실행 하도록 허용할 수 있습니다. 해당 권한을 제어 하려면 [Azure 역할 기반 Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) 를 사용 하 여 azure 구독의 구성원에 사용자 지정 또는 기본 제공 역할을 할당 합니다.

* [논리 앱 참가자](../role-based-access-control/built-in-roles.md#logic-app-contributor): 논리 앱을 관리할 수 있지만 해당 앱에 대 한 액세스를 변경할 수는 없습니다.

* [논리 앱 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator): 논리 앱을 읽고 사용 하거나 사용 하지 않도록 설정할 수 있지만 편집 하거나 업데이트할 수는 없습니다.

다른 사용자가 논리 앱을 변경 하거나 삭제 하지 못하도록 하기 위해 [Azure 리소스 잠금을](../azure-resource-manager/resource-group-lock-resources.md)사용 하 여 다른 사용자가 프로덕션 리소스를 변경 하거나 삭제 하지 못하도록 할 수 있습니다.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>실행 기록 데이터에 대 한 액세스

논리 앱을 실행 하는 동안 [TLS (Transport Layer Security](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) ) 및 [rest](../security/fundamentals/encryption-atrest.md)를 사용 하 여 전송 하는 동안 모든 데이터가 암호화 됩니다. 논리 앱의 실행이 완료 되 면 각 동작에 대 한 상태, 기간, 입력 및 출력과 함께 실행 된 단계를 포함 하 여 해당 실행에 대 한 기록을 볼 수 있습니다. 이 풍부한 정보는 논리 앱이 실행 되는 방법 및 발생 하는 문제 해결을 시작할 수 있는 위치에 대 한 통찰력을 제공 합니다.

논리 앱의 실행 기록에 액세스 하는 경우 Logic Apps는 액세스를 인증 하 고 논리 앱 실행에서 요청 및 응답의 입력 및 출력에 대 한 링크를 제공 합니다. 그러나 암호, 비밀, 키 또는 기타 중요 한 정보를 처리 하는 작업의 경우 다른 사용자가 해당 데이터를 보고 액세스 하지 못하도록 하는 것이 좋습니다. 예를 들어 논리 앱이 HTTP 작업을 인증할 때 사용할 [Azure Key Vault](../key-vault/key-vault-overview.md) 에서 비밀을 가져오는 경우 해당 비밀을 뷰에서 숨깁니다.

논리 앱의 실행 기록에서 입력 및 출력에 대 한 액세스를 제어 하기 위해 다음 옵션을 사용할 수 있습니다.

* [IP 주소 범위를 기준으로 액세스를 제한](#restrict-ip)합니다.

  이 옵션을 사용 하면 특정 IP 주소 범위의 요청을 기반으로 실행 기록에 대 한 액세스를 보호할 수 있습니다.

* [난독 처리를 사용 하 여 실행 기록에서 데이터를 숨깁니다](#obfuscate).

  많은 트리거와 작업에서 논리 앱의 실행 기록에서 입력, 출력 또는 둘 다를 숨길 수 있습니다.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP 주소 범위를 통한 액세스 제한

특정 IP 주소 범위의 요청만 해당 데이터를 볼 수 있도록 논리 앱의 실행 기록에서 입력 및 출력에 대 한 액세스를 제한할 수 있습니다. 예를 들어 모든 사용자가 입력 및 출력에 액세스 하는 것을 차단 하려면 IP 주소 범위 (예: `0.0.0.0-0.0.0.0`)를 지정 합니다. 관리자 권한이 있는 사용자만 논리 앱의 데이터에 "just-in-time" 액세스를 제공 하는이 제한을 제거할 수 있습니다. 논리 앱 배포에 사용 하는 Azure Resource Manager 템플릿 또는 Azure Portal를 사용 하 여 제한할 IP 범위를 지정할 수 있습니다.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure Portal에서 IP 범위 제한

1. Azure Portal의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세서 제어 구성** > **허용된 인바운드 IP 주소**에서 **특정 IP 범위**를 선택합니다.

1. **콘텐츠의 IP 범위**에서 입력 및 출력의 콘텐츠에 액세스할 수 있는 IP 주소 범위를 지정합니다. 

   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다.

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 IP 범위 제한

[Azure Resource Manager 템플릿을](../logic-apps/logic-apps-create-deploy-template.md)사용 하 여 논리 앱 배포를 자동화 하는 경우 논리 앱의 리소스 정의에서 `contents` 섹션과 `accessControl` 섹션을 사용 하 여 IP 범위를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>난독 처리를 사용 하 여 실행 기록에서 데이터 숨기기

많은 트리거와 작업에는 논리 앱의 실행 기록에서 입력, 출력 또는 둘 다를 숨기는 설정이 있습니다. 이러한 설정을 사용 하 여이 데이터를 보호 하는 경우 고려해 야 할 몇 가지 [고려 사항은](#obfuscation-considerations) 다음과 같습니다.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>디자이너에서 입력 및 출력 보안

1. 논리 앱이 [Azure Portal](https://portal.azure.com)에서 아직 열려 있지 않은 경우 논리 앱 디자이너에서 논리 앱을 엽니다.

   ![샘플 논리 앱 열기](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. 데이터를 보호 하려는 트리거 또는 작업에서 줄임표 ( **...** ) 단추를 선택한 다음 **설정**을 선택 합니다.

   !["설정" 열기](media/logic-apps-securing-a-logic-app/open-settings.png)

1. **보안 입력**, **보안 출력**또는 둘 다를 설정 합니다. 완료되면 **완료**를 선택합니다.

   ![보안 입력 또는 출력 설정](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   이제 작업 또는 트리거가 제목 표시줄에 잠금 아이콘을 표시 합니다.

   ![제목 표시줄의 잠금 아이콘](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   이전 작업의 보안 출력을 나타내는 토큰에도 잠금 아이콘이 표시 됩니다. 예를 들어 동적 콘텐츠 목록에서 작업에 사용할 출력을 선택 하면 해당 토큰에 잠금 아이콘이 표시 됩니다.

   ![출력 선택](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 논리 앱을 실행 한 후에는 해당 실행에 대 한 기록을 볼 수 있습니다.

   1. 논리 앱의 **개요** 창에서 보려는 실행을 선택 합니다.

   1. **논리 앱 실행** 창에서 검토 하려는 작업을 확장 합니다.

      입력과 출력을 모두 보호 하도록 선택한 경우에는 이제 해당 값이 숨김 상태로 표시 됩니다.

      ![실행 기록의 숨겨진 데이터](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>코드 보기에서 입력 및 출력 보안

기본 트리거 또는 작업 정의에서 다음 값 중 하나 또는 모두를 사용 하 여 `runtimeConfiguration.secureData.properties` 배열을 추가 하거나 업데이트 합니다.

* `"inputs"`: 실행 기록의 입력을 보호 합니다.
* `"outputs"`: 실행 기록의 출력을 보호 합니다.

이러한 설정을 사용 하 여이 데이터를 보호 하는 경우 고려해 야 할 몇 가지 [고려 사항은](#obfuscation-considerations) 다음과 같습니다.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>입력 및 출력을 숨길 때의 고려 사항

* 트리거 또는 작업에서 입력 또는 출력의 보안을 유지 하는 경우 Logic Apps는 보안 데이터를 Azure Log Analytics에 보내지 않습니다. 또한 모니터링에 대해 [추적 된 속성](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) 을 해당 트리거나 작업에 추가할 수 없습니다.

* [워크플로 기록을 처리 하는 LOGIC APPS API](https://docs.microsoft.com/rest/api/logic/) 는 보안 출력을 반환 하지 않습니다.

* 입력을 보호 하거나 보안 된 출력을 명시적으로 사용 하는 작업에서이 작업이이 보안 데이터를 포함 하는 출력과 함께 응답을 반환 하는 경우 이러한 출력을 보호 하려면이 작업에서 수동으로 **보안 출력** 을 설정 해야 합니다.

* 실행 기록이 해당 데이터를 보호 해야 하는 다운스트림 작업에서 **보안 입력** 또는 **보안 출력** 을 설정 해야 합니다.

  **보안 출력 설정**

  트리거 또는 작업에서 수동으로 **보안 출력** 을 설정 하는 경우 Logic Apps 실행 기록에서 이러한 출력을 보호 합니다. 다운스트림 작업에서 이러한 보안 출력을 입력으로 명시적으로 사용 하는 경우 Logic Apps 실행 기록에서이 작업의 입력을 숨기 지만 작업의 **보안 입력** 설정을 *사용 하도록 설정 하지는 않습니다* .

  ![대부분의 동작에 대 한 입력 및 다운스트림 영향으로 보안 출력](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  작성, 구문 분석 JSON 및 응답 작업에는 **보안 입력** 설정만 있습니다. 설정 하면이 작업의 출력도 숨겨집니다. 이러한 작업에서 업스트림 보안 출력을 입력으로 명시적으로 사용 하는 경우 Logic Apps 이러한 작업의 입력 및 출력을 숨길 수 있지만 이러한 작업의 **보안 입력** 설정은 *사용 하지 않습니다* . 다운스트림 작업에서 작성, 구문 분석 JSON 또는 응답 작업의 숨겨진 출력을 입력으로 명시적으로 사용 하는 경우 *이 다운스트림 작업의 입력 또는 출력을 숨기지*Logic Apps.

  ![특정 작업에 대 한 다운스트림 영향의 입력으로 보호 된 출력](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **보안 입력 설정**

  트리거 또는 작업에서 **보안 입력** 을 수동으로 설정 하는 경우 Logic Apps 실행 기록에서 이러한 입력을 보호 합니다. 다운스트림 작업에서 해당 트리거나 작업에서 표시 되는 출력을 입력으로 명시적으로 사용 하는 경우 실행 기록에서이 다운스트림 작업의 입력을 숨길 Logic Apps 있지만이 작업에서 **보안 입력** 을 *사용 하도록 설정 하지* 않고이 작업의 출력.

  ![대부분의 동작에 대 한 보안 입력 및 다운스트림 영향](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  작성, 구문 분석 JSON 및 응답 작업에서 보안 된 입력을 포함 하는 트리거 또는 작업의 표시 되는 출력을 명시적으로 사용 하는 경우 이러한 작업의 입력 및 출력을 숨길 Logic Apps 이러한 작업의 **보안 입력** 을 *사용 하지 않습니다* . 설정. 다운스트림 작업에서 작성, 구문 분석 JSON 또는 응답 작업의 숨겨진 출력을 입력으로 명시적으로 사용 하는 경우 *이 다운스트림 작업의 입력 또는 출력을 숨기지*Logic Apps.

  ![특정 작업에 대 한 보안 입력 및 다운스트림 영향](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>매개 변수 입력에 액세스

여러 환경에 걸쳐 배포 하는 경우 해당 환경에 따라 달라 지는 워크플로 정의의 값을 매개 변수화 하는 것이 좋습니다. 이렇게 하면 [Azure Resource Manager 템플릿을](../azure-resource-manager/resource-group-authoring-templates.md#parameters) 사용 하 여 논리 앱을 배포 하 고, 보안 매개 변수를 정의 하 여 중요 한 정보를 보호 하 고, 매개 변수를 사용 하 여 템플릿의 매개 변수를 통해 해당 매개 변수 입력을 별도로 제공할 수 있습니다. [ 파일](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

예를 들어 [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)를 사용 하 여 HTTP 작업을 인증 하는 경우 인증에 사용 되는 클라이언트 ID 및 클라이언트 암호를 허용 하는 매개 변수를 정의 하 고 보호할 수 있습니다. 논리 앱에 대 한 이러한 매개 변수를 정의 하려면 논리 앱의 워크플로 정의 내에서 `parameters` 섹션을 사용 합니다. 논리 앱을 편집 하거나 실행 기록을 볼 때 표시 하지 않으려는 매개 변수 값을 보호 하려면 `securestring` 또는 `secureobject` 유형을 사용 하 여 매개 변수를 정의 하 고 필요에 따라 인코딩을 사용 합니다. 이 형식의 매개 변수는 리소스 정의와 함께 반환 되지 않으며 배포 후 리소스를 볼 때 액세스할 수 없습니다. 런타임 중에 이러한 매개 변수 값에 액세스 하려면 워크플로 정의 내에서 `@parameters('<parameter-name>')` 식을 사용 합니다. 이 식은 런타임에만 계산 되며 [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에 의해 설명 됩니다.

> [!NOTE]
> HTTP 요청의 헤더 또는 본문에 매개 변수를 사용 하는 경우 논리 앱의 실행 기록과 나가는 HTTP 요청을 볼 때 해당 매개 변수가 표시 될 수 있습니다. 또한 콘텐츠 액세스 정책도 적절 하 게 설정 해야 합니다. 권한 부여 헤더는 입력 또는 출력을 통해 볼 수 없습니다. 따라서 권한 부여 헤더에 비밀이 사용되면 해당 비밀을 검색할 수 없습니다.

자세한 내용은이 항목의 뒷부분에 나오는 [워크플로 정의의 보안 매개 변수](#secure-parameters-workflow) 를 참조 하세요.

[Azure Resource Manager 템플릿을](../azure-resource-manager/resource-group-authoring-templates.md#parameters)사용 하 여 배포를 자동화 하는 경우 `securestring` 및 `secureobject` 유형을 사용 하 여 배포 시 평가 되는 보안 템플릿 매개 변수를 정의할 수 있습니다. 템플릿 매개 변수를 정의 하려면 템플릿의 최상위 수준 `parameters` 섹션을 사용 합니다 .이 섹션은 워크플로 정의의 `parameters` 섹션과 별개 이며 서로 다릅니다. 템플릿 매개 변수에 대 한 값을 제공 하려면 별도의 [매개 변수 파일](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)을 사용 합니다.

예를 들어, 비밀을 사용 하는 경우 배포 시 [Azure Key Vault](../key-vault/key-vault-overview.md) 에서 해당 비밀을 검색 하는 보안 템플릿 매개 변수를 정의 하 고 사용할 수 있습니다. 그런 다음 매개 변수 파일에서 키 자격 증명 모음 및 암호를 참조할 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [Azure Key Vault를 사용 하 여 배포 시 보안 매개 변수 값 전달](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* 이 항목의 뒷부분에 [Azure Resource Manager 템플릿의 보안 매개 변수](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>워크플로 정의의 보안 매개 변수

논리 앱의 워크플로 정의에서 중요 한 정보를 보호 하려면 논리 앱을 저장 한 후이 정보가 표시 되지 않도록 보안 매개 변수를 사용 합니다. 예를 들어, HTTP 작업에는 사용자 이름 및 암호를 사용 하는 기본 인증이 필요 하다 고 가정 합니다. 워크플로 정의에서 `parameters` 섹션은 `securestring` 유형을 사용 하 여 `basicAuthPasswordParam` 및 `basicAuthUsernameParam` 매개 변수를 정의 합니다. 그런 다음 작업 정의는 `authentication` 섹션에서 이러한 매개 변수를 참조 합니다.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 보안 매개 변수

논리 앱에 대 한 리소스 관리자 템플릿에는 여러 `parameters` 섹션이 있습니다. 암호, 키, 암호 및 기타 중요 한 정보를 보호 하려면 `securestring` 또는 `secureobject` 유형을 사용 하 여 템플릿 수준 및 워크플로 정의 수준에서 보안 매개 변수를 정의 합니다. 그런 다음 [Azure Key Vault](../key-vault/key-vault-overview.md) 에 이러한 값을 저장 하 고 [매개 변수 파일](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) 을 사용 하 여 키 자격 증명 모음 및 암호를 참조할 수 있습니다. 그런 다음 템플릿에서 해당 정보를 검색 합니다. 자세한 내용은 [Azure Key Vault를 사용 하 여 배포 시 보안 매개 변수 값 전달을](../azure-resource-manager/resource-manager-keyvault-parameter.md)참조 하세요.

이러한 `parameters` 섹션에 대 한 자세한 내용은 다음과 같습니다.

* 템플릿의 최상위 수준에서 `parameters` 섹션은 템플릿에서 *배포*에 사용 하는 값에 대 한 매개 변수를 정의 합니다. 예를 들어 이러한 값에는 특정 배포 환경에 대 한 연결 문자열이 포함 될 수 있습니다. 그런 다음이 값을 별도의 [매개 변수 파일](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)에 저장 하면 이러한 값을 보다 쉽게 변경할 수 있습니다.

* 논리 앱의 리소스 정의 내에서 워크플로 정의 외부에 있는 `parameters` 섹션은 워크플로 정의의 매개 변수에 대 한 값을 지정 합니다. 이 섹션에서는 템플릿 매개 변수를 참조 하는 템플릿 식을 사용 하 여 이러한 값을 할당할 수 있습니다. 이러한 식은 배포 시 평가 됩니다.

* 워크플로 정의 내에서 `parameters` 섹션은 런타임에 논리 앱에서 사용 하는 매개 변수를 정의 합니다. 그런 다음 런타임에 평가 되는 워크플로 정의 식을 사용 하 여 논리 앱의 워크플로 내에서 이러한 매개 변수를 참조할 수 있습니다.

@No__t-0 유형을 사용 하는 여러 보안 매개 변수 정의를 포함 하는이 예제 템플릿입니다.

| 매개 변수 이름 | 설명 |
|----------------|-------------|
| `TemplatePasswordParam` | 워크플로 정의의 `basicAuthPasswordParam` 매개 변수에 전달 되는 암호를 수락 하는 템플릿 매개 변수입니다. |
| `TemplateUsernameParam` | 워크플로 정의의 @no__t 매개 변수에 전달 되는 사용자 이름을 허용 하는 템플릿 매개 변수입니다. |
| `basicAuthPasswordParam` | HTTP 작업의 기본 인증에 대 한 암호를 허용 하는 워크플로 정의 매개 변수 |
| `basicAuthUserNameParam` | HTTP 작업의 기본 인증에 대 한 사용자 이름을 허용 하는 워크플로 정의 매개 변수 |
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>논리 앱에서 호출 되는 서비스 및 시스템에 액세스

논리 앱에서 요청을 보내기 위해 액세스 해야 하는 끝점을 보호할 수 있는 몇 가지 방법은 다음과 같습니다.

* 아웃 바운드 요청에 대 한 인증을 추가 합니다.

  HTTP, HTTP + Swagger (Open API) 또는 Webhook 작업을 사용 하는 경우 논리 앱에서 보낸 요청에 인증을 추가할 수 있습니다. 예를 들어 기본 인증, 인증서 인증 또는 Azure Active Directory 인증을 사용할 수 있습니다. 자세한 내용은 [트리거 또는 작업 인증](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)을 참조하세요.

* 논리 앱 IP 주소에서 액세스를 제한 합니다.

  논리 앱의 끝점에 대 한 모든 호출은 논리 앱의 영역을 기반으로 하는 지정 된 특정 IP 주소에서 시작 됩니다. 이러한 IP 주소의 요청만 수락하는 필터링을 추가할 수 있습니다. 이러한 IP 주소를 가져오려면 [Azure Logic Apps에 대 한 제한 및 구성](logic-apps-limits-and-config.md#configuration)을 참조 하세요.

* 관리 되는 id로 리소스를 인증 하 고 액세스 합니다.

  다른 Azure Active Directory (Azure AD) 테 넌 트의 리소스에 액세스 하기 위해 논리 앱은 자격 증명이 나 비밀이 아닌 관리 되는 id (이전의 관리 서비스 ID 또는 MSI)를 사용 하 여 로그인 하지 않고 id를 인증할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다. 논리 앱에 대 한 시스템 할당 관리 id를 설정 하 고 사용 하는 방법에 대 한 자세한 내용은 [Azure Logic Apps에서 관리 id를 사용 하 여 리소스 인증 및 액세스](../logic-apps/create-managed-service-identity.md)를 참조 하세요.

* 온-프레미스 시스템에 대 한 연결을 보호 합니다.

  Azure Logic Apps는 안전하고 신뢰할 수 있는 온-프레미스 통신을 위해 이러한 서비스와의 통합을 제공합니다.

  * 온-프레미스 데이터 게이트웨이

    Azure Logic Apps의 많은 관리 커넥터는 파일 시스템, SQL, SharePoint, DB2 등의 온-프레미스 시스템에 대 한 보안 연결을 제공 합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널에서 온-프레미스 원본의 데이터를 보냅니다. 보안으로 시작하는 모든 트래픽은 게이트웨이 에이전트에서 트래픽을 아웃바운드합니다. [온-프레미스 데이터 게이트웨이 작동 방식](logic-apps-gateway-install.md#gateway-cloud-service)을 알아보세요.

  * Azure API Management을 통해 연결

    [Azure API Management](../api-management/api-management-key-concepts.md)는 보안 프록시를 위한 사이트 간 가상 사설망 및 ExpressRoute 통합과 온-프레미스 시스템에 대한 통신 등의 온-프레미스 연결 옵션을 제공합니다. 논리 앱 디자이너의 논리 앱 워크플로에서 온-프레미스 시스템에 빠르게 액세스할 수 있는 API Management에 의해 노출 되는 API를 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [배포 템플릿 만들기](logic-apps-create-deploy-template.md)  
* [논리 앱 모니터링](logic-apps-monitor-your-logic-apps.md)  
* [논리 앱 오류 및 문제 진단](logic-apps-diagnosing-failures.md)  
* [논리 앱 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)
