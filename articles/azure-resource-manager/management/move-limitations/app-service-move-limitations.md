---
title: Azure App Service 리소스 이동
description: Azure Resource Manager를 사용하여 App Service 리소스를 새 리소스 그룹이나 구독으로 이동할 수 있습니다.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531375"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service 리소스 이동 지침

이 문서에서는 App Service 리소스를 이동하는 단계를 설명합니다. App Service 리소스를 새 구독으로 이동하기 위한 특정 요구 사항이 있습니다.

## <a name="move-across-subscriptions"></a>구독 간 이동

구독 간에 웹앱을 이동하는 경우 다음 지침이 적용됩니다.

- 대상 리소스 그룹에 기존 App Service 리소스가 없어야 합니다. App Service 리소스에는 다음이 포함됩니다.
    - Web Apps
    - App Service 계획
    - 업로드되었거나 가져온 TLS/SSL 인증서
    - App Service Environment
- 리소스 그룹의 모든 App Service 리소스는 함께 이동해야 합니다.
- App Service Environment는 새 리소스 그룹이나 구독으로 이동할 수 없습니다. 그러나 App Service Environment를 이동하지 않고 웹앱 및 앱 서비스 요금제를 새 구독으로 이동할 수 있습니다. 이동 후에는 웹앱이 더 이상 App Service Environment에서 호스트되지 않습니다.
- 리소스 그룹의 다른 모든 리소스와 함께 인증서를 이동하기만 하면 TLS 바인딩을 삭제하지 않고 웹에 바인딩된 인증서를 이동할 수 있습니다.
- App Service 리소스는 처음 만들었던 리소스 그룹에서만 이동할 수 있습니다. App Service 리소스가 원래 리소스 그룹에 더 이상 없는 경우 원래 리소스 그룹으로 다시 이동합니다. 그런 다음 구독 간에 리소스를 이동합니다.

원래 리소스 그룹이 기억나지 않으면 진단을 통해 찾을 수 있습니다. 웹앱의 경우 **문제 진단 및 해결** 을 선택합니다. 그런 다음 **구성 및 관리** 를 선택합니다.

![진단 선택](./media/app-service-move-limitations/select-diagnostics.png)

**마이그레이션 옵션** 을 선택합니다.

![마이그레이션 옵션 선택](./media/app-service-move-limitations/select-migration.png)

웹앱을 이동하기 위한 권장 단계 옵션을 선택합니다.

![권장 단계 선택](./media/app-service-move-limitations/recommended-steps.png)

리소스를 이동하기 전에 수행해야 하는 권장 작업이 표시됩니다. 정보에는 웹앱의 원래 리소스 그룹이 포함됩니다.

![Microsoft dot 웹 리소스를 이동하기 위한 권장 단계 화면 캡처](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>이동 지원

이동할 수 있는 App Service 리소스를 확인하려면 다음 항목의 이동 지원 상태를 참조하세요.

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.
