---
title: Azure DevTest 랩에서 자동 종료 정책 관리 | 마이크로 소프트 문서
description: 가상 시스템이 사용되지 않을 때 자동으로 종료되도록 랩에 대한 자동 종료 정책을 설정하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: spelluru
ms.openlocfilehash: a2d0b9bdfba1b96ad42e45d54faf106b2361e29d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264793"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest 랩에서 랩용 자동 종료 구성 및 가상 컴퓨터 계산

이 문서에서는 DevTest Labs에서 랩 VM에 대한 자동 종료 설정을 구성하고 VM을 계산하는 방법을 설명합니다. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>랩 VM에 대한 자동 종료 구성(개발자 테스트 랩)
Azure DevTest Labs를 통해 각 랩에 대한 정책(설정)을 관리하여 비용을 제어하고 랩에서의 낭비를 최소화할 수 있습니다. 이 문서에서는 랩 계정에 대한 자동 종료 정책을 구성하고 랩 계정의 랩에 대한 자동 종료 설정을 구성하는 방법을 보여 주며 이 문서에서는 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.  

### <a name="set-auto-shut-down-policy-for-a-lab"></a>랩에 대한 자동 종료 정책 설정
랩 소유자로서 랩에서 모든 VM에 대한 종료 일정을 구성할 수 있습니다. 이 작업을 수행하면 사용되지 않는(유휴 상태) 머신의 실행 비용을 절약할 수 있습니다. 모든 랩 VM에서 중앙 집중식으로 종료 정책을 적용할 수 있을 뿐만 아니라 랩 사용자가 개별 머신에 대한 일정을 설정하는 수고를 줄일 수도 있습니다. 이 기능을 사용하면 랩 사용자에게 모든 권한에 대한 권한 없음 제공에서 시작하는 랩 일정에 대한 정책을 설정할 수 있습니다. 랩 소유자로서 다음 단계를 수행하여 이 정책을 구성할 수 있습니다.

