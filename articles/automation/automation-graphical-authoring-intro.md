<properties 
   pageTitle="Azure 자동화에서 그래픽 작성"
   description="그래픽 작성을 통해 코드 작업 없이 Azure 자동화에 대한 Runbook을 만들 수 있습니다. 이 문서에서는 그래픽 작성을 소개하고 그래픽 Runbook 만들기를 시작하는 데 필요한 모든 세부 정보를 제공합니다."
   services="automation"   
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/10/2015"
   ms.author="bwren" />

# Azure 자동화에서 그래픽 작성

## 소개

그래픽 작성을 통해 기본 Windows PowerShell 워크플로 코드의 복잡성 없이 Azure 자동화용 Runbook을 만들 수 있습니다. cmdlet 라이브러리의 활동 및 기타 활동을 캔버스에 추가하고 이를 함께 연결하여 하나의 워크플로를 구성할 수 있습니다.

이 문서에서는 그래픽 작성을 소개하고 그래픽 Runbook 만들기를 시작하는 데 필요한 개념을 설명합니다.

## 그래픽 Runbook

Azure 자동화의 모든 Runbook은 Windows PowerShell 워크플로입니다. 그래픽 Runbook은 자동화 작업자가 실행하는 PowerShell 코드를 생성하지만 사용자가 이를 보거나 직접 수정할 수는 없습니다. 그래픽 Runbook을 텍스트 Runbook으로 변환할 수 없으며, 기존 텍스트 Runbook을 그래픽 편집기로 가져올 수도 없습니다.


## 그래픽 편집기 개요

그래픽 Runbook을 만들거나 편집하여 Azure Preview 포털에서 그래픽 편집기를 열 수 있습니다.

![그래픽 작업 영역](media/automation-graphical-authoring-intro/graphical-editor.png)


다음 섹션에서는 그래픽 편집기의 컨트롤에 대해 설명합니다.


### 캔버스
캔버스는 Runbook을 디자인하는 곳입니다. 라이브러리 컨트롤의 노드에서 Runbook에 활동을 추가하고 Runbook의 논리를 정의하는 링크와 연결합니다.

### 라이브러리 컨트롤

