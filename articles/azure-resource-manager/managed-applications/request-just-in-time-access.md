---
title: Just-In-Time 액세스 요청
description: Azure 관리되는 응용 프로그램의 게시자가 관리되는 응용 프로그램에 대한 적시에 액세스를 요청하는 방법을 설명합니다.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651385"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure 관리형 응용 프로그램에 대한 적시 액세스 활성화 및 요청

관리되는 응용 프로그램의 소비자는 관리되는 리소스 그룹에 대한 영구적인 액세스 권한을 부여하는 것을 꺼릴 수 있습니다. 관리자 응용 프로그램의 게시자는 소비자가 관리되는 리소스에 액세스해야 하는 시기를 정확히 알고 있는 것을 선호할 수 있습니다. 소비자에게 관리되는 리소스에 대한 액세스 권한을 부여하는 것을 보다 세중하게 제어할 수 있도록 Azure Managed Application은 현재 미리 보기 중인 JIT(적시) 액세스라는 기능을 제공합니다.

JIT 액세스를 사용하면 문제 해결 또는 유지 관리를 위해 관리되는 응용 프로그램의 리소스에 대한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대한 읽기 전용 액세스 권한이 있지만 특정 기간 동안 더 많은 액세스 권한을 가질 수 있습니다.

액세스 권한을 부여하기 위한 작업 흐름은 다음과 입니다.

1. 마켓플레이스에 관리되는 응용 프로그램을 추가하고 JIT 액세스를 사용할 수 있도록 지정합니다.

1. 배포 하는 동안 소비자는 관리 되는 응용 프로그램의 해당 인스턴스에 대 한 JIT 액세스를 사용 합니다.

1. 배포 후 소비자는 JIT 액세스에 대한 설정을 변경할 수 있습니다.

1. 관리되는 리소스를 트러블슈팅하거나 업데이트해야 할 때 액세스 요청을 보냅니다.

1. 소비자가 요청을 승인합니다.

이 문서에서는 게시자가 JIT 액세스 및 요청을 제출할 수 있도록 하기 위해 취하는 작업에 중점을 둡니다. JIT 액세스 요청 승인에 대한 자세한 내용은 [Azure 관리형 응용 프로그램에서 적시 액세스 승인 을](approve-just-in-time-access.md)참조하십시오.

## <a name="add-jit-access-step-to-ui"></a>UI에 JIT 액세스 단계 추가

CreateUiDefinition.json 파일은 소비자가 JIT 액세스를 활성화할 수 있는 단계를 포함해야 하는 경우를 제외하고 영구 액세스를 위해 만든 UI 파일과 정확히 같습니다. Azure 마켓플레이스에서 첫 번째 관리되는 응용 프로그램 제공을 게시하는 방법에 대한 자세한 내용은 [마켓플레이스의 Azure 관리 응용 프로그램 참조를](publish-marketplace-app.md)참조하세요.

오퍼에 대한 JIT 기능을 지원하려면 CreateUiDefinition.json 파일에 다음 콘텐츠를 추가합니다.

"단계"에서:

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
 
"출력"에서:

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT 액세스가 미리 보기상태입니다. JIT 구성에 대한 스키마는 향후 반복에서 변경될 수 있습니다.

## <a name="enable-jit-access"></a>JIT 액세스 사용

마켓플레이스에서 제품을 정의할 때JIT 액세스를 사용하도록 설정해야 합니다.

1. 클라우드 파트너 [게시 포털에](https://cloudpartner.azure.com)로그인합니다.

1. 마켓플레이스에 관리되는 응용 프로그램을 게시할 값을 제공합니다. **JIT 액세스 사용에** 대한 **예선택을** 선택합니까?

   ![Just-In-Time VM 액세스 사용](./media/request-just-in-time-access/marketplace-enable.png)

UI에 JIT 구성 단계를 추가하고 마켓플레이스 오퍼링에서 JIT 액세스를 사용하도록 설정했습니다. 소비자가 관리되는 응용 프로그램을 배포할 때 [인스턴스에 대한 JIT 액세스를 켤](approve-just-in-time-access.md#enable-during-deployment)수 있습니다.

## <a name="request-access"></a>액세스 요청

소비자의 관리되는 리소스에 액세스해야 하는 경우 특정 역할, 시간 및 기간에 대한 요청을 보냅니다. 그러면 소비자가 요청을 승인해야 합니다.

JIT 액세스 요청을 보내려면 다음을 수행하십시오.

1. 액세스해야 하는 관리되는 응용 프로그램에 대한 **JIT 액세스를** 선택합니다.

1. **적격 역할을**선택하고 원하는 역할에 대한 작업 열에서 **활성화를** 선택합니다.

   ![액세스 요청 활성화](./media/request-just-in-time-access/send-request.png)

1. 역할 **활성화** 양식에서 역할이 활성화될 시작 시간과 기간을 선택합니다. 요청을 보내려면 **활성화를** 선택합니다.

   ![액세스 활성화](./media/request-just-in-time-access/activate-access.png) 

1. 알림을 보고 새 JIT 요청이 소비자에게 성공적으로 전송되는지 확인합니다.

   ![알림](./media/request-just-in-time-access/in-progress.png)

   이제 소비자가 [요청을 승인할](approve-just-in-time-access.md#approve-requests)때까지 기다려야 합니다.

1. 관리되는 응용 프로그램에 대한 모든 JIT 요청의 상태를 보려면 **JIT 액세스** 및 **요청 기록을**선택합니다.

   ![상태 보기](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>알려진 문제

JIT 액세스를 요청하는 계정의 주 ID는 관리되는 응용 프로그램 정의에 명시적으로 포함되어야 합니다. 계정은 패키지에 지정된 그룹을 통해서만 포함할 수 없습니다. 이 제한은 향후 릴리스에서 수정될 예정입니다.

## <a name="next-steps"></a>다음 단계

JIT 액세스에 대한 요청 승인에 대한 자세한 내용은 [Azure 관리형 응용 프로그램에서 적시 액세스 승인 을 참조합니다.](approve-just-in-time-access.md)
