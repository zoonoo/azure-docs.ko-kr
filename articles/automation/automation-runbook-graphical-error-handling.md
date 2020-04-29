---
title: Azure Automation 그래픽 runbook의 오류 처리
description: 이 문서에서는 Azure Automation 그래픽 runbook에서 오류 처리 논리를 구현하는 방법에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367077"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure Automation 그래픽 runbook의 오류 처리

Azure Automation 그래픽 runbook에 대해 고려할 주요 디자인 원칙은 runbook이 실행 중에 발생할 수 있는 문제를 식별 하는 것입니다. 이러한 문제는 성공, 예상된 오류 상태 및 예기치 않은 오류 조건을 포함할 수 있습니다.

경우에 따라 runbook 작업에서 발생 하는 종료 되지 않는 오류가 발생 하는 경우 Windows PowerShell은 오류와 관계 없이 뒤에 나오는 활동을 처리 하 여 활동을 처리 합니다. 오류는 예외를 생성할 수 있지만 다음 작업이 계속 실행되도록 허용됩니다.

그래픽 runbook은 실행 문제를 처리 하는 오류 처리 코드를 포함 해야 합니다. 활동의 출력에 대 한 유효성을 검사 하거나 오류를 처리 하려면 PowerShell 코드 활동을 사용 하 여 활동의 출력 링크에서 조건부 논리를 정의 하거나 다른 메서드를 적용 합니다.

Azure Automation 그래픽 runbook은 오류 처리를 포함하는 기능으로 개선되었습니다. 이제 예외를 종료되지 않는 오류로 전환하고 활동 간에 오류 링크를 만들 수 있습니다. 향상 된 프로세스를 통해 runbook에서 오류를 포착 하 고 실현 되거나 예기치 않은 상태를 관리할 수 있습니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="powershell-error-types"></a>PowerShell 오류 유형

Runbook을 실행 하는 동안 발생할 수 있는 PowerShell 오류의 유형은 종료 오류 및 종료 되지 않는 오류입니다.
 
### <a name="terminating-error"></a>종료 오류

종료 오류는 실행 중에 명령이 나 스크립트 실행을 완전히 중단 하는 심각한 오류입니다. 예를 들어 존재 하지 않는 cmdlet, cmdlet이 실행 되지 않도록 하는 구문 오류 및 기타 치명적 오류가 있습니다.

### <a name="non-terminating-error"></a>종료 되지 않는 오류

종료 되지 않는 오류는 오류 조건에도 불구 하 고 실행을 계속할 수 있는 심각 하지 않은 오류입니다. 예를 들어 파일을 찾을 수 없음 오류와 사용 권한 문제 등의 작업 오류가 있습니다.

## <a name="when-to-use-error-handling"></a>오류 처리를 사용하는 경우

중요 한 작업이 오류 또는 예외를 throw 하는 경우 runbook에서 오류 처리를 사용 합니다. Runbook의 다음 활동이 처리 되는 것을 방지 하 고 오류를 적절 하 게 처리 하는 것이 중요 합니다. Runbook이 비즈니스 또는 서비스 운영 프로세스를 지원할 때 오류 처리가 특히 중요 합니다.

오류를 생성할 수 있는 각 활동에 대해 다른 활동을 가리키는 오류 링크를 추가할 수 있습니다. 대상 활동은 코드 활동, cmdlet 호출, 다른 runbook 호출 등을 비롯 한 모든 형식일 수 있습니다. 대상 활동에는 나가는 링크 (일반 또는 오류 링크)도 있을 수 있습니다. 링크를 사용 하면 runbook에서 코드 작업을 적용 하지 않고도 복잡 한 오류 처리 논리를 구현할 수 있습니다.

일반적인 기능을 사용 하 여 전용 오류 처리 runbook을 만드는 것이 좋습니다. 그러나이 방법은 반드시 필요한 것은 아닙니다. 예를 들어 가상 컴퓨터를 시작 하 고 응용 프로그램을 설치 하려고 하는 runbook을 생각해 보겠습니다. VM이 제대로 시작 되지 않으면 다음을 수행 합니다.

1. 이 문제에 대 한 알림을 보냅니다.
2. 대신 새 VM을 자동으로 프로 비전 하는 다른 runbook을 시작 합니다.

한 가지 해결 방법은 runbook에서 1 단계를 처리 하는 활동을 가리키는 오류 링크를 포함 하는 것입니다. 예를 들어 runbook은 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) cmdlet과 `Write-Warning` 같은 2 단계에 대 한 작업에 cmdlet을 연결할 수 있습니다.

또한 이러한 두 활동을 별도의 오류 처리 runbook에 배치 하 여 많은 runbook에서 사용 하기 위해이 동작을 일반화할 수 있습니다. 원래 runbook이이 오류 처리 runbook을 호출 하기 전에 해당 데이터에서 사용자 지정 메시지를 생성 한 다음 오류 처리 runbook에 매개 변수로 전달할 수 있습니다.

## <a name="how-to-use-error-handling"></a>오류 처리 사용 방법

Runbook의 각 작업에는 예외를 종료 되지 않는 오류로 전환 하는 구성 설정이 있습니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다. Runbook에서 오류를 처리 하는 모든 작업에 대해이 설정을 사용 하는 것이 좋습니다. 이 설정은 runbook에서 오류 링크를 사용 하 여 작업의 종료 오류와 종료 되지 않는 오류를 모두 처리 하지 않도록 합니다.  

구성 설정을 사용 하도록 설정한 후 runbook에서 오류를 처리 하는 작업을 만들도록 합니다. 활동에서 오류를 생성 하는 경우 나가는 오류 링크를 따릅니다. 활동에서 일반 출력을 생성 하는 경우에도 일반 링크는 따르지 않습니다.<br><br> ![Automation runbook 오류 링크 예제](media/automation-runbook-graphical-error-handling/error-link-example.png)

다음 예에서는 runbook이 VM의 컴퓨터 이름을 포함 하는 변수를 검색 합니다. 그런 다음, 다음 작업을 사용 하 여 VM을 시작 하려고 시도 합니다.<br><br> ![Automation runbook 오류 처리 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` 작업 및 [new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet은 예외를 오류로 변환 하도록 구성 됩니다. 변수를 가져오거나 VM을 시작 하는 데 문제가 있는 경우 코드에서 오류를 생성 합니다.<br><br> ![Automation runbook 오류-작업 설정을](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)처리 하는 중입니다.

오류 링크는 이러한 활동에서 단일 `error management` 코드 활동으로 흐릅니다. 이 활동은 `throw` 키워드를 사용 하 여 처리를 `$Error.Exception.Message` 중지 하 고 현재 예외를 설명 하는 메시지를 가져오는 간단한 PowerShell 식으로 구성 됩니다.<br><br> ![Automation runbook 오류 처리 코드 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>다음 단계

* 그래픽 runbook의 링크 및 링크 유형에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md#links-and-workflow)을 참조하세요.

* Runbook 실행, runbook 작업 모니터링 및 기타 기술 세부 정보에 대 한 자세한 내용은 [Azure Automation에서 runbook 실행](automation-runbook-execution.md)을 참조 하세요.