---
title: "Log Analytics 경고에서 Azure Automation Runbook 호출 | Microsoft Docs"
description: "이 문서는 Microsoft OMS Log Analytics 경고에서 Automation Runbook을 호출하는 방법에 대한 개요를 제공합니다."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: 10b445f8fcaa80182119e47f37ffb11240a46869
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2017
---
# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>OMS Log Analytics 경고에서 Azure Automation Runbook 호출

프로세스 이용률 또는 비즈니스 응용 프로그램의 기능에 중요한 응용 프로그램 프로세서의 장시간 급증 등의 기준과 결과가 일치하는 경우 경고 레코드를 만들기 위해 Log Analytics에서 경고를 구성하면, 문제를 자동으로 해결하기 위해 경고가 Automation Runbook을 자동으로 실행할 수 있습니다.  

다음과 같은 경고 구성에서 Runbook을 호출하는 데는 두 가지 옵션이 있습니다.

1. 웹후크를 사용합니다.
   * OMS 작업 영역이 Automation 계정에 연결되지 않은 경우에만 사용할 수 있는 옵션입니다.
   * OMS 작업 영역에 연결된 Automation 계정이 이미 있는 경우에도 이 옵션을 계속 사용할 수 있습니다.  

2. Runbook을 직접 선택합니다.
   * 이 옵션은 OMS 작업 영역이 Automation 계정에 연결된 경우에만 사용할 수 있습니다.

## <a name="calling-a-runbook-using-a-webhook"></a>웹후크를 사용하여 Runbook 호출

웹후크를 사용하면 단일 HTTP 요청을 통해 Azure Automation에서 특정 Runbook을 시작할 수 있습니다. [Log Analytics 경고](../log-analytics/log-analytics-alerts.md#alert-rules)를 구성하여 경고 작업으로서 웹후크를 사용하여 Runbook을 호출하려면 먼저 이 방법을 사용하여 호출되는 Runbook에 대한 웹후크를 만들어야 합니다. [웹후크 만들기](automation-webhooks.md#creating-a-webhook) 문서의 단계를 수행하고, 경고 규칙을 구성하는 동안 참조할 수 있도록 웹후크 URL을 기록하기 위해 기억해 둡니다.   

## <a name="calling-a-runbook-directly"></a>Runbook 직접 호출

Automation & Control 제품이 OMS 작업 영역에 설치되고 구성된 경우 경고에 대한 Runbook 작업 옵션을 구성하면, **Runbook 선택** 드롭다운 목록에서 모든 Runbook을 볼 수 있고 경고에 대한 응답으로 실행하려는 특정 Runbook을 선택할 수 있습니다. 선택한 Runbook은 Azure Cloud 또는 Hybrid Runbook Worker의 작업 영역에서 실행할 수 있습니다. Runbook 옵션을 사용하여 경고를 만들면 웹후크는 Runbook에 대해 생성됩니다. Automation 계정으로 이동하고 선택한 Runbook의 웹후크 창을 탐색하면 웹후크를 볼 수 있습니다. 경고를 삭제하더라도 웹후크는 삭제되지 않지만 사용자는 웹후크를 수동으로 삭제할 수 있습니다. 이는 구성된 Automation 계정을 유지하기 위해 결국은 삭제해야 하는 분리된 항목이기 때문에 웹후크를 삭제하지 않더라도 문제가 되지 않습니다.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Runbook의 특징(두 가지 옵션)

Log Analytics 경고에서 Runbook을 호출하기 위한 두 가지 방법에는 경고 규칙을 구성하기 전에 이해할 필요가 있는 특징이 있습니다.

* **Object** 유형인 **WebhookData**라는 Runbook 입력 매개 변수가 있어야 합니다. 이는 필수 사항이거나 선택 사항일 수 있습니다. 경고는 이 입력 매개 변수를 사용하여 검색 결과를 Runbook으로 전달합니다.

    ```powershell
    param  
        (  
        [Parameter (Mandatory=$true)]  
        [object] $WebhookData  
        )
    ```
*  WebhookData를 PowerShell 개체로 변환하는 코드가 있어야 합니다.

    ```powershell
    $SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
    ```

    *$SearchResult*는 개체 배열이며, 각 개체는 하나의 검색 결과의 값을 가진 필드를 포함합니다.

## <a name="example-walkthrough"></a>연습 예

Windows 서비스를 시작하는 다음 예제 그래픽 Runbook을 사용하여 이 프로세스가 어떻게 작동하는지에 대해 설명합니다.<br><br> ![Windows 서비스 그래픽 Runbook 시작](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Runbook에는 **WebhookData**라고 하는 **Object** 유형의 입력 매개 변수가 한 개 있으며 \*.SearchResult\*를 포함한 경고에서 전달된 웹후크 데이터를 포함합니다.<br><br> ![Runbook 입력 매개 변수](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

이 예에서는 Log Analytics에서 두 개의 사용자 지정 필드인 \*SvcDisplayName\_CF\* 및 \*SvcState\_CF\*를 만들어 시스템 이벤트 로그에 기록된 이벤트에서 서비스 표시 이름 및 서비스의 상태(즉, 실행 중 또는 중지)를 추출합니다. 그런 다음 Windows 시스템에서 인쇄 스풀러 서비스가 중지되었을 때 이를 감지할 수 있도록 `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 검색 쿼리로 경고 규칙을 만듭니다. 모든 서비스가 관심의 대상이 될 수 있지만 이 예에서는 Windows OS에 포함된 기존 서비스 중 하나를 참조합니다. 경고 작업은 이 예제에서 사용된 Runbook을 실행하고 대상 시스템에서 사용할 수 있는 Hybrid Runbook Worker에서 실행하도록 구성됩니다.   

Runbook 코드 활동 **LA에서 서비스 이름 가져오기**는 JSON 형식 문자열을 개체 형식으로 변환하고 \*SvcDisplayName\_CF\* 항목으로 필터링하여 Windows 서비스의 표시 이름을 추출하고, 이 값을 서비스를 다시 시작하기 전에 서비스가 중지되는지 확인하는 다음 활동으로 전달합니다. *SvcDisplayName_CF*는 이 예제를 보여주기 위해 Log Analytics에서 생성된 [사용자 지정 필드](../log-analytics/log-analytics-custom-fields.md)입니다.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

서비스가 중지되면 Log Analytics의 경고 규칙은 일치 항목을 검색하고 Runbook을 트리거하고 경고 컨텍스트를 Runbook에 보냅니다. Runbook은 서비스가 중지되었는지 확인하고, 중지된 경우 서비스를 다시 시작하며, 정상적으로 시작되는지 확인하고, 결과를 출력하는 작업을 수행합니다.     

또는 OMS 작업 영역에 연결된 Automation 계정에 없는 경우, 웹후크 작업으로 경고 규칙을 구성하여 Runbook을 트리거하고 앞서 언급한 지침을 따라 Runbook을 구성하여 \*.SearchResult\*에서 JSON 형식 문자열 및 필터를 변환합니다.    

## <a name="next-steps"></a>다음 단계

* Log Analytics의 경고와 생성 방법에 대한 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md)를 참조하세요.

* 웹후크를 사용하여 Runbook을 트리거하는 방법을 이해하려면 [Azure Automation 웹후크](automation-webhooks.md)를 참조하세요.