라이브러리 컨트롤은 Runbook에 추가할 [활동](#activities)을 선택하는 곳입니다. 이를 다른 활동에 연결하는 캔버스에 추가할 수 있습니다. 라이브러리 컨트롤은 다음 표에 설명된 4개의 섹션으로 구성됩니다.

| 섹션 | 설명 |
|:---|:---|
| Cmdlet | Runbook에서 사용할 수 있는 모든 cmdlet을 포함합니다. 이러한 cmdlet은 모듈별로 구성됩니다. 자동화 계정에 설치한 모든 모듈을 사용할 수 있습니다. |
| Runbook | 태그로 구성된 자동화 계정의 Runbook을 포함합니다. Runbook은 둘 이상의 태그를 가질 수 있으므로 여러 태그 아래에 나열될 수 있습니다. 이러한 Runbook을 캔버스에 추가하여 자식 Runbook으로 사용할 수 있습니다. Runbook은 자체를 호출할 수 없으므로 현재 편집 중인 Runbook은 표시되지만 캔버스에 추가할 수는 없습니다.
| 자산 | Runbook에서 사용할 수 있는 자동화 계정의 [자동화 자산](http://msdn.microsoft.com/library/dn939988.aspx)을 포함합니다. 자산을 Runbook에 추가하면 선택한 자산을 가져오는 워크플로 활동이 추가됩니다. 변수 자산의 경우 변수를 가져오는 활동을 추가할지 또는 변수를 설정하는 활동을 추가할지 선택할 수 있습니다.
| Runbook 컨트롤 | 현재 Runbook에서 사용할 수 있는 Runbook 컨트롤 활동을 포함합니다. *분기 동기화*는 여러 입력을 사용하며 모두 완료될 때까지 기다렸다가 워크플로를 계속합니다. *워크플로 스크립트*는 PowerShell 워크플로 코드 줄을 하나 이상 실행합니다. 사용자 지정 코드 또는 다른 활동으로 수행할 수 없는 기능에 이 활동을 사용할 수 있습니다.|

### 구성 컨트롤

구성 컨트롤은 캔버스에서 선택한 개체에 대한 세부 정보를 제공하는 곳입니다. 이 컨트롤에서 사용할 수 있는 속성은 선택한 개체 유형에 따라 달라집니다. 구성 컨트롤에서 옵션을 선택하면 추가 정보를 제공하도록 추가 블레이드가 열립니다.

### 테스트 컨트롤

테스트 컨트롤은 그래픽 편집기를 처음 시작할 때 표시되지 않습니다. 대화형으로 [그래픽 Runbook을 테스트](#graphical-runbook-procedures)할 때 열립니다.

## 그래픽 Runbook 절차 

### 그래픽 Runbook 테스트

게시된 버전의 Runbook을 변경되지 않은 상태로 두고 Azure Preview 포털에서 초안 버전의 Runbook을 테스트하거나, Runbook을 게시한 후 새 Runbook을 테스트할 수 있습니다. 이를 통해 게시된 버전을 바꾸기 전에 Runbook이 올바르게 작동하는지 확인할 수 있습니다. Runbook을 테스트할 때 초안 Runbook이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 만들어지지 않지만 테스트 출력 창에 출력이 표시됩니다.

편집용 Runbook을 열고 **테스트 창** 단추를 클릭하여 Runbook의 테스트 컨트롤을 엽니다.

![테스트 창 단추](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

테스트 컨트롤에서 입력 매개 변수를 묻는 메시지가 표시되면 **시작** 단추를 클릭하여 Runbook을 시작할 수 있습니다.

![테스트 컨트롤 단추](media/automation-graphical-authoring-intro/runbook-test-start.png)

### 그래픽 Runbook 게시

Azure 자동화의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있는 준비가 완료되면 이를 게시합니다. 그러면 초안 버전이 게시된 버전을 덮어씁니다.

편집용 Runbook을 열고 **게시** 단추를 클릭하여 그래픽 Runbook을 게시할 수 있습니다.

![게시 단추](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

아직 게시하지 않은 Runbook은 상태가 **신규**입니다. 게시된 Runbook은 상태가 **게시됨**입니다. Runbook을 게시한 후 편집할 때 초안 버전과 게시된 버전이 다른 경우 Runbook의 상태는 **편집 중**이 됩니다.

![Runbook 상태](media/automation-graphical-authoring-intro/runbook-statuses.png)

게시된 버전의 Runbook으로 되돌릴 수 있는 옵션도 있습니다. 이 옵션을 사용하면 Runbook이 마지막으로 게시된 이후에 적용된 모든 변경 내용이 취소되고 초안 버전이 Runbook이 게시된 버전으로 바뀝니다.

![게시됨으로 되돌리기 단추](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)


## 활동

활동은 Runbook의 구성 요소입니다. 활동은 PowerShell cmdlet, 자식 Runbook 또는 워크플로 활동일 수 있습니다. 라이브러리 컨트롤에서 활동을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 Runbook에 활동을 추가합니다. 그런 다음 활동을 클릭하고 끌어서 캔버스의 원하는 위치에 배치할 수 있습니다. 캔버스의 활동 위치는 Runbook의 작업에 영향을 주지 않습니다. 해당 작업을 시각화하는 데 가장 적합한 곳에 Runbook을 배치할 수 있습니다.

![캔버스에 추가](media/automation-graphical-authoring-intro/add-to-canvas.png)

캔버스에서 활동을 선택하여 구성 블레이드에서 해당 속성 및 매개 변수를 구성할 수 있습니다. 활동의 **레이블**을 이해하기 쉽게 변경할 수 있습니다. 여전히 원래 cmdlet이 실행되며 그래픽 편집기에서 사용할 표시 이름만 변경됩니다. 레이블은 Runbook 내에서 고유해야 합니다.

### 매개 변수 집합

매개 변수 집합은 특정 cmdlet에 대한 값을 허용하는 필수 및 선택적 매개 변수를 정의합니다. 모든 cmdlet에는 적어도 하나의 매개 변수 집합이 있으며, 여러 매개 변수 집합이 있는 cmdlet도 있습니다. cmdlet에 여러 매개 변수 집합이 있는 경우 매개 변수를 구성하려면 먼저 사용할 매개 변수 집합을 선택해야 합니다. 구성할 수 있는 매개 변수는 선택한 매개 변수 집합에 따라 달라집니다. **매개 변수 집합**을 선택하고 다른 집합을 선택하여 활동에서 사용하는 매개 변수 집합을 변경할 수 있습니다. 이 경우 구성한 모든 매개 변수 값이 손실됩니다.

다음 예제의 Get-AzureVM cmdlet에는 두 개의 매개 변수 집합이 있습니다. 매개 변수 집합 중 하나를 선택할 때까지 매개 변수 값을 구성할 수 없습니다. ListAllVMs 매개 변수 집합은 모든 가상 컴퓨터를 반환하는 데 사용되며, 단일 선택적 매개 변수를 가집니다. GetVMByServiceand VMName은 반환할 가상 컴퓨터를 지정하는 데 사용되며, 하나의 필수 매개 변수와 두 개의 선택적 매개 변수를 가집니다.

![매개 변수 집합](media/automation-graphical-authoring-intro/parameter-set.png)

#### 매개 변수 값

매개 변수 값을 지정하려면 데이터 원본을 선택하여 값 지정 방법을 결정합니다. 특정 매개 변수에 사용할 수 있는 데이터 원본은 해당 매개 변수의 유효한 값에 따라 달라집니다. 예를 들어 Null은 null 값을 허용하지 않는 매개 변수에 사용 가능한 옵션이 아닙니다.

| 데이터 원본 | 설명 |
|:---|:---|
|상수 값|매개 변수 값을 입력합니다. 이는 Int32, Int64, String, Boolean, DateTime, Switch 데이터 형식에만 사용할 수 있습니다. |
|활동 출력|워크플로에서 현재 활동 앞에 오는 활동의 출력입니다. 유효한 모든 활동이 나열됩니다. 매개 변수 값에 해당 출력을 사용할 활동만 선택하세요. 활동에서 여러 속성을 가진 개체를 출력하는 경우에는 활동을 선택한 후 속성 이름을 입력할 수 있습니다.|
|Runbook 입력 매개 변수|작업 매개 변수에 대한 입력으로 runbook의 입력 매개 변수를 선택합니다.|  
|자동화 변수 자산|입력으로 자동화 변수를 선택합니다.|  
|자동화 자격 증명 자산|입력으로 자동화 자격 증명을 선택합니다.|  
|자동화 인증서 자산|입력으로 자동화 인증서를 선택합니다.|  
|자동화 연결 자산|입력으로 자동화 연결을 선택합니다.| 
|PowerShell 식|간단한 PowerShell 식을 지정합니다. 활동 전에 식이 계산되며, 그 결과가 매개 변수 값에 사용됩니다. 변수를 사용하여 활동의 출력 또는 Runbook 입력 매개 변수를 참조할 수 있습니다.|
|빈 문자열|빈 문자열 값입니다.|
|Null|Null 값입니다.|
|선택 취소|이전에 구성된 모든 값을 지웁니다.|


#### 선택적 추가 매개 변수

모든 cmdlet에는 추가 매개 변수를 제공할 수 있는 옵션이 있습니다. 이는 PowerShell 일반 매개 변수 또는 기타 사용자 지정 매개 변수입니다. PowerShell 구문을 사용하여 매개 변수를 제공할 수 있는 텍스트 상자가 표시됩니다. 예를 들어 **Verbose** 일반 매개 변수를 사용하려면 **"-Verbose:$True"**를 지정합니다.

### 워크플로 스크립트 컨트롤

워크플로 스크립트 컨트롤은 다른 방법으로 사용할 수 없는 기능을 제공하기 위해 PowerShell 워크플로 코드를 허용하는 특별한 활동입니다. 완전한 워크플로는 아니지만 PowerShell 워크플로 코드의 유효한 줄을 포함해야 합니다. 매개 변수를 사용할 수 없지만 활동 출력 및 Runbook 입력 매개 변수에 변수를 사용할 수는 있습니다. 활동의 출력은 나가는 링크가 없는 경우 데이터 버스에 추가되고, 나가는 링크가 있는 경우 Runbook의 출력에 추가됩니다.

예를 들어 다음 코드는 $NumberOfDays라는 Runbook 입력 변수를 사용하여 날짜 계산을 수행합니다. 그런 다음 Runbook의 후속 활동에서 사용할 출력으로 계산된 날짜 시간을 보냅니다.

    $DateTimeNow = InlineScript{(Get-Date).ToUniversalTime()}
    $DateTimeStart = InlineScript{($using:DateTimeNow).AddDays(-$using:NumberOfDays)}
	$DateTimeStart


## 링크 및 워크플로

그래픽 Runbook의 **링크**는 두 개의 활동을 연결합니다. 원본 활동에서 대상 활동을 가리키는 화살표로 캔버스에 표시됩니다. 활동은 화살표 방향으로 실행되며, 원본 활동이 완료된 대상 활동이 시작됩니다.

### 링크 만들기

원본 활동을 선택하고 셰이프 아래쪽에 있는 원을 클릭하여 두 활동 간의 링크를 만듭니다. 화살표를 대상 활동으로 끌어 놓습니다.

![링크 만들기](media/automation-graphical-authoring-intro/create-link.png)

링크를 선택하여 구성 블레이드에서 해당 속성을 구성할 수 있습니다. 여기에는 다음 표에 설명된 링크 형식이 포함됩니다.

| 링크 형식 | 설명 |
|:---|:---|
| 파이프라인 | 대상 활동은 원본 활동의 각 개체 출력에 대해 한 번씩 실행됩니다. 원본 활동의 출력이 없는 경우에는 대상 활동이 실행되지 않습니다. 원본 활동의 출력은 개체로 제공됩니다. |
| 시퀀스 | 대상 활동이 한 번만 실행됩니다. 원본 활동에서 개체 배열을 받습니다. 원본 활동의 출력이 개체 배열로 제공됩니다. |

### 시작 활동

그래픽 Runbook은 들어오는 링크가 없는 활동으로 시작됩니다. Runbook의 시작 활동 역할을 하는 활동이 하나뿐인 경우도 있습니다. 여러 활동에 들어오는 링크가 없는 경우 이러한 활동을 병렬로 실행하면 Runbook이 시작됩니다. 그런 다음 각 활동이 완료되는 대로 링크에 따라 다른 활동을 실행합니다.

### 조건

링크에 대한 조건을 지정하면 조건이 true로 확인된 경우에만 대상 활동이 실행됩니다. 일반적으로 조건에서 $ActivityOutput 변수를 사용하여 원본 활동의 출력을 검색합니다.

파이프라인 링크의 경우 단일 개체에 대한 조건을 지정하면 원본 활동의 각 개체 출력에 대해 조건이 평가됩니다. 그런 다음 조건을 충족하는 각 개체에 대해 대상 활동이 실행됩니다. 예를 들어 원본 활동이 Get-AzureVM인 경우 조건부 파이프라인 링크에 다음 구문을 사용하여 현재 실행 중인 가상 컴퓨터만 검색할 수 있습니다.

	$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'

시퀀스 링크의 경우 원본 활동의 모든 개체 출력이 포함된 단일 배열이 반환되므로 조건이 한 번만 평가됩니다. 따라서 시퀀스 링크는 파이프라인 링크처럼 필터링에 사용할 수 없으며 다음 활동의 실행 여부만 결정합니다. 다음 코드는 Get-AzureVM의 출력을 평가하여 실행 중인 가상 컴퓨터를 확인하는 동일한 예제를 보여 줍니다. 이 예제의 코드는 배열의 각 개체를 살펴보고 하나 이상의 가상 컴퓨터가 실행 중인 경우 true로 확인합니다. 대상 활동은 이 데이터의 구문 분석을 담당합니다.

	$test = $false
	$VMs = $ActivityOutput['Get-AzureVm']
	Foreach ($VM in VMs)
	{
		If ($VM.PowerState –eq 'Started')
			{
				$test = $true
			}
	}
	$test

조건부 링크를 사용하면 원본 활동에서 해당 분기의 다른 활동까지 사용 가능한 데이터가 조건별로 필터링됩니다. 활동이 여러 링크의 원본인 경우에는 각 분기의 활동에 사용할 수 있는 데이터가 해당 분기에 연결된 링크의 조건에 따라 달라집니다.

예를 들어 아래 Runbook의 원본 활동은 모든 가상 컴퓨터를 가져옵니다. 여기에는 두 개의 조건부 링크와 하나의 조건 없는 링크가 있습니다. 첫 번째 조건부 링크는 *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'* 식을 사용하여 현재 실행 중인 가상 컴퓨터만 필터링합니다. 두 번째 조건부 링크는 *$ActivityOutput['Get-AzureVM'].PowerState -eq 'Stopped'* 식을 사용하여 현재 중지된 가상 컴퓨터만 필터링합니다.

![조건부 링크 예제](media/automation-graphical-authoring-intro/conditional-links.png)

첫 번째 링크를 따르고 Get-AzureVM의 활동 출력을 사용하는 모든 활동은 Get-AzureVM이 실행될 당시에 시작된 가상 컴퓨터만 가져옵니다. 두 번째 링크를 따르는 모든 활동은 Get-AzureVM이 실행될 당시에 중지된 가상 컴퓨터만 가져옵니다. 세 번째 링크를 따르는 모든 활동은 실행 상태에 상관없이 모든 가상 컴퓨터를 가져옵니다.

### 분기 동기화

분기 동기화는 들어오는 모든 분기가 완료될 때까지 대기하는 특별한 활동입니다. 이를 통해 여러 활동을 병렬로 실행하고 모든 활동이 완료된 후 다음 단계를 진행할 수 있습니다.

분기 동기화에서는 들어오는 링크의 개수에 제한이 없지만 이러한 링크가 두 개 이상이 아닌 경우에는 파이프라인일 수 있습니다. 들어오는 시퀀스 링크의 수는 제한되지 않습니다. 여러 개의 들어오는 파이프라인 링크가 있는 분기 동기화를 만들고 Runbook을 저장할 수 있지만 이를 실행하면 오류가 발생합니다.

아래 예제는 가상 컴퓨터 집합을 시작하고 이와 동시에 해당 컴퓨터에 적용할 패치를 다운로드하는 Runbook의 일부입니다. 두 프로세스 모두 완료된 후 Runbook을 계속하기 위해 분기 동기화가 사용되었습니다.

![분기 동기화](media/automation-graphical-authoring-intro/junction.png)

### 주기

주기는 대상 활동이 해당 원본 활동에 다시 연결되거나 최종적으로 해당 원본에 다시 연결되는 다른 활동에 연결된 경우를 의미합니다. 주기는 그래픽 작성에서 현재 허용되지 않습니다. Runbook에 주기가 있는 경우 정상적으로 저장되지만 실행하면 오류가 발생합니다.

![주기](media/automation-graphical-authoring-intro/cycle.png)

### 루프

루프는 활동을 지정한 횟수만큼 반복하거나 특정 조건이 충족될 때까지 계속 반복하는 경우를 의미합니다. 루프는 현재 그래픽 Runbook에서 지원되지 않습니다.

### 활동 간의 데이터 공유

나가는 링크가 있는 활동에서 출력된 모든 데이터는 Runbook의 *데이터 버스*에 기록됩니다. Runbook의 모든 활동은 데이터 버스에 있는 데이터를 사용하여 매개 변수 값을 채우거나 스크립트 코드에 포함할 수 있습니다. 활동은 워크플로의 모든 이전 활동에 대한 출력에 액세스할 수 있습니다.

데이터가 데이터 버스에 기록되는 방식은 활동의 링크 형식에 따라 달라집니다. **파이프라인**의 경우 데이터는 여러 개체로 출력됩니다. **시퀀스** 링크의 경우 데이터는 배열로 출력됩니다. 값이 하나만 있으면 단일 요소 배열로 출력됩니다.

두 가지 방법 중 하나를 사용하여 데이터 버스의 데이터에 액세스할 수 있습니다. 첫째는 **활동 출력** 데이터 원본을 사용하여 다른 활동의 매개 변수를 채우는 것입니다. 출력이 개체인 경우에는 단일 속성을 지정할 수 있습니다.

![활동 출력](media/automation-graphical-authoring-intro/activity-output-datasource.png)

**PowerShell 식** 데이터 원본에서 활동 출력을 검색하거나 ActivityOutput 변수를 사용하여 **워크플로 스크립트** 활동에서 활동 출력을 검색할 수도 있습니다. 출력이 개체인 경우에는 단일 속성을 지정할 수 있습니다. ActivityOutput 변수는 다음 구문을 사용합니다.

	$ActivityOutput['Activity Label']
	$ActivityOutput['Activity Label'].PropertyName 

### 검사점

Runbook에서 [검사점](automation-powershell-workflow/#checkpoints)을 설정하는 데 적용되는 동일한 지침이 그래픽 Runbook에 적용됩니다. 검사점을 설정해야 하는 Checkpoint-Workflow cmdlet에 대한 활동을 추가할 수 있습니다. 그런 다음 이 검사점에서 다른 작업자에 대해 Runbook이 시작되는 경우 Add-AzureAccount를 사용하여 이 활동을 따릅니다.

## Azure 리소스 인증

Azure 자동화의 Runbook에는 대부분 Azure 리소스에 대한 인증이 필요합니다. 이 인증에 사용되는 일반적인 메서드는 Azure 계정에 대한 액세스 권한이 있는 Active Directory 사용자를 나타내는 [자격 증명 자산](http://msdn.microsoft.com/library/dn940015.aspx)이 포함된 Add-AzureAccount cmdlet입니다. 이에 관한 내용은 [Azure 자동화 구성](automation-configuring.md)에 설명되어 있습니다.

Add-AzureAccount 활동이 따르는 캔버스에 자격 증명 자산을 추가하여 그래픽 Runbook에 이 기능을 추가할 수 있습니다. Add-AzureAccount는 자격 증명 활동을 해당 입력으로 사용합니다. 다음 예제에 이 내용이 나와 있습니다.

![인증 활동](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook을 시작할 때와 각 검사점 이후에 인증해야 합니다. 즉, 모든 Checkpoint-Workflow 활동 뒤에 Add-AzureAccount 활동을 추가해야 합니다. 동일한 자격 증명 활동을 사용할 수 있으므로 추가 자격 증명 활동은 필요하지 않습니다.

![활동 출력](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Runbook 입력 및 출력

### Runbook 입력

Runbook에는 사용자의 입력(사용자가 Azure Preview 포털을 통해 Runbook을 시작하는 경우) 또는 다른 Runbook의 입력(현재 Runbook이 자식 Runbook으로 사용되는 경우)이 필요할 수 있습니다. 예를 들어 가상 컴퓨터를 만드는 Runbook이 있는 경우 Runbook을 시작할 때마다 가상 컴퓨터의 이름 및 기타 속성과 같은 정보를 제공해야 할 수 있습니다.

하나 이상의 입력 매개 변수를 정의하여 Runbook에 대한 입력을 허용합니다. Runbook을 시작할 때마다 이러한 매개 변수의 값을 제공합니다. Azure Preview 포털을 사용하여 Runbook을 시작하는 경우 Runbook의 각 입력 매개 변수 값을 제공하라는 메시지가 표시됩니다.

Runbook 도구 모음에서 **입력 및 출력** 단추를 클릭하여 Runbook의 입력 매개 변수에 액세스할 수 있습니다.

![Runbook 입력 및 출력](media/automation-graphical-authoring-intro/runbook-edit-input-output.png)

이렇게 하면 **입력 및 출력** 컨트롤이 열리며, 여기에서 기존 입력 매개 변수를 편집하거나 **입력 추가**를 클릭하여 새 입력 매개 변수를 만들 수 있습니다.

![입력 추가](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

각 입력 매개 변수는 다음 표의 속성으로 정의됩니다.

|속성|설명|
|:---|:---|
| 이름 | 매개 변수의 고유한 이름입니다. 영숫자 문자만 포함할 수 있으며, 공백을 포함할 수 없습니다. |
| 설명 | 입력 매개 변수에 대한 선택적 설명입니다. |
| 형식 | 매개 변수 값에 필요한 데이터 형식입니다. Azure Preview 포털에서는 입력 메시지를 표시할 때 각 매개 변수의 데이터 형식에 대한 적절한 컨트롤을 제공합니다. |
| 필수 | 매개 변수에 대해 값을 제공해야 하는지 여부를 지정합니다. 기본값이 정의되지 않은 각 필수 매개 변수의 값을 제공하지 않으면 Runbook을 시작할 수 없습니다. |
| 기본값 | 값을 제공하지 않은 경우 매개 변수에 사용되는 값을 지정합니다. Null 또는 특정 값일 수 있습니다. |


### Runbook 출력

나가는 링크가 없는 모든 활동에서 생성된 데이터는 [Runbook의 출력](http://msdn.microsoft.com/library/azure/dn879148.aspx)에 추가됩니다. 출력은 Runbook 작업과 함께 저장되며, 해당 Runbook이 자식으로 사용되는 경우 부모 Runbook에서 사용할 수 있습니다.


## 관련된 문서

- [Windows PowerShell 워크플로 학습](automation-powershell-workflow.md)
- [자동화 자산](http://msdn.microsoft.com/library/azure/dn939988.aspx)
 

<!---HONumber=July15_HO5-->