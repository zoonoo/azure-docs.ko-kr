---
title: Azure App Service 리소스를 새 구독 또는 리소스 그룹 이동
description: Azure Resource Manager를 사용 하 여 App Service 리소스를 새 리소스 그룹 또는 구독으로 이동 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723572"
---
# <a name="move-guidance-for-app-service-resources"></a>App Service 리소스에 대 한 지침을 이동

다른 App Service 리소스를 이동 하는 단계 구독 내 또는 새 구독으로 리소스를 이동 하는 여부를 기반으로 합니다.

## <a name="move-in-same-subscription"></a>동일한 구독에서 이동

‘동일한 구독 내’에서 웹앱을 이동할 경우 타사 SSL 인증서는 이동할 수 없습니다.  그러나 타사 인증서를 이동하지 않고 웹앱을 새 리소스 그룹으로 이동할 수 있으며 앱의 SSL 기능도 계속 작동합니다.

Web App을 사용하여 SSL 인증서를 이동하려면 다음 단계를 수행합니다.

1. 웹앱에서 타사 인증서를 삭제하지만 인증서 복사본 유지
2. Web App을 이동합니다.
3. 이동된 웹앱에 타사 인증서를 업로드합니다.

## <a name="move-across-subscriptions"></a>구독 간 이동

_구독 간에_ Web App을 이동할 때 적용되는 제한 사항은 다음과 같습니다.

- 대상 리소스 그룹에 기존 App Service 리소스가 없어야 합니다. App Service 리소스에는 다음이 포함됩니다.
    - Web Apps
    - App Service 계획
    - 업로드되거나 가져온 SSL 인증서
    - App Service Environment
- 리소스 그룹의 모든 App Service 리소스는 함께 이동해야 합니다.
- App Service 리소스는 처음 만들었던 리소스 그룹에서만 이동할 수 있습니다. App Service 리소스를 더 이상 없는 원래의 리소스 그룹에서 원래 리소스 그룹에이 다시 이동 합니다. 그런 다음 구독 간에 리소스를 이동 합니다.

원래 리소스 그룹을 기억 하지 못하는 경우에 진단을 통해 찾을 수 있습니다. 웹 앱 선택 **진단 및 문제 해결**합니다. 그런 다음 선택 **구성 및 관리**합니다.

![진단 선택](./media/app-service-move-limitations/select-diagnostics.png)

선택 **마이그레이션 옵션**합니다.

![마이그레이션 옵션을 선택 합니다.](./media/app-service-move-limitations/select-migration.png)

웹 앱을 이동 하는 권장된 단계에 대 한 옵션을 선택 합니다.

![권장된 단계를 선택 합니다.](./media/app-service-move-limitations/recommended-steps.png)

리소스를 이동 하기 전에 수행할 권장된 작업을 표시 합니다. 정보는 웹 앱에 대 한 원래 리소스 그룹을 포함합니다.

![권장 사항](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>App Service Certificate를 이동 합니다.

App Service Certificate를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다. App Service Certificate가 웹앱에 바인딩되어 있으면 리소스를 새 구독으로 이동하기 전에 몇 가지 단계를 수행해야 합니다. 리소스를 이동하기 전에 웹앱에서 SSL 바인딩 및 프라이빗 인증서를 삭제합니다. App Service Certificate를 삭제할 필요가 없고 웹앱의 프라이빗 인증서만 삭제하면 됩니다.

## <a name="move-support"></a>이동 지원

App Service 리소스를 이동할 수를 확인 하려면 참조에 대 한 지원 상태를 이동 합니다.

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../resource-group-move-resources.md)을 참조하세요.
