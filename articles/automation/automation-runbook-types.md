---
title: Azure Automation Runbook 형식
description: Azure Automation에서 사용할 수 있는 다양 한 유형의 runbook과 사용할 유형을 결정 하기 위한 고려 사항에 대해 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535522"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook 형식

Azure Automation process Automation 서비스는 다음 표에 정의 된 대로 여러 유형의 runbook을 지원 합니다. 프로세스 자동화 환경에 대해 알아보려면 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)을 참조 하세요.

| Type | Description |
|:--- |:--- |
| [그래픽](#graphical-runbooks)|Windows PowerShell을 기반으로 하는 그래픽 runbook을 Azure Portal의 그래픽 편집기에서 완전히 작성 및 편집 합니다. |
| [그래픽 PowerShell 워크플로](#graphical-runbooks)|Windows PowerShell 워크플로를 기반으로 하는 그래픽 runbook은 Azure Portal의 그래픽 편집기에서 완전히 만들어지고 편집 됩니다. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell 스크립팅을 기반으로 하는 텍스트 runbook입니다. |
| [PowerShell 워크플로](#powershell-workflow-runbooks)|Windows PowerShell 워크플로 스크립팅을 기반으로 하는 텍스트 runbook입니다. |
| [Python](#python-runbooks) |Python 스크립팅을 기반으로 하는 텍스트 runbook입니다. |

특정 runbook에 사용할 형식을 결정할 때 다음 사항을 고려해 야 합니다.

* Runbook을 그래픽에서 텍스트 형식으로 또는 그 밖의 다른 방법으로는 변환할 수 없습니다.
* 다른 형식의 runbook을 자식 runbook으로 사용 하는 경우 제한 사항이 있습니다. 자세한 내용은 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure Portal에서 그래픽 편집기를 사용하여 그래픽 및 그래픽 PowerShell 워크플로 Runbook을 만들고 편집할 수 있습니다. 그러나 다른 도구를 사용 하 여이 유형의 runbook을 만들거나 편집할 수 없습니다. 그래픽 runbook의 주요 기능:

* Automation 계정의 파일로 내보낸 다음 다른 Automation 계정으로 가져올 수 있습니다. 
* PowerShell 코드를 생성 합니다. 
* 가져오는 동안 그래픽 PowerShell 워크플로 runbook으로 변환 하거나 변환할 수 있습니다. 

### <a name="advantages"></a>장점

* 비주얼 삽입 링크-구성 제작 모델을 사용 합니다.
* 프로세스를 통해 데이터가 흐르는 방식에 중점을 둡니다.
* 관리 과정을 시각적으로 나타냅니다.
* 다른 runbook을 자식 runbook으로 포함 하 여 높은 수준의 워크플로를 만들 수 있습니다.
* 모듈식 프로그래밍을 권장 합니다.

### <a name="limitations"></a>제한 사항

* Azure Portal 외부에서 만들거나 편집할 수 없습니다.
* 복잡 한 논리를 실행 하기 위해 PowerShell 코드를 포함 하는 코드 작업이 필요할 수 있습니다.
* [텍스트 형식](automation-runbook-types.md)중 하나로 변환할 수 없으며 텍스트 runbook을 그래픽 형식으로 변환할 수 없습니다. 
* 그래픽 워크플로에서 만드는 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 코드 작업에서 만든 코드를 볼 수 있습니다.
* Linux Hybrid Runbook Worker에서 runbook을 실행할 수 없습니다. [Hybrid Runbook Worker를 사용 하 여 데이터 센터 또는 클라우드에서 리소스 자동화](automation-hybrid-runbook-worker.md)를 참조 하세요.

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로의 부가적인 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
* PowerShell 워크플로 runbook을 실행 하기 전에 컴파일할 필요가 없기 때문에 PowerShell 워크플로 runbook 보다 빨리 시작 합니다.
* Windows 및 Linux 모두에 대해 Azure 및 Hybrid Runbook Worker에서를 실행 합니다.

### <a name="limitations"></a>제한 사항

* PowerShell 스크립팅에 대해 잘 알고 있어야 합니다.
* Runbook은 [병렬 처리](automation-powershell-workflow.md#parallel-processing) 를 사용 하 여 여러 작업을 병렬로 실행할 수 없습니다.
* 오류가 발생 하면 runbook에서 [검사점](automation-powershell-workflow.md#checkpoints) 을 사용 하 여 runbook을 다시 시작할 수 없습니다.
* 새 작업을 만드는 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) cmdlet을 사용 하 여 PowerShell 워크플로 runbook 및 그래픽 runbook만 자식 runbook으로 포함할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

다음은 PowerShell runbook에 대 한 현재 알려진 문제입니다.

* PowerShell runbook은 null 값을 사용 하 여 암호화 되지 않은 [변수 자산](automation-variables.md) 을 검색할 수 없습니다.
* PowerShell Runbook은 이름에 가 포함된 `*~*`변수 자산을 검색할 수 없습니다.
* PowerShell runbook에서 루프의 [Get Process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) 작업은 약 80 회 반복 후에 충돌할 수 있습니다.
* PowerShell runbook은 한 번에 많은 양의 데이터를 출력 스트림에 쓰려고 하면 실패할 수 있습니다. 일반적으로 runbook에서 많은 개체를 사용 하는 데 필요한 정보만 출력 하도록 하면이 문제를 해결할 수 있습니다. 예를 들어를 제한 없이 `Get-Process` 사용 하는 대신 cmdlet이에서 `Get-Process | Select ProcessName, CPU`와 같이 필요한 매개 변수만 출력 하도록 할 수 있습니다.

## <a name="powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook

PowerShell 워크플로 runbook은 [Windows Powershell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 runbook입니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다. 오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
* 오류가 발생 하면 [검사점](automation-powershell-workflow.md#checkpoints) 을 사용 하 여 작업을 다시 시작 합니다.
* [병렬 처리](automation-powershell-workflow.md#parallel-processing) 를 사용 하 여 여러 작업을 병렬로 수행 합니다.
* 다른 그래픽 runbook 및 PowerShell 워크플로 runbook을 자식 runbook으로 포함 하 여 높은 수준의 워크플로를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항

* PowerShell 워크플로에 대해 잘 알고 있어야 합니다.
* Runbook은 [deserialize 된 개체](automation-powershell-workflow.md#code-changes)와 같은 PowerShell 워크플로의 추가 복잡성을 처리 해야 합니다.
* Runbook은 실행 전에 컴파일해야 하므로 PowerShell runbook을 시작 하는 데 더 오랜 시간이 걸립니다.
* Cmdlet을 `Start-AzAutomationRunbook` 사용 하 여 PowerShell runbook을 자식 runbook 으로만 포함할 수 있습니다.
* Runbook은 Linux Hybrid Runbook Worker에서 실행할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbook

Python Runbook은 Python 2에서 컴파일됩니다. Azure Portal의 텍스트 편집기를 사용하여 Runbook의 코드를 직접 편집할 수 있습니다. 오프 라인 텍스트 편집기를 사용 하 여 [runbook을](manage-runbooks.md) Azure Automation으로 가져올 수도 있습니다.

### <a name="advantages"></a>장점

* 강력한 Python 라이브러리를 사용 합니다.
* Azure 또는 Linux Hybrid Runbook Worker에서 실행할 수 있습니다. Windows Hybrid Runbook Worker는 [python 2.7](https://www.python.org/downloads/release/latest/python2) 이 설치 된 상태에서 지원 됩니다.

### <a name="limitations"></a>제한 사항

* Python 스크립팅에 대해 잘 알고 있어야 합니다.
* 현재 Python 2만 지원 됩니다. Python 3 관련 함수는 실패 합니다.
* 타사 라이브러리를 사용 하려면 패키지를 Automation 계정으로 [가져와야](python-packages.md) 합니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 runbook 작성에 대해 자세히 알아보려면 [Azure Automation의 그래픽 작성](automation-graphical-authoring-intro.md)을 참조 하세요.
* Runbook에 대 한 PowerShell 워크플로 및 powershell 워크플로 간의 차이점을 이해 하려면 [Windows Powershell 워크플로 학습](automation-powershell-workflow.md)을 참조 하세요.
* Runbook을 만들거나 가져오는 방법에 대 한 자세한 내용은 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조 하세요.
* 언어 참조 및 학습 모듈을 포함 하 여 PowerShell에 대해 자세히 알아보려면 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.
