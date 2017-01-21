---
title: "Azure Automation 그래픽 runbook의 오류 처리 | Microsoft Docs"
description: "이 문서에서는 Azure Automation 그래픽 runbook에서 오류 처리 논리를 구현하는 방법에 대해 설명합니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure Automation 그래픽 runbook의 오류 처리

고려해야 할 주요 runbook 디자인 요소는 성공, 예상된 오류 상태 및 예기치 않은 오류 조건과 같은 runbook에서 발생할 수 있는 다른 문제를 식별하는 것입니다.  이에 따라 감지할 오류 처리가 runbook에 포함되어야 합니다.  그래픽 runbook을 사용하여 활동의 출력에 대한 유효성을 검사하거나 적절한 방식으로 오류를 처리하려는 경우 PowerShell 코드 활동을 수행하거나 활동 출력 링크에서 조건부 논리를 정의하거나 다른 방법을 적용할 수 있습니다.          

runbook을 실행할 때 종종 활동과 함께 발생하는 종료되지 않는 오류가 있을 경우 모든 후속 활동은 이러한 오류와 관계 없이 수행됩니다.  물론 예외를 생성할 수도 있지만 다음 활동이 실행되도록 허용하는 것이 중요합니다. 이 동작의 근거는 PowerShell 언어로 오류를 처리하도록 설계되었기 때문입니다.    

실행 중에 발생할 수 있는 PowerShell 오류 유형으로 종료 오류 또는 종료되지 않는 오류가 있습니다.  차이점은 다음과 같습니다.

* 종료 오류: 실행 중에 명령 (또는 스크립트 실행)을 완전히 중단하는 심각한 오류입니다. 예를 들어 존재하지 않는 cmdlet, cmdlet을 실행하지 못하도록 하는 구문 오류 또는 기타 심각한 오류가 있을 수 있습니다.

* 종료되지 않는 오류: 실패에도 불구하고 실행을 계속할 수 있는 심각하지 않은 오류입니다. 예를 들어 찾을 수 없는 파일, 권한 문제 등의 작동 오류가 있습니다.

Azure Automation 그래픽 runbook은 오류 처리를 포함할 수 있는 기능으로 향상되어 예외를 종료되지 않는 오류로 전환할 수 있고 활동 간에 오류 링크를 만들 수 있습니다. 이렇게 하면 runbook 작성자가 오류를 포착하고 실현되거나 예기치 않은 조건을 관리하는 경로를 가질 수 있습니다.  

## <a name="when-to-use"></a>사용하는 경우

워크플로 실행을 제어하는 경우 오류 또는 예외를 throw하는 중요한 활동이 있을 때마다 runbook의 다음 활동으로 넘어 가지 않도록 적절하게 오류를 처리할 수 있는지 확인하는 것이 중요합니다.  이는 runbook에서 비즈니스 또는 서비스 운영 프로세스를 지원할 때 특히 필요합니다.

Runbook 작성자는 오류를 발생시킬 수 있는 각 활동에 대해 다른 활동을 가리키는 오류 링크를 추가할 수 있습니다.  대상 활동은 코드 활동, cmdlet 호출, 다른 runbook 호출 또는 다른 어떤 유형이 될 수 있습니다. 

또한 대상 활동에는 나가는 링크가 있을 수 있으며, 일반 링크 또는 오류 링크일 수 있으므로 runbook 작성자가 코드 활동을 포함하지 않은 채 복잡한 오류 처리 논리를 구현할 수 있습니다.  일반적 기능을 갖춘 전용 오류 처리 runbook을 만드는 것이 좋지만, PowerShell 코드 활동에서 필수 및 오류 처리 논리가 아닌 유일한 대안입니다.  

예를 들어 VM을 시작하고 여기에 응용 프로그램을 설치하려는 runbook을 고려해 보세요. 하지만 VM이 제대로 시작되지 않으면 다음 두 가지 작업을 수행합니다. 

1. 이 문제에 대한 알림을 보냅니다.
2. 대신 새 VM을 자동으로 프로비전하는 다른 runbook을 시작합니다. 

한 가지 해결 방안으로 2단계의 활동(예: **Start-AzureRmAutomationRunbook** cmdlet)에 연결된 1단계를 처리하는 활동(예: **Write-Warning** cmdlet)을 가리키는 오류 링크를 만드는 것입니다. 

또한 많은 runbook에서 사용하기 위해 이 동작을 일반화하고 앞에서 제안한 지침에 따라 이 두 가지 활동을 별도의 오류 처리 runbook에 배치할 수 있습니다.  이 오류 처리 runbook을 호출하기 전에 원래 runbook의 데이터에서 사용자 지정 메시지를 생성한 다음 이 메시지를 오류 처리 runbook에 매개 변수로 전달할 수 있습니다. 

## <a name="how-to-use"></a>사용 방법

각 활동에는 예외를 종료되지 않는 오류로 전환하는 구성이 있습니다. 이 옵션은 기본적으로 사용하지 않도록 설정됩니다.  오류를 처리하려는 활동에서 이 옵션을 사용하도록 설정해야 합니다.  구성을 사용하면 활동의 종료 오류 및 종료되지 않는 오류를 모두 종료되지 않는 오류로 처리한 다음 오류 링크로 처리할 수 있습니다.  이 설정을 구성한 후에는 오류를 처리할 활동을 만듭니다.  활동이 오류를 생성하면 활동에서 일반 출력을 생성했더라도 일반 링크가 아니라 나가는 오류 링크를 따릅니다.<br><br> ![Automation runbook 오류 링크 예제](media/automation-runbook-graphical-error-handling/error-link-example.png)

간단한 다음 예제에는 가상 컴퓨터의 컴퓨터 이름을 포함하는 변수를 검색한 후 다음 활동으로 가상 컴퓨터를 시작하려고 하는 runbook이 있습니다.<br><br> ![Automation runbook 오류 처리 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

**Get-AutomationVariable** 활동과 **Start-AzureRmVm**은 예외를 오류로 변환하도록 구성됩니다.  변수를 가져오거나 VM을 시작하는 데 문제가 있으면 오류가 생성됩니다.<br><br> ![Automation runbook 오류 처리 활동 설정](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

오류 링크는 이러한 활동에서 단일 **오류 관리** 활동(코드 활동)으로 이동합니다. 이 활동은 *Throw* 키워드를 사용하여 간단한 PowerShell 식으로 구성되어 *$Error.Exception.Message*와 함께 처리를 중지하고 현재 예외를 설명하는 메시지를 가져옵니다.<br><br> ![Automation runbook 오류 처리 코드 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>다음 단계

* 그래픽 runbook의 링크 및 링크 유형에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md#links-and-workflow)을 참조하세요.

* Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [Runbook 작업 추적](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


