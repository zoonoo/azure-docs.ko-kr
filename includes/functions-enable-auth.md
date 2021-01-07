---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648922"
---
#### <a name="enable-app-service-authenticationauthorization"></a>App Service 인증/권한 부여를 사용하도록 설정

App Service 플랫폼에서는 AAD(Azure Active Directory) 및 기타 여러 타사 ID 공급자를 사용하여 클라이언트를 인증할 수 있습니다. 이 전략을 사용하여 함수용 사용자 지정 권한 부여 규칙을 구현할 수 있으며, 함수 코드에서 사용자 정보를 사용할 수 있습니다. 자세히 알아보려면 [Azure App Service에서 인증 및 권한 부여](../articles/app-service/overview-authentication-authorization.md) 및 [클라이언트 ID 사용](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)을 참조하세요.

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Azure APIM(API Management)를 사용하여 요청 인증

APIM은 수신 요청에 사용할 수 있는 여러 가지 API 보안 옵션을 제공합니다. 자세히 알아보려면 [API Management 인증 정책](../articles/api-management/api-management-authentication-policies.md)을 참조하세요. APIM을 적용하면 APIM 인스턴스의 IP 주소에서 보내는 요청만 수락하도록 함수 앱을 구성할 수 있습니다. 자세히 알아보려면 [IP 주소 제한](../articles/azure-functions/ip-addresses.md#ip-address-restrictions)을 참조하세요.
