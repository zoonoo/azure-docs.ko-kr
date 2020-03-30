---
title: 알림이 있는 관리되는 앱
description: 웹후크 끝점을 사용하여 관리되는 응용 프로그램을 구성하여 관리되는 응용 프로그램 인스턴스의 생성, 업데이트, 삭제 및 오류에 대한 알림을 수신합니다.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715675"
---
# <a name="azure-managed-applications-with-notifications"></a>알림이 있는 Azure 관리 응용 프로그램

Azure 관리 응용 프로그램 알림을 통해 게시자는 관리되는 응용 프로그램 인스턴스의 수명 주기 이벤트를 기반으로 작업을 자동화할 수 있습니다. 게시자는 새 및 기존 관리되는 응용 프로그램 인스턴스에 대한 이벤트 알림을 수신하도록 사용자 지정 알림 webhook 끝점을 지정할 수 있습니다. 게시자는 응용 프로그램 프로비저닝, 업데이트 및 삭제 시 사용자 지정 워크플로를 설정할 수 있습니다.

## <a name="getting-started"></a>시작
관리되는 응용 프로그램 수신을 시작하려면 공용 HTTPS 끝점을 스핀업하고 서비스 카탈로그 응용 프로그램 정의 또는 Azure Marketplace 오퍼를 게시할 때 지정합니다.

빠르게 시작하는 데 권장되는 단계는 다음과 같습니다.
1. 들어오는 POST 요청을 기록하고 반환하는 `200 OK`공용 HTTPS 끝점을 스핀업합니다.
2. 이 문서의 후반부에서 설명한 대로 서비스 카탈로그 응용 프로그램 정의 또는 Azure Marketplace 제품에 끝점을 추가합니다.
3. 응용 프로그램 정의 또는 Azure Marketplace 오퍼를 참조하는 관리되는 응용 프로그램 인스턴스를 만듭니다.
4. 알림이 수신되고 있는지 확인합니다.
5. 이 문서의 **끝점 인증** 섹션에 설명된 대로 권한 부여를 사용하도록 설정합니다.
6. 이 문서의 **알림 스키마** 섹션의 지침에 따라 알림 요청을 구문 분석하고 알림을 기반으로 비즈니스 논리를 구현합니다.

## <a name="add-service-catalog-application-definition-notifications"></a>서비스 카탈로그 응용 프로그램 정의 알림 추가
#### <a name="azure-portal"></a>Azure portal
시작하려면 Azure [포털을 통해 서비스 카탈로그 응용 프로그램 게시를](./publish-portal.md)참조하십시오.

