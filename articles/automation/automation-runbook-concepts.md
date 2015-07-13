<properties 
   pageTitle="Azure 자동화 Runbook 개념"
   description="Azure 자동화에서 Runbook을 만들기 위해 이해해야 하는 기본 개념을 설명합니다."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure 자동화 Runbook 개념

Azure 자동화의 Runbook은 Windows PowerShell 워크플로로 구현됩니다. 이 섹션에서는 자동화 Runbook에 공통적인 워크플로의 중요한 기능에 대해 간략하게 설명합니다. 워크플로에 대한 전체 세부 정보는 [Windows PowerShell 워크플로 시작](http://technet.microsoft.com/library/jj134242.aspx)에서 확인할 수 있습니다.


## Windows PowerShell 워크플로

워크플로는 장기 실행 작업을 수행하거나 여러 장치 또는 관리 노드에서 여러 단계를 조정해야 하는 프로그래밍 방식으로 연결된 일련의 단계입니다. 워크플로는 일반적인 스크립트에 비해 여러 장치에서 작업을 동시에 수행하고 오류로부터 자동으로 복구할 수 있다는 장점이 있습니다. Windows PowerShell 워크플로는 Windows Workflow Foundation을 활용하는 Windows PowerShell 스크립트입니다. 워크플로는 Windows PowerShell 구문으로 작성되고 Windows PowerShell에서 시작되지만 Windows Workflow Foundation에서 처리됩니다.

### 기본 구조

Windows PowerShell 워크플로는 **Workflow** 키워드와 그 뒤에 중괄호로 묶인 스크립트의 본문으로 시작됩니다. 워크플로 이름은 다음 구문과 같이 **Workflow** 키워드를 따릅니다. 워크플로 이름은 자동화 Runbook의 이름과 일치합니다.

    Workflow Test-Runbook
    {
       <Commands>
    }

워크플로에 매개 변수를 추가하려면 다음 구문과 같이 **Param** 키워드를 사용합니다. 사용자가 Runbook을 시작하면 관리 포털에 이러한 매개 변수의 값을 제공하라는 메시지가 표시됩니다. 이 샘플에서는 매개 변수가 필수인지 여부를 지정하는 선택적 Parameter 특성을 사용합니다.

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### 이름 지정

워크플로 이름은 Windows PowerShell의 표준인 동사-명사 형식을 따라야 합니다. 사용하도록 승인된 동사 목록은 [Windows PowerShell 명령의 승인된 동사](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx)를 참조하세요. 워크플로 이름은 자동화 Runbook의 이름과 일치해야 합니다. Runbook을 가져오려면 파일 이름이 워크플로 이름과 일치하고 .ps1로 끝나야 합니다.

### 제한 사항

Windows PowerShell 워크플로와 Windows PowerShell 간의 구문 차이 및 제한 사항에 대한 전체 목록은 [스크립트 워크플로와 스크립트 간의 구문 차이](http://technet.microsoft.com/library/jj574140.aspx)를 참조하세요.

## 활동

활동은 워크플로의 특정 작업입니다. 하나 이상의 명령으로 구성된 스크립트와 마찬가지로 워크플로는 시퀀스로 수행되는 하나 이상의 활동으로 구성됩니다. Windows PowerShell 워크플로는 워크플로를 실행할 때 많은 Windows PowerShell cmdlet을 활동으로 자동으로 변환합니다. Runbook에서 이러한 cmdlet 중 하나를 지정한 경우 해당 활동은 실제로 Windows Workflow Foundation에 의해 실행됩니다. 해당 활동이 없는 cmdlet의 경우 Windows PowerShell 워크플로는 [InlineScript](#inlinescript) 활동 내에서 cmdlet을 자동으로 실행합니다. InlineScript 블록에 명시적으로 포함하지 않으면 워크플로에서 제외되고 사용할 수 없는 cmdlet 집합이 있습니다. 이러한 개념에 자세한 내용은 [스크립트 워크플로에서 활동 사용](http://technet.microsoft.com/library/jj574194.aspx)을 참조하세요.

워크플로 활동은 해당 작업을 구성하기 위해 일반 매개 변수 집합을 공유합니다. 워크플로 일반 매개 변수에 대한 자세한 내용은 [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)를 참조하세요.

## 통합 모듈

*통합 모듈*은 Windows PowerShell 모듈을 포함하는 패키지로서, Azure 자동화로 가져올 수 있습니다. Azure 자동화로 가져온 통합 모듈의 cmdlet은 같은 자동화 계정의 모든 Runbook에서 자동으로 사용할 수 있습니다. Azure 자동화는 Windows PowerShell 4.0을 기반으로 하기 때문에 모듈의 자동 로드를 지원합니다. 따라서 [Import-Module](http://technet.microsoft.com/library/hh849725.aspx)을 사용하여 스크립트로 가져오지 않고도 설치된 모듈의 cmdlet을 사용할 수 있습니다.

## 병렬 실행

Windows PowerShell 워크플로의 한 가지 장점은 일반적인 스크립트처럼 명령 집합을 순차적으로 수행하지 않고 병렬로 수행할 수 있다는 점입니다. 이는 완료하는 데 오래 걸리는 여러 작업을 수행할 수 있는 Runbook에서 특히 유용합니다. 예를 들어 Runbook에서 가상 컴퓨터 집합을 프로비전할 수 있습니다. 이 경우 각 프로비저닝 프로세스를 순차적으로 수행하는 대신 작업을 동시에 수행하여 전체 효율성을 높일 수 있습니다. 모든 작업이 완료되어야만 Runbook이 계속됩니다.

**Parallel** 키워드를 사용하여 동시에 실행할 여러 명령이 포함된 스크립트 블록을 만들 수 있습니다. 여기에는 아래 표시된 구문이 사용됩니다. 이 예제에서 Activity1과 Activity2는 동시에 시작됩니다. Activity3은 Activity1과 Activity2가 모두 완료된 후에만 시작됩니다.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

**ForEach -Parallel** 구문을 사용하여 컬렉션의 각 항목에 대한 명령을 동시에 처리할 수 있습니다. 이 경우 컬렉션의 항목은 병렬로 처리되지만 스크립트 블록의 명령은 순차적으로 실행됩니다. 여기에는 아래 표시된 구문이 사용됩니다. 이 예제에서 Activity1은 컬렉션의 모든 항목에 대해 동시에 시작됩니다. 각 항목에 대해 Activity2는 Activity1이 완료된 후에 시작됩니다. Activity3은 모든 항목에 대해 Activity1과 Activity2가 모두 완료된 후에만 시작됩니다.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

**Sequence** 키워드는 **Parallel** 스크립트 블록 내의 명령을 시퀀스로 실행하는 데 사용됩니다. **Sequence** 스크립트 블록은 다른 명령과 병렬로 실행되지만 블록 내의 명령은 순차적으로 실행됩니다. 여기에는 아래 표시된 구문이 사용됩니다. 이 예제에서 Activity1, Activity2 및 Activity3은 동시에 시작됩니다. Activity4는 Activity3이 완료된 후에만 시작됩니다. Activity5는 다른 모든 활동이 완료된 후에 시작됩니다.

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## 검사점

*검사점* 은 워크플로의 현재 상태에 대한 스냅숏으로, 변수의 현재 값 및 해당 지점에 생성된 모든 출력을 포함합니다. Runbook에서 완료할 마지막 검사점은 런타임 중 컴퓨터 중단과 같은 문제가 있는 경우 워크플로를 다시 시작할 수 있도록 자동화 데이터베이스에 저장됩니다. 검사점이 없으면 워크플로가 처음부터 시작됩니다. 검사점 데이터는 Runbook 작업이 완료되면 제거됩니다.

**Checkpoint-Workflow** 활동을 사용하여 워크플로에서 검사점을 설정할 수 있습니다. 이 활동을 Runbook에 포함하면 검사점이 즉시 적용됩니다. 예외로 인해 Runbook이 일시 중단된 경우 작업을 다시 시작하면 설정된 마지막 검사점부터 다시 시작됩니다.

다음 샘플 코드에서는 Activity2 이후에 예외가 발생하여 Runbook이 일시 중단되었습니다. 설정된 마지막 검사점 직후이므로 작업을 다시 시작하면 Activity2를 실행하는 것으로 작업이 시작됩니다.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

예외가 발생할 수 있으며 Runbook을 다시 시작한 경우 반복되지 않아야 하는 활동 뒤에 Runbook의 검사점을 설정해야 합니다. 예를 들어 Runbook에서 가상 컴퓨터를 만들 수 있습니다. 가상 컴퓨터를 만드는 명령 앞뒤 모두에 검사점을 설정할 수 있습니다. 만들기에 실패한 경우 Runbook을 다시 시작하면 명령이 반복됩니다. 만들기에 성공했지만 이후의 Runbook에 실패한 경우 Runbook을 다시 시작하면 가상 컴퓨터가 다시 만들어지지 않습니다.

검사점에 대한 자세한 내용은 [스크립트 워크플로에 검사점 추가](http://technet.microsoft.com/library/jj574114.aspx)를 참조하세요.

## 워크플로 일시 중단

**Suspend-Workflow** 활동을 사용하여 Runbook을 강제로 일시 중단할 수 있습니다. 이 활동은 검사점을 설정하고 워크플로가 즉시 일시 중단되도록 합니다. 워크플로를 일시 중단하는 것은 다른 활동 집합을 실행하기 전에 수동 단계를 수행해야 할 수 있는 Runbook에 유용합니다.

워크플로 일시 중단에 대한 자세한 내용은 [워크플로를 자체적으로 일시 중단하도록 설정](http://technet.microsoft.com/library/jj574175.aspx)을 참조하세요.

## InlineScript

**InlineScript** 활동은 PowerShell 워크플로 대신 기존 PowerShell 스크립트에서 명령 블록을 실행하여 해당 출력을 워크플로에 반환합니다. 워크플로의 명령은 처리를 위해 Windows Workflow Foundation으로 전송되지만 InlineScript 블록의 명령은 Windows PowerShell에 의해 처리됩니다. 활동에서는 대체 자격 증명을 사용하여 코드 블록을 실행하도록 지정할 수 있는 **PSCredential** 등의 표준 워크플로 일반 매개 변수를 사용합니다.

InlineScript에는 아래 표시된 구문이 사용됩니다.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

InlineScript 활동은 특정 Runbook에서 중요할 수 있지만 워크플로 구문을 지원하지 않으며 다음과 같은 이유로 필요한 경우에만 사용해야 합니다.

- InlineScript 블록 내에서 검사점을 사용할 수 없습니다. 블록 내에서 오류가 발생한 경우 블록의 처음부터 다시 시작해야 합니다.
- InlineScript는 InlineScript 블록의 전체 길이 동안 Windows PowerShell 세션을 유지하므로 Runbook의 확장성에 영향을 줍니다.
- Get-AutomationVariable 및 Get-AutomationPSCredential과 같은 활동은 InlineScript 블록에서 사용할 수 없습니다.  

InlineScript를 사용해야 하는 경우 해당 범위를 최소화해야 합니다. 예를 들어 각 항목에 동일한 작업을 적용하면서 Runbook이 컬렉션에서 반복되는 경우 InlineScript 외부에서 루프가 발생해야 합니다. 이는 다음과 같은 이점을 제공합니다.

- 각 반복 후 워크플로에 [검사점](#checkpoints)을 설정할 수 있습니다. 작업이 일시 중지되거나 중단되었다가 다시 시작된 경우 루프를 다시 시작할 수 있습니다.
- **ForEach –Parallel**을 사용하여 컬렉션 항목을 동시에 처리할 수 있습니다.

Runbook에서 InlineScript를 사용하는 경우 다음 권장 사항에 유의하세요.

- **$Using** 범위 한정자를 사용하여 스크립트로 값을 전달할 수 있습니다. 예를 들어 InlineScript 외부에 설정된 $abc라는 변수는 InlineScript 내에서 $using:abc가 됩니다.

- InlineScript에서 출력을 반환하려면 출력을 변수에 할당하고 출력 스트림에 반환할 모든 데이터를 출력합니다. 다음 예제에서는 문자열 "hi"를 $output이라는 변수에 할당합니다.

	<pre><code>$output = InlineScript { Write-Output "hi" }</code></pre>

- InlineScript 범위 내에서 워크플로를 정의하지 마세요. 일부 워크플로가 제대로 작동하지 않을 수 있지만 이는 테스트된 시나리오가 아닙니다. 따라서 오류 메시지 또는 예기치 않은 동작과 혼동될 수 있습니다.

InlineScript 사용에 대한 자세한 내용은 [워크플로에서 Windows PowerShell 명령 실행](http://technet.microsoft.com/library/jj574197.aspx) 및 [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)를 참조하세요.


## 관련된 문서

- [Runbook 만들기 또는 가져오기](http://technet.microsoft.com/library/dn919921.aspx) 

<!---HONumber=62-->