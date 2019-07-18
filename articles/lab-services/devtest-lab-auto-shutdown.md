---
title: Azure DevTest Labs에서 자동 종료 정책 관리 | Microsoft Docs
description: 사용에서 하지 않은 경우 가상 컴퓨터 종료 자동으로 랩에 대 한 자동 종료 정책을 설정 하는 방법에 알아봅니다.
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873992"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 대 한 자동 종료 정책 관리
Azure DevTest Labs를 통해 각 랩에 대한 정책(설정)을 관리하여 비용을 제어하고 랩에서의 낭비를 최소화할 수 있습니다. 이 문서에서는 랩 계정에 대 한 자동 종료 정책을 구성 하 여 랩 계정에 랩에 대 한 자동 종료 설정을 구성 하는 방법을 보여 줍니다. 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>랩에 대 한 자동 종료 정책 설정
랩 소유자로서 랩에서 모든 VM에 대한 종료 일정을 구성할 수 있습니다. 이 작업을 수행하면 사용되지 않는(유휴 상태) 머신의 실행 비용을 절약할 수 있습니다. 모든 랩 VM에서 중앙 집중식으로 종료 정책을 적용할 수 있을 뿐만 아니라 랩 사용자가 개별 머신에 대한 일정을 설정하는 수고를 줄일 수도 있습니다. 이 기능을 사용하면 랩 사용자에게 모든 권한에 대한 권한 없음 제공에서 시작하는 랩 일정에 대한 정책을 설정할 수 있습니다. 랩 소유자로서 다음 단계를 수행하여 이 정책을 구성할 수 있습니다.

