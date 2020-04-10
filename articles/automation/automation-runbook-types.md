---
title: Azure Automation Runbook 형식
description: Azure Automation에서 사용할 수 있는 다양한 형식의 Runbook을 설명하고 사용할 형식을 결정할 때 고려해야 하는 사항을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010174"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook 형식

Azure 자동화 프로세스 자동화 서비스는 여러 유형의 Runbook을 지원합니다. 형식은 다음 표에 간략하게 정의되어 있으며 아래 섹션에서 자세히 설명합니다. 프로세스 자동화 환경에 대한 자세한 내용은 [Azure Automation의 Runbook 실행을](automation-runbook-execution.md)참조하십시오.

| Type | Description |
|:--- |:--- |
| [그래픽](#graphical-runbooks)|Windows PowerShell을 기반으로 하며 Azure 포털의 그래픽 편집기에서 완전히 만들고 편집한 그래픽 런북입니다. |
| [그래픽 PowerShell 워크플로](#graphical-runbooks)|Windows PowerShell 워크플로를 기반으로 하며 Azure 포털의 그래픽 편집기에서 완전히 만들고 편집한 그래픽 런북입니다. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell 스크립팅을 기반으로 하는 텍스트 런북입니다. |
| [PowerShell 워크플로](#powershell-workflow-runbooks)|Windows PowerShell 워크플로 스크립팅을 기반으로 하는 텍스트 런북입니다. |
| [Python](#python-runbooks) |파이썬 스크립팅을 기반으로 하는 텍스트 런북입니다. |

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure 포털의 그래픽 편집기에서 그래픽 편집기에서 그래픽 및 그래픽 PowerShell 워크플로 런북을 만들고 편집할 수 있습니다. 그러나 다른 도구로는 이 유형의 Runbook을 만들거나 편집할 수 없습니다.

그래픽 런북에는 다음과 같은 주요 기능이 있습니다.

* 자동화 계정의 파일로 내보낸 다음 다른 자동화 계정으로 가져올 수 있습니다. 
* PowerShell 코드를 생성합니다. 
* 가져오는 동안 그래픽 PowerShell 워크플로 런북으로 또는 그래픽PowerShell 워크플로에서 변환할 수 있습니다. 

### <a name="advantages"></a>장점

* 시각적 삽입 링크 구성 작성 모델을 사용합니다.
* 프로세스가 어떻게 흐르는지에 초점을 맞춥니다.
* 시각적으로 관리 프로세스를 나타냅니다.
* 다른 Runbook을 하위 실행책으로 포함하여 상위 수준의 워크플로를 만듭니다.
* 모듈식 프로그래밍을 장려합니다.

### <a name="limitations"></a>제한 사항

* Azure 포털 외부에서 만들거나 편집할 수 없습니다.
* 복잡한 논리를 실행하려면 PowerShell 코드가 포함된 코드 작업이 필요할 수 있습니다.
* [텍스트 형식](automation-runbook-types.md)중 하나로 변환할 수 없으며 텍스트 Runbook을 그래픽 형식으로 변환할 수도 없습니다. 
* 그래픽 워크플로에서 만드는 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 모든 코드 활동에서 만든 코드를 볼 수 있습니다.
* Linux 하이브리드 Runbook 작업자에서 실행되지 않습니다. [하이브리드 Runbook 작업자를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화를](automation-hybrid-runbook-worker.md)참조하십시오.

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로의 부가적인 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
* Runbook은 실행 전에 컴파일이 필요 없기 때문에 PowerShell 워크플로 Runbook보다 빨리 시작됩니다.
* Azure 또는 Linux 및 Windows 하이브리드 Runbook 작업자 모두에서 실행할 수 있습니다.

### <a name="limitations"></a>제한 사항

* PowerShell 스크립팅에 대해 잘 알아야 합니다.
* [병렬 처리](automation-powershell-workflow.md#parallel-processing)를 사용하여 여러 작업을 병렬로 실행할 수 없습니다.
* 오류 발생 시 [검사점](automation-powershell-workflow.md#checkpoints)을 사용하여 Runbook을 다시 시작할 수 없습니다.
* PowerShell 워크플로 Runbook 및 그래픽 Runbook은 새 작업을 만드는 Start-AzureAutomationRunbook cmdlet을 사용해야만 자식 Runbook으로 포함할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

PowerShell Runbook에 대해 현재 알려진 문제는 다음과 같습니다.

* PowerShell Runbook은 null 값으로 암호화되지 않은 [변수 자산을](automation-variables.md) 검색할 수 없습니다.
* PowerShell Runbook은 이름에 *~* 가 포함된 [변수 자산](automation-variables.md)을 검색할 수 없습니다.
* PowerShell Runbook의 반복적인 Get-Process는 80회 반복 후에 작동이 중단될 수 있습니다.
* PowerShell Runbook이 한 번에 대량의 데이터를 출력 스트림에 쓰려고 하면 실패할 수 있습니다.   일반적으로 큰 개체로 작업하는 경우 필요한 정보만 출력하면 이 문제를 극복할 수 있습니다.  예를 들어 *Get-Process* 같은 출력 대신 *Get-Process | Select ProcessName, CPU*를 사용하여 필요한 필드만 출력할 수 있습니다.

## <a name="powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook

PowerShell 워크플로 Runbook은 [Windows PowerShell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 Runbook입니다.  Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
* 오류 발생 시 [검사점](automation-powershell-workflow.md#checkpoints)을 사용하여 Runbook을 다시 시작합니다.
* [병렬 처리](automation-powershell-workflow.md#parallel-processing) 를 사용하여 여러 작업을 병렬로 수행합니다.
* 다른 그래픽 Runbook과 PowerShell 워크플로 Runbook을 자식 Runbook으로 포함시켜 고급 워크플로를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항

* 작성자는 PowerShell 워크플로를 잘 알아야 합니다.
* Runbook은 [역직렬화된 개체](automation-powershell-workflow.md#code-changes)와 같은 PowerShell 워크플로의 부가적인 복잡성을 다루어야 합니다.
* Runbook은 실행 전에 컴파일이 필요하기 때문에 PowerShell Runbook보다 시작 시간이 깁니다.
* PowerShell Runbook은 새 작업을 만드는 Start-AzureAutomationRunbook cmdlet을 사용해야만 자식 Runbook으로 포함할 수 있습니다.
* Linux 하이브리드 Runbook 작업자에서 실행할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbook

Python Runbook은 Python 2에서 컴파일됩니다. Azure Portal의 텍스트 편집기 또는 오프라인 텍스트 편집기를 사용하여 직접 Runbook 코드를 편집한 다음, Azure Automation으로 [Runbook을 가져올 수](manage-runbooks.md) 있습니다.

### <a name="advantages"></a>장점

* 유용한 Python 라이브러리를 활용할 수 있습니다.
* Azure 또는 Linux 하이브리드 Runbook 작업자 모두에서 실행할 수 있습니다. Windows 하이브리드 Runbook 작업자는 [python2.7설치를](https://www.python.org/downloads/release/latest/python2) 통해 지원됩니다.

### <a name="limitations"></a>제한 사항

* Python 스크립팅에 대해 잘 알아야 합니다.
* 현재는 Python 2만 지원되므로 Python 3 특정 함수는 실패합니다.
* 타사 라이브러리를 사용하려면 패키지를 자동화 계정으로 [가져와야](python-packages.md) 사용할 수 있습니다.

## <a name="considerations"></a>고려 사항

특정 Runbook에 사용할 형식을 결정할 때 다음과 같은 사항을 추가로 고려합니다.

* Runbook를 그래픽에서 텍스트 형식으로 또는 그 반대로 변환할 수 없습니다.
* 형식이 다른 Runbook을 자식 Runbook으로 사용하는 경우 제한 사항이 있습니다. 자세한 내용은 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 그래픽 Runbook 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* Runbook용 PowerShell 및 PowerShell 워크플로 간의 차이점을 이해하려면 [Windows PowerShell 워크플로 학습](automation-powershell-workflow.md)
* Runbook을 만들거나 가져오는 방법에 대한 자세한 내용은 [Runbook 만들기 또는 가져오기](manage-runbooks.md)
* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
