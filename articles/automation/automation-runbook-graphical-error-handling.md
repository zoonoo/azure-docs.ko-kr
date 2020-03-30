---
title: Azure Automation 그래픽 runbook의 오류 처리
description: 이 문서에서는 Azure Automation 그래픽 runbook에서 오류 처리 논리를 구현하는 방법에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367077"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure Automation 그래픽 runbook의 오류 처리

Azure 자동화 그래픽 런북에서 고려해야 할 주요 디자인 원칙은 실행 중에 Runbook에서 발생할 수 있는 문제를 식별하는 것입니다. 이러한 문제는 성공, 예상된 오류 상태 및 예기치 않은 오류 조건을 포함할 수 있습니다.

Runbook 활동에서 발생하는 종료되지 않는 오류가 있는 경우 Windows PowerShell은 오류에 관계없이 다음 작업을 처리하여 활동을 처리합니다. 오류는 예외를 생성할 수 있지만 다음 작업이 계속 실행되도록 허용됩니다.

그래픽 런북에는 실행 문제를 처리하기 위한 오류 처리 코드가 포함되어야 합니다. 활동의 출력을 확인하거나 오류를 처리하려면 PowerShell 코드 활동을 사용하거나 활동의 출력 링크에서 조건부 논리를 정의하거나 다른 메서드를 적용할 수 있습니다.

Azure Automation 그래픽 runbook은 오류 처리를 포함하는 기능으로 개선되었습니다. 이제 예외를 종료되지 않는 오류로 전환하고 활동 간에 오류 링크를 만들 수 있습니다. 개선된 프로세스를 통해 Runbook에서 오류를 포착하고 실현또는 예기치 않은 조건을 관리할 수 있습니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="powershell-error-types"></a>PowerShell 오류 유형

Runbook 실행 중에 발생할 수 있는 PowerShell 오류 유형은 종료 오류 및 종료 되지 않는 오류입니다.
 
### <a name="terminating-error"></a>종료 오류

종료 오류는 명령 또는 스크립트 실행을 완전히 중지 하는 실행 하는 동안 심각한 오류입니다. 예를 들어 존재하지 않는 cmdlet, cmdlet실행을 방지하는 구문 오류 및 기타 치명적인 오류가 있습니다.

### <a name="non-terminating-error"></a>종료되지 않는 오류

종료되지 않는 오류는 오류 조건에도 불구하고 실행을 계속할 수 있는 심각하지 않은 오류입니다. 예를 들어 파일에서 찾을 수 없는 오류 및 사용 권한 문제와 같은 작동 오류가 있습니다.

## <a name="when-to-use-error-handling"></a>오류 처리를 사용하는 경우

중요한 활동이 오류 나 예외를 던질 때 Runbook에서 오류 처리를 사용합니다. Runbook의 다음 활동이 처리되지 않도록 하고 오류를 적절하게 처리하는 것이 중요합니다. Runbook이 비즈니스 또는 서비스 운영 프로세스를 지원할 때 오류를 처리하는 것이 특히 중요합니다.

오류를 생성할 수 있는 각 활동에 대해 다른 활동을 가리키는 오류 링크를 추가할 수 있습니다. 대상 활동은 코드 활동, cmdlet 호출, 다른 Runbook 호출 등을 비롯한 모든 유형일 수 있습니다. 대상 활동에는 일반 링크 또는 오류 링크중 나가는 링크가 있을 수도 있습니다. 이 링크를 사용하면 Runbook에서 코드 활동에 의존하지 않고 복잡한 오류 처리 논리를 구현할 수 있습니다.

일반적인 기능을 갖춘 전용 오류 처리 Runbook을 만드는 것이 권장되지만 이 방법은 필수는 아닙니다. 예를 들어 가상 컴퓨터를 시작하고 응용 프로그램을 설치하려는 Runbook을 생각해 보십시오. VM이 올바르게 시작되지 않으면 다음과 같은

1. 이 문제에 대한 알림을 보냅니다.
2. 대신 새 VM을 자동으로 프로비전하는 다른 Runbook을 시작합니다.

한 가지 해결책은 Runbook에 1단계를 처리하는 활동을 가리키는 오류 링크를 두는 것입니다. 예를 들어 Runbook은 `Write-Warning` [시작-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) cmdlet과 같은 2단계의 활동에 cmdlet을 연결할 수 있습니다.

또한 이러한 두 작업을 별도의 오류 처리 Runbook에 배치하여 여러 Runbook에서 사용하도록 이 동작을 일반화할 수도 있습니다. 원래 Runbook에서 이 오류 처리 Runbook을 호출하기 전에 해당 데이터에서 사용자 지정 메시지를 생성한 다음 오류 처리 Runbook에 매개 변수로 전달할 수 있습니다.

## <a name="how-to-use-error-handling"></a>오류 처리 사용 방법

Runbook의 각 활동에는 예외를 종료되지 않는 오류로 변환하는 구성 설정이 있습니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다. Runbook에서 오류를 처리하는 모든 활동에 대해 이 설정을 사용하도록 설정하는 것이 좋습니다. 이 설정을 사용하면 Runbook에서 오류 링크를 사용하여 활동의 종료 오류와 종료되지 않는 오류를 모두 종료되지 않는 오류로 처리합니다.  

구성 설정을 사용하도록 설정한 후 Runbook에서 오류를 처리하는 활동을 만들게 합니다. 활동이 오류를 생성하면 나가는 오류 링크가 따릅니다. 활동이 일반 출력을 생성하는 경우에도 일반 링크는 따르지 않습니다.<br><br> ![Automation runbook 오류 링크 예제](media/automation-runbook-graphical-error-handling/error-link-example.png)

다음 예제에서 Runbook은 VM의 컴퓨터 이름을 포함하는 변수를 검색합니다. 그런 다음 다음 활동으로 VM을 시작하려고 시도합니다.<br><br> ![자동화 Runbook 오류 처리 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` 활동 및 [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet은 예외를 오류로 변환하도록 구성됩니다. 변수를 얻거나 VM을 시작하는 데 문제가 있는 경우 코드는 오류를 생성합니다.<br><br> ![자동화 Runbook 오류 처리](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)활동 설정 .

오류 링크는 이러한 활동에서 `error management` 단일 코드 활동으로 흐르게 됩니다. 이 활동은 현재 예외를 설명하는 메시지를 얻기 `throw` `$Error.Exception.Message` 위해 키워드를 사용하여 처리를 중지하는 간단한 PowerShell 식으로 구성됩니다.<br><br> ![자동화 Runbook 오류 처리 코드 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>다음 단계

* 그래픽 runbook의 링크 및 링크 유형에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md#links-and-workflow)을 참조하세요.

* Runbook 실행, Runbook 작업 모니터링 및 기타 기술 세부 정보에 대한 자세한 내용은 [Azure Automation의 Runbook 실행을](automation-runbook-execution.md)참조하십시오.