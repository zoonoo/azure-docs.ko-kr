---
title: Azure 보안 센터의 워크플로 자동화 | 마이크로 소프트 문서
description: Azure 보안 센터에서 워크플로를 만들고 자동화하는 방법 알아보기
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397906"
---
# <a name="workflow-automation"></a>워크플로 자동화

모든 보안 프로그램에는 사고 대응을 위한 여러 워크플로가 포함됩니다. 이러한 프로세스에는 관련 이해 관계자에게 알리고, 변경 관리 프로세스를 시작하고, 특정 수정 단계를 적용하는 것이 포함될 수 있습니다. 보안 전문가는 이러한 절차의 많은 단계를 가능한 한 자동화하는 것이 좋습니다. 자동화는 오버헤드를 줄입니다. 또한 미리 정의된 요구 사항에 따라 프로세스 단계가 신속하고 일관되게 수행되도록 하여 보안을 향상시킬 수 있습니다.

이 문서에서는 Azure 보안 센터의 워크플로 자동화 기능에 대해 설명합니다. 이 기능은 보안 경고 및 권장 사항에 대한 논리 앱을 트리거할 수 있습니다. 예를 들어 보안 센터에서 경고가 발생할 때 특정 사용자에게 전자 메일을 보내도록 할 수 있습니다. [Azure 논리](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)앱을 사용하여 논리 앱을 만드는 방법도 알아봅니다.

> [!NOTE]
> 이전에 사이드바에서 플레이북(미리 보기) 보기를 사용한 경우 새 워크플로 자동화 페이지에서 확장된 기능과 함께 동일한 기능을 찾을 수 있습니다.


## <a name="requirements"></a>요구 사항

* Azure Logic Apps 워크플로에서 작업하려면 다음 논리 앱 역할/사용 권한이 있어야 합니다.

    * [논리 앱 운영자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) 권한이 필요하거나 논리 앱 읽기/트리거 액세스(이 역할은 논리 앱을 만들거나 편집할 수 없으며 기존 앱만 *실행할* 수 있음)

    * 논리 앱 생성 및 수정에는 [논리 앱 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) 권한이 필요합니다.

* Logic App 커넥터를 사용하려면 해당 서비스에 로그인하기 위해 추가 자격 증명이 필요할 수 있습니다(예: Outlook/Teams/Slack 인스턴스).


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>논리 앱을 만들고 자동으로 실행해야 하는 시기를 정의합니다. 

1. 보안 센터의 사이드바에서 **워크플로 자동화를 선택합니다.**

    [![워크플로 자동화 목록](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    이 페이지에서 새 자동화 규칙을 만들고 기존 규칙을 활성화, 비활성화 또는 삭제할 수 있습니다.  
1. 새 워크플로를 정의하려면 **워크플로 자동화 추가를**클릭합니다. 

    새 자동화 옵션과 함께 창이 나타납니다. 여기에서 다음을 입력할 수 있습니다.
    1. 자동화에 대한 이름 및 설명입니다.
    1. 이 자동 워크플로를 시작하는 트리거입니다. 예를 들어 "SQL"을 포함하는 보안 경고가 생성될 때 Logic App을 실행하도록 할 수 있습니다.
    1. 트리거 조건이 충족될 때 실행되는 논리 앱입니다. 

        [![워크플로 자동화 목록](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. 작업 섹션에서 새 **만들기를** 클릭하여 논리 앱 생성 프로세스를 시작합니다.

    Azure 논리 앱으로 이동합니다.

    [![새 논리 앱 만들기](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 이름, 리소스 그룹 및 위치를 입력하고 에서 **만들기를**클릭합니다.

1. 새 논리 앱에서 보안 범주에서 미리 정의된 기본 제공 템플릿 중에서 선택할 수 있습니다. 또는 이 프로세스가 트리거될 때 발생하는 이벤트의 사용자 지정 흐름을 정의할 수 있습니다.

    논리 앱 디자이너에서 보안 센터 커넥터의 다음 트리거가 지원됩니다.

    * **Azure 보안 센터 권장 사항을 만들거나 트리거하는 경우**
    * **Azure 보안 센터 경고를 만들거나 트리거하는 경우** 
    
    > [!TIP]
    > 트리거를 사용자 지정하여 관심 있는 심각도 수준이 있는 경고에만 관련되도록 할 수 있습니다.
    
    > [!NOTE]
    > 레거시 트리거 "Azure 보안 센터 경고에 대한 응답이 트리거되는 경우"를 사용하는 경우 워크플로 자동화 기능으로 논리 앱이 시작되지 않습니다. 대신 위에서 언급한 트리거 중 하나를 사용합니다. 

    [![샘플 논리 앱](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Logic App을 정의한 후 워크플로 자동화 정의 창("워크플로 자동화 추가")으로 돌아갑니다. **새로 고침을** 클릭하여 새 로직 앱을 선택할 수 있는지 확인합니다.

    ![새로 고침](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 로직 앱을 선택하고 자동화를 저장합니다. 논리 앱 드롭다운에는 위에서 언급한 보안 센터 커넥터를 지원하는 논리 앱만 표시됩니다.


## <a name="manually-trigger-a-logic-app"></a>논리 앱을 수동으로 트리거합니다.

보안 경고 또는 [빠른 수정 수정을](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)제공하는 권장 사항을 볼 때 논리 앱을 수동으로 실행할 수도 있습니다.

논리 앱을 수동으로 실행하려면 빠른 수정 수정을 지원하는 경고 또는 권장 사항을 열고 **트리거 논리 앱을**클릭합니다.

[![논리 앱을 수동으로 트리거합니다.](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>데이터 형식 스키마

Logic App 인스턴스에 전달된 보안 경고 또는 권장 사항 이벤트의 원시 이벤트 스키마를 보려면 [워크플로 자동화 데이터 형식 스키마를](https://aka.ms/ASCAutomationSchemas)방문하십시오. 위에서 언급한 보안 센터의 기본 제공 논리 앱 커넥터를 사용하지 않고 대신 Logic App의 일반 HTTP 커넥터를 사용하는 경우 이 기능이 유용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 논리 앱을 만들고, 보안 센터에서 실행을 자동화하고, 수동으로 실행하는 것에 대해 배웠습니다. 

기타 관련 자료는 다음을 참조하십시오. 

- [워크플로 자동화를 사용하여 보안 응답을 자동화하는 방법에 대한 Microsoft Learn 모듈](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Azure Security Center의 보안 권장 사항](security-center-recommendations.md)
- [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
- [Azure Logic Apps 정보](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps 커넥터](https://docs.microsoft.com/connectors/)
- [워크플로 자동화 데이터 유형 스키마](https://aka.ms/ASCAutomationSchemas)
