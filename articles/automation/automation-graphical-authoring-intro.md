---
title: Azure Automation에서 그래픽 작성
description: 그래픽 작성을 통해 코드 작업 없이 Azure Automation에 대한 Runbook을 만들 수 있습니다. 이 문서에서는 그래픽 작성을 소개하고 그래픽 Runbook 만들기를 시작하는 데 필요한 모든 세부 정보를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 09afca7eaf385795baf9c4a3c94232622527e357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500978"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure Automation에서 그래픽 작성

그래픽 작성을 사용하면 기본 Windows PowerShell 또는 PowerShell 워크플로 코드의 복잡성 없이 Azure 자동화용 런북을 만들 수 있습니다. cmdlet 및 Runbook 라이브러리에서 캔버스에 활동을 추가하고 함께 연결하고 워크플로를 구성하도록 구성할 수 있습니다. 시스템 센터 오케스트레이터 또는 SMA(서비스 관리 자동화)로 작업한 적이 있는 경우 그래픽 작성에 익숙해져야 합니다. 이 문서에서는 그래픽 Runbook 만들기를 시작하는 데 필요한 개념에 대해 소개합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure 자동화의 모든 Runbook은 Windows PowerShell 워크플로입니다. 그래픽 런북및 그래픽 PowerShell 워크플로런북은 자동화 작업자가 실행하지만 보거나 수정할 수 없는 PowerShell 코드를 생성합니다. 그래픽 런북을 그래픽 PowerShell 워크플로러 런북으로 변환할 수 있으며 그 반대의 경우도 마찬가지입니다. 그러나 이러한 Runbook을 텍스트 런북으로 변환할 수는 없습니다. 또한 자동화 그래픽 편집기는 텍스트 runbook을 가져올 수 없습니다.

## <a name="overview-of-graphical-editor"></a>그래픽 편집기 개요

그래픽 Runbook을 만들거나 편집하여 Azure 포털에서 그래픽 편집기를 열 수 있습니다.

