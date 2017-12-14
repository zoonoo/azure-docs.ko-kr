---
title: "작업 시간 외 VM 시작/중지 솔루션 | Microsoft Docs"
description: "VM 관리 솔루션은 Azure Resource Manager Virtual Machines을 일정에 따라 시작 및 중지하고 Log Analytics에서 사전에 모니터링합니다."
services: automation
documentationCenter: 
authors: georgewallace
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: e6f1189b9729c57718a5cd6d6f6a583b94f6f142
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Automation의 작업 시간 외 VM 시작/중지 솔루션

작업 시간 외 VM 시작/중지 솔루션은 사용자 정의 일정에 따라 Azure Virtual Machines를 시작 및 중지하고, Log Analytics를 통해 중요 정보를 제공하고, [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview)를 사용하여 선택적 전자 메일을 전송합니다. 대부분의 시나리오에서 Azure Resource Manager 및 클래식 VM이 둘 다 지원됩니다. 

이 솔루션은 서버가 없는 저렴한 리소스를 활용하여 비용을 줄이려는 고객에게 분산된 자동화 기능을 제공합니다. 기능은 다음과 같습니다.

* VM 시작/중지 예약
* Azure Tags(클래식 VM에서는 지원되지 않음)를 사용하여 VM 시작/중지를 오름차순으로 예약
* 낮은 CPU 사용량을 기준으로 VM 자동 중지

## <a name="prerequisites"></a>필수 조건

