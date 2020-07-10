---
title: Azure Automation Runbook 형식
description: 이 문서에서는 Azure Automation에서 사용할 수 있는 Runbook의 유형 및 사용할 형식을 결정하기 위한 고려 사항을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 24d0123eecc56b56573e94d831283d8d360cd16e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185928"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook 형식

Azure Automation 프로세스 자동화 기능은 다음 표에 정의된 대로 여러 유형의 Runbook을 지원합니다. 프로세스 자동화 환경에 대한 자세한 내용은 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)을 참조하세요.

| Type | Description |
|:--- |:--- |
| [그래픽](#graphical-runbooks)|Windows PowerShell을 기반으로 하며 Azure Portal의 그래픽 편집기로 완전하게 생성 및 편집된 그래픽 Runbook입니다. |
| [그래픽 PowerShell 워크플로](#graphical-runbooks)|Windows PowerShell 워크플로를 기반으로 하며 Azure Portal의 그래픽 편집기로 완전하게 생성 및 편집된 그래픽 Runbook입니다. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell 스크립팅을 기반으로 하는 텍스트 Runbook입니다. |
| [PowerShell 워크플로](#powershell-workflow-runbooks)|Windows PowerShell 워크플로 스크립팅을 기반으로 하는 텍스트 Runbook입니다. |
| [Python](#python-runbooks) |Python 스크립팅을 기반으로 하는 텍스트 Runbook입니다. |

특정 Runbook에 사용할 형식을 결정할 때 다음과 같은 사항을 고려합니다.

* Runbook을 그래픽에서 텍스트 형식으로 또는 그 반대로 변환할 수 없습니다.
* 형식이 다른 Runbook을 자식 Runbook으로 사용하는 경우 제한 사항이 있습니다. 자세한 내용은 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure Portal에서 그래픽 편집기를 사용하여 그래픽 및 그래픽 PowerShell 워크플로 Runbook을 만들고 편집할 수 있습니다. 그러나 다른 도구를 사용하여 이 유형의 Runbook을 만들거나 편집할 수 없습니다. 그래픽 Runbook의 주요 기능:

* Automation 계정의 파일로 내보낸 다음, 다른 Automation 계정으로 가져올 수 있습니다. 
* PowerShell 코드를 생성합니다. 
* 가져오는 동안 그래픽 PowerShell 워크플로 Runbook으로 변환하거나 그 반대로 변환될 수 있습니다. 

### <a name="advantages"></a>장점

* 시각적 삽입-링크-구성 제작 모델을 사용합니다.
* 프로세스를 통해 데이터가 흐르는 방식에 집중합니다.
* 관리 과정을 시각적으로 나타냅니다.
* 다른 Runbook을 자식 Runbook으로 포함하여 고급 워크플로를 만듭니다.
* 모듈식 프로그래밍을 장려합니다.

### <a name="limitations"></a>제한 사항

* Azure Portal 외부에서 만들거나 편집할 수 없습니다.
* 복잡한 논리를 실행하기 위해 PowerShell 코드를 포함하는 코드 작업이 필요할 수 있습니다.
* [텍스트 형식](automation-runbook-types.md) 중 하나로 변환할 수 없으며 텍스트 Runbook을 그래픽 형식으로 변환할 수 없습니다. 
* 그래픽 워크플로가 만든 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 코드 작업에서 만든 코드를 볼 수 있습니다.
* Linux Hybrid Runbook Worker에서 Runbook을 실행할 수 없습니다. [Hybrid Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화](automation-hybrid-runbook-worker.md)를 참조하세요.

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로의 부가적인 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
* 실행 전에 컴파일이 필요 없기 때문에 PowerShell 워크플로 Runbook보다 빨리 시작됩니다.
* Windows 및 Linux 둘 다 Azure 및 Hybrid Runbook Worker에서 실행합니다.

### <a name="limitations"></a>제한 사항

* PowerShell 스크립팅에 대해 잘 알아야 합니다.
* Runbook에서는 [병렬 처리](automation-powershell-workflow.md#use-parallel-processing)를 사용하여 여러 작업을 병렬로 실행할 수 없습니다.
* Runbook에서는 오류 발생 시 [검사점](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)을 사용하여 Runbook을 다시 시작할 수 없습니다.
* 새 작업을 만드는 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) cmdlet을 사용하여 자식 Runbook으로서의 PowerShell 워크플로 Runbook 및 그래픽 Runbook만 포함할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

PowerShell Runbook에 대해 현재 알려진 문제는 다음과 같습니다.

* PowerShell Runbook이 null 값을 갖는 암호화되지 않은 [변수 자산](./shared-resources/variables.md)을 가져올 수 없습니다.
* PowerShell Runbook은 이름에 `*~*`가 포함된 변수 자산을 검색할 수 없습니다.
* PowerShell Runbook의 반복적인 [Get-Process](/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) 작업은 80회 반복 후에 크래시가 발생할 수 있습니다.
* PowerShell Runbook이 한 번에 대량의 데이터를 출력 스트림에 쓰려고 하면 실패할 수 있습니다. Runbook에서 큰 개체로 작업하는 데 필요한 정보만 출력하면 일반적으로 이 문제를 해결할 수 있습니다. 예를 들어 제한 없이 `Get-Process`를 사용하는 대신 `Get-Process | Select ProcessName, CPU`에서와 같이 cmdlet이 필요한 매개 변수만 출력하도록 할 수 있습니다.

## <a name="powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook

PowerShell 워크플로 Runbook은 [Windows PowerShell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 Runbook입니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다. 오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
* 오류 발생 시 [검사점](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)을 사용하여 작업을 다시 시작합니다.
* [병렬 처리](automation-powershell-workflow.md#use-parallel-processing)를 사용하여 여러 작업을 병렬로 수행합니다.
* 다른 그래픽 Runbook과 PowerShell 워크플로 Runbook을 자식 Runbook으로 포함하여 고급 워크플로를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항

* PowerShell 워크플로를 잘 알아야 합니다.
* Runbook은 [역직렬화된 개체](automation-powershell-workflow.md#deserialized-objects)와 같은 PowerShell 워크플로의 부가적인 복잡성을 다루어야 합니다.
* Runbook은 실행 전에 컴파일해야 하기 때문에 PowerShell Runbook보다 시작 시간이 깁니다.
* `Start-AzAutomationRunbook` cmdlet을 사용하여 PowerShell Runbook을 자식 Runbook으로만 포함할 수 있습니다.
* Runbook은 Linux Hybrid Runbook Worker에서 실행할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbook

Python Runbook은 Python 2에서 컴파일됩니다. Azure Portal의 텍스트 편집기를 사용하여 Runbook의 코드를 직접 편집할 수 있습니다. 또한 오프라인 텍스트 편집기를 사용할 수 있고 Azure Automation으로 [Runbook을 가져올 수](manage-runbooks.md) 있습니다.

### <a name="advantages"></a>장점

* 강력한 Python 라이브러리를 사용합니다.
* Azure 또는 Linux Hybrid Runbook Worker에서 실행할 수 있습니다. Windows Hybrid Runbook Worker는 [Python 2.7](https://www.python.org/downloads/release/latest/python2)에 설치되어 지원됩니다.

### <a name="limitations"></a>제한 사항

* Python 스크립팅에 대해 잘 알아야 합니다.
* 현재 Python 2만 지원됩니다. Python 3 관련 함수는 실패합니다.
* 타사 라이브러리를 사용하려면 Automation 계정으로 [패키지를 가져와야](python-packages.md) 합니다.

## <a name="next-steps"></a>다음 단계

* PowerShell Runbook에 대해 자세히 알아보려면 [자습서: PowerShell Runbook 만들기](learn/automation-tutorial-runbook-textual-powershell.md)를 참조하세요.
* PowerShell 워크플로 Runbook에 대한 자세한 내용은 [자습서: PowerShell 워크플로 Runbook 만들기](learn/automation-tutorial-runbook-textual.md)를 참조하세요.
* 그래픽 Runbook에 대한 자세한 내용은 [자습서: 그래픽 Runbook 만들기](learn/automation-tutorial-runbook-graphical.md)를 참조하세요.
* Python Runbook에 대한 자세한 내용은 [자습서: Python Runbook 만들기](learn/automation-tutorial-runbook-textual-python2.md)를 참조하세요.
