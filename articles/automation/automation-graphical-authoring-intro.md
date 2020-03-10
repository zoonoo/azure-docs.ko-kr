---
title: Azure Automation에서 그래픽 작성
description: 그래픽 작성을 통해 코드 작업 없이 Azure Automation에 대한 Runbook을 만들 수 있습니다. 이 문서에서는 그래픽 작성을 소개하고 그래픽 Runbook 만들기를 시작하는 데 필요한 모든 세부 정보를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a50dbe4d1e100032282891ccd15a94330f7fead4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373377"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure Automation에서 그래픽 작성

그래픽 작성을 사용 하면 기본 Windows PowerShell 또는 PowerShell 워크플로 코드의 복잡성 없이 Azure Automation 용 runbook을 만들 수 있습니다. Cmdlet 및 runbook의 라이브러리에서 캔버스에 활동을 추가 하 고, 함께 연결 하 고, 워크플로를 형성 하도록 구성할 수 있습니다. System Center Orchestrator 또는 Service Management Automation (SMA)로 작업 한 적이 있다면 그래픽 작성이 잘 느껴질 것입니다. 이 문서에서는 그래픽 runbook 만들기를 시작 하는 데 필요한 개념을 소개 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure Automation의 모든 runbook은 Windows PowerShell 워크플로입니다. 그래픽 runbook 및 그래픽 PowerShell 워크플로 runbook은 자동화 작업 자가 실행 하지만 보거나 수정할 수 없는 PowerShell 코드를 생성 합니다. 그래픽 runbook을 그래픽 PowerShell 워크플로 runbook으로 변환 하거나 그 반대로 변환할 수 있습니다. 그러나 이러한 runbook을 텍스트 runbook으로 변환할 수는 없습니다. 또한 자동화 그래픽 편집기는 텍스트 runbook을 가져올 수 없습니다.

## <a name="overview-of-graphical-editor"></a>그래픽 편집기 개요

그래픽 Runbook을 만들거나 편집하여 Azure 포털에서 그래픽 편집기를 열 수 있습니다.

