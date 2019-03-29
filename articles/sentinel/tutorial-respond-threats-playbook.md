---
title: Azure Sentinel 미리 보기에서 플레이북 실행 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel에서 플레이북을 실행하는 방법을 설명합니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5ce409b984ab9110a8ca44f47675045e4d80aed5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088641"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>자습서: Azure Sentinel 미리 보기에서 자동화된 위협 응답 설정

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 자습서를 통해 Azure Sentinel에서 보안 플레이북을 사용하여 Azure Sentinel에서 감지된 보안 관련 문제에 대한 자동화된 위협 응답을 설정할 수 있습니다.


> [!div class="checklist"]
> * 플레이북 이해
> * 플레이북 만들기
> * 플레이북 실행


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Azure Sentinel에서 보안 플레이북이란?

보안 플레이북은 경고에 응답하여 Azure Sentinel에서 실행할 수 있는 절차의 모음입니다. 보안 플레이북을 통해 사용자의 응답을 자동화하고 오케스트레이션할 수 있으며, 수동으로 실행하거나 특정 경고가 트리거되면 자동 실행하도록 설정할 수 있습니다. Azure Sentinel의 보안 플레이북은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)에 기반합니다. 즉, Logic Apps의 모든 성능, 사용자 지정 기능은 물론, 기본 제공되는 템플릿을 활용할 수 있습니다. 각 플레이북은 사용자가 선택하는 특정 구독용으로 만들어지며, Playbooks 페이지를 보면 선택한 구독 전반의 모든 플레이북이 보입니다.

> [!NOTE]
> 플레이북은 Azure Logic Apps를 활용하므로 요금이 청구됩니다. 자세한 내용은 [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) 가격 책정 페이지를 방문하세요.

예를 들어, 네트워크 리소스에 액세스하는 악의적인 공격자가 염려되는 경우에는 사용자의 네트워크에 액세스하는 악의적인 IP 주소를 찾는 경고를 설정할 수 있습니다. 그런 다음, 다음을 수행하는 플레이북을 만들 수 있습니다.
1. 경고가 트리거되면 ServiceNow 또는 다른 IT 티케팅 시스템에서 티켓이 열립니다.
2. Microsoft Teams 또는 Slack의 보안 운영 채널로 메시지를 전송하여 보안 분석가가 이 인시던트를 알게 합니다.
3. 경고의 모든 정보를 선임 네트워크 관리자 및 보안 관리자에게 전송합니다. 또한 이메일 메시지에는 두 가지 사용자 옵션 단추 **차단** 또는 **무시**가 포함되어 있습니다.
4. 플레이북은 관리자로부터 응답을 수신한 후에도 계속 실행됩니다.
5. 관리자가 **차단**을 선택할 경우 해당 IP 주소는 방화벽에서 차단되고 사용자는 Azure AD에서 비활성화됩니다.
6. 관리자가 **무시**를 선택할 경우 Azure Sentinel에서 경고가 종료되고 해당 인시던트는 ServiceNow에서 종료됩니다.

보안 플레이북은 수동 또는 자동으로 실행할 수 있습니다. 수동으로 실행한다는 것은 경고를 받으면 선택한 경고에 대한 응답으로 요청 시 플레이북을 실행하도록 선택할 수 있다는 것을 의미합니다. 자동으로 실행한다는 상관 관계 규칙을 작성하는 동안 경고 트리거 시 하나 이상의 플레이북을 자동 실행하도록 설정할 수 있다는 것을 의미합니다.


## <a name="create-a-security-playbook"></a>보안 플레이북 만들기

다음 단계를 따라 Azure Sentinel에서 새 보안 플레이북을 만듭니다.

1. **Azure Sentinel** 대시보드를 엽니다.
2. **관리**에서 **플레이북**을 선택합니다.

   ![논리 앱](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. **Azure Sentinel - 플레이북(미리 보기)** 페이지에서 **추가** 단추를 클릭합니다.

   ![논리 앱 만들기](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. **논리 앱 만들기** 페이지에서 요청된 정보를 입력하여 새 논리 앱을 만들고 **만들기**를 클릭합니다. 

5. [**논리 앱 디자이너**](../logic-apps/logic-apps-overview.md)에서 사용하려는 템플릿을 선택합니다. 자격 증명이 필요한 템플릿을 선택할 경우에는 자격 증명을 제공해야 합니다. 또는 처음부터 새로운 빈 플레이북을 만들 수도 있습니다. **빈 논리 앱**을 선택합니다. 

   ![논리 앱 디자이너](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. 템플릿을 새로 빌드하거나 편집할 수 있는 논리 앱 디자이너로 연결됩니다. [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md)를 사용하여 플레이북을 만드는 방법에 대한 자세한 내용

7. 빈 플레이북을 만들려는 경우 **모든 커넥터 및 트리거 검색** 필드에서 *Azure Sentinel*을 입력하고 **Azure Sentinel 경고에 대한 응답이 트리거되는 경우**를 선택합니다. <br>플레이북이 만들어지면 **플레이북** 목록에 새 플레이북이 나타납니다. 나타나지 않으면 **새로 고침** 단추를 클릭합니다. 

7. 이제 플레이북을 트리거하는 경우 상황을 정의할 수 있습니다. 작업, 논리 조건, 전환 사례 조건 또는 루프를 추가할 수 있습니다.

   ![논리 앱 디자이너](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>보안 플레이북 실행 방법

요청 시 플레이북을 실행할 수 있습니다.

요청 시 플레이북을 실행하려면

1. **사례** 페이지에서 사례를 선택하고 **전체 세부 정보 보기**를 클릭합니다.

2. **경고** 탭에서 플레이북을 실행하려는 경고를 클릭하고 오른쪽으로 스크롤하여 **플레이북 보기**를 클릭한 후 구독에서 사용 가능한 플레이북 목록에서 **실행**할 플레이북을 선택합니다. 




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel에서 플레이북을 실행하는 방법에 대해 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요. 이 자습서에서는 Azure Sentinel에서 플레이북을 실행하는 방법에 대해 알아보았습니다. Azure Sentinel을 사용하여 [사전에 위협을 탐지하는 방법](hunting.md)으로 계속 진행합니다.
> [!div class="nextstepaction"]
> [위협을 탐지](hunting.md)하여 네트워크의 위협을 사전에 찾아냅니다.

