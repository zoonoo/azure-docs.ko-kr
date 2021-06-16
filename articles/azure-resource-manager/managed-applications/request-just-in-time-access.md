---
title: Just-In-Time 액세스 요청
description: Azure Managed Applications의 게시자가 관리형 애플리케이션에 대한 Just-In-Time 액세스를 요청하는 방법을 설명합니다.
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: ae7c4e61107518676b2cc8308904816d075ecf7e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951495"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure Managed Applications의 Just-In-Time 액세스 사용 및 요청

관리형 애플리케이션의 소비자는 관리되는 리소스 그룹에 대한 영구 액세스 권한을 부여하는 것을 꺼릴 수 있습니다. 관리형 애플리케이션의 게시자는 관리되는 리소스에 액세스해야 하는 경우 소비자에게 정확하게 알려 주는 것이 좋습니다. 소비자가 관리되는 리소스에 대한 액세스 권한 부여를 더욱 강력하게 제어할 수 있도록 하기 위해 Azure Managed Applications는 JIT(Just-In-Time) 액세스라는 기능을 제공합니다. 이 기능은 현재 미리 보기로 제공됩니다.

JIT 액세스를 사용하면 문제 해결 또는 유지 관리를 위해 관리형 애플리케이션의 리소스에 대한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대한 읽기 전용 액세스 권한을 가지지만 특정 기간 동안 더 큰 액세스 권한을 가질 수 있습니다.

권한 부여 워크플로는 다음과 같습니다.

1. 게시자가 Marketplace에 관리형 애플리케이션을 추가하고 JIT 액세스를 사용할 수 있도록 지정합니다.

1. 배포하는 동안 소비자가 관리형 애플리케이션의 해당 인스턴스에 대한 JIT 액세스를 사용하도록 설정합니다.

1. 배포 후 소비자가 JIT 액세스에 대한 설정을 변경할 수 있습니다.

1. 게시자가 관리되는 리소스를 업데이트하거나 관련 문제를 해결해야 하는 경우 액세스 요청을 보냅니다.

1. 소비자가 게시자의 요청을 승인합니다.

이 문서에서는 게시자가 JIT 액세스를 사용하도록 설정하고 요청을 제출하기 위해 수행하는 작업에 대해 중점적으로 다룹니다. JIT 액세스 요청 승인에 대해 알아보려면 [Azure Managed Applications의 Just-In-Time 액세스 승인](approve-just-in-time-access.md)을 참조하세요.

## <a name="add-jit-access-step-to-ui"></a>UI에 JIT 액세스 단계 추가

CreateUiDefinition.json 파일에는 소비자가 JIT 액세스를 사용하도록 설정하는 단계가 포함되어 있습니다. 제품에 대한 JIT 기능을 지원하려면 CreateUiDefinition.json 파일에 다음 콘텐츠를 추가합니다.

"단계":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```

"출력":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT 액세스는 미리 보기 상태입니다. JIT 구성의 스키마는 향후 반복 시 변경될 수 있습니다.

## <a name="enable-jit-access"></a>JIT 액세스 사용

파트너 센터에서 제품을 만들 때 JIT 액세스를 사용하도록 설정해야 합니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)의 상업용 Marketplace 포털에 로그인합니다.

1. 새 관리형 애플리케이션을 만드는 방법은 [Azure 애플리케이션 제품 만들기](../../marketplace/azure-app-offer-setup.md)의 단계를 따르세요.

1. **기술 구성** 페이지에서 **JIT(Just-In-Time) 액세스 사용** 확인란을 선택합니다.

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="Just-In-Time VM 액세스 사용":::

게시자는 UI에 JIT 구성 단계를 추가하고 상업용 Marketplace 제품에서 JIT 액세스를 사용하도록 설정했습니다. 소비자는 관리형 애플리케이션을 배포할 때 [해당 인스턴스에 대한 JIT 액세스를 설정](approve-just-in-time-access.md#enable-during-deployment)할 수 있습니다.

## <a name="request-access"></a>액세스 요청

소비자의 관리되는 리소스에 액세스해야 하는 경우 특정 역할, 시간 및 기간에 대한 요청을 보냅니다. 그런 다음, 소비자가 요청을 승인해야 합니다.

JIT 액세스 요청을 보내려면 다음을 수행합니다.

1. 액세스해야 하는 관리형 애플리케이션에 대한 **JIT 액세스** 를 선택합니다.

1. **적격 역할** 을 선택하고 작업 열에서 원하는 역할에 **활성화** 를 선택합니다.

   ![액세스 요청 활성화](./media/request-just-in-time-access/send-request.png)

1. **역할 활성화** 양식에서 역할이 활성화될 시작 시간 및 기간을 선택합니다. **활성화** 를 선택하여 요청을 보냅니다.

   ![액세스 활성화](./media/request-just-in-time-access/activate-access.png) 

1. 알림을 보고 새 JIT 요청이 소비자에게 성공적으로 전송되었는지 확인합니다.

   ![알림](./media/request-just-in-time-access/in-progress.png)

   이제 소비자가 [요청을 승인](approve-just-in-time-access.md#approve-requests)할 때까지 기다려야 합니다.

1. 관리형 애플리케이션에 대한 모든 JIT 요청의 상태를 보려면 **JIT 액세스** 및 **요청 기록** 을 선택합니다.

   ![상태 보기](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>알려진 문제

JIT 액세스를 요청하는 계정의 보안 주체 ID는 관리형 애플리케이션 정의에 명시적으로 포함해야 합니다. 계정은 패키지에 지정된 그룹을 통해 포함할 수 없습니다. 이 제한 사항은 향후 릴리스에서 수정될 예정입니다.

## <a name="next-steps"></a>다음 단계

JIT 액세스 요청 승인에 대해 알아보려면 [Azure Managed Applications의 Just-In-Time 액세스 승인](approve-just-in-time-access.md)을 참조하세요.