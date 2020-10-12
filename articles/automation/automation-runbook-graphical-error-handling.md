---
title: Azure Automation 그래픽 Runbook의 오류 처리
description: 이 문서에서는 그래픽 Runbook에서 오류 처리 논리를 구현하는 방법에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 8af64f2189625bcff5271855d6c0102551d1a535
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185962"
---
# <a name="handle-errors-in-graphical-runbooks"></a>그래픽 Runbook의 오류 처리

Azure Automation 그래픽 Runbook에서 고려할 주요 설계 원칙은 Runbook 실행 중에 발생할 수 있는 문제의 식별입니다. 이러한 문제는 성공, 예상된 오류 상태 및 예기치 않은 오류 조건을 포함할 수 있습니다.

종종 Runbook 작업에서 발생한 종료되지 않는 오류가 있는 경우, Windows PowerShell은 오류에 관계없이 이후의 모든 작업을 처리하여 해당 작업을 처리합니다. 오류는 예외를 생성할 수 있지만 다음 작업이 계속 실행되도록 허용됩니다.

그래픽 Runbook은 실행 문제를 처리하는 오류 처리 코드를 포함해야 합니다. 작업 출력의 유효성을 검사하거나 오류를 처리하기 위해 PowerShell 코드 작업을 사용하거나, 작업의 출력 링크에 조건부 논리를 정의하거나, 다른 방법을 적용할 수 있습니다.

Azure Automation 그래픽 runbook은 오류 처리를 포함하는 기능으로 개선되었습니다. 이제 예외를 종료되지 않는 오류로 전환하고 활동 간에 오류 링크를 만들 수 있습니다. 이 향상된 프로세스를 통해 Runbook이 오류를 포착하고, 실현되거나 예기치 않은 조건을 관리할 수 있습니다. 

## <a name="powershell-error-types"></a>PowerShell 오류 유형

Runbook 실행 중에 발생할 수 있는 PowerShell 오류 유형에는 종료 오류와 종료되지 않는 오류가 있습니다.
 
### <a name="terminating-error"></a>종료 오류

종료 오류는 실행 중에 명령 또는 스크립트 실행을 완전히 중단하는 심각한 오류입니다. 존재하지 않는 cmdlet, cmdlet 실행을 차단하는 구문 오류 또는 기타 심각한 오류 등이 여기에 해당합니다.

### <a name="non-terminating-error"></a>종료되지 않는 오류

종료되지 않는 오류는 오류 조건이지만 실행을 계속할 수 있는 심각하지 않은 오류입니다. 찾을 수 없는 파일 오류 및 권한 문제 등의 작동 오류가 여기에 해당합니다.

## <a name="when-to-use-error-handling"></a>오류 처리를 사용하는 경우

중요한 작업이 오류 또는 예외를 throw하는 경우 Runbook에서 오류 처리를 사용합니다. Runbook의 다음 활동은 처리되지 않게 하면서 오류를 적합하게 처리하는 것이 중요합니다. 오류 처리는 Runbook에서 비즈니스 또는 서비스 운영 프로세스를 지원할 때 특히 중요합니다.

## <a name="add-error-links"></a>오류 링크 추가

오류를 야기할 수 있는 각각의 작업에 대해 다른 작업을 가리키는 오류 링크를 추가할 수 있습니다. 대상 작업은 코드 작업, cmdlet 호출, 다른 Runbook 호출 등을 비롯한 모든 유형이 될 수 있습니다. 또한 대상 작업에 정상 또는 오류인 나가는 링크가 있을 수도 있습니다. 이러한 링크를 통해 Runbook이 코드 작업에 의존하지 않고도 복잡한 오류 처리 논리를 구현할 수 있습니다.

권장되는 방식은 일반적인 기능으로 전용 오류 처리 Runbook을 만드는 것이지만 필수는 아닙니다. 가상 머신을 시작하고 그 위에 애플리케이션을 설치하는 Runbook을 예로 들어 보겠습니다. VM이 제대로 시작되지 않는 경우

1. 이 문제에 대한 알림을 보냅니다.
2. 대신 새 VM을 자동으로 프로비저닝하는 다른 Runbook을 시작합니다.

한 가지 해결 방법은 1단계를 처리하는 작업을 가리키는 오류 링크를 Runbook에 배치하는 것입니다. 예를 들어 Runbook이 `Write-Warning` cmdlet을 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) cmdlet 등의 2단계용 작업에 연결할 수 있습니다.

또한 이 두 가지 동작을 별도의 오류 처리 Runbook에 배치하여 여러 Runbook에서 이 동작을 사용하도록 일반화할 수 있습니다. 원래의 Runbook이 이 오류 처리 Runbook을 호출하기 전에 데이터에서 사용자 지정 메시지를 구성한 다음, 오류 처리 Runbook에 매개 변수로 전달할 수 있습니다.

## <a name="turn-exceptions-into-non-terminating-errors"></a>예외를 종료되지 않는 오류로 전환

Runbook의 각 작업에는 예외를 종료되지 않는 오류로 전환하는 구성 설정이 있습니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다. Runbook이 오류를 처리하는 모든 작업에 대해 이 설정을 사용하는 것이 좋습니다. 이 설정은 Runbook이 작업의 종료 및 종료되지 않은 오류를 모두 오류 링크를 사용하여 종료되지 않은 오류로 처리합니다.  

구성 설정을 사용하도록 설정한 후 Runbook이 오류를 처리하는 작업을 만들게 합니다. 작업에서 오류가 발생하면 나가는 오류 링크를 따릅니다. 작업에서 정상 출력이 발생하더라도 정상 링크를 따르지 않습니다.<br><br> ![Automation runbook 오류 링크 예제](media/automation-runbook-graphical-error-handling/error-link-example.png)

다음 예에서 Runbook은 VM의 컴퓨터 이름을 포함하는 변수를 검색합니다. 그런 다음, VM을 시작하는 다음 작업을 시도합니다.<br><br> ![Automation Runbook 오류 처리 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` 작업과 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet은 예외를 오류로 변환하도록 구성됩니다. 변수를 가져오거나 VM을 시작하는 데 문제가 있으면 코드에서 오류가 생성됩니다.<br><br> ![Automation Runbook 오류 처리 활동 설정](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

오류 링크는 이러한 작업에서 단일 `error management` 코드 작업으로 흐릅니다. 이 활동은 `throw` 키워드를 사용하여 처리를 중지하고 `$Error.Exception.Message`를 통해 현재 예외를 설명하는 메시지를 가져오는 간단한 PowerShell 식으로 구성되어 있습니다.<br><br> ![Automation Runbook 오류 처리 코드 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>다음 단계

* 그래픽 Runbook 오류를 해결에 대한 자세한 내용은 [Runbook 문제 해결](troubleshoot/runbooks.md)을 참조하세요.
