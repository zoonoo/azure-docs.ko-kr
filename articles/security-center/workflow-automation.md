---
title: Azure Security Center의 워크플로 자동화 | Microsoft Docs
description: Azure Security Center에서 워크플로를 만들고 자동화 하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b713977d811411ea2ccd7dfa22c7757321ecd7aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712292"
---
# <a name="create-automatic-responses-to-alerts-and-recommendations-with-workflow-automation"></a>워크플로 자동화를 사용 하 여 경고 및 권장 사항에 대 한 자동 응답 만들기

모든 보안 프로그램에는 인시던트 응답을 위한 여러 워크플로가 포함되어 있습니다. 이러한 프로세스에는 관련 이해 관계자에게 알리고, 변경 관리 프로세스를 시작하고, 특정 수정 단계를 적용하는 것이 포함될 수 있습니다. 보안 전문가는 가능한 한 해당 절차의 여러 단계를 자동화할 것을 권장합니다. 자동화를 통해 오버 헤드가 감소 합니다. 또한 프로세스 단계가 미리 정의 된 요구 사항에 따라 빠르고 일관 되 게 수행 되도록 하 여 보안을 향상 시킬 수 있습니다.

이 문서에서는 Azure Security Center의 워크플로 자동화 기능에 대해 설명 합니다. 이 기능은 보안 경고 및 권장 사항에 대 한 Logic Apps를 트리거할 수 있습니다. 예를 들어 경고가 발생 하는 경우 특정 사용자에 게 전자 메일을 보내는 Security Center를 원할 수 있습니다. 또한 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)를 사용 하 여 Logic Apps를 만드는 방법도 알아봅니다.

> [!NOTE]
> 이전에 사이드바에서 플레이 북 (미리 보기) 보기를 사용한 경우 새 워크플로 자동화 페이지에서 확장 된 기능과 함께 동일한 기능을 찾을 수 있습니다.



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|Free|
|필요한 역할 및 권한:|리소스 그룹에 대 한 **보안 관리자 역할** 또는 **소유자**<br>또한 대상 리소스에 대 한 쓰기 권한이 있어야 합니다.<br><br>Azure Logic Apps 워크플로를 사용 하려면 다음과 같은 Logic Apps 역할/권한도 있어야 합니다.<br> - [논리 앱 운영자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) 권한은 필수 또는 논리 앱 읽기/트리거 액세스입니다 .이 역할은 논리 앱을 만들거나 편집할 수 없으며 기존 항목만 *실행* 합니다.<br> - 논리 앱을 만들고 수정 하려면 [논리 앱 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) 권한이 필요 합니다.<br>논리 앱 커넥터를 사용 하려는 경우 해당 서비스 (예: Outlook/팀/여유 시간 인스턴스)에 로그인 하려면 추가 자격 증명이 필요할 수 있습니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>논리 앱을 만들고 자동으로 실행 되는 시기를 정의 합니다. 

1. Security Center의 사이드바에서 **워크플로 자동화**를 선택 합니다.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="워크플로 자동화 목록":::

    이 페이지에서 새 자동화 규칙을 만들고 기존 규칙을 사용, 사용 안 함 또는 삭제할 수 있습니다.

1. 새 워크플로를 정의 하려면 **워크플로 자동화 추가**를 클릭 합니다. 

    새 자동화에 대 한 옵션이 포함 된 창이 나타납니다. 여기에서 다음을 입력할 수 있습니다.
    1. 자동화에 대 한 이름 및 설명입니다.
    1. 이 자동 워크플로를 시작 하는 트리거입니다. 예를 들어 "SQL"을 포함 하는 보안 경고가 생성 될 때 논리 앱을 실행 하려고 할 수 있습니다.
    1. 트리거 조건이 충족 될 때 실행 되는 논리 앱입니다. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="워크플로 자동화 목록" 레거시 트리거를 사용 하는 경우 논리 앱은 워크플로 자동화 기능을 통해 시작 되지 않습니다. 대신 위에서 언급 한 트리거 중 하나를 사용 합니다. 

    [![샘플 논리 앱](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 논리 앱을 정의한 후 워크플로 자동화 정의 창 ("workflow automation 추가")으로 돌아갑니다. **새로 고침** 을 클릭 하 여 새 논리 앱을 선택할 수 있는지 확인 합니다.

    ![새로 고침](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 논리 앱을 선택 하 고 자동화를 저장 합니다. 논리 앱 드롭다운은 위에서 설명한 Security Center 커넥터를 지 원하는 Logic Apps 표시 합니다.


## <a name="manually-trigger-a-logic-app"></a>수동으로 논리 앱 트리거

보안 경고나 권장 사항을 볼 때 Logic Apps를 수동으로 실행할 수도 있습니다.

논리 앱을 수동으로 실행 하려면 경고 또는 권장 사항을 열고 **논리 앱 트리거**를 클릭 합니다.

[![수동으로 논리 앱 트리거](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>데이터 형식 스키마

논리 앱 인스턴스에 전달 되는 보안 경고 또는 권장 사항 이벤트의 원시 이벤트 스키마를 보려면 [워크플로 자동화 데이터 형식 스키마](https://aka.ms/ASCAutomationSchemas)를 참조 하세요. 이는 위에서 언급 한 Security Center의 기본 제공 논리 앱 커넥터를 사용 하지 않고 논리 앱의 일반 HTTP 커넥터를 사용 하는 경우에 유용할 수 있습니다. 이벤트 JSON 스키마를 사용 하 여 적합 한 것 처럼 수동으로 구문 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Logic Apps을 만들고 Security Center에서 실행을 자동화 하 고 수동으로 실행 하는 방법을 배웠습니다. 

관련 자료는 다음을 참조 하세요. 

- [워크플로 자동화를 사용 하 여 보안 응답을 자동화 하는 방법에 대 한 Microsoft Learn 모듈](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure Security Center의 보안 권장 사항](security-center-recommendations.md)
- [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
- [Azure Logic Apps 정보](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps 커넥터](https://docs.microsoft.com/connectors/)
- [워크플로 자동화 데이터 형식 스키마](https://aka.ms/ASCAutomationSchemas)
