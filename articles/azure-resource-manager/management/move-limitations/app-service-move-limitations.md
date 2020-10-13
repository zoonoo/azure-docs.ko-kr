---
title: Azure App Service 리소스 이동
description: Azure Resource Manager를 사용 하 여 App Service 리소스를 새 리소스 그룹 또는 구독으로 이동 합니다.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531375"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service 리소스에 대 한 이동 지침

이 문서에서는 App Service 리소스를 이동 하는 단계를 설명 합니다. App Service 리소스를 새 구독으로 이동 하기 위한 특정 요구 사항이 있습니다.

## <a name="move-across-subscriptions"></a>구독 간 이동

구독 간에 웹 앱을 이동할 때 다음 지침이 적용 됩니다.

- 대상 리소스 그룹에 기존 App Service 리소스가 없어야 합니다. App Service 리소스에는 다음이 포함됩니다.
    - Web Apps
    - App Service 계획
    - 업로드 되거나 가져온 TLS/SSL 인증서
    - App Service Environment
- 리소스 그룹의 모든 App Service 리소스는 함께 이동해야 합니다.
- App Service 환경은 새 리소스 그룹 또는 구독으로 이동할 수 없습니다. 그러나 App Service Environment 이동 하지 않고 웹 앱 및 app service 계획을 새 구독으로 이동할 수 있습니다. 이동 후에는 웹 앱이 더 이상 App Service Environment에서 호스트 되지 않습니다.
- 인증서가 리소스 그룹의 다른 모든 리소스와 함께 이동 하는 한 TLS 바인딩을 삭제 하지 않고 웹에 바인딩된 인증서를 이동할 수 있습니다.
- App Service 리소스는 처음 만들었던 리소스 그룹에서만 이동할 수 있습니다. App Service 리소스가 원래 리소스 그룹에 더 이상 없는 경우 다시 원래 리소스 그룹으로 이동 합니다. 그런 다음 구독 간에 리소스를 이동 합니다.

원래 리소스 그룹을 기억할 수 없는 경우 진단을 통해 찾을 수 있습니다. 웹 앱의 경우 **문제 진단 및 해결**을 선택 합니다. 그런 다음 **구성 및 관리**를 선택 합니다.

![진단 선택](./media/app-service-move-limitations/select-diagnostics.png)

**마이그레이션 옵션**을 선택 합니다.

![마이그레이션 옵션 선택](./media/app-service-move-limitations/select-migration.png)

웹 앱을 이동 하는 권장 단계에 대 한 옵션을 선택 합니다.

![권장 단계 선택](./media/app-service-move-limitations/recommended-steps.png)

리소스를 이동 하기 전에 수행 해야 하는 권장 작업이 표시 됩니다. 이 정보는 웹 앱에 대 한 원래 리소스 그룹을 포함 합니다.

![화면 캡처는 Microsoft dot 웹 리소스를 이동 하는 권장 단계를 보여 줍니다.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>이동 지원

이동할 수 있는 리소스 App Service를 확인 하려면 다음에 대 한 이동 지원 상태를 참조 하세요.

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.
