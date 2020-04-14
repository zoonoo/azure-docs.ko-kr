---
title: Azure Automation Runbook 형식
description: Azure 자동화에서 사용할 수 있는 다양한 Runbook 유형과 사용할 유형을 결정하는 고려 사항에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 4e8a5d2d168b8f60b7a32a8af358c6097003de60
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261298"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook 형식

Azure 자동화 프로세스 자동화 서비스는 다음 표에 정의된 대로 여러 유형의 Runbook을 지원합니다. 프로세스 자동화 환경에 대한 자세한 내용은 [Azure Automation의 Runbook 실행을](automation-runbook-execution.md)참조하십시오.

| Type | Description |
|:--- |:--- |
| [그래픽](#graphical-runbooks)|Windows PowerShell을 기반으로 하며 Azure 포털의 그래픽 편집기에서 완전히 만들고 편집한 그래픽 런북입니다. |
| [그래픽 PowerShell 워크플로](#graphical-runbooks)|Windows PowerShell 워크플로를 기반으로 하며 Azure 포털의 그래픽 편집기에서 완전히 만들고 편집한 그래픽 런북입니다. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell 스크립팅을 기반으로 하는 텍스트 런북입니다. |
| [PowerShell 워크플로](#powershell-workflow-runbooks)|Windows PowerShell 워크플로 스크립팅을 기반으로 하는 텍스트 런북입니다. |
| [Python](#python-runbooks) |파이썬 스크립팅을 기반으로 하는 텍스트 런북입니다. |

특정 Runbook에 사용할 유형을 결정할 때 다음 사항을 고려하십시오.

* Runbook을 그래픽에서 텍스트 유형으로 변환하거나 다른 방법으로 변환할 수 없습니다.
* 다른 유형의 Runbook을 자식 런북으로 사용할 때에는 제한이 있습니다. 자세한 내용은 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure 포털의 그래픽 편집기에서 그래픽 편집기에서 그래픽 및 그래픽 PowerShell 워크플로 런북을 만들고 편집할 수 있습니다. 그러나 다른 도구로는 이 유형의 Runbook을 만들거나 편집할 수 없습니다. 그래픽 런북의 주요 기능 :

* 자동화 계정의 파일로 내보낸 다음 다른 자동화 계정으로 가져올 수 있습니다. 
* PowerShell 코드를 생성합니다. 
* 가져오는 동안 그래픽 PowerShell 워크플로 런북으로 또는 그래픽으로 변환할 수 있습니다. 

### <a name="advantages"></a>장점

* 시각적 인삽입 링크 구성 작성 모델을 사용합니다.
* 프로세스가 어떻게 흐르는지에 초점을 맞춥니다.
* 관리 과정을 시각적으로 나타냅니다.
* 다른 Runbook을 하위 실행책으로 포함시켜 상위 수준의 워크플로를 만듭니다.
* 모듈식 프로그래밍을 장려합니다.

### <a name="limitations"></a>제한 사항

* Azure 포털 외부에서 만들거나 편집할 수 없습니다.
* 복잡한 논리를 실행하려면 PowerShell 코드가 포함된 코드 작업이 필요할 수 있습니다.
* [텍스트 형식](automation-runbook-types.md)중 하나로 변환할 수 없으며 텍스트 Runbook을 그래픽 형식으로 변환할 수도 없습니다. 
* 그래픽 워크플로에서 만드는 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 모든 코드 활동에서 만든 코드를 볼 수 있습니다.
* Linux 하이브리드 Runbook 작업자에서 런북을 실행할 수 없습니다. [하이브리드 Runbook 작업자를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화를](automation-hybrid-runbook-worker.md)참조하십시오.

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로의 부가적인 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
* 실행하기 전에 컴파일할 필요가 없으므로 PowerShell 워크플로 런북보다 빠르게 시작합니다.
* Azure 및 하이브리드 Runbook 작업자에서 Windows 및 Linux 모두에서 실행합니다.

### <a name="limitations"></a>제한 사항

* PowerShell 스크립팅에 익숙해야 합니다.
* Runbook은 병렬 [처리를](automation-powershell-workflow.md#parallel-processing) 사용하여 여러 작업을 병렬로 실행할 수 없습니다.
* Runbook은 [검사점에서](automation-powershell-workflow.md#checkpoints) 오류가 있는 경우 Runbook을 다시 시작할 수 없습니다.
* 새 작업을 만드는 [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) cmdlet을 사용하여 PowerShell 워크플로 런북과 그래픽 런북만 자식 런북으로 포함할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

다음은 PowerShell Runbook에서 현재 알려진 문제입니다.

* PowerShell Runbook은 null 값으로 암호화되지 않은 [변수 자산을](automation-variables.md) 검색할 수 없습니다.
* PowerShell Runbook은 이름에 가 포함된 `*~*`변수 자산을 검색할 수 없습니다.
* PowerShell 실행책의 루프에서 [프로세스 를](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) 가져옵니다 약 80번의 반복 후에 충돌이 일 수 있습니다.
* PowerShell Runbook은 대량의 데이터를 한 번에 출력 스트림에 쓰려고 하면 실패할 수 있습니다. 일반적으로 Runbook 출력을 큰 개체로 작업하는 데 필요한 정보만 있으면 이 문제를 해결할 수 있습니다. 예를 들어 제한 `Get-Process` 없이 사용하는 대신 `Get-Process | Select ProcessName, CPU`에서와 같이 cmdlet 출력만 필요한 매개 변수를 가질 수 있습니다.

## <a name="powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook

PowerShell 워크플로 Runbook은 [Windows PowerShell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 Runbook입니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다. 오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
* 검사점 [사용으로](automation-powershell-workflow.md#checkpoints) 오류가 있는 경우 작업을 다시 시작합니다.
* [병렬 처리를](automation-powershell-workflow.md#parallel-processing) 사용하여 여러 작업을 병렬로 수행합니다.
* 다른 그래픽 런북 및 PowerShell 워크플로런북을 하위 런북으로 포함시켜 상위 수준의 워크플로를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항

* PowerShell 워크플로우에 익숙해야 합니다.
* Runbook은 [역직렬화된 개체와](automation-powershell-workflow.md#code-changes)같은 PowerShell 워크플로의 추가 복잡성을 처리해야 합니다.
* Runbook을 실행하기 전에 컴파일해야 하므로 PowerShell 런북보다 시작하는 데 시간이 더 오래 걸릴 수 있습니다.
* `Start-AzAutomationRunbook` cmdlet을 사용하여 PowerShell 런북만 자식 런북으로 포함할 수 있습니다.
* Runbook은 Linux 하이브리드 Runbook 작업자에서 실행할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbook

Python Runbook은 Python 2에서 컴파일됩니다. Azure 포털의 텍스트 편집기에서 Runbook의 코드를 직접 편집할 수 있습니다. 오프라인 텍스트 편집기와 [Runbook을](manage-runbooks.md) Azure 자동화로 가져올 수도 있습니다.

### <a name="advantages"></a>장점

* 강력한 파이썬 라이브러리를 사용합니다.
* Azure 또는 Linux 하이브리드 Runbook 작업자에서 실행할 수 있습니다. Windows 하이브리드 Runbook 작업자는 [python2.7설치를](https://www.python.org/downloads/release/latest/python2) 통해 지원됩니다.

### <a name="limitations"></a>제한 사항

* 파이썬 스크립팅에 익숙해야 합니다.
* 현재 파이썬 2만 지원됩니다. 모든 파이썬 3 특정 함수가 실패합니다.
* 타사 라이브러리를 사용하려면 패키지를 자동화 계정으로 [가져와야](python-packages.md) 합니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 런북 작성에 대한 자세한 내용은 [Azure Automation의 그래픽 작성을](automation-graphical-authoring-intro.md)참조하십시오.
* Runbook에 대한 PowerShell 및 PowerShell 워크플로의 차이점을 이해하려면 [Windows PowerShell 워크플로 학습을](automation-powershell-workflow.md)참조하십시오.
* Runbook을 만들거나 가져오는 방법에 대한 자세한 내용은 [Azure Automation의 Runbook 관리를](manage-runbooks.md)참조하십시오.
* 언어 참조 및 학습 모듈을 포함하여 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