1. 랩의 홈 페이지에서 **구성 및 정책**을 선택합니다.
2. 왼쪽 메뉴의 **일정** 섹션에서 **자동 종료 정책**을 선택합니다.
3. 옵션 중 하나를 선택합니다. 다음 섹션에서는 이러한 옵션에 대 한 자세한 정보를 제공 합니다. 정책 설정에는 기존 Vm 아니라 lab에서 생성 하는 새 Vm에만 적용 됩니다. 

    ![자동 종료 정책 옵션](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>자동 종료 설정을 구성 합니다.
자동 종료 정책이이 랩의이 Vm이 종료 시간을 지정할 수 있도록 하 여 랩 낭비를 최소화할 수 있습니다.

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.   
4. **구성 및 정책**을 선택합니다.

    ![정책 설정 창](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 랩의 **구성 및 정책** 창 **자동 종료** 아래의 **일정**합니다.
   
    ![자동 종료](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.
7. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 시간(및 표준 시간대)을 지정합니다.
8. 지정할 **예** 또는 **No** 보낼 알림을 지정 된 자동 종료 하기 전에 15 분 옵션에 대 한 합니다. **예**를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 참조는 [알림을](#notifications) 섹션입니다. 
9. **저장**을 선택합니다.

    기본적으로 이 정책을 사용하도록 설정하면 현재 랩의 모든 VM에 적용됩니다. 이 설정을 특정 VM에서 제거 하려면 VM의 관리 창을 열고 변경 해당 **자동 종료** 설정 합니다.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>사용자가 일정을 설정하고 옵트아웃할 수 있음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 있습니다. 이 옵션은 랩 사용자에게 해당 VM의 자동 종료 일정에 대한 모든 권한을 부여합니다. 랩 사용자는 해당 VM 자동 종료 일정 페이지에 변경 내용이 표시되지 않습니다.

![자동 종료 정책 옵션-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>사용자가 일정을 설정하지만 옵트아웃할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의할 수 있습니다. 그러나 자동 종료 정책을 옵트아웃할 수 없습니다. 이 옵션을 통해 랩의 모든 머신이 자동 종료 일정에 포함됩니다. 랩 사용자는 해당 VM의 자동 종료 일정을 업데이트하고, 종료 알림을 설정할 수 있습니다.

![자동 종료 정책 옵션-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>랩 관리자가 설정한 일정을 사용자가 제어할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 없습니다. 이 옵션은 랩 관리자에게 랩의 모든 머신에 대한 일정의 완전한 권한을 제공합니다. 랩 사용자는 해당 VM에 대한 자동 종료 알림만 설정할 수 있습니다.

![자동 종료 정책 옵션-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>알림
랩 소유자가 설정 하는 자동 종료를 경고를 보낼 랩 사용자에 게 해당 Vm의 경우 트리거되는 자동 종료 하기 전에 15 분 후 영향을 받습니다. 이 옵션 랩 사용자가 종료 전에 작업을 저장할 수 있는 기회를 제공 합니다. 또한 알림을 다음 작업에 대해 각 VM에 대 한 링크를 제공 합니다.

- 이 이번에는 자동 종료를 건너뛰고
- VM에서 작업 하 수 유지 되도록는 자동 종료 시간에 2 시간 동안 다시 알림.

알림이 구성 된 웹 후크 끝점을 통해 전송 됩니다 또는 랩 소유자는 자동 종료 설정에서 전자 메일 주소를 지정 합니다. 웹 후크를 사용 하면 빌드 또는 특정 이벤트를 구독 하는 통합을 설정할 수 있습니다. 이러한 이벤트 중 하나가 트리거될 때 DevTest Labs는 HTTP POST의 페이로드는 웹 후크 구성 된 URL을 보냅니다. 웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-create-a-web-hook-or-api-function.md)를 참조하세요. 

다양 한 앱 (예를 들어, Slack, Azure Logic Apps 및 등)에서 지 원하는 광범위 하 게 하는 알림을 보내기 위한 고유한 방식으로 구현할 수 있기 때문에 웹 후크를 사용 하는 것이 좋습니다. 예를 들어이 문서는 방법을 단계별로 안내 Azure Logic Apps를 사용 하 여 자동 종료 알림 이메일에서 가져오려고 합니다. 첫째, 보겠습니다 랩에서 자동 종료 알림을 사용 하도록 설정 하는 기본 단계를 신속 하 게 진행 합니다.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>전자 메일 알림을 수신 하는 논리 앱 만들기
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 쉽게 하는 많은 기본 제공 커넥터를 Office 365 및 twitter와 같은 다른 클라이언트와 서비스 통합을 제공 합니다. 높은 수준에서 전자 메일 알림에 대 한 논리 앱을 설정 하는 단계를 네 단계로 나눌 수 있습니다. 

- 논리 앱을 만듭니다. 
- 기본 제공 템플릿을 구성 합니다.
- 전자 메일 클라이언트를 사용 하 여 통합
- 웹 후크 URL을 가져옵니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
시작 하려면 다음 단계를 사용 하 여 Azure 구독에서 논리 앱을 만듭니다.

1. 선택 **+ 리소스 만들기** 왼쪽된 메뉴에서 선택 **Integration**를 선택 하 고 **논리 앱**합니다. 

    ![새 논리 앱 메뉴](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 에 **논리 앱 만들기** 페이지에서 다음이 단계를 수행 합니다. 
    1. 입력 한 **이름을** 논리 앱에 대 한 합니다.
    2. Azure **구독**을 선택합니다.
    3. 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 선택합니다. 
    4. 선택 된 **위치** 논리 앱에 대 한 합니다. 

        ![새 논리 앱-설정](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 에 **알림을**를 선택 **리소스로 이동** 알림을 합니다. 

    ![리소스로 이동](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 선택 **논리 앱 디자이너** 아래에서 **배포 도구** 범주입니다.

    ![HTTP 요청/응답 선택](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. 에 **HTTP 요청-응답** 페이지에서 **이 템플릿을 사용 하 여**입니다. 

    ![이 서식 파일 옵션 사용 선택](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 에 다음 JSON을 복사 합니다 **요청 본문 JSON 스키마** 섹션: 

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
    
    ![요청 본문 JSON 스키마](./media/devtest-lab-auto-shutdown/request-json.png)
7. 선택 **+ 새 단계** 디자이너에서 다음이 단계를 수행 합니다.
    1. 검색할 **Office 365 Outlook-전자 메일 보내기**합니다. 
    2. 선택 **전자 메일 보내기** 에서 **작업**합니다. 
    
        ![옵션 전자 메일 보내기](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 선택 **로그인** 전자 메일 계정에 로그인 합니다. 
    4. 선택 **TO** 필드 및 소유자를 선택 합니다.
    5. 선택 **주체**, 및 전자 메일 알림 제목을 입력 합니다. 예를 들면 다음과 같습니다. "컴퓨터 vmName 랩에 대 한 종료: labName."
    6. 선택 **본문**, 전자 메일 알림에 대 한 본문 콘텐츠를 정의 합니다. 예를 들어: "vmName는 15 분 이내에 종료 되도록 예약 됩니다. 클릭 하 여이 종료를 건너뜁니다. URL. 1 시간 동안 지연 종료: delayUrl60 합니다. 2 시간 동안 지연 종료: delayUrl120. "

        ![요청 본문 JSON 스키마](./media/devtest-lab-auto-shutdown/email-options.png)
1. 도구 모음에서 **저장**을 선택합니다. 이제, 복사할 수 있습니다 합니다 **HTTP POST URL**합니다. URL을 클립보드에 복사 하려면 복사 단추를 선택 합니다. 

    ![웹 후크 URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>다음 단계
모든 정책을 설정 하는 방법에 알아보려면 참조 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)합니다.