- Runbook이 [Azure 실행 계정](automation-offering-get-started.md#authentication-methods)으로 작동됩니다.  실행 계정은 자주 만료되거나 변경될 수 있는 암호 대신 인증서 인증을 사용하기 때문에 선호하는 인증 방법입니다.  

- 이 솔루션은 Automation 계정이 상주하는 위치와 동일한 구독에 있는 VM만 관리할 수 있습니다.  

- 이 솔루션은 Azure 지역, 오스트레일리아 남동부, 캐나다 중부, 인도 중부, 미국 동부, 일본 동부, 동남 아시아, 영국 남부 및 유럽 서부로만 배포됩니다.  
    
    > [!NOTE]
    > VM 일정을 관리하는 Runbook은 모든 지역의 VM을 대상으로 할 수 있습니다.  

- VM runbook 시작 및 중지가 완료될 때 전자 메일 알림을 전송하려면 Azure Marketplace에서 등록하는 동안 **예**를 선택하여 SendGrid를 배포해야 합니다. 

    > [!IMPORTANT]
    > SendGrid는 타사 서비스입니다. SendGrid에 대한 지원을 얻으려면 [SendGrid](https://sendgrid.com/contact/)에 문의하세요.  
    >
   
    SendGrid의 제한 사항은 다음과 같습니다.

    * 사용자 구독당 최대 1개의 SendGrid 계정
    * 구독당 최대 2개의 SendGrid 계정

이 솔루션의 이전 버전을 배포한 경우 먼저 이 릴리스를 배포하기 전에 계정에서 삭제해야 합니다.  

## <a name="solution-components"></a>솔루션 구성 요소

이 솔루션에는 비즈니스 요구에 맞게 가상 머신의 시작 및 종료를 조정할수 있도록 하는 미리 구성된 Runbook, 일정 및 Log Analytics와의 통합이 포함되어 있습니다. 

### <a name="runbooks"></a>Runbook

다음 표에서는 Automation 계정에 배포된 Runbook이 나열됩니다.  Runbook 코드를 변경하는 것은 권장되지 않으며 새 기능에 대해 자체 Runbook을 작성하는 것이 좋습니다.

> [!NOTE]
> 이름 끝에 "Child"가 추가된 Runbook은 직접 실행하지 마세요.
>

모든 상위 Runbook에는 *WhatIf* 매개 변수가 포함됩니다. 이 매개 변수가 **True**로 설정되면 *WhatIf* 매개 변수 없이 실행될 때 Runbook이 수행하는 정확한 동작이 자세히 설명되고 올바른 VM이 대상으로 지정되었는지 확인됩니다.  Runbook은 *WhatIf* 매개 변수가 **False**로 설정된 경우에만 정의된 작업을 수행합니다. 

|**Runbook** | **매개 변수** | **설명**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 상위 runbook에서만 호출됩니다. AutoStop 시나리오에 대해 리소스별 경고를 만듭니다.| 
|AutoStop_CreateAlert_Parent | WhatIf: True 또는 False <br> VMList | 대상 구독 또는 리소스 그룹에서 VM에 대해 Azure 경고 규칙을 만들거나 업데이트합니다. <br> VMList: 쉼표로 구분된 VM 목록입니다.  예: *vm1,vm2,vm3*| 
|AutoStop_Disable | 없음 | AutoStop 경고 및 기본 일정을 사용하지 않도록 설정합니다.| 
|AutoStop_StopVM_Child | WebHookData | 상위 runbook에서만 호출됩니다. 경고 규칙은 이 Runbook을 호출하며 VM 중지에 작동합니다.|  
|Bootstrap_Main | 없음 | 일반적으로 Azure Resource Manager에서 액세스할 수 없는 webhookURI 같은 부트스트랩 구성을 설정하기 위해 한 번 사용됩니다. 이 runbook은 배포가 끝나면 자동으로 제거됩니다.|  
|ScheduledStartStop_Child | VMName <br> 작업: 중지 또는 시작 <br> ResourceGroupName | 상위 runbook에서만 호출됩니다. 예약된 중지에 대해 중지 및 시작을 실제로 수행합니다.|  
|ScheduledStartStop_Parent | 작업: 중지 또는 시작 <br> WhatIf: True 또는 False | 이 경우 이러한 대상 리소스 그룹에서만 실행되도록 제한하는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**를 편집하지 않는 한, 구독의 모든 VM에 영향을 미칩니다. **External_ExcludeVMNames** 변수를 업데이트하여 특정 VM을 제외할 수도 있습니다. WhatIf는 다른 Runbook과 같이 동작합니다.|  
|SequencedStartStop_Parent | 작업: 중지 또는 시작 <br> WhatIf: True 또는 False | 시작\\중지 작업을 시퀀싱하려는 각 VM에 대해 **SequenceStart**라는 태그와 **SequenceStop**이라는 또 다른 태그를 만듭니다. 이 태그의 값은 시작\\중지하려는 오름차순 순서에 해당하는 양의 정수(1,2,3)여야 합니다. WhatIf는 다른 Runbook과 같이 동작합니다. <br> **참고: 이러한 동작이 적용되려면 VM은 Azure Automation 변수의 정의된 리소스 그룹, External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames 및 External_ExcludeVMNames 내에 있어야 하며 적절한 태그가 있어야 합니다.**|

### <a name="variables"></a>variables

다음 표에서는 Automation 계정에서 만든 변수를 나열합니다.  변수 접두사로 수정 하는 것이 좋습니다. **External**이 접두사로 붙은 변수만 수정하는 것이 좋으며 접두사가 **Internal**인 변수를 수정하면 원치 않는 결과가 발생합니다.  

|**변수** | **설명**|
---------|------------|
|External_AutoStop_Condition | 경고를 트리거하기 전에 조건을 구성하는 데 필요한 조건부 연산자입니다. 사용 가능한 값은 **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, **LessThanOrEqual**입니다.|  
|External_AutoStop_Description | CPU %가 임계값을 초과하는 경우 VM을 중지하도록 경고합니다.|  
|External_AutoStop_MetricName | Azure 경고 규칙을 구성해야 하는 성능 메트릭의 이름입니다.| 
|External_AutoStop_Threshold | *External_AutoStop_MetricName* 변수에 지정된 Azure 경고 규칙에 대한 임계값입니다. 백분율 값의 범위는 1에서 100까지입니다.|  
|External_AutoStop_TimeAggregationOperator | 조건을 평가하기 위해 선택한 기간 크기에 적용되는 시간 집계 연산자입니다. 사용 가능한 값은 **Average**, **Minimum**, **Maximum**, **Total**, **Last**입니다.|  
|External_AutoStop_TimeWindow | Azure에서 경고를 트리거하기 위해 선택된 메트릭을 분석할 기간입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간까지입니다.|  
|External_EmailFromAddress | 전자 메일 보낸 사람을 지정합니다.|  
|External_EmailSubject | 전자 메일의 제목 줄에 대한 텍스트를 지정합니다.|  
|External_EmailToAddress | 전자 메일의 받는 사람을 지정합니다. 쉼표를 사용하여 이름을 구분합니다.|  
|External_ExcludeVMNames | 공백 없이 쉼표로 이름을 구분하여 제외할 VM 이름을 입력합니다.|  
|External_IsSendEmail | 완료 시 전자 메일 알림을 보내기 위한 옵션을 지정합니다.  **Yes** 또는 **No**(전자 메일을 보내지 않으려면)를 지정합니다.  초기 배포 동안 SendGrid를 만들지 않은 경우 이 옵션은 **No**여야 합니다.|  
|External_Start_ResourceGroupNames | 시작 작업의 대상이 될 하나 이상의 리소스 그룹을 쉼표로 구분해서 지정합니다.|  
|External_Stop_ResourceGroupNames | 중지 작업의 대상이 될 하나 이상의 리소스 그룹을 쉼표로 구분해서 지정합니다.|  
|Internal_AutomationAccountName | Automation 계정의 이름을 지정합니다.|  
|Internal_AutoSnooze_WebhookUri | AutoStop 시나리오에 대해 호출되는 Webhook URI를 지정합니다.|  
|Internal_AzureSubscriptionId | Azure 구독 ID를 지정합니다.|  
|Internal_ResourceGroupName | Azure Automation 계정 리소스 그룹 이름을 지정합니다.|  
|Internal_SendGridAccountName | SendGrid 계정 이름을 지정합니다.|  
|Internal_SendGridPassword | SendGrid 계정 암호를 지정합니다.|  

<br>

모든 시나리오에서 **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수는 **AutoStop_CreateAlert_Parent** Runbook에 대해 쉼표로 구분된 VM 목록을 제공하는 경우를 제외하고 대상 VM을 지정하는 데 필요합니다. 즉, 시작/중지 작업이 진행되려면 VM이 대상 리소스 그룹에 있어야 합니다. 이 논리는 구독 또는 리소스 그룹을 대상으로 지정하며 새로 만든 VM에서 작업이 상속된다는 측면에서 Azure 정책과 약간 비슷하게 작동합니다. 이 방법을 사용하면 모든 VM에 대해 별도 일정을 유지하고 시작/중지를 전체적으로 관리할 필요가 없습니다.

### <a name="schedules"></a>일정

다음 표에서는 Automation 계정에서 만든 각 기본 일정을 나열합니다.  일정을 수정하거나 고유한 사용자 지정 일정을 만들 수 있습니다.  기본적으로 이러한 각 일정은 **Scheduled_StartVM** 및 **Scheduled-StopVM**을 제외하고 사용되지 않도록 설정됩니다.

모든 일정을 사용하도록 설정하면 작업을 수행하는 일정이 중복될 수 있으므로 바람직하지 않습니다.  대신, 그에 맞게 수행하고 수정할 최적화를 결정하는 것이 가장 좋습니다.  추가 설명을 보려면 개요 섹션에서 예제 시나리오를 참조하세요.   

|**ScheduleName** | **Frequency(빈도)** | **설명**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8시간마다 실행 | AutoStop_CreateAlert_Parent Runbook을 8시간 간격으로 실행합니다. 그러면 Azure Automation 변수에서 "External_Start_ResourceGroupNames", "External_Stop_ResourceGroupNames" 및 "External_ExcludeVMNames"에서 VM 기반 값이 중지됩니다.  또는 "VMList" 매개 변수를 사용하여 쉼표로 구분된 VM 목록을 지정할 수 있습니다.|  
|Scheduled_StopVM | 사용자 정의, 매일 | 매일 지정된 시간에 "Stop" 매개 변수를 사용하여 Scheduled_Parent Runbook이 실행됩니다.  자산 변수를 통해 정의된 규칙을 충족하는 모든 VM이 자동으로 중지됩니다.  동급 일정인 Scheduled-StartVM을 사용하도록 설정하는 것이 좋습니다.|  
|Scheduled_StartVM | 사용자 정의, 매일 | 매일 지정된 시간에 *Start* 매개 변수를 사용하여 Scheduled_Parent Runbook이 실행됩니다.  정의된 규칙 및 해당 변수의 일부를 충족하는 모든 VM이 자동으로 시작됩니다.  동급 일정인 **Scheduled-StopVM**을 사용하도록 설정하는 것이 좋습니다.|
|Sequenced-StopVM | 오전 1시(UTC), 매주 금요일 | 매주 금요일 지정된 시간에 *Stop* 매개 변수를 사용하여 Sequenced_Parent Runbook이 실행됩니다.  **SequenceStop** 태그가 정의되어 있고 해당 변수의 일부인 모든 VM이 순차적으로(오름차순으로) 중지됩니다.  태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요.  동급 일정인 **Sequenced-StartVM**을 사용하도록 설정하는 것이 좋습니다.|
|Sequenced-StartVM | 오후 1시(UTC), 매주 월요일 | 매주 월요일 지정된 시간에 *Start* 매개 변수를 사용하여 Sequenced_Parent Runbook이 실행됩니다.  **SequenceStart** 태그가 정의되어 있고 해당 변수의 일부인 모든 VM이 순차적으로(내림차순으로) 시작됩니다.  태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요.  동급 일정인 **Sequenced-StopVM**을 사용하도록 설정하는 것이 좋습니다.|

<br>

## <a name="configuration"></a>구성

업무 시간 외 VM 시작/중지 [미리 보기] 솔루션을 Automation 계정에 추가하고 솔루션을 사용자 정의하도록 변수를 구성하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **새로 만들기**를 클릭합니다.<br> ![Azure 포털](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. Marketplace 창에서 **VM 시작**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 검색 결과에서 **작업 시간 외 VM 시작/중지[미리 보기]**를 선택합니다.  
3. 선택한 솔루션에 대한 **작업 시간 외 VM 시작/중지 [미리 보기]** 창에서 요약 정보를 검토한 다음 **만들기**를 클릭합니다.  
4. **솔루션 추가** 창이 표시되고 Automation 구독으로 솔루션을 가져오려면 그 전에 솔루션을 구성하라는 메시지가 표시됩니다.<br><br> ![VM 관리 솔루션 추가 블레이드](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  **솔루션 추가** 블레이드에서 **작업 영역**을 선택하고 여기에서 Automation 계정이 속하는 Azure 구독에 연결된 작업 영역을 선택하거나 작업 영역을 새로 만듭니다.  작업 영역이 없으면 **새 작업 영역 만들기**를 선택하고 **OMS 작업 영역** 창에서 다음을 수행합니다. 
   - 새 **OMS 작업 영역**에 대한 이름을 지정합니다.
   - 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 선택하여 연결할 **구독**을 선택합니다.
   - **리소스 그룹**의 경우, 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있습니다.  
   - **위치**를 선택합니다.  현재 선택 영역에 대해 **오스트레일리아 남동부**, **캐나다 중부**, **인도 중부**, **미국 동부**, **일본 동부**, **동남 아시아**, **영국 남부** 및 **유럽 서부** 위치만 제공됩니다.
   - **가격 책정 계층**을 선택합니다.  솔루션은 무료 및 OMS 유료 계층이라는 두 가지 계층으로 제공됩니다.  무료 계층은 하루에 수집되는 데이터의 양, 보존 기간 및 Runbook 작업 런타임 시간(분)이 제한됩니다.  OMS 유료 계층은 하루에 수집할 수 있는 데이터의 양이 제한되지 않습니다.  

        > [!NOTE]
        > 독립 실행형 유료(GB 기준) 계층이 옵션으로 표시되는 반면 적용할 수는 없습니다.  이를 선택하고 구독에서 이 솔루션의 만들기를 계속하는 경우 실패합니다.  이 솔루션이 공식적으로 릴리스되면 해결될 것입니다.<br>이 솔루션을 사용하는 경우 Automation 작업 분 및 로그 수집을 사용합니다.  솔루션은 사용자 환경에 추가 OMS 노드를 추가하지 않습니다.  

6. **OMS 작업 영역** 블레이드에서 필요한 정보를 제공한 후에 **만들기**를 클릭합니다.  정보가 확인되고 작업 영역이 만들어지는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.  **솔루션 추가** 블레이드가 다시 열립니다.  
7. **솔루션 추가** 블레이드에서 **Automation 계정**을 선택합니다.  OMS 작업 영역을 새로 만드는 경우, Azure 구독, 리소스 그룹 및 지역을 비롯하여 앞서 지정한 새 OMS 작업 영역과 연결되는 새 Automation 계정도 만들어야 합니다.  **Automation 계정 만들기**을 선택하고 **Automation 계정 추가** 블레이드에서 다음을 제공합니다. 
  - **이름** 필드에서 Automation 계정의 이름을 입력합니다.

    다른 모든 옵션은 선택한 OMS 작업 영역을 기반으로 자동으로 채워지며 이러한 옵션은 수정할 수 없습니다.  Azure 실행 계정은 이 솔루션에 포함된 Runbook에 대한 기본 인증 방법입니다.  **확인**을 클릭하면 구성 옵션의 유효성이 검사되고 Automation 계정이 생성됩니다.  메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 

    그렇지 않으면, 기존 Automation 실행 계정을 선택할 수 있습니다.  선택한 계정은 다른 OMS 작업 영역에 이미 연결되어 있을 수 없습니다. 그렇지 않으면 이를 알리는 메시지가 블레이드에 표시됩니다.  이미 연결되어 있다면 다른 Automation 실행 계정을 선택하거나 새 계정을 만들어야 합니다.<br><br> ![OMS 작업 영역에 이미 연결된 Automation 계정](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 마지막으로 **솔루션 추가** 블레이드에서 **구성**을 선택하면 **매개 변수** 블레이드가 표시됩니다.<br><br> ![솔루션에 대한 매개 변수 창](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  **매개 변수** 블레이드에서 다음을 수행해야 합니다.  
   - 이 솔루션으로 관리될 VM을 포함하는 리소스 그룹 이름인 **대상 리소스 그룹 이름**을 지정합니다.  이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표(;)으로 구분해야 하고 대/소문자는 구분되지 않습니다.  구독 내 모든 리소스 그룹의 VM을 대상으로 하려는 경우에는 와일드카드 사용이 지원됩니다.
   - 대상 리소스 그룹에서 하나 이상의 가상 머신의 이름인 **VM 제외 목록(문자열)**을 지정합니다.  이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표(;)으로 구분해야 하고 대/소문자는 구분되지 않습니다.  와일드카드를 사용할 수 있습니다.
   - 대상 리소스 그룹의 VM을 시작하고 중지하는 되풀이 날짜 및 시간인 **일정**을 선택합니다.  기본적으로 일정은 UTC 표준 시간대로 구성되며 다른 지역을 선택할 수 없습니다.  솔루션을 구성한 후 일정을 특정 표준 시간대로 구성하려면 아래의 [시작 및 종료 일정 수정](#modifying-the-startup-and-shutdown-schedule)을 참조하세요.
   - SendGrid에서 **전자 메일 알림** 받으려면 기본값인 **예**를 그대로 두고 유효한 전자 메일 주소를 제공합니다.  **아니요**를 선택하며 나중에 전자 메일 알림을 수신하려는 경우 Azure Marketplace에서 솔루션을 다시 배포해야 합니다.  

10. 솔루션에 필요한 초기 설정을 모두 구성하고 나면 **만들기**를 선택합니다.  모든 설정에 대한 유효성이 검사되고 구독 내에 솔루션 배포가 시도됩니다.  이 프로세스를 완료하려면 몇 초 정도가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 

## <a name="collection-frequency"></a>수집 빈도

Automation 작업 로그 및 작업 스트림 데이터가 5분마다 Log Analytics 리포지토리로 수집됩니다.  

## <a name="using-the-solution"></a>솔루션 사용

VM 관리 솔루션을 추가하면 Azure Portal의 Log Analytics 작업 영역에서 **StartStopVM 보기** 타일이 대시보드에 추가됩니다.  이 타일에는 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표현이 표시됩니다.<br><br> ![VM 관리 StartStopVM 보기 타일](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Automation 계정에서 **작업 영역** 옵션을 선택하고 Log Analytics 페이지의 왼쪽 창에서 **솔루션**을 선택하여 솔루션을 액세스 및 관리할 수 있습니다.  **솔루션** 페이지의 목록에서 **Start-Stop-VM[Workspace]**를 선택합니다.<br><br> ![Log Analytics의 솔루션 목록](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

솔루션을 선택하면 **Start-Stop-VM[Workspace]** 솔루션 블레이드가 표시되고, 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표현을 나타내는 Log Analytics 작업 영역과 같은 곳에서 **StartStopVM** 타일과 같은 중요 세부 정보를 검토할 수 있습니다.<br><br> ![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

여기에서 도넛 타일을 클릭하여 작업 레코드를 추가로 분석할 수 있습니다. 그러면 솔루션 대시보드에 작업 기록, 미리 정의된 로그 검색 쿼리가 표시되며, Log Analytics 고급 포털로 전환하여 검색 쿼리에 따라 검색할 수 있습니다.  

모든 상위 Runbook에는 *WhatIf* 매개 변수가 포함됩니다. 이 매개 변수가 **True**로 설정되면 *WhatIf* 매개 변수 없이 실행될 때 Runbook이 수행하는 정확한 동작이 자세히 설명되고 올바른 VM이 대상으로 지정되었는지 확인됩니다.  Runbook은 *WhatIf* 매개 변수가 **False**로 설정된 경우에만 정의된 작업을 수행합니다.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>시나리오 1: 구독 또는 대상리 소스 그룹에서 VM 매일 중지/시작 

솔루션을 처음 배포할 때 이것이 기본 구성입니다.  예를 들어 저녁에 사무실을 나갈 때 구독의 모든 VM을 중지하고, 아침에 사무실로 복귀할 때 시작하도록 구성할 수 있습니다. 배포 중에 일정 **Scheduled-StartVM" 및 **Scheduled-StopVM**을 구성할 경우 대상 VM을 시작 및 중지합니다.
>[!NOTE]
>표준 시간대는 예약 시간 매개 변수를 구성할 당시의 현재 표준 시간대이지만 Azure Automation에서 UTC 형식으로 저장됩니다.  따라서 배포 중에 처리될 때 표준 시간대 변환 작업을 수행할 필요가 없습니다.

2개의 변수 **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames 및 **External_ExcludeVMNames**를 구성하여 범위 안에 포함되는 VM을 제어합니다.  

>[!NOTE]
> 변수 **Target ResourceGroup Names**"의 값은 두 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames** 변수의 값으로 저장됩니다. 추가 세분성을 위해 각기 다른 리소스 그룹을 대상으로 하도록 이러한 각 변수를 수정할 수 있습니다.  시작 작업의 경우 한 **External_Start_ResourceGroupNames**를 사용하고, 중지 작업의 경우 **External_Stop_ResourceGroupNames**를 사용합니다. 새 VM이 시작 및 중지 일정에 자동으로 추가됩니다.

구성을 테스트하고 유효성을 검사하려면 **ScheduledStartStop_Parent** Runbook을 수동으로 시작하고 *ACTION* 매개 변수를 **start** 또는 **stop**으로 설정하고, *WhatIf* 매개 변수를 **true**로 설정합니다.<br><br> ![Runbook에 대한 매개 변수 구성](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> 이 옵션을 사용하면 프로덕션 VM에 대해 구현하기 전에 발생할 작업을 미리 보고 필요에 맞게 변경할 수 있습니다.  상황에 문제가 없을 경우 이 매개 변수를 **false**로 설정하여 Runbook을 수동으로 실행하거나 Automation 일정 **Schedule-StartVM** 및 **Schedule-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>시나리오 2: 태그를 사용하여 구독에서 VM 중지/시작 시퀀스 지정
분산 워크로드를 지원하는 여러 VM에 둘 이상의 구성 요소를 포함하는 환경에서는 순서대로 시작/중지되는 구성 요소 시퀀스를 지원하는 것이 중요합니다.  다음 단계를 수행하여 이 작업을 수행할 수 있습니다.

1. **External_Start_ResourceGroupNames**및 **External_Stop*ResourceGroupNames** 변수에서 대상으로 지정된 구독의 VM에 양의 정수 값을 갖는 **SequenceStart** 및 **SequenceStop** 태그 추가.  시작 및 중지 작업은 오름차순으로 수행됩니다.  VM 태그를 지정하는 방법을 알아보려면 [Azure에서 Windows 가상 머신에 태그 지정](../virtual-machines/windows/tag.md) 및 [Azure에서 Linux 가상 머신에 태그 지정](../virtual-machines/linux/tag.md)을 참조하세요.
2. 요구 사항을 충족하는 날짜 및 시간으로 일정 **Sequenced-StartVM** 및 **Sequenced StopVM**을 수정하고 일정을 사용하도록 설정합니다.  

구성을 테스트하고 유효성을 검사하려면 **SequencedStartStop_Parent** Runbook을 수동으로 시작하고 *ACTION* 매개 변수를 **start** 또는 **stop**으로 설정하고, *WhatIf* 매개 변수를 **True**로 설정합니다.<br><br> ![Runbook에 대한 매개 변수 구성](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> 이 옵션을 사용하면 프로덕션 VM에 대해 구현하기 전에 발생할 작업을 미리 보고 필요에 맞게 변경할 수 있습니다.  상황에 문제가 없을 경우 이 매개 변수를 **false**로 설정하여 Runbook을 수동으로 실행하거나 Automation 일정 **Sequenced-StartVM** 및 **Sequenced-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>시나리오 3: CPU 사용률에 따라 구독에서 VM 자동 시작/중지
구독에서 VM 실행에 필요한 비용을 관리하는 데 도움을 주기 위해, 이 솔루션은 사용량이 많지 않은 기간 동안에 사용되지 않는 Azure VM을 평가하고, 프로세서 사용률이 x%보다 작아질 경우(예: 몇 시간 후) 자동으로 종료하도록 합니다.  

기본적으로 이 솔루션은 CPU 백분율 메트릭을 평가하고 평균 사용률이 5% 이하인지 파악하도록 미리 구성됩니다.  이러한 기능은 다음 변수를 통해 제어되며, 기본값이 요구 사항을 충족하지 않는 경우 수정할 수 있습니다.

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

구독 및 리소스 그룹 또는 특정 VM 목록 중 하나로만 작업 대상을 지정할 수 있습니다.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>중지 작업의 대상으로 구독 및 리소스 그룹 지정

1. 대상 VM을 지정하도록 **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수를 구성합니다.  
2. **Schedule_AutoStop_CreateAlert_Parent** 일정을 사용하도록 설정하고 업데이트합니다.
3. *ACTION* 매개 변수를 **start**로 설정하고 *WhatIf* 매개 변수를 **True**로 설정하여 **AutoStop_CreateAlert_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

#### <a name="target-stop-action-by-vm-list"></a>VM 목록별로 중지 작업 대상 지정

1. *ACTION* 매개 변수를 **start**로 설정하고, *VMList* 매개 변수에 쉼표로 구분된 VM 목록을 추가하고, *WhatIf* 매개 변수를 **True**로 설정하여 **AutoStop_CreateAlert_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.  
2. 쉼표로 구분된 VM 목록(VM1,VM2,VM3)을 사용하여 **External_ExcludeVMNames** 매개 변수를 구성합니다.
3. 이 시나리오는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupnames** 변수를 고려합니다.  이 시나리오에서는 고유한 자체 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

CPU 사용량에 따라 VM을 중지하는 일정을 만들었으므로 아래 일정 중 하나를 사용하도록 설정하여 시작해야 합니다.

* 구독 및 리소스 그룹로 시작 작업 대상 지정.  **Scheduled-StartVM** 일정을 테스트하고 사용하도록 설정하려면 [시나리오 #1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups)의 단계를 참조하세요.
* 구독, 리소스 그룹 및 태그별로 시작 작업 트리거.  "Sequenced-StartVM" 일정을 테스트하고 사용하도록 설정하려면 [시나리오 #2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags)의 단계를 참조하세요.


### <a name="configuring-e-mail-notifications"></a>전자 메일 알림 구성

솔루션이 배포된 이후의 전자 메일 알림을 구성하려면 다음 3가지 변수를 수정할 수 있습니다.

* External_EmailFromAddress - 보낸 사람 전자 메일 주소를 지정합니다.
* External_EmailToAddress - 알림 전자 메일을 받을 쉼표로 구분된 전자 메일 주소 목록(user@hotmail.com, user@outlook.com)입니다.
* External_IsSendEmail - **Yes**로 설정하는 경우 전자 메일을 받게 되며, 전자 메일 알림을 사용하지 않도록 설정하려면 값을 설정 **No**로 설정합니다.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>시작 및 종료 일정 수정

이 솔루션에서 시작 및 종료 일정을 관리하는 작업은 [Azure Automation에서 runbook 예약](automation-schedules.md)에 설명된 대로 동일한 단계를 따릅니다.     

## <a name="log-analytics-records"></a>Log Analytics 레코드

Automation은 OMS 리포지토리에 두 가지 유형의 레코드를 만듭니다.

### <a name="job-logs"></a>작업 로그

속성 | 설명|
----------|----------|
Caller |  작업을 시작한 사람입니다.  가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
Category | 데이터 유형의 분류입니다.  Automation의 경우 값은 JobLogs입니다.|
CorrelationId | runbook 작업의 상관 관계 ID인 GUID입니다.|
JobId | runbook 작업의 ID인 GUID입니다.|
operationName | Azure에서 수행되는 작업 유형을 지정합니다.  Automation의 경우 값은 Job입니다.|
resourceId | Azure의 리소스 유형을 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다.  Automation에 대한 값은 Azure Automation입니다.|
ResourceType | Azure의 리소스 유형을 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
resultType | runbook 작업의 상태입니다.  가능한 값은 다음과 같습니다.<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- Succeeded입니다.|
resultDescription | runbook 작업 결과 상태를 설명합니다.  가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다.|
RunbookName | Runbook의 이름을 지정합니다.|
SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다.  Automation에 대한 값은 :OpsManager입니다.|
StreamType | 이벤트의 유형을 지정합니다. 가능한 값은 다음과 같습니다.<br>- Verbose입니다.<br>- 출력<br>- 오류<br>- 경고|
SubscriptionId | 작업의 구독 ID를 지정합니다.
Time | runbook 작업이 실행된 날짜 및 시간입니다.|


### <a name="job-streams"></a>작업 스트림

속성 | 설명|
----------|----------|
Caller |  작업을 시작한 사람입니다.  가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
Category | 데이터 유형의 분류입니다.  Automation의 경우 값은 JobStreams입니다.|
JobId | runbook 작업의 ID인 GUID입니다.|
operationName | Azure에서 수행되는 작업 유형을 지정합니다.  Automation의 경우 값은 Job입니다.|
ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
resourceId | Azure의 리소스 ID를 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다.  Automation에 대한 값은 Azure Automation입니다.|
ResourceType | Azure의 리소스 유형을 지정합니다.  Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
resultType | 이벤트가 생성될 당시 Runbook 작업의 결과입니다.  가능한 값은 다음과 같습니다.<br>- InProgress입니다.|
resultDescription | runbook의 출력 스트림을 포함합니다.|
RunbookName | runbook의 이름입니다.|
SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다.  Automation에 대한 값은 OpsManager입니다.|
StreamType | 작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다.|
Time | runbook 작업이 실행된 날짜 및 시간입니다.|

**JobLogs** 또는 **JobStreams** 범주의 레코드를 반환하는 로그 검색을 수행하는 경우, 검색에 의해 반환되는 업데이트를 요약하는 타일 집합을 표시하는 **JobLogs** 또는 **JobStreams** 보기를 선택할 수 있습니다.

## <a name="sample-log-searches"></a>샘플 로그 검색

다음 테이블은 이 솔루션에 의해 수집된 작업 레코드에 대한 샘플 로그 검색을 제공합니다. 

쿼리 | 설명|
----------|----------|
성공적으로 완료된 ScheduledStartStop_Parent Runbook에 대한 작업을 찾습니다. | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
성공적으로 완료된 SequencedStartStop_Parent Runbook에 대한 작업을 찾습니다. | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>솔루션 제거

솔루션을 더 이상 추가로 사용할 필요가 없으면 Automation 계정에서 삭제할 수 있습니다.  솔루션을 삭제하면 runbook만 제거되며, 솔루션이 추가될 때 만들어진 일정 또는 변수는 삭제되지 않습니다.  다른 runbook에서 사용하지 않으려면 해당 자산을 수동으로 삭제해야 합니다.  

솔루션을 삭제하려면 다음 단계를 수행합니다.

1.  Automation 계정의 왼쪽 창에서 **작업 영역**을 선택합니다.  
2.  **솔루션** 페이지에서 솔루션 **Start-Stop-VM[Workspace]**를 선택합니다.  **VMManagementSolution[Workspace]** 페이지의 메뉴에서 **삭제**를 클릭합니다.<br><br> ![VM Mgmt 솔루션 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  **솔루션 삭제** 창에서 솔루션을 삭제할 것임을 확인합니다.
4.  정보가 확인되고 솔루션이 삭제되는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.  솔루션 제거 프로세스가 시작되면 **솔루션** 페이지로 복귀됩니다.  

이 프로세스의 일부로 Automation 계정 및 Log Analytics 작업 영역이 삭제되지는 않습니다.  Log Analytics 작업 영역을 유지하지 않으려는 경우 수동으로 삭제해야 합니다.  Azure Portal에서도 이 작업을 수행할 수 있습니다.   Azure Portal의 홈 화면에서 **Log Analytics**을 선택하고 **Log Analytics** 블레이드에서 작업 영역을 선택한 후 작업 영역 설정 블레이드의 메뉴에서 **삭제**를 선택합니다.  
      
## <a name="next-steps"></a>다음 단계

- Log Analytics를 사용하여 여러 검색 쿼리를 작성하고 Automation 작업 로그를 검토하는 방법에 대한 자세한 내용은 [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)
- Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [Runbook 작업 추적](automation-runbook-execution.md)
- Log Analytics 및 데이터 수집 소스에 대한 자세한 내용은 [Log Analytics에서 Azure Storage 데이터 수집 개요](../log-analytics/log-analytics-azure-storage.md)를 참조하세요.






   