1. 랩의 홈 페이지에서 **구성 및 정책**을 선택합니다.
2. 왼쪽 메뉴의 **일정** 섹션에서 **자동 종료 정책**을 선택합니다.
3. 옵션 중 하나를 선택합니다. 다음 섹션에서는 이러한 옵션에 대한 자세한 정보를 제공합니다. 설정 정책은 기존 VM이 아닌 랩에서 생성된 새 VM에만 적용됩니다. 

    ![정책 옵션 자동 종료](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>자동 종료 설정 구성
자동 종료 정책은 이 랩의 VM이 종료되는 시간을 지정하여 랩 낭비를 최소화하는 데 도움이 됩니다.

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. **모든 서비스를**선택한 다음 목록에서 **DevTest 랩을 선택합니다.**
3. 랩 목록에서 원하는 랩을 탭합니다.   
4. **구성 및 정책**을 선택합니다.

    ![정책 설정 창](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 랩의 구성 **및 정책** 창에서 일정 아래에서 **자동 종료를** **선택합니다.**
   
    ![자동 종료](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.
7. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 시간(및 표준 시간대)을 지정합니다.
8. 지정된 자동 종료 시간 30분 전에 알림을 보낼 수 있는 옵션의 **예** 또는 **아니요를** 지정합니다. **예**를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 [알림](#notifications) 섹션을 참조하세요. 
9. **저장**을 선택합니다.

    기본적으로 이 정책을 사용하도록 설정하면 현재 랩의 모든 VM에 적용됩니다. 특정 VM에서 이 설정을 제거하려면 VM의 관리 창을 열고 **자동 종료** 설정을 변경합니다.
    
> [!NOTE]
> 현재 예약된 시간의 30분 이내에 랩 또는 특정 랩 가상 시스템의 자동 종료 일정을 업데이트하면 업데이트된 종료 시간이 다음 날 일정에 적용됩니다. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>사용자가 일정을 설정하고 옵트아웃할 수 있음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 있습니다. 이 옵션은 랩 사용자에게 해당 VM의 자동 종료 일정에 대한 모든 권한을 부여합니다. 랩 사용자는 해당 VM 자동 종료 일정 페이지에 변경 내용이 표시되지 않습니다.

![자동 종료 정책 옵션 - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>사용자가 일정을 설정하지만 옵트아웃할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의할 수 있습니다. 그러나 자동 종료 정책을 옵트아웃할 수 없습니다. 이 옵션을 통해 랩의 모든 머신이 자동 종료 일정에 포함됩니다. 랩 사용자는 해당 VM의 자동 종료 일정을 업데이트하고, 종료 알림을 설정할 수 있습니다.

![자동 종료 정책 옵션 - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>랩 관리자가 설정한 일정을 사용자가 제어할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 없습니다. 이 옵션은 랩 관리자에게 랩의 모든 머신에 대한 일정의 완전한 권한을 제공합니다. 랩 사용자는 해당 VM에 대한 자동 종료 알림만 설정할 수 있습니다.

![자동 종료 정책 옵션 - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>공지
랩 소유자가 자동 종료를 설정하면 VM이 영향을 받는 경우 자동 종료가 트리거되기 30분 전에 랩 사용자에게 알림이 전송됩니다. 이 옵션을 사용하면 랩 사용자가 종료 전에 작업을 저장할 수 있습니다. 또한 알림은 다음 작업에 대한 각 VM에 대한 링크를 제공합니다.

- 이 시간 동안 자동 종료 건너뛰기
- VM에서 계속 작업할 수 있도록 1시간 또는 2시간 동안 자동 종료를 일시 중지합니다.

알림은 구성된 웹 후크 끝점 또는 자동 종료 설정에서 랩 소유자가 지정한 전자 메일 주소를 통해 전송됩니다. Webhook을 사용하면 특정 이벤트를 구독하는 통합을 빌드하거나 설정할 수 있습니다. 이러한 이벤트 중 하나가 트리거되면 DevTest 연구소는 HTTP POST 페이로드를 웹후크의 구성된 URL로 보냅니다. 웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-create-a-web-hook-or-api-function.md)를 참조하세요. 

웹 후크는 다양한 앱(예: Slack, Azure Logic Apps 등)에서 광범위하게 지원되며 알림을 보내는 고유한 방법을 구현할 수 있으므로 웹 후크를 사용하는 것이 좋습니다. 예를 들어 이 문서에서는 Azure Logic Apps를 사용하여 전자 메일에서 자동 종료 알림을 받는 방법을 안내합니다. 먼저 랩에서 자동 종료 알림을 사용하도록 설정하는 기본 단계를 신속하게 살펴보겠습니다.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>이메일 알림을 받는 논리 앱 만들기
[Azure Logic Apps는](../logic-apps/logic-apps-overview.md) Office 365 및 twitter와 같은 다른 클라이언트와 서비스를 쉽게 통합할 수 있는 많은 기본 커넥터를 제공합니다. 높은 수준에서 전자 메일 알림에 대한 논리 앱을 설정하는 단계는 다음 네 단계로 나눌 수 있습니다. 

- 논리 앱을 만듭니다. 
- 기본 제공 템플릿을 구성합니다.
- 이메일 클라이언트와 통합
- 웹후크 URL을 가져옵니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
시작하려면 다음 단계를 사용하여 Azure 구독에서 논리 앱을 만듭니다.

1. + 왼쪽 메뉴에서 **리소스 만들기를** 선택하고 **통합을**선택하고 **논리 앱을**선택합니다. 

    ![새 논리 앱 메뉴](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 논리 앱 - 페이지 **만들기에서** 다음 단계를 수행합니다. 
    1. 논리 앱의 **이름을** 입력합니다.
    2. Azure **구독을**선택합니다.
    3. 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 선택합니다. 
    4. 논리 앱의 **위치를** 선택합니다. 

        ![새로운 논리 응용 프로그램 - 설정](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. **알림에서**알림의 **리소스로 이동을** 선택합니다. 

    ![리소스로 이동](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 배포 도구 범주에서 **논리 앱 디자이너를** **선택합니다.**

    ![HTTP 요청/응답 선택](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. HTTP **요청-응답** 페이지에서 **이 템플릿 사용을**선택합니다. 

    ![이 템플릿 사용 옵션 선택](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 다음 JSON을 **요청 본문 JSON 스키마** 섹션으로 복사합니다. 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![요청 바디 JSON 스키마](./media/devtest-lab-auto-shutdown/request-json.png)
7. 디자이너에서 **+ 새 단계를** 선택하고 다음 단계를 따릅니다.
    1. Office **365 Outlook**검색 - 전자 메일 보내기 . 
    2. 작업에서 **전자 메일 보내기를** **선택합니다.** 
    
        ![이메일 보내기 옵션](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 이메일 계정에 로그인하려면 **로그인을** 선택합니다. 
    4. **TO** 필드를 선택하고 소유자를 선택합니다.
    5. **제목을**선택하고 전자 메일 알림의 제목을 입력합니다. 예를 들어 " 랩용 컴퓨터 vmName 종료: labName."
    6. **BODY를**선택하고 전자 메일 알림의 본문 내용을 정의합니다. 예를 들어 " vmName은 15분 안에 종료될 예정입니다. URL을 클릭하여 이 종료를 건너뜁니다. 한 시간 동안 종료 지연: delayUrl60. 2 시간 동안 종료 지연: delayUrl120."

        ![요청 바디 JSON 스키마](./media/devtest-lab-auto-shutdown/email-options.png)
1. 도구 모음에서 **저장**을 선택합니다. 이제 HTTP POST **URL을**복사할 수 있습니다. 복사 단추를 선택하여 URL을 클립보드에 복사합니다. 

    ![웹후크 URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>VM 계산을 위한 자동 종료 구성

1. 가상 **컴퓨터** 페이지에서 왼쪽 메뉴에서 **자동 종료를** 선택합니다. 
2. 자동 **종료** 페이지에서 이 정책을 활성화하려면 **켜기를** 선택하고 **끄기를** 선택하여 사용하지 않도록 설정합니다.
3. 이 정책을 사용하도록 설정하면 VM을 종료할 **시간(및** **표준 시간대)을**지정합니다.
4. 지정된 자동 종료 시간 30분 전에 알림을 보낼 수 있는 옵션의 **예** 또는 **아니요를** 지정합니다. **예**를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 [알림](#notifications) 섹션을 참조하세요. 
9. **저장**을 선택합니다.

    ![계산 VM에 대한 자동 종료 구성](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

## <a name="next-steps"></a>다음 단계
모든 정책을 설정하는 방법에 대해 알아보려면 [Azure DevTest Labs의 랩 정책 정의를](devtest-lab-set-lab-policy.md)참조하십시오.