![그래픽 작업 영역](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

다음 섹션에서는 그래픽 편집기의 컨트롤에 대해 설명합니다.

### <a name="canvas-control"></a>Canvas 컨트롤

Canvas 컨트롤을 사용 하면 runbook을 디자인할 수 있습니다. 라이브러리 컨트롤의 노드에서 runbook에 활동을 추가 하 고 runbook 논리를 정의 하는 링크를 사용 하 여 해당 활동을 연결할 수 있습니다. 캔버스 맨 아래에는 확대 및 축소 하는 데 사용할 수 있는 컨트롤이 있습니다.

### <a name="library-control"></a>라이브러리 컨트롤

라이브러리 컨트롤을 사용 하면 runbook에 추가할 [작업](#activities) 을 선택할 수 있습니다. 캔버스에 추가 하 여 다른 작업에 연결할 수 있습니다. 라이브러리 컨트롤에는 다음 표에 정의 된 섹션이 포함 되어 있습니다.

| 섹션 | Description |
|:--- |:--- |
| Cmdlet |Runbook에서 사용할 수 있는 모든 cmdlet입니다. 이러한 cmdlet은 모듈별로 구성됩니다. Automation 계정에 설치한 모든 모듈을 사용할 수 있습니다. |
| Runbook |Automation 계정의 runbook입니다. 이러한 runbook을 자식 runbook으로 사용할 캔버스에 추가할 수 있습니다. 편집 중인 runbook과 동일한 코어 유형의 runbook만 표시 됩니다. 그래픽 runbook의 경우 PowerShell 기반 runbook만 표시 됩니다. 그래픽 PowerShell 워크플로 runbook의 경우 PowerShell 워크플로 기반 runbook만 표시 됩니다. |
| 자산 |Runbook에서 사용할 수 있는 automation 계정의 [자동화 자산](/previous-versions/azure/dn939988(v=azure.100)) 입니다. Runbook에 자산을 추가 하면 선택한 자산을 가져오는 워크플로 활동이 추가 됩니다. 변수 자산의 경우 변수를 가져오는 활동을 추가할지 또는 변수를 설정하는 활동을 추가할지 선택할 수 있습니다. |
| Runbook 컨트롤 |현재 runbook에서 사용할 수 있는 작업을 제어 합니다. 분기 동기화 작업은 여러 입력을 사용 하 여 워크플로를 계속 하기 전에 모든 작업이 완료 될 때까지 대기 합니다. 코드 활동은 그래픽 runbook 유형에 따라 하나 이상의 PowerShell 또는 PowerShell 워크플로 코드 줄을 실행 합니다. 사용자 지정 코드 또는 다른 작업으로 수행하기 어려운 기능에 이 작업을 사용할 수 있습니다. |

### <a name="configuration-control"></a>구성 컨트롤

구성 컨트롤을 사용 하면 캔버스에서 선택 된 개체에 대 한 세부 정보를 제공할 수 있습니다. 이 컨트롤에서 사용할 수 있는 속성은 선택한 개체 유형에 따라 달라 집니다. 구성 컨트롤에서 옵션을 선택 하면 추가 블레이드가 열리며 추가 정보를 제공 합니다.

### <a name="test-control"></a>테스트 컨트롤

테스트 컨트롤은 그래픽 편집기를 처음 시작할 때 표시되지 않습니다. 그래픽 runbook을 대화형으로 테스트할 때 열립니다.

## <a name="activities"></a>활동

활동은 Runbook의 구성 요소입니다. 활동은 PowerShell cmdlet, 자식 runbook 또는 워크플로가 될 수 있습니다. 라이브러리 컨트롤에서 활동을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 하 여 runbook에 활동을 추가할 수 있습니다. 그런 다음 활동을 클릭하고 끌어서 캔버스의 원하는 위치에 배치할 수 있습니다. 캔버스에서 활동의 위치는 runbook의 작업에 영향을 주지 않습니다. 작업을 시각화 하는 데 가장 적합 한 방식으로 runbook의 레이아웃을 지정할 수 있습니다.

![캔버스에 추가](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

캔버스에서 작업을 선택 하 여 구성 블레이드에서 해당 속성 및 매개 변수를 구성 합니다. 활동의 레이블을 설명이 포함 된 이름으로 변경할 수 있습니다. Runbook은 여전히 원래 cmdlet을 실행 합니다. 그래픽 편집기에서 사용 하는 표시 이름만 변경 하면 됩니다. 레이블은 runbook 내에서 고유 해야 합니다.

### <a name="parameter-sets"></a>매개 변수 집합

매개 변수 집합은 특정 cmdlet에 대한 값을 허용하는 필수 및 선택적 매개 변수를 정의합니다. 모든 cmdlet에는 하나 이상의 매개 변수 집합이 있고 일부에는 여러 집합이 있습니다. Cmdlet에 여러 매개 변수 집합이 있는 경우 매개 변수를 구성 하기 전에 사용할 하나를 선택 해야 합니다. **매개 변수 집합** 을 선택 하 고 다른 집합을 선택 하 여 활동에서 사용 하는 매개 변수 집합을 변경할 수 있습니다. 이 경우 이미 구성 된 매개 변수 값은 모두 손실 됩니다.

다음 예제에서 [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet에는 세 개의 매개 변수 집합이 있습니다. 이 예제에서는 리소스 그룹의 모든 가상 컴퓨터를 반환 하기 위해 하나의 선택적 매개 변수를 사용 하 여 **Listvirtualmachineinresourcegroupparamset**이라는 집합 하나를 사용 합니다. 또한이 예제에서는 반환할 가상 컴퓨터를 지정 하기 위해 **Getvirtualmachineinresourcegroupparamset** 매개 변수 집합을 사용 합니다. 이 집합에는 두 개의 필수 매개 변수와 선택적 매개 변수 하나가 있습니다.

![매개 변수 집합](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>매개 변수 값

매개 변수 값을 지정하려면 데이터 원본을 선택하여 값 지정 방법을 결정합니다. 특정 매개 변수에 사용할 수 있는 데이터 원본은 해당 매개 변수의 유효한 값에 따라 달라 집니다. 예 **를 들어 null은 null** 값을 허용 하지 않는 매개 변수에 사용 가능한 옵션이 아닙니다.

| 데이터 원본 | Description |
|:--- |:--- |
| 상수 값 |매개 변수 값을 입력합니다. 이 데이터 원본은 Int32, Int64, String, Boolean, DateTime, Switch 데이터 형식에만 사용할 수 있습니다. |
| 활동 출력 |워크플로의 현재 활동 앞에 나오는 활동의 출력을 사용 합니다. 유효한 모든 활동이 나열됩니다. 매개 변수 값에 대해 출력을 생성 하는 활동만 사용 합니다. 활동에서 여러 속성을 가진 개체를 출력 하는 경우 활동을 선택한 후 특정 속성의 이름을 입력할 수 있습니다. |
| Runbook 입력 |작업 매개 변수에 대 한 입력으로 runbook 입력을 선택 합니다. |
| 변수 자산 |입력으로 Automation 변수를 선택 합니다. |
| 자격 증명 자산 |입력으로 Automation 자격 증명을 선택 합니다. |
| 인증서 자산 |입력으로 Automation 인증서를 선택 합니다. |
| 연결 자산 |입력으로 Automation 연결을 선택 합니다. |
| PowerShell 식 |간단한 [PowerShell 식을](#powershell-expressions)지정 하십시오. 식은 활동 전에 계산 되며 결과는 매개 변수 값에 사용 됩니다. 변수를 사용하여 활동의 출력 또는 Runbook 입력 매개 변수를 참조할 수 있습니다. |
| 구성 되지 않음 |이전에 구성 된 값을 모두 지웁니다. |

#### <a name="optional-additional-parameters"></a>선택적 추가 매개 변수

모든 cmdlet에는 추가 매개 변수를 제공할 수 있는 옵션이 있습니다. 이러한 매개 변수는 PowerShell 일반 매개 변수 또는 기타 사용자 지정 매개 변수입니다. 그래픽 편집기는 PowerShell 구문을 사용 하 여 매개 변수를 제공할 수 있는 텍스트 상자를 제공 합니다. 예를 들어 *Verbose* 일반 매개 변수를 사용 하려면 `-Verbose:$True`를 지정 해야 합니다.

### <a name="retry-activity"></a>작업 다시 시도

활동에 대 한 다시 시도 기능을 사용 하면 루프와 비슷하게 특정 조건이 충족 될 때까지 여러 번 실행할 수 있습니다. 여러 번 실행 해야 하는 작업에 대해이 기능을 사용할 수 있으며, 오류가 발생 하기 쉬우며, 성공에 대해 두 번 이상 시도 하거나 유효한 데이터에 대 한 작업의 출력 정보를 테스트 해야 할 수 있습니다.

활동에 재시도 사용하면 지연 및 조건을 설정할 수 있습니다. 지연은 작업을 다시 실행하기 전에 Runbook이 대기하는 시간(분 또는 초 단위로 측정됨)입니다. 지연 시간을 지정 하지 않으면 작업이 완료 된 직후에 작업이 다시 실행 됩니다.

![작업 다시 시도 지연](media/automation-graphical-authoring-intro/retry-delay.png)

재시도 조건은 활동이 실행 될 때마다 평가 되는 PowerShell 식입니다. 식이 True로 확인 되 면 활동이 다시 실행 됩니다. 식이 False로 확인 되 면 활동이 다시 실행 되지 않고 runbook이 다음 활동으로 이동 합니다.

![작업 다시 시도 지연](media/automation-graphical-authoring-intro/retry-condition.png)

재시도 조건은 활동 재시도에 대 한 정보에 대 한 액세스를 제공 하는 *RetryData* 라는 변수를 사용할 수 있습니다. 이 변수는 다음 테이블의 속성을 가집니다.

| 속성 | Description |
|:--- |:--- |
| NumberOfAttempts |활동이 실행된 횟수입니다. |
| 출력 |활동의 마지막 실행에서 출력입니다. |
| TotalDuration |작업이 처음으로 시작된 이후 경과된 시간입니다. |
| StartedAt |작업을 처음 시작 하는 시간 (UTC 형식)입니다. |

활동 재시도 조건의 예는 다음과 같습니다.

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

작업에 다시 시도 조건을 구성하면 이를 알리는 두 개의 시각 신호가 작업에 포함됩니다. 하나는 활동에 표시 되 고 다른 하나는 활동의 구성을 검토할 때 표시 됩니다.

![작업 다시 시도 시각적 표시기](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>워크플로 스크립트 컨트롤

워크플로 스크립트 컨트롤은 작성 되는 그래픽 runbook의 유형에 따라 PowerShell 또는 PowerShell 워크플로 스크립트를 허용 하는 특별 한 활동입니다. 이 컨트롤은 다른 방법으로는 사용 하지 못할 수 있는 기능을 제공 합니다. 매개 변수를 사용할 수 없지만 활동 출력 및 Runbook 입력 매개 변수에 변수를 사용할 수는 있습니다. 활동의 모든 출력이 databus에 추가 됩니다. 예외는 나가는 링크가 없는 output 이며,이 경우 출력이 runbook의 출력에 추가 됩니다.

예를 들어 다음 코드는 *Numberofdays*라는 runbook 입력 변수를 사용 하 여 날짜 계산을 수행 합니다. 그런 다음 계산 된 DateTime 값을 runbook의 후속 작업에서 사용할 출력으로 보냅니다.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>링크 및 워크플로

그래픽 runbook의 링크는 두 개의 작업을 연결 합니다. 원본 활동에서 대상 활동을 가리키는 화살표로 캔버스에 표시됩니다. 활동은 화살표 방향으로 실행되며, 원본 활동이 완료된 대상 활동이 시작됩니다.

### <a name="link-creation"></a>링크 만들기

원본 활동을 선택 하 고 셰이프 아래쪽의 원을 클릭 하 여 두 활동 간에 링크를 만들 수 있습니다. 화살표를 대상 활동으로 끌어 놓습니다.

![링크 만들기](media/automation-graphical-authoring-intro/create-link-revised20165.png)

링크를 선택하여 구성 블레이드에서 해당 속성을 구성할 수 있습니다. 속성에는 다음 표에 설명 된 링크 형식이 포함 됩니다.

| 링크 형식 | Description |
|:--- |:--- |
| 파이프라인 |대상 활동은 원본 활동의 각 개체 출력에 대해 한 번씩 실행 됩니다. 원본 활동의 출력이 없는 경우에는 대상 활동이 실행되지 않습니다. 원본 활동의 출력은 개체로 제공됩니다. |
| 시퀀스 |대상 활동은 원본 활동에서 출력을 받을 때 한 번만 실행 됩니다. 원본 활동의 출력이 개체 배열로 제공됩니다. |

### <a name="start-of-activity"></a>작업 시작

그래픽 Runbook은 들어오는 링크가 없는 활동으로 시작됩니다. Runbook의 시작 활동 역할을 하는 활동은 종종 하나 뿐입니다. 여러 활동에 들어오는 링크가 없는 경우 runbook은 병렬로 실행 하 여 시작 합니다. 각 활동이 완료되는 대로 링크에 따라 다른 활동을 실행합니다.

### <a name="link-conditions"></a>링크 조건

링크에 조건을 지정 하면 조건이 True로 확인 되는 경우에만 대상 활동이 실행 됩니다. 일반적으로 조건에 *ActivityOutput* 변수를 사용 하 여 원본 활동의 출력을 검색 합니다.

파이프라인 링크의 경우 단일 개체에 대 한 조건을 지정 해야 합니다. Runbook은 원본 활동의 각 개체 출력에 대 한 조건을 평가 합니다. 그런 다음 조건을 충족 하는 각 개체에 대해 대상 활동을 실행 합니다. 예를 들어 **new-azvm**의 소스 활동을 사용 하 여 조건부 파이프라인 링크에 대해 다음 구문을 사용 하 여 Group1 이라는 리소스 그룹의 가상 머신만 검색할 수 있습니다.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

시퀀스 링크의 경우 원본 작업의 모든 개체가 포함 된 단일 배열이 반환 되므로 runbook은 조건을 한 번만 평가 합니다. 이로 인해 runbook은 파이프라인 링크를 사용할 수 있는 것 처럼 필터링을 위해 시퀀스 링크를 사용할 수 없습니다. 시퀀스 링크는 다음 활동의 실행 여부를 간단히 확인할 수 있습니다.

예를 들어 **VM 시작** runbook에서 다음 작업 집합을 사용 합니다.

![시퀀스와 조건부 링크](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook은 입력 매개 변수 *VMName* 및 *ResourceGroupName* 의 값을 확인 하는 세 개의 서로 다른 시퀀스 링크를 사용 하 여 수행할 적절 한 작업을 결정 합니다. 가능한 작업은 단일 VM을 시작 하거나, 리소스 그룹의 모든 Vm을 시작 하거나, 구독에서 모든 Vm을 시작 하는 것입니다. **Azure에 연결** 및 **단일 VM 가져오기**간의 시퀀스 링크의 경우 조건 논리는 다음과 같습니다.

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

조건부 링크를 사용하면 원본 활동에서 해당 분기의 다른 활동까지 사용 가능한 데이터가 조건별로 필터링됩니다. 활동이 여러 링크의 원본인 경우 각 분기의 활동에 사용할 수 있는 데이터가 해당 분기에 연결 된 링크의 조건에 따라 달라 집니다.

예를 들어 아래 runbook의 **new-azvm** 작업은 모든 가상 컴퓨터를 시작 합니다. 두 개의 조건부 링크가 있습니다. 첫 번째 조건부 링크는 식 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`를 사용 하 여 **new-azvm** 작업이 성공적으로 완료 되었는지 필터링 합니다. 두 번째 조건부 링크는 식 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`를 사용 하 여 **new-azvm** 작업이 가상 컴퓨터를 시작 하지 못하는 경우를 필터링 합니다.

![조건부 링크 예제](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

첫 번째 링크를 따르고 **get-add-azurevm** 의 활동 출력을 사용 하는 모든 활동 **은, node.js가 실행 될 때** 시작 된 가상 머신을 검색 합니다. 두 번째 링크를 따르는 모든 활동은 **node.js가 실행 될 때** 중지 된 가상 머신만 가져옵니다. 세 번째 링크를 따르는 모든 활동은 실행 상태에 상관없이 모든 가상 머신을 가져옵니다.

### <a name="junctions"></a>분기 동기화

분기 동기화는 들어오는 모든 분기가 완료될 때까지 대기하는 특별한 활동입니다. 이렇게 하면 runbook이 여러 작업을 병렬로 실행 하 고를 진행 하기 전에 모두 완료 되었는지 확인할 수 있습니다.

분기 동기화에는 들어오는 링크 수가 제한에 제한이 없지만 이러한 링크 중 하나만 파이프라인이 될 수 있습니다. 들어오는 시퀀스 링크의 수는 제한되지 않습니다. 여러 개의 들어오는 파이프라인 링크가 있는 분기 동기화를 만들고 runbook을 저장할 수 있지만이를 실행 하면 오류가 발생 합니다.

아래 예제는 가상 머신 집합을 시작하고 이와 동시에 해당 컴퓨터에 적용할 패치를 다운로드하는 Runbook의 일부입니다. Runbook을 계속 하기 전에 두 프로세스가 모두 완료 되도록 연결을 사용 합니다.

![분기 동기화](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

주기는 대상 활동이 다시 소스 활동 또는 다른 활동에 연결 되어 해당 소스에 다시 연결 되는 경우에 형성 됩니다. 그래픽 작성은 현재 순환을 지원 하지 않습니다. Runbook에 주기가 있는 경우 정상적으로 저장되지만 실행하면 오류가 발생합니다.

![주기](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>작업 간 데이터 공유

활동에서 나가는 링크로 출력 하는 모든 데이터는 runbook의 databus에 기록 됩니다. Runbook의 모든 활동은 데이터 버스에 있는 데이터를 사용하여 매개 변수 값을 채우거나 스크립트 코드에 포함할 수 있습니다. 활동은 워크플로의 모든 이전 활동에 대한 출력에 액세스할 수 있습니다.

데이터가 데이터 버스에 기록되는 방식은 활동의 링크 형식에 따라 달라집니다. 파이프라인 링크의 경우 데이터는 여러 개체로 출력 됩니다. 시퀀스 링크의 경우 데이터는 배열로 출력 됩니다. 값이 하나만 있으면 단일 요소 배열로 출력 됩니다.

Runbook에는 databus의 데이터에 액세스 하는 두 가지 방법이 있습니다. 
* 활동 출력 데이터 원본을 사용 합니다.
* PowerShell 식 데이터 원본을 사용 합니다.

첫 번째 메커니즘은 활동 출력 데이터 원본을 사용 하 여 다른 활동의 매개 변수를 채웁니다. 출력이 개체인 경우 runbook에서 단일 속성을 지정할 수 있습니다.

![작업 출력](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

두 번째 데이터 액세스 메커니즘은 아래와 같은 구문을 사용 하 여 PowerShell 식 데이터 원본 또는 워크플로 스크립트 작업의 *ActivityOutput* 변수를 사용 하는 작업의 출력을 검색 합니다. 출력이 개체인 경우 runbook에서 단일 속성을 지정할 수 있습니다.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>검사점

활동에서 **검사점 runbook** 을 선택 하 여 그래픽 PowerShell 워크플로 runbook에서 [검사점](automation-powershell-workflow.md#checkpoints) 을 설정할 수 있습니다. 활동을 실행한 후에 검사점을 설정하게 됩니다.

![검사점](media/automation-graphical-authoring-intro/set-checkpoint.png)

검사점은 그래픽 PowerShell 워크플로 runbook 에서만 사용 되며 그래픽 runbook에서는 사용할 수 없습니다. Runbook에서 Azure cmdlet을 사용 하는 경우 **AzAccount** 활동을 사용 하 여 검사점 작업을 수행 해야 합니다. Runbook이 일시 중단 되 고 다른 작업자의이 검사점에서 다시 시작 해야 하는 경우 연결 작업이 사용 됩니다.

## <a name="runbook-input-and-output"></a>Runbook 입력 및 출력

### Runbook 입력<a name="runbook-input"></a>

Runbook은 Azure Portal 또는 다른 runbook에서 runbook을 시작 하는 사용자의 입력이 필요 합니다 (현재 항목이 자식으로 사용 되는 경우). 예를 들어 가상 컴퓨터를 만드는 runbook의 경우 사용자는 runbook이 시작 될 때마다 가상 컴퓨터의 이름 및 기타 속성에 이러한 정보를 제공 해야 할 수 있습니다.

Runbook은 하나 이상의 입력 매개 변수를 정의 하 여 입력을 허용 합니다. 사용자는 runbook이 시작 될 때마다 이러한 매개 변수에 대 한 값을 제공 합니다. 사용자가 Azure Portal를 사용 하 여 runbook을 시작 하면 runbook에서 지 원하는 각 입력 매개 변수에 대 한 값을 제공 하 라는 메시지가 사용자에 게 표시 됩니다.

Runbook을 제작할 때 runbook 도구 모음에서 **입력 및 출력** 단추를 클릭 하 여 해당 입력 매개 변수에 액세스할 수 있습니다. 이렇게 하면 입력 및 출력 컨트롤이 열리며 여기에서 기존 입력 매개 변수를 편집 하거나 **입력 추가**를 클릭 하 여 새 매개 변수를 만들 수 있습니다.

![입력 추가](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

각 입력 매개 변수는 다음 표의 속성으로 정의됩니다.

| 속성 | Description |
|:--- |:--- |
| 속성 | 필수 사항입니다. 매개 변수의 이름입니다. 이름은 runbook 내에서 고유 해야 합니다. 문자로 시작 해야 하며 문자, 숫자 및 밑줄만 포함할 수 있습니다. 이름에는 공백이 포함 될 수 없습니다. |
| Description |(선택 사항) 입력 매개 변수의 용도에 대 한 설명입니다. |
| Type | (선택 사항) 매개 변수 값에 필요한 데이터 형식입니다. Azure Portal에서는 입력 메시지를 표시할 때 각 매개 변수의 데이터 형식에 대한 적절한 컨트롤을 제공합니다. 지원 되는 매개 변수 형식은 String, Int32, Int64, Decimal, Boolean, DateTime 및 Object입니다. 데이터 형식을 선택 하지 않으면 기본값은 String이 됩니다.|
| 필수 | (선택 사항) 매개 변수에 대 한 값을 제공 해야 하는지 여부를 지정 하는 설정입니다. **예**를 선택 하는 경우 runbook이 시작 될 때 값을 제공 해야 합니다. **아니요**를 선택 하면 runbook이 시작 될 때 값이 필요 하지 않으며 기본값을 사용할 수 있습니다. 기본값이 정의 되지 않은 각 필수 매개 변수에 값을 제공 하지 않으면 runbook을 시작할 수 없습니다. |
| 기본값 | (선택 사항) Runbook이 시작 될 때 매개 변수가 전달 되지 않는 경우 매개 변수에 사용 되는 값입니다. 기본값을 설정하려면 **사용자 지정**을 선택합니다. 기본값을 제공 하지 않으려면 **없음** 을 선택 합니다. |

### <a name="runbook-output"></a>Runbook 출력

그래픽 작성은 [runbook의 출력](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)에 대 한 나가는 링크가 없는 활동에 의해 생성 된 데이터를 저장 합니다. 출력은 Runbook 작업과 함께 저장되며, 해당 Runbook이 자식으로 사용되는 경우 부모 Runbook에서 사용할 수 있습니다.

## <a name="powershell-expressions"></a>PowerShell 식

그래픽 작성의 장점 중 하나는 PowerShell에 대 한 최소한의 지식으로 runbook을 빌드할 수 있다는 것입니다. 그러나 현재는 특정 [매개 변수 값](#activities) 을 채우고 [링크 조건을](#links-and-workflow)설정 하기 위한 PowerShell의 약간 알고 있어야 합니다. 이 섹션에서는 PowerShell 식에 대 한 간략 한 소개를 제공 합니다. PowerShell의 세부 사항 전체는 [Windows PowerShell을 사용하는 스크립트](https://technet.microsoft.com/library/bb978526.aspx)에서 사용 가능합니다.

### <a name="powershell-expression-data-source"></a>PowerShell 식 데이터 원본

PowerShell 식을 데이터 원본으로 사용 하 여 [작업 매개 변수의](#activities) 값을 powershell 코드의 결과로 채울 수 있습니다. 식은 간단한 함수를 수행 하는 코드 한 줄 이나 복잡 한 논리를 수행 하는 여러 줄 일 수 있습니다. 변수에 할당되지 않은 명령의 출력은 매개 변수 값에 대한 출력입니다.

예를 들어 다음 명령은 현재 날짜를 출력 합니다.

```powershell-interactive
Get-Date
```

다음 코드 조각은 현재 날짜에서 문자열을 작성 하 고 변수에 할당 합니다. 이 코드는 변수의 내용을 출력으로 보냅니다.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

다음 명령은 현재 날짜를 평가 하 고 현재 날짜가 주말 또는 평일 인지 여부를 나타내는 문자열을 반환 합니다.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>활동 출력

Runbook에서 이전 작업의 출력을 사용 하려면 다음 구문을 사용 하 여 *ActivityOutput* 변수를 사용 합니다.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

예를 들어 가상 컴퓨터의 이름을 요구 하는 속성을 사용 하 여 작업을 수행할 수 있습니다. 이 경우 runbook에서 다음 식을 사용할 수 있습니다.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

속성에 단순한 이름 대신 가상 머신 개체가 필요한 경우 runbook은 다음 구문을 사용 하 여 전체 개체를 반환 합니다.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook은 다음과 같은 보다 복잡 한 식에서 활동의 출력을 사용할 수 있습니다. 이 식은 텍스트를 가상 컴퓨터 이름에 연결 합니다.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>조건

[비교 연산자](https://technet.microsoft.com/library/hh847759.aspx) 를 사용하여 값을 비교 하거나 값이 지정된 패턴과 일치하는지를 확인합니다. 비교는 True 또는 False 값을 반환 합니다.

예를 들어 다음 조건은 **Get-add-azurevm** 라는 활동의 가상 머신이 현재 중지 되었는지 여부를 확인 합니다.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

다음 조건은 동일한 가상 컴퓨터가 중지 되지 않은 상태 인지 여부를 확인 합니다.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

**-And** 또는 **-or**와 같은 [논리 연산자](https://technet.microsoft.com/library/hh847789.aspx)를 사용 하 여 runbook에서 여러 조건을 조인할 수 있습니다. 예를 들어 다음 조건은 이전 예제의 가상 컴퓨터가 **중지 됨** 또는 **중지**중 상태 인지 확인 합니다.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>해시 테이블

[해시 테이블](https://technet.microsoft.com/library/hh847780.aspx) 는 값 집합을 반환 하는 데 유용 하 게 사용할 수 있는 이름-값 쌍입니다. 사전 이라고 하는 해시 테이블을 볼 수도 있습니다. 특정 작업에 대 한 속성에는 단순 값 대신 해시 테이블이 있어야 합니다.

다음 구문을 사용 하 여 해시 테이블을 만듭니다. 모든 항목을 포함할 수 있지만 각 항목은 이름과 값으로 정의 됩니다.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

예를 들어 다음 식은 인터넷 검색 값의 해시 테이블을 예상 하는 활동 매개 변수의 데이터 원본으로 사용할 hashtable을 만듭니다.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

다음 예제에서는 **Twitter 연결 가져오기** 라는 작업에서 출력을 사용하여 해시 테이블을 채움니다.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Azure 리소스 인증

Azure 리소스를 관리하는 Azure Automation의 Runbook에는 Azure에 대한 인증이 필요합니다. 서비스 주체 라고도 하는 [실행 계정은](automation-create-runas-account.md)Automation runbook이 구독의 Azure Resource Manager 리소스에 액세스 하는 데 사용 하는 기본 메커니즘입니다. PowerShell [Get automationconnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet을 사용 하는 **AzureRunAsConnection** 연결 자산을 캔버스에 추가 하 여 그래픽 runbook에이 기능을 추가할 수 있습니다. [AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 추가할 수도 있습니다. 이 시나리오는 다음 예제에 나와 있습니다.

![실행 인증 작업](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

**Get Run As connection 작업**또는 **get Automationconnection**은 **AzureRunAsConnection**이라는 상수 값 데이터 원본을 사용 하 여 구성 됩니다.

![실행 연결 구성](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

다음 작업 **AzAccount**는 runbook에서 사용할 인증 된 실행 계정을 추가 합니다.

![AzAccount 매개 변수 집합](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>PowerShell runbook의 경우 **AzAccount** 및 **connect-azurermaccount** 는 **AzAccount**에 대 한 별칭입니다. 그래픽 runbook에서는 이러한 별칭을 사용할 수 없습니다. 그래픽 runbook은 **AzAccount** 자체를 사용할 수 있습니다.

작업에서 여러 속성을 가진 개체를 출력 하기 때문에 매개 변수 필드, **APPLICATIONID**, **CERTIFICATETHUMBPRINT**및 **TENANTID**의 경우 필드 경로에 대 한 속성 이름을 지정 합니다. 그렇지 않으면 runbook이 실행 될 때 인증을 시도 하는 동안 오류가 발생 합니다. 실행 계정을 사용하여 runbook을 인증하는데 필요한 최소한의 사항입니다.

일부 구독자는 azure [AD 사용자 계정을](automation-create-aduser-account.md) 사용 하 여 azure 클래식 배포 또는 Azure Resource Manager 리소스를 관리 하는 Automation 계정을 만듭니다. 이러한 구독자에 대해 이전 버전과의 호환성을 유지 하기 위해 runbook에서 사용 하는 인증 메커니즘은 [자격 증명 자산이](automation-credentials.md)포함 된 **추가 azureaccount** cmdlet입니다. 자산은 Azure 계정에 대 한 액세스 권한이 있는 Active Directory 사용자를 나타냅니다.

캔버스에 자격 증명 자산을 추가 하 고 그 뒤에 입력에 대 한 자격 증명 자산을 사용 하는 **AzureAccount 추가** 작업을 수행 하 여 그래픽 runbook에 대해이 기능을 사용 하도록 설정할 수 있습니다. 다음 예제를 참조하세요.

![인증 활동](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook은 시작 및 각 검사점 후에 인증 해야 합니다. 따라서 **검사점 워크플로** 작업 후에는 **azureaccount 추가** 작업을 사용 해야 합니다. 추가 자격 증명 작업을 사용할 필요는 없습니다.

![활동 출력](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>그래픽 Runbook 내보내기 및 가져오기

게시된 버전의 그래픽 Runbook만 내보낼 수 있습니다. Runbook이 아직 게시 되지 않은 경우 **내보내기** 단추를 사용할 수 없습니다. **내보내기** 단추를 클릭 하면 runbook이 로컬 컴퓨터에 다운로드 됩니다. 파일 이름은 **. graphrunbook** 확장의 runbook 이름과 일치 합니다.

Runbook을 추가할 때 **가져오기** 옵션을 선택 하 여 그래픽 또는 그래픽 PowerShell 워크플로 runbook 파일을 가져올 수 있습니다. 가져올 파일을 선택 하는 경우 동일한 이름을 유지 하거나 새 파일을 제공할 수 있습니다. **Runbook 유형** 필드는 선택한 파일을 평가 하 고 runbook 유형을 표시 합니다. 잘못 된 다른 형식을 선택 하려고 하면 그래픽 편집기에서 충돌이 발생할 수 있다는 메시지를 표시 하 고 변환 하는 동안 구문 오류가 발생할 수 있습니다.

![Runbook 가져오기](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

## <a name="testing-a-graphical-runbook"></a>그래픽 Runbook 테스트

Azure Automation의 각 그래픽 runbook에는 초안 버전과 게시 된 버전이 있습니다. 게시 된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 준비가 되 면 게시 하 여 현재 게시 된 버전을 초안 버전으로 덮어씁니다.

게시 된 버전을 변경 하지 않고 Azure Portal에서 runbook의 초안 버전을 테스트할 수 있습니다. 또는 runbook이 게시 되기 전에 새 runbook을 테스트 하 여 버전이 교체 되기 전에 runbook이 제대로 작동 하는지 확인할 수 있습니다. Runbook 테스트는 초안 버전을 실행 하 고 수행 하는 모든 작업이 완료 되도록 합니다. 작업 기록은 생성 되지 않지만 테스트 출력 창에는 출력이 표시 됩니다.

편집을 위해 runbook을 열고 **테스트 창**을 클릭 하 여 그래픽 runbook에 대 한 테스트 컨트롤을 엽니다. 테스트 컨트롤에서 입력 매개 변수를 묻는 메시지를 표시 하 고 **시작**을 클릭 하 여 runbook을 시작할 수 있습니다.

## <a name="publishing-a-graphical-runbook"></a>그래픽 Runbook 게시

편집용 runbook을 열고 **게시**를 클릭 하 여 그래픽 runbook을 게시 합니다. Runbook에 대 한 가능한 상태는 다음과 같습니다.

* 새로 만들기-runbook이 아직 게시 되지 않았습니다. 
* 게시 됨-runbook이 게시 되었습니다.
* 편집--runbook이 게시 된 후 편집 되었으며 초안 및 게시 된 버전이 다릅니다.

![Runbook 상태](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Runbook의 게시 된 버전으로 되돌릴 수 있는 옵션이 있습니다. 이 작업을 수행 하면 runbook을 마지막으로 게시 한 이후 변경 된 내용이 모두 throw 됩니다. Runbook의 초안 버전을 게시 된 버전으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
* 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](automation-first-runbook-graphical.md)을 참조 하세요.
* Runbook 형식 및 해당 장점 및 제한 사항에 대 한 자세한 내용은 [runbook types Azure Automation](automation-runbook-types.md)를 참조 하세요.
* Automation 실행 계정을 사용 하 여 인증 하는 방법을 이해 하려면 [Azure 실행 계정 구성](automation-sec-configure-azure-runas-account.md)을 참조 하세요.
