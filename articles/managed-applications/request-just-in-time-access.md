---
title: Azure Managed Applications에 대 한 설정 및 요청-just-in-time 액세스
description: Azure Managed Applications의 게시자에서 관리 되는 응용 프로그램에 just in time 액세스를 요청 하는 방법을 설명 합니다.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481776"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Azure Managed Applications에 대 한 설정 및 요청-just-in-time 액세스

관리 되는 응용 프로그램의 소비자는 관리 되는 리소스 그룹에 영구 액세스 권한을 부여할 것을 꺼릴 수 있습니다. 관리자 응용 프로그램의 게시자로 소비자가 관리 되는 리소스에 액세스 해야 하는 경우 알고 있다고 수도 있습니다. 소비자에 게 관리 되는 리소스를 Azure Managed Applications에 대 한 액세스 권한을 부여 하는 보다 강력한 제어가 현재 미리 보기 상태인-just-in-time (JIT) 액세스 라는 기능을 제공 합니다.

JIT 액세스를 사용 하면 문제 해결 또는 유지 관리에 대 한 관리 되는 응용 프로그램의 리소스에 대 한 관리자 권한 액세스를 요청할 수 있습니다. 항상 리소스를 읽기 전용 권한을 갖지만 특정 기간에 대 한 더 큰 액세스 할 수 있습니다.

액세스 권한 부여의 작업 흐름은 다음과 같습니다.

1. Marketplace에 관리 되는 응용 프로그램을 추가 하 고이 정보를 JIT 액세스를 사용할 수 있는지를 지정 합니다.

1. 배포 하는 동안 소비자에 관리 되는 응용 프로그램의 해당 인스턴스에 대 한 JIT 액세스할을 수 있습니다.

1. 배포 후 소비자 JIT 액세스에 대 한 설정을 변경할 수 있습니다.

1. 문제를 해결 하거나 관리 되는 리소스를 업데이트 해야 할 때 액세스에 대 한 요청을 보냅니다.

1. 소비자가 사용자 요청을 승인 합니다.

이 문서에서는 게시자 JIT 액세스를 사용 하도록 설정 하 고 요청을 제출 하기 위해 수행할 작업에 중점을 둡니다. JIT 액세스 요청을 승인 하는 방법에 대 한 자세한 참조 [Azure Managed Applications의 just in time 액세스 승인](approve-just-in-time-access.md)합니다.

## <a name="add-jit-access-step-to-ui"></a>UI에 JIT 액세스 단계를 추가 합니다.

CreateUiDefinition.json 파일이 비슷합니다 정확 하 게 영구 액세스를 위해 만든 UI 파일 JIT 액세스를 사용 하도록 설정 하는 소비자를 수 있는 단계를 포함 해야 합니다. Azure Marketplace에 제품 첫 번째 관리 되는 응용 프로그램을 게시 하는 방법에 대 한 자세한 내용은 참조 하세요 [Marketplace에서 Azure Managed Applications](publish-marketplace-app.md)합니다.

제품에 대 한 JIT 기능을 지원 하려면 CreateUiDefinition.json 파일에 다음 내용을 추가 합니다.

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
 
"outputs":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> JIT 액세스 미리 보기입니다. JIT 구성에 대 한 스키마는 나중에 반복 변경할 수 없습니다.

## <a name="enable-jit-access"></a>JIT 액세스를 사용 하도록 설정

Marketplace에서 귀하의 제품을 정의할 때 JIT 액세스를 사용 하도록 설정 해야 합니다.

1. 에 로그인 합니다 [클라우드 파트너 게시 포털](https://cloudpartner.azure.com)합니다.

1. Marketplace에서 관리 되는 응용 프로그램을 게시 하는 값을 제공 합니다. 선택 **Yes** 에 대 한 **JIT 액세스를 사용 하도록 설정?**

   ![Just-In-Time 액세스 사용](./media/request-just-in-time-access/marketplace-enable.png)

Ui를 JIT 구성 단계를 추가 하 고 marketplace 제품에 대 한 JIT 액세스를 사용 하도록 설정 합니다. 소비자가 관리 되는 응용 프로그램을 배포 하는 경우 가능 [해당 인스턴스에 대 한 JIT 액세스를 켤](approve-just-in-time-access.md#enable-during-deployment)합니다.

## <a name="request-access"></a>액세스 요청

소비자의 관리 되는 리소스에 액세스 해야 할 때 특정 역할, 시간 및 기간에 대 한 요청을 보냅니다. 소비자는 다음 요청을 승인 해야 합니다.

JIT 액세스 요청을 보내야 합니다.

1. 선택 **JIT 액세스** 관리 되는 응용 프로그램에 액세스 해야 합니다.

1. 선택 **적격 역할**, 선택한 **활성화** 원하는 역할에 대 한 작업 열에서.

   ![액세스 권한 요청 활성화](./media/request-just-in-time-access/send-request.png)

1. 에 **역할 활성화** 양식에서 시작 시간 및 활성 역할에 대 한 기간을 선택 합니다. 선택 **활성화** 여 요청을 보냅니다.

   ![액세스 활성화](./media/request-just-in-time-access/activate-access.png) 

1. 소비자에 게 성공적으로 새 JIT 요청을 보냈음을 확인 하려면 알림을 봅니다.

   ![알림](./media/request-just-in-time-access/in-progress.png)

   이제 소비자에 대 한 기다려야 [요청을 승인할](approve-just-in-time-access.md#approve-requests)합니다.

1. 관리 응용 프로그램에 대 한 모든 JIT 요청의 상태를 보려면 선택 **JIT 액세스** 하 고 **요청 기록**합니다.

   ![상태 보기](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>알려진 문제

관리 되는 응용 프로그램 정의 JIT 액세스를 요청 하는 계정 보안 주체 ID는 명시적으로 포함 되어야 합니다. 계정을 포함 된 패키지에 지정 된 그룹을 통해 수 없습니다. 이 제한은 향후 릴리스에서 수정 될 예정입니다.

## <a name="next-steps"></a>다음 단계

JIT 액세스에 대 한 요청을 승인 하는 방법에 대 한 자세한 참조 [Azure Managed Applications의 just in time 액세스 승인](approve-just-in-time-access.md)합니다.