![그래픽 작업 영역](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

다음 섹션에서는 그래픽 편집기의 컨트롤에 대해 설명합니다.

### <a name="canvas-control"></a>Canvas 컨트롤

캔버스 컨트롤을 사용하면 Runbook을 디자인할 수 있습니다. 라이브러리 컨트롤의 노드에서 런북에 활동을 추가하고 링크와 연결하여 Runbook 논리를 정의할 수 있습니다. 캔버스 하단에는 확대/축소할 수 있는 컨트롤이 있습니다.

### <a name="library-control"></a>라이브러리 컨트롤

라이브러리 컨트롤을 사용하면 Runbook에 추가할 [활동을](#activities) 선택할 수 있습니다. 캔버스에 추가하여 다른 활동에 연결할 수 있습니다. 라이브러리 컨트롤에는 다음 표에 정의된 섹션이 포함됩니다.

| 섹션 | 설명 |
|:--- |:--- |
| Cmdlet |Runbook에 사용할 수 있는 모든 cmdlet입니다. 이러한 cmdlet은 모듈별로 구성됩니다. 자동화 계정에 설치한 모든 모듈을 사용할 수 있습니다. |
| Runbook |자동화 계정의 Runbook입니다. 이러한 Runbook을 캔버스에 추가하여 자식 런북으로 사용할 수 있습니다. 편집 중인 Runbook과 동일한 코어 유형의 Runbook만 표시됩니다. 그래픽 런북의 경우 PowerShell 기반 런북만 표시됩니다. 그래픽 PowerShell 워크플로런북의 경우 PowerShell 워크플로 기반 런북만 표시됩니다. |
| 자산 |Runbook에서 사용할 수 있는 자동화 계정의 [자동화 자산입니다.](/previous-versions/azure/dn939988(v=azure.100)) Runbook에 에셋을 추가하면 선택한 자산을 얻는 워크플로 활동이 추가됩니다. 변수 자산의 경우 변수를 가져오는 활동을 추가할지 또는 변수를 설정하는 활동을 추가할지 선택할 수 있습니다. |
| Runbook 컨트롤 |현재 Runbook에서 사용할 수 있는 활동을 제어합니다. 정션 활동은 여러 입력을 받고 워크플로를 계속하기 전에 모두 완료될 때까지 기다립니다. 코드 활동은 그래픽 런북 유형에 따라 하나 이상의 PowerShell 또는 PowerShell 워크플로 우호 코드를 실행합니다. 사용자 지정 코드 또는 다른 작업으로 수행하기 어려운 기능에 이 작업을 사용할 수 있습니다. |

### <a name="configuration-control"></a>구성 컨트롤

구성 컨트롤을 사용하면 캔버스에서 선택한 개체에 대한 세부 정보를 제공할 수 있습니다. 이 컨트롤에서 사용할 수 있는 속성은 선택한 개체 유형에 따라 다릅니다. 구성 컨트롤에서 옵션을 선택하면 추가 블레이드가 열리므로 자세한 정보를 제공합니다.

### <a name="test-control"></a>테스트 컨트롤

테스트 컨트롤은 그래픽 편집기를 처음 시작할 때 표시되지 않습니다. 대화형으로 그래픽 Runbook을 테스트할 때 열립니다.

## <a name="activities"></a>활동

활동은 Runbook의 구성 요소입니다. 활동은 PowerShell cmdlet, 자식 실행북 또는 워크플로일 수 있습니다. 라이브러리 컨트롤에서 활동을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가를**선택하여 Runbook에 활동을 추가할 수 있습니다. 그런 다음 활동을 클릭하고 끌어서 캔버스의 원하는 위치에 배치할 수 있습니다. 캔버스의 활동 위치는 Runbook의 작업에 영향을 주지 않습니다. Runbook의 작업을 시각화하는 데 가장 적합한 방법으로 Runbook을 레이아웃할 수 있습니다.

![캔버스에 추가](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

캔버스에서 활동을 선택하여 구성 블레이드에서 해당 속성 및 매개 변수를 구성합니다. 활동의 레이블을 설명이 있는 이름으로 변경할 수 있습니다. Runbook은 여전히 원래 cmdlet을 실행합니다. 그래픽 편집기에서 사용하는 표시 이름을 변경하기만 하면 됩니다. 레이블은 Runbook 내에서 고유해야 합니다.

### <a name="parameter-sets"></a>매개 변수 집합

매개 변수 집합은 특정 cmdlet에 대한 값을 허용하는 필수 및 선택적 매개 변수를 정의합니다. 모든 cmdlet에는 하나 이상의 매개 변수 집합이 있으며 일부는 여러 집합을 갖습니다. cmdlet에 여러 매개 변수 집합이 있는 경우 매개 변수를 구성하기 전에 사용할 매개 변수를 선택해야 합니다. 매개 변수 집합을 선택하고 다른 집합을 선택하여 활동에 사용되는 **매개 변수 집합을** 변경할 수 있습니다. 이 경우 이미 구성한 매개 변수 값이 모두 손실됩니다.

다음 예제에서 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet에는 세 개의 매개 변수 집합이 있습니다. 이 예제에서는 리소스 그룹의 모든 가상 컴퓨터를 반환하기 위해 단일 선택적 매개 변수가 있는 **ListVirtualMachineInInResourceGroupParamSet이라는**하나의 집합을 사용합니다. 또한 이 예제에서는 반환할 가상 컴퓨터를 지정하기 위해 **GetVirtualMachineInResourceGroupParamSet** 매개 변수 집합을 사용합니다. 이 집합에는 두 개의 필수 매개 변수와 하나의 선택적 매개 변수가 있습니다.

![매개 변수 집합](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>매개 변수 값

매개 변수 값을 지정하려면 데이터 원본을 선택하여 값 지정 방법을 결정합니다. 특정 매개 변수에 사용할 수 있는 데이터 원본은 해당 매개 변수에 대한 유효한 값에 따라 다릅니다. 예를 들어 Null은 null 값을 허용하지 않는 매개 변수에 사용 가능한 옵션이 아닙니다.

| 데이터 원본 | 설명 |
|:--- |:--- |
| 상수 값 |매개 변수 값을 입력합니다. 이 데이터 원본은 Int32, Int64, 문자열, 부울, DateTime, Switch와 같은 데이터 형식에만 사용할 수 있습니다. |
| 활동 출력 |워크플로의 현재 활동 앞에 오는 활동의 출력을 사용합니다. 유효한 모든 활동이 나열됩니다. 매개 변수 값의 경우 출력을 생성하는 활동만 사용합니다. 활동이 여러 속성으로 개체를 출력하는 경우 활동을 선택한 후 특정 속성의 이름을 입력할 수 있습니다. |
| Runbook 입력 |런북 입력을 활동 매개 변수에 대한 입력으로 선택합니다. |
| 변수 자산 |자동화 변수를 입력으로 선택합니다. |
| 자격 증명 자산 |자동화 자격 증명을 입력으로 선택합니다. |
| 인증서 자산 |자동화 인증서를 입력으로 선택합니다. |
| 연결 자산 |자동화 연결을 입력으로 선택합니다. |
| PowerShell 식 |간단한 [PowerShell 식을 지정합니다.](#powershell-expressions) 식은 활동 전에 평가되고 결과는 매개 변수 값에 사용됩니다. 변수를 사용하여 활동의 출력 또는 Runbook 입력 매개 변수를 참조할 수 있습니다. |
| 구성되지 않음 |이전에 구성된 값을 지웁울 수 있습니다. |

#### <a name="optional-additional-parameters"></a>선택적 추가 매개 변수

모든 cmdlet에는 추가 매개 변수를 제공할 수 있는 옵션이 있습니다. 다음은 PowerShell 공통 매개 변수 또는 기타 사용자 지정 매개 변수입니다. 그래픽 편집기는 PowerShell 구문을 사용하여 매개 변수를 제공할 수 있는 텍스트 상자를 제공합니다. 예를 들어 공통 `Verbose` 매개 변수를 사용하려면 을 지정해야 `-Verbose:$True`합니다.

### <a name="retry-activity"></a>작업 다시 시도

활동에 대한 재시도 기능을 사용하면 루프와 마찬가지로 특정 조건이 충족될 때까지 여러 번 실행할 수 있습니다. 이 기능을 여러 번 실행해야 하거나 오류가 발생하기 쉽거나 성공을 위해 두 번 이상의 시도가 필요할 수 있는 활동에 사용하거나 유효한 데이터에 대한 활동의 출력 정보를 테스트할 수 있습니다.

활동에 재시도 사용하면 지연 및 조건을 설정할 수 있습니다. 지연은 작업을 다시 실행하기 전에 Runbook이 대기하는 시간(분 또는 초 단위로 측정됨)입니다. 지연을 지정하지 않으면 활동이 완료된 직후에 다시 실행됩니다.

![작업 다시 시도 지연](media/automation-graphical-authoring-intro/retry-delay.png)

재시도 조건은 활동이 실행될 때마다 평가되는 PowerShell 식입니다. 식이 True로 확인되면 활동이 다시 실행됩니다. 식이 False로 확인되면 활동이 다시 실행되지 않고 Runbook이 다음 활동으로 이동합니다.

![작업 다시 시도 지연](media/automation-graphical-authoring-intro/retry-condition.png)

재시도 조건은 활동 재시도에 대한 정보에 대한 액세스를 제공하는 라는 `RetryData` 변수를 사용할 수 있습니다. 이 변수는 다음 테이블의 속성을 가집니다.

| 속성 | 설명 |
|:--- |:--- |
| `NumberOfAttempts` |활동이 실행된 횟수입니다. |
| `Output` |활동의 마지막 실행에서 출력입니다. |
| `TotalDuration` |작업이 처음으로 시작된 이후 경과된 시간입니다. |
| `StartedAt` |활동이 처음 시작된 시간(UTC 형식)입니다. |

다음은 활동 재시도 조건의 예입니다.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

작업에 다시 시도 조건을 구성하면 이를 알리는 두 개의 시각 신호가 작업에 포함됩니다. 하나는 활동에 표시되고 다른 하나는 활동의 구성을 검토할 때 표시됩니다.

![작업 다시 시도 시각적 표시기](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>워크플로 스크립트 컨트롤

워크플로 스크립트 컨트롤은 작성중인 그래픽 런북의 유형에 따라 PowerShell 또는 PowerShell 워크플로 스크립트를 허용하는 특수 활동입니다. 이 컨트롤은 다른 방법으로는 사용할 수 없는 기능을 제공합니다. 매개 변수를 사용할 수 없지만 활동 출력 및 Runbook 입력 매개 변수에 변수를 사용할 수는 있습니다. 활동의 모든 출력이 데이터버스에 추가됩니다. 예외는 나가는 링크가 없는 출력이며, 이 경우 출력이 Runbook의 출력에 추가됩니다.

예를 들어 다음 코드는 runbook 입력 변수라는 `NumberOfDays`이름을 사용하여 날짜 계산을 수행합니다. 그런 다음 실행첩의 후속 활동에서 사용할 출력으로 계산된 DateTime 값을 보냅니다.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>링크 및 워크플로

그래픽 Runbook의 링크 는 두 개의 활동을 연결합니다. 원본 활동에서 대상 활동을 가리키는 화살표로 캔버스에 표시됩니다. 활동은 화살표 방향으로 실행되며, 원본 활동이 완료된 대상 활동이 시작됩니다.

### <a name="link-creation"></a>링크 생성

원본 활동을 선택하고 셰이프 맨 아래에 있는 원을 클릭하여 두 활동 간의 링크를 만들 수 있습니다. 화살표를 대상 활동으로 끌어 놓습니다.

![링크 만들기](media/automation-graphical-authoring-intro/create-link-options.png)

링크를 선택하여 구성 블레이드에서 해당 속성을 구성할 수 있습니다. 속성에는 다음 표에 설명된 링크 형식이 포함됩니다.

| 링크 형식 | 설명 |
|:--- |:--- |
| 파이프라인 |대상 활동은 원본 활동의 각 개체 출력에 대해 한 번 실행됩니다. 원본 활동의 출력이 없는 경우에는 대상 활동이 실행되지 않습니다. 원본 활동의 출력은 개체로 제공됩니다. |
| 시퀀스 |대상 활동은 원본 활동에서 출력을 받을 때 한 번만 실행됩니다. 원본 활동의 출력이 개체 배열로 제공됩니다. |

### <a name="start-of-activity"></a>활동 시작

그래픽 Runbook은 들어오는 링크가 없는 활동으로 시작됩니다. Runbook의 시작 활동역할을 하는 활동은 하나뿐인 경우가 많습니다. 여러 활동에 들어오는 링크가 없는 경우 Runbook은 병렬로 실행하여 시작합니다. 각 활동이 완료되는 대로 링크에 따라 다른 활동을 실행합니다.

### <a name="link-conditions"></a>링크 조건

링크에서 조건을 지정하면 조건이 True로 확인되는 경우에만 대상 활동이 실행됩니다. 일반적으로 조건에서 `ActivityOutput` 변수를 사용하여 소스 활동에서 출력을 검색합니다.

파이프라인 링크의 경우 단일 개체에 대한 조건을 지정해야 합니다. Runbook은 소스 활동별로 각 개체 출력에 대한 조건을 평가합니다. 그런 다음 조건을 제공하는 각 개체에 대한 대상 활동을 실행합니다. 예를 들어 `Get-AzVM`의 소스 활동을 사용하면 조건부 파이프라인 링크에 대해 다음 구문을 사용하여 Group1이라는 리소스 그룹의 가상 컴퓨터만 검색할 수 있습니다.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

시퀀스 링크의 경우 Runbook은 원본 활동의 모든 개체를 포함하는 단일 배열이 반환되므로 조건을 한 번만 평가합니다. 따라서 Runbook은 파이프라인 링크와 마찬가지로 필터링을 위해 시퀀스 링크를 사용할 수 없습니다. 시퀀스 링크는 단순히 다음 활동이 실행되는지 여부를 결정할 수 있습니다.

예를 들어 **시작 VM** 실행책에서 다음과 같은 활동 집합을 수행합니다.

![시퀀스와 조건부 링크](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook은 입력 매개 변수의 `VMName` `ResourceGroupName` 값을 확인하고 취할 적절한 작업을 결정하는 세 가지 서로 다른 시퀀스 링크를 사용합니다. 가능한 작업은 단일 VM을 시작하거나, 리소스 그룹의 모든 VM을 시작하거나, 구독에서 모든 VM을 시작하는 것입니다. `Connect to Azure` 및 `Get single VM`의 시퀀스 링크의 경우 조건 논리는 다음과 같습니다.

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

조건부 링크를 사용하면 원본 활동에서 해당 분기의 다른 활동까지 사용 가능한 데이터가 조건별로 필터링됩니다. 활동이 여러 링크의 원본인 경우 각 분기의 활동에 사용할 수 있는 데이터는 해당 분기에 연결하는 링크의 조건에 따라 달라집니다.

예를 들어 `Start-AzVM` 아래 Runbook의 활동은 모든 가상 컴퓨터를 시작합니다. 두 개의 조건부 링크가 있습니다. 첫 번째 조건부 링크는 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` `Start-AzVM` 표현식을 사용하여 활동이 성공적으로 완료되면 필터링합니다. 두 번째 조건부 링크는 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` `Start-AzVm` 활동이 가상 컴퓨터를 시작하지 못하는 경우 를 필터링하기 위해 식을 사용합니다.

![조건부 링크 예제](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

첫 번째 링크를 따르고 활동 출력을 `Get-AzureVM` 사용하는 모든 활동은 실행 당시 시작된 가상 `Get-AzureVM` 시스템만 검색합니다. 두 번째 링크를 따르는 모든 활동은 실행 당시 중지된 `Get-AzureVM` 가상 시스템만 가져옵니다. 세 번째 링크를 따르는 모든 활동은 실행 상태에 상관없이 모든 가상 머신을 가져옵니다.

### <a name="junctions"></a>분기 동기화

분기 동기화는 들어오는 모든 분기가 완료될 때까지 대기하는 특별한 활동입니다. 이렇게 하면 Runbook에서 여러 활동을 병렬로 실행하고 계속하기 전에 모두 완료되었는지 확인할 수 있습니다.

접합은 들어오는 링크를 무제한으로 가질 수 있지만 이러한 링크 중 하나만 파이프라인이 될 수 있습니다. 들어오는 시퀀스 링크의 수는 제한되지 않습니다. 여러 들어오는 파이프라인 링크가 있는 접합부를 만들고 Runbook을 저장할 수 있지만 실행될 때 실패합니다.

아래 예제는 가상 머신 집합을 시작하고 이와 동시에 해당 컴퓨터에 적용할 패치를 다운로드하는 Runbook의 일부입니다. 런북이 계속되기 전에 두 프로세스가 모두 완료되도록 하려면 접합을 사용합니다.

![분기 동기화](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

사이클은 대상 활동이 소스 활동또는 결국 원본으로 다시 연결되는 다른 활동으로 다시 연결될 때 형성됩니다. 그래픽 작성은 현재 주기를 지원하지 않습니다. Runbook에 주기가 있는 경우 정상적으로 저장되지만 실행하면 오류가 발생합니다.

![주기](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>활동 간의 데이터 공유

나가는 링크와 함께 활동이 출력하는 모든 데이터는 Runbook의 데이터버스에 기록됩니다. Runbook의 모든 활동은 데이터 버스에 있는 데이터를 사용하여 매개 변수 값을 채우거나 스크립트 코드에 포함할 수 있습니다. 활동은 워크플로의 모든 이전 활동에 대한 출력에 액세스할 수 있습니다.

데이터가 데이터 버스에 기록되는 방식은 활동의 링크 형식에 따라 달라집니다. 파이프라인 링크의 경우 데이터가 여러 개체로 출력됩니다. 시퀀스 링크의 경우 데이터는 배열로 출력됩니다. 값이 하나만 있는 경우 단일 요소 배열로 출력됩니다.

Runbook에는 데이터 버스의 데이터에 액세스하는 두 가지 방법이 있습니다. 
* 활동 출력 데이터 원본을 사용합니다.
* PowerShell 식 데이터 원본을 사용합니다.

첫 번째 메커니즘은 활동 출력 데이터 원본을 사용하여 다른 활동의 매개 변수를 채웁니다. 출력이 개체인 경우 Runbook은 단일 속성을 지정할 수 있습니다.

![활동 출력](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

두 번째 데이터 액세스 메커니즘은 아래 표시된 구문을 사용하여 PowerShell 식 데이터 `ActivityOutput` 원본 또는 변수가 있는 워크플로 스크립트 활동의 작업 출력을 검색합니다. 출력이 개체인 경우 Runbook에서 단일 속성을 지정할 수 있습니다.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>검사점

모든 활동에서 [검사점](automation-powershell-workflow.md#checkpoints) 실행책을 선택하여 그래픽 PowerShell 워크플로 런북에서 **검사점 집합을** 설정할 수 있습니다. 활동을 실행한 후에 검사점을 설정하게 됩니다.

![검사점](media/automation-graphical-authoring-intro/set-checkpoint.png)

검사점은 그래픽 PowerShell 워크플로 런북에서만 사용할 수 있으며 그래픽 런북에서는 사용할 수 없습니다. Runbook에서 Azure cmdlet을 사용하는 경우 `Connect-AzAccount` 활동이 있는 검사된 작업을 따라야 합니다. 연결 작업은 Runbook이 일시 중단되고 다른 작업자의 이 검사점에서 다시 시작해야 하는 경우에 사용됩니다.

## <a name="runbook-input-and-output"></a>Runbook 입력 및 출력

### <a name="runbook-input"></a>Runbook 입력<a name="runbook-input"></a>

Runbook은 현재 런북이 자식으로 사용되는 경우 Azure 포털을 통해 Runbook을 시작하는 사용자 또는 다른 Runbook의 입력이 필요합니다. 예를 들어 가상 컴퓨터를 만드는 Runbook의 경우 사용자는 Runbook이 시작될 때마다 가상 컴퓨터의 이름 및 기타 속성과 같은 정보를 제공해야 할 수 있습니다.

Runbook은 하나 이상의 입력 매개 변수를 정의하여 입력을 허용합니다. 사용자는 Runbook이 시작될 때마다 이러한 매개 변수에 대한 값을 제공합니다. 사용자가 Azure 포털을 사용하여 Runbook을 시작하면 Runbook에서 지원하는 각 입력 매개 변수에 대한 값을 제공하라는 메시지가 표시됩니다.

Runbook을 작성할 때 Runbook 도구 모음에서 **입력 및 출력을** 클릭하여 입력 매개 변수에 액세스할 수 있습니다. 이렇게 하면 입력 및 출력 컨트롤이 열리며, 여기에서 기존 입력 매개 변수를 편집하거나 **입력 추가**를 클릭하여 새 입력 매개 변수를 만들 수 있습니다.

![입력 추가](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

각 입력 매개 변수는 다음 표의 속성으로 정의됩니다.

| 속성 | 설명 |
|:--- |:--- |
| 이름 | 필수 사항입니다. 매개 변수의 이름입니다. 이름은 Runbook 내에서 고유해야 합니다. 문자로 시작해야 하며 문자, 숫자 및 밑줄만 포함할 수 있습니다. 이름에 공백을 포함할 수 없습니다. |
| 설명 |(선택 사항) 입력 매개 변수의 용도에 대한 설명입니다. |
| Type | (선택 사항) 매개 변수 값에 필요한 데이터 형식입니다. Azure Portal에서는 입력 메시지를 표시할 때 각 매개 변수의 데이터 형식에 대한 적절한 컨트롤을 제공합니다. 지원되는 매개 변수 형식은 문자열, Int32, Int64, Decimal, Boolean, DateTime 및 개체입니다. 데이터 형식이 선택되어 있지 않으면 문자열에 대한 기본값으로 지정됩니다.|
| 필수 | (선택 사항) 매개 변수에 대해 값을 제공해야 하는지 지정하는 설정입니다. 을 선택하면 `yes`Runbook이 시작될 때 값을 제공해야 합니다. 을 선택하면 `no`Runbook이 시작될 때 값이 필요하지 않으며 기본값을 사용할 수 있습니다. 정의된 기본값이 없는 각 필수 매개 변수에 대한 값을 제공하지 않으면 Runbook을 시작할 수 없습니다. |
| 기본값 | (선택 사항) Runbook이 시작될 때 매개 변수가 전달되지 않은 경우 매개 변수에 사용되는 값입니다. 기본값을 설정하려면 을 `Custom`선택합니다. 기본값을 제공하지 않으려는 경우 선택합니다. `None` |

### <a name="runbook-output"></a>Runbook 출력

그래픽 작성은 [Runbook의 출력에](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)대한 나가는 링크가 없는 모든 활동에 의해 생성된 데이터를 저장합니다. 출력은 Runbook 작업과 함께 저장되며, 해당 Runbook이 자식으로 사용되는 경우 부모 Runbook에서 사용할 수 있습니다.

## <a name="powershell-expressions"></a>PowerShell 식

그래픽 작성의 장점 중 하나는 PowerShell에 대한 최소한의 지식으로 Runbook을 빌드할 수 있다는 것입니다. 그러나 현재는 특정 [매개 변수 값을](#activities) 채우고 링크 [조건을](#links-and-workflow)설정하기 위해 PowerShell을 약간 알아야 합니다. 이 섹션에서는 PowerShell 식에 대한 간략한 소개를 제공합니다. PowerShell의 세부 사항 전체는 [Windows PowerShell을 사용하는 스크립트](https://technet.microsoft.com/library/bb978526.aspx)에서 사용 가능합니다.

### <a name="powershell-expression-data-source"></a>PowerShell 식 데이터 원본

PowerShell 식을 데이터 원본으로 사용하여 [활동 매개 변수의](#activities) 값을 PowerShell 코드의 결과와 채울 수 있습니다. 식은 간단한 함수를 수행하는 코드의 한 줄 또는 일부 복잡한 논리를 수행하는 여러 줄일 수 있습니다. 변수에 할당되지 않은 명령의 출력은 매개 변수 값에 대한 출력입니다.

예를 들어 다음 명령은 현재 날짜를 출력합니다.

```powershell-interactive
Get-Date
```

다음 코드 코드 조각은 현재 날짜부터 문자열을 빌드하고 변수에 할당합니다. 코드는 변수의 내용을 출력으로 보냅니다.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

다음 명령은 현재 날짜를 평가하고 현재 날짜가 주말인지 평일인지를 나타내는 문자열을 반환합니다.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>활동 출력

Runbook의 이전 활동의 출력을 사용하려면 다음 `ActivityOutput` 구문과 함께 변수를 사용합니다.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

예를 들어 가상 컴퓨터의 이름이 필요한 속성이 있는 활동을 가질 수 있습니다. 이 경우 Runbook에서 다음 식을 사용할 수 있습니다.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

속성에 이름 대신 가상 시스템 개체가 필요한 경우 Runbook은 다음 구문을 사용하여 전체 개체를 반환합니다.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook은 다음과 같이 보다 복잡한 식에서 활동의 출력을 사용할 수 있습니다. 이 식은 텍스트를 가상 시스템 이름에 연결합니다.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>조건

[비교 연산자](https://technet.microsoft.com/library/hh847759.aspx) 를 사용하여 값을 비교 하거나 값이 지정된 패턴과 일치하는지를 확인합니다. 비교는 True 또는 False 값을 반환합니다.

예를 들어 다음 조건은 명명된 `Get-AzureVM` 활동의 가상 시스템이 현재 중지되었는지 여부를 결정합니다.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

다음 조건은 동일한 가상 시스템이 중지된 상태가 아닌 다른 상태에 있는지 여부를 결정합니다.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

또는 와 `-or`같은 [논리 연산자를](https://technet.microsoft.com/library/hh847789.aspx)사용하여 Runbook에서 여러 조건을 조인할 수 있습니다. `-and` 예를 들어 다음 조건은 이전 예제의 가상 컴퓨터가 중지 또는 중지 상태에 있는지 확인합니다.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>해시 테이블

[해시테이블은](https://technet.microsoft.com/library/hh847780.aspx) 값 집합을 반환하는 데 유용한 이름-값 쌍입니다. 사전이라고 하는 해시 테이블을 볼 수도 있습니다. 특정 활동에 대한 속성은 단순 값 대신 해시 테이블을 기대합니다.

다음 구문을 사용하여 해시 테이블을 만듭니다. 여러 항목을 포함할 수 있지만 각 항목은 이름과 값으로 정의됩니다.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

예를 들어 다음 식에서는 인터넷 검색에 대한 값의 해시 테이블을 예상하는 활동 매개 변수의 데이터 원본으로 사용할 해시 테이블을 만듭니다.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

다음 예제에서는 해시 테이블을 `Get Twitter Connection` 채우기 위해 호출된 활동의 출력을 사용합니다.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Azure 리소스 인증

Azure 리소스를 관리하는 Azure Automation의 Runbook에는 Azure에 대한 인증이 필요합니다. 서비스 주체라고도 하는 [Run As 계정은](automation-create-runas-account.md)자동화 Runbook이 구독에서 Azure Resource Manager 리소스에 액세스하는 데 사용하는 기본 메커니즘입니다. PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet을 사용하는 `AzureRunAsConnection` 연결 자산을 캔버스에 추가하여 그래픽 런북에 이 기능을 추가할 수 있습니다. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 추가할 수도 있습니다. 이 시나리오는 다음 예제에 나와 있습니다.

![실행 인증 작업](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection` 활동 또는 `Get-AutomationConnection`은 .라는 `AzureRunAsConnection`상수 값 데이터 원본으로 구성됩니다.

![실행 연결 구성](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

다음 활동 `Connect-AzAccount`, 는 Runbook에서 사용할 인증 된 실행 계정 추가 합니다.

![연결-AzAccount 매개 변수 집합](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>PowerShell `Add-AzAccount` 런북의 `Add-AzureRMAccount` 경우 에 대한 `Connect-AzAccount`별칭입니다. 이러한 별칭은 그래픽 런북에 사용할 수 없습니다. 그래픽 런북은 자체만 `Connect-AzAccount` 사용할 수 있습니다.

매개 변수 필드의 경우 활동이 여러 속성을 가진 개체를 출력하므로 **APPLICATIONID,** **CERTIFICATETHUMBPRINT**및 **TENANTID는**필드 경로에 대한 속성 이름을 지정합니다. 그렇지 않으면 Runbook이 실행되면 인증을 시도하는 동안 실패합니다. 실행 계정을 사용하여 runbook을 인증하는데 필요한 최소한의 사항입니다.

일부 구독자는 [Azure AD 사용자 계정을](automation-create-aduser-account.md) 사용하여 Azure 클래식 배포또는 Azure 리소스 관리자 리소스를 관리하는 자동화 계정을 만듭니다. 이러한 구독자에 대한 이전 버전과의 호환성을 유지하려면 Runbook에서 사용할 인증 메커니즘은 자격 증명 `Add-AzureAccount` [자산이](automation-credentials.md)있는 cmdlet입니다. 자산은 Azure 계정에 액세스할 수 있는 Active Directory 사용자를 나타냅니다.

캔버스에 자격 증명 자산을 추가한 다음 입력에 자격 증명 자산을 `Add-AzureAccount` 사용하는 활동을 추가하여 그래픽 Runbook에 이 기능을 활성화할 수 있습니다. 다음 예제를 참조하세요.

![인증 활동](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook은 각 검사점의 시작 과 이후에 인증해야 합니다. 따라서 활동 `Checkpoint-Workflow` 후에 `Add-AzureAccount` 활동을 사용해야 합니다. 추가 자격 증명 활동을 사용할 필요가 없습니다.

![활동 출력](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>그래픽 Runbook 내보내기 및 가져오기

게시된 버전의 그래픽 Runbook만 내보낼 수 있습니다. Runbook이 아직 게시되지 않은 경우 **내보내기** 단추를 사용할 수 없습니다. **내보내기** 단추를 클릭하면 Runbook이 로컬 컴퓨터로 다운로드됩니다. 파일 의 이름은 **.graphrunbook** 확장과 Runbook의 이름과 일치합니다.

Runbook을 추가할 때 **가져오기** 옵션을 선택하여 그래픽 또는 그래픽 PowerShell 워크플로 런북 파일을 가져올 수 있습니다. 가져올 파일을 선택하면 동일한 이름을 유지하거나 새 이름을 제공할 수 있습니다. **Runbook 유형** 필드에는 선택한 파일을 평가한 후 Runbook 의 유형이 표시됩니다. 올바르지 않은 다른 유형을 선택하려고 하면 그래픽 편집기에서 충돌 가능성이 있으며 변환 중에 구문 오류가 있을 수 있다는 메시지가 표시됩니다.

![Runbook 가져오기](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>그래픽 Runbook 테스트

Azure 자동화의 각 그래픽 런북에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있지만 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 준비가 되면 현재 게시된 버전을 초안 버전으로 덮어쓰는 게시를 게시합니다.

게시된 버전은 변경되지 않은 상태로 유지하면서 Azure 포털에서 Runbook의 초안 버전을 테스트할 수 있습니다. 또는 새 Runbook이 게시되기 전에 테스트하여 버전 교체 전에 Runbook이 제대로 작동하는지 확인할 수 있습니다. Runbook의 테스트는 초안 버전을 실행하고 수행하는 모든 작업이 완료되도록 합니다. 작업 기록이 만들어지지 않지만 테스트 출력 창에 출력이 표시됩니다.

편집할 Runbook을 연 다음 **테스트 창을**클릭하여 그래픽 Runbook에 대한 테스트 컨트롤을 엽니다. 테스트 컨트롤은 입력 매개 변수를 묻는 메시지를 표시하고 **시작**을 클릭하여 Runbook을 시작할 수 있습니다.

## <a name="publishing-a-graphical-runbook"></a>그래픽 Runbook 게시

편집할 Runbook을 연 다음 **게시를**클릭하여 그래픽 런북을 게시합니다. Runbook에 대한 가능한 상태는 다음과 같습니다.

* 새로운 - Runbook은 아직 게시되지 않았습니다. 
* 게시됨 - Runbook이 게시되었습니다.
* 편집에서 Runbook이 게시된 후 편집되었으며 초안 및 게시된 버전이 다릅니다.

![Runbook 상태](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Runbook의 게시된 버전으로 되돌릴 수 있는 옵션이 있습니다. 이 작업은 Runbook이 마지막으로 게시된 이후 변경된 내용을 throw합니다. Runbook의 초안 버전을 게시된 버전으로 바꿉습니다.

## <a name="next-steps"></a>다음 단계

* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
* 그래픽 런북을 시작하려면 [내 첫 번째 그래픽 실행북을](automation-first-runbook-graphical.md)참조하십시오.
* Runbook 유형 및 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 유형을](automation-runbook-types.md)참조하십시오.
* 자동화 실행 을 계정으로 사용하여 인증하는 방법을 이해하려면 [Azure 실행 을 계정으로 구성](automation-sec-configure-azure-runas-account.md)을 참조하십시오.