![Azure 포털의 서비스 카탈로그 응용 프로그램 정의 알림](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 현재 응용 프로그램 정의 속성에서 `notificationEndpoints` 하나의 끝점만 제공할 수 있습니다.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure 마켓플레이스 관리되는 응용 프로그램 알림 추가
자세한 내용은 [Azure 응용 프로그램 오퍼 만들기](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)를 참조하십시오.

![Azure 포털에서 Azure 마켓플레이스 관리응용 프로그램 알림](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>이벤트 트리거
다음 표는 EventType 및 프로비저닝상태와 해당 트리거의 가능한 모든 조합을 설명합니다.

EventType | ProvisioningState | 알림 트리거
---|---|---
PUT | 수락됨 | 관리되는 리소스 그룹이 응용 프로그램 PUT 후 성공적으로 만들어지고 프로젝션되었습니다(관리되는 리소스 그룹 내의 배포가 시작되기 전).
PUT | 성공 | PUT 후 관리되는 응용 프로그램의 전체 프로비저닝이 성공했습니다.
PUT | 실패 | 언제 든 지 응용 프로그램 인스턴스 프로비저닝의 PUT의 실패.
패치 | 성공 | 태그, JIT 액세스 정책 또는 관리되는 ID를 업데이트하기 위해 관리되는 응용 프로그램 인스턴스에서 성공적으로 PATCH를 수행했습니다.
Delete | 삭제 중 | 사용자가 관리되는 앱 인스턴스의 DELETE를 시작하는 즉시.
Delete | Deleted | 관리되는 응용 프로그램의 완전하고 성공적인 삭제 후.
Delete | 실패 | 삭제를 차단하는 프로비저닝 해제 프로세스 중에 오류가 발생한 경우
## <a name="notification-schema"></a>알림 스키마
알림을 처리하기 위해 웹후크 끝점을 회전할 때 페이로드를 구문 분석하여 중요한 속성을 사용하여 알림에 따라 조치를 취해야 합니다. 서비스 카탈로그 및 Azure Marketplace 관리되는 응용 프로그램 알림은 많은 동일한 속성을 제공합니다. 샘플 다음에 오는 표에는 두 가지 작은 차이점이 설명되어 있습니다.

#### <a name="service-catalog-application-notification-schema"></a>서비스 카탈로그 응용 프로그램 알림 스키마
다음은 관리되는 응용 프로그램 인스턴스를 성공적으로 프로비저닝한 후의 샘플 서비스 카탈로그 알림입니다.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

프로비저닝에 실패하면 오류 세부 정보가 있는 알림이 지정된 끝점으로 전송됩니다.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Azure 마켓플레이스 응용 프로그램 알림 스키마

다음은 관리되는 응용 프로그램 인스턴스를 성공적으로 프로비저닝한 후의 샘플 서비스 카탈로그 알림입니다.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

프로비저닝에 실패하면 오류 세부 정보가 있는 알림이 지정된 끝점으로 전송됩니다.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

매개 변수 | 설명
---|---
eventType | 알림을 트리거한 이벤트 유형입니다. (예를 들어, PUT, PATCH, DELETE.)
applicationId | 알림이 트리거된 관리되는 응용 프로그램의 정규화된 리소스 식별자입니다.
eventTime | 알림을 트리거한 이벤트의 타임스탬프입니다. (UTC ISO 8601 형식의 날짜 및 시간))
provisioningState | 관리되는 응용 프로그램 인스턴스의 프로비저닝 상태입니다. (예를 들어, 성공, 실패, 삭제, 삭제.)
error | *프로비저닝상태가 실패한 경우에만 지정됩니다.* 오류 코드, 메시지 및 오류를 발생 시킨 문제에 대 한 세부 정보를 포함 합니다.
응용 프로그램정의ID | *서비스 카탈로그 관리 응용 프로그램에 대해서만 지정됩니다.* 관리되는 응용 프로그램 인스턴스가 프로비전된 응용 프로그램 정의의 정규화된 리소스 식별자를 나타냅니다.
계획 | *Azure 마켓플레이스 관리 응용 프로그램에 대해서만 지정됩니다.* 게시자, 오퍼, SKU 및 관리되는 응용 프로그램 인스턴스 버전을 나타냅니다.
청구 세부 정보 | *Azure 마켓플레이스 관리 되는 응용 프로그램에 대해서만 지정 됩니다.* 관리되는 응용 프로그램 인스턴스의 청구 세부 정보입니다. 사용 세부 정보에 대 한 Azure Marketplace를 쿼리 하는 데 사용할 수 있는 리소스UsageId를 포함 합니다.

## <a name="endpoint-authentication"></a>엔드포인트 인증
웹후크 엔드포인트를 보호하고 알림의 신뢰성을 확인하려면 다음을 수행하십시오.
1. 다음과 같이\:웹후크 URI 위에 쿼리 매개 변수를 제공합니다. 각 알림에서 쿼리 매개 `sig` 변수에 예상 `Guid`값이 있는지 확인합니다.
2. applicationId를 사용하여 관리되는 응용 프로그램 인스턴스에서 GET을 발급합니다. 프로비저닝State가 프로비저닝과 일치하는지 확인일관성을 보장하기 위해 알림의 상태입니다.

## <a name="notification-retries"></a>알림 재시도

관리되는 응용 프로그램 알림 `200 OK` 서비스는 웹후크 끝점에서 알림에 대한 응답을 기대합니다. 웹후크 끝점이 500보다 크거나 같은 HTTP 오류 코드를 반환하거나 429의 오류 코드를 반환하거나 끝점에 일시적으로 연결할 수 없는 경우 알림 서비스가 다시 시도됩니다. 10시간 이내에 웹후크 끝점을 사용할 수 없게 되면 알림 메시지가 삭제되고 재시도가 중지됩니다.
