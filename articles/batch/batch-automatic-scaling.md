<properties
	pageTitle="Azure 배치 풀에서 자동으로 계산 노드 크기 조정 | Microsoft Azure"
	description="클라우드 풀에서 자동 크기 조정을 사용하면 풀의 계산 노드 수를 동적으로 조정합니다."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="09/27/2016"
	ms.author="marsma"/>

# Azure Batch 풀에서 자동으로 계산 노드 크기 조정

자동 크기 조정으로 Azure 배치 서비스에서는 정의한 매개 변수에 따라 풀에서 계산 노드를 동적으로 추가하거나 제거할 수 있습니다. 응용 프로그램에서 사용되는 계산 능력의 양을 자동으로 조정하여 시간과 비용을 잠재적으로 절약하고 작업의 작업 수요가 증가하면 노드를 추가하고 감소될 때 제거합니다.

[배치 .NET](batch-dotnet-get-started.md) 라이브러리의 [PoolOperations.EnableAutoScale][net_enableautoscale] 메서드와 같이 정의한 *자동 크기 조정 수식*을 연결하여 계산 노드의 풀에서 자동 크기 조정을 사용하도록 설정할 수 있습니다. 그러면 배치 서비스는 이 수식을 사용하여 워크로드를 실행하는 데 필요한 계산 노드의 수를 결정합니다. 배치는 주기적으로 수집되는 서비스 메트릭 데이터 샘플에 응답하고 풀의 계산 노드 수를 수식에 따라 구성 가능한 간격으로 조정합니다.

풀이 만들어질 때 또는 기존 풀에서 자동 크기 조정을 사용하도록 설정할 수 있습니다. "자동 크기 조정"이 활성화된 풀의 기존 수식을 변경할 수도 있습니다. 배치는 자동 크기 조정 실행의 상태를 모니터링할 뿐만 아니라 수식을 풀에 할당하기 전에 평가하는 기능을 제공합니다.

## 자동 크기 조정 수식

자동 크기 조정 수식은 하나 이상의 문을 포함하고 풀의 [autoScaleFormula][rest_autoscaleformula] 요소(배치 REST) 또는 [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] 속성(배치 .NET)에 할당된 것으로 정의된 문자열 값입니다. 풀에 할당할 경우 배치 서비스는 처리할 다음 간격을 위해 풀에 계산 노드의 대상 수를 결정하는 수식을 사용합니다(간격은 나중에 자세히 설명함). 이 수식 문자열의 크기는 8KB를 초과할 수 없으며, 세미콜론으로 구분된 구문을 100개까지 포함할 수 있으며 줄 바꿈과 주석을 포함할 수 있습니다.

배치 크기 자동 조정 "언어"를 사용할 때 자동 크기 조정 수식을 고려할 수 있습니다. 수식 문은 자유 형식이고 서비스가 정의한 변수(배치 서비스에 의해 정의된 변수) 및 사용자가 정의한 변수(사용자가 정의한 변수)를 포함할 수 있습니다. 기본 제공 형식, 연산자 및 함수를 사용하여 이러한 값에 다양한 작업을 수행할 수 있습니다. 예를 들어 문은 다음과 같은 형태일 수 있습니다.

`$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);`

수식은 일반적으로 이전 문에서 가져온 값에 대한 작업을 수행하는 여러 문을 포함합니다. 예를 들어 먼저 `variable1`에 대한 값을 구한 다음 `variable2`을(를) 채우는 함수로 전달합니다.

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

수식에서 이러한 문으로 풀의 크기를 조정하는 계산 노드 수인 **전용 노드**의 **대상** 수에 도달하는 것이 목표입니다. 이 수는 현재 풀의 노드 수 보다 높거나, 낮거나 또는 동일할 수 있습니다. 배치는 특정 간격으로 풀의 자동 크기 조정 수식을 평가합니다([간격 자동 크기 조정](#automatic-scaling-interval)은 아래에서 설명함). 그런 다음 평가 시 자동 크기 조정 수식이 지정하는 수로 풀의 노드 대상 수를 조정합니다.

간단한 예로 이 두 줄 자동 크기 조정 수식은 숫자 노드가 활성 작업 수에 따라 최대 10개의 계산 노드로 조정되어야 한다고 지정합니다.

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

문서의 다음 섹션은 변수, 연산자, 작업 및 함수를 포함하여 자동 크기 조정 수식을 구성하는 다양한 엔터티를 설명합니다. 배치 내의 다양한 계산 리소스 및 작업 메트릭을 가져오는 방법을 알아봅니다. 이러한 메트릭을 사용하여 리소스 사용량 및 작업 상태에 따라 풀의 노드 수를 적절하게 조정할 수 있습니다. 그런 다음 배치 REST 및 .NET API를 모두 사용하여 수식을 구성하고 풀에서 자동 크기 조정을 사용하는 방법을 알아봅니다. 몇 가지 예제 수식으로 마무리하겠습니다.

> [AZURE.IMPORTANT] 각 Azure Batch 계정은 처리에 사용할 수 있는 최대 코어 수(및 따라서 계산 노드)로 제한됩니다. 배치 서비스는 해당 코어 제한까지만 노드를 만듭니다. 따라서 수식에 지정된 대상 계산 노드 수에 도달하지 않을 수 있습니다. 계정 할당량을 보고 늘리는 방법에 대한 내용은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

## 변수

자동 크기 조정 수식에 **서비스 정의 변수**와 **사용자 정의 변수** 모두를 사용할 수 있습니다. 서비스 정의 변수는 배치 서비스에 기본 제공되고 일부는 읽기-쓰기이며 일부는 읽기 전용입니다. 사용자 정의 변수는 *사용자*가 정의한 변수입니다. 위의 두 줄 예제 수식에서 `$averageActiveTaskCount`이(가) 사용자 정의 변수인 반면 `$TargetDedicated`은(는) 서비스 정의 변수입니다.

아래 테이블은 배치 서비스에서 정의된 읽고 쓰기 및 읽기 전용 변수를 모두 보여 줍니다.

이러한 서비스 정의 변수 값을 **가져와서** **설정**하여 풀의 계산 노드 개수를 관리할 수 있습니다.

| 읽기-쓰기 서비스 정의 변수 | 설명 |
| --- | --- |
| $TargetDedicated | 해당 풀의 **전용 계산 노드** **대상** 수입니다. 풀의 크기를 조정해야 하는 계산 노드 수입니다. 풀이 대상 노드 수에 도달하지 않을 수 있기 때문에 "대상" 숫자입니다. 풀이 최초 목표에 도달하기 전에 대상 노드 수가 후속 자동 크기 조정 평가에 의해 다시 수정될 때 발생할 수 있습니다. 대상 노드 수에 도달하기 전에 도달한 배치 계정 노드나 코어 할당량으로 인해 발생할 수도 있습니다. |
| $NodeDeallocationOption | 풀에서 계산 노드가 제거되는 경우 발생하는 작업입니다. 가능한 값: <ul><li>**requeue** -- 즉시 작업을 종료하고 재예약을 위해 작업 큐로 되돌립니다.<li>**terminate** -- 즉시 작업을 종료하고 작업 큐에서 제거합니다. <li>**taskcompletion** -- 현재 실행 중인 작업이 완료될 때까지 기다린 다음 풀에서 노드를 제거합니다. <li>**retaineddata** -- 풀에서 노드를 제거하기 전에 노드의 모든 로컬 작업 보존 데이터가 정리될 때까지 기다립니다.</ul> |

이러한 서비스 정의 변수의 값을 **가져와서** 배치 서비스에서 메트릭을 기반으로 조정할 수 있습니다.

| 읽기 전용 서비스 정의 변수 | 설명 |
| --- | --- |
| $CPUPercent | 평균 CPU 사용량 비율. |
| $WallClockSeconds | 사용된 시간(초) 수. |
| $MemoryBytes | 사용된 평균 메가바이트 수. |
| $DiskBytes | 로컬 디스크에서 사용된 평균 기가바이트 수. |
| $DiskReadBytes | 읽은 바이트 수. |
| $DiskWriteBytes | 쓴 바이트 수. |
| $DiskReadOps | 수행된 디스크 읽기 작업 수. |
| $DiskWriteOps | 수행된 디스크 쓰기 작업 수. |
| $NetworkInBytes | 인바운드 바이트 수. |
| $NetworkOutBytes | 아웃바운드 바이트 수. |
| $SampleNodeCount | 계산 노드 수. |
| $ActiveTasks | 활성 상태인 태스크 수. |
| $RunningTasks | 실행 중 상태인 태스크 수. |
| $PendingTasks | $ActiveTasks 및 $RunningTasks의 합입니다. |
| $SucceededTasks | 성공적으로 완료된 태스크 수. |
| $FailedTasks | 실패한 태스크 수. |
| $CurrentDedicated | 현재 전용 계산 노드 수. |

> [AZURE.TIP] 위에 표시된 읽기 전용, 서비스 정의 변수는 각각에 연결된 데이터에 액세스하는 다양한 메서드를 제공하는 *개체*입니다. 자세한 내용은 아래에서 [샘플 데이터 가져오기](#getsampledata)를 참조하세요.

## 형식

수식에서 지원되는 **형식**은 다음과 같습니다.

- double
- doubleVec
- doubleVecList
- string
- timestamp--타임스탬프는 다음의 멤버를 포함하는 복합 구조입니다.

	- year
	- month (1-12)
	- day (1-31)
	- weekday(숫자 형식, 예: 1은 월요일을 의미)
	- hour(24시간 형식, 예: 13은 1PM을 의미)
	- minute (00-59)
	- second (00-59)
- timeinterval

	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

## 작업

이들 **연산**은 위에 나열된 형식에서 허용됩니다.

| 작업 | 지원되는 연산자 | 결과 형식 |
| ------------------------------------- | --------------------- | ------------- |
| double *operator* double | +, -, *, / | double |
| double *operator* timeinterval | * | timeinterval |
| doubleVec *operator* double | +, -, *, / | doubleVec |
| doubleVec *operator* doubleVec | +, -, *, / | doubleVec |
| timeinterval *operator* double | *, / | timeinterval |
| timeinterval *operator* timeinterval | +, - | timeinterval |
| timeinterval *operator* timestamp | + | timestamp |
| timestamp *operator* timeinterval | + | timestamp |
| timestamp *operator* timestamp | - | timeinterval | 
| *operator*double | -, ! | double | 
| *operator*timeinterval | - | timeinterval | 
| double *operator* double | <, <=, ==, >=, >, != | double | 
| string *operator* string | <, <=, ==, >=, >, != | double | 
| timestamp *operator* timestamp | <, <=, ==, >=, >, != | double | 
| timeinterval *operator* timeinterval | <, <=, ==, >=, >, != | double | 
| double *operator* double | &&, &#124;&#124; | double |

3항 연산자(`double ? statement1 : statement2`)가 있는 이중 연산자를 테스트할 경우 0이 아님이 **true**이고 0이 **false**입니다.

## 함수

이러한 미리 정의된 **함수**는 자동 크기 조정 수식을 정의하는 데 사용할 수 있습니다.

| 함수 | 반환 형식 | 설명
| --------------------------------- | ------------- | --------- |
| avg(doubleVecList) | double | doubleVecList에 있는 모든 값의 평균 값을 반환합니다.
| len(doubleVecList) | double | doubleVecList에서 만든 벡터의 길이를 반환합니다.
| lg(double) | double | double의 로그 밑 2를 반환합니다.
| lg(doubleVecList) | doubleVec | doubleVecList의 구성 요소 로그 밑 2를 반환합니다. vec(double)은 단일 이중 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다.
| ln(double) | double | double의 자연 로그를 반환합니다.
| ln(doubleVecList) | doubleVec | doubleVecList의 구성 요소 로그 밑 2를 반환합니다. vec(double)은 단일 이중 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다.
| log(double) | double | double의 로그 밑 10을 반환합니다.
| log(doubleVecList) | doubleVec | doubleVecList의 구성 요소 로그 밑 10을 반환합니다. vec(double)은 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double log(double) 버전으로 간주됩니다.
| max(doubleVecList) | double | doubleVecList의 최대값을 반환합니다.
| min(doubleVecList) | double | doubleVecList의 최소값을 반환합니다.
| norm(doubleVecList) | double | doubleVecList에서 만든 벡터의 두 기준을 반환합니다.
| percentile(doubleVec v, double p) | double | 벡터 v의 백분위수 요소를 반환합니다.
| rand() | double | 0\.0에서 1.0 사이의 임의 값을 반환합니다.
| range(doubleVecList) | double | doubleVecList에 있는 최소값과 최대값 사이의 차이를 반환합니다.
| std(doubleVecList) | double | doubleVecList에 있는 값의 샘플 표준 편차를 반환합니다.
| stop() | | 자동 크기 조정 식의 평가를 중지합니다.
| sum(doubleVecList) | double | doubleVecList에 있는 모든 구성 요소의 합계를 반환합니다.
| time(string dateTime="") | timestamp | 매개 변수가 전달되지 않는 경우 현재 시간의 타임스탬프 또는 매개 변수가 전달되는 경우 dateTime 문자열의 타임스탬프를 반환합니다. 지원되는 dateTime 형식은 W3C-DTF 및 RFC 1123입니다.
| val(doubleVec v, double i) | double | 시작 인덱스가 0인 벡터 v의 위치 i 요소 값을 반환합니다.

위 표에 설명된 함수 중 일부는 목록을 인수로 사용할 수 있습니다. 쉼표로 구분된 목록은 *double* 및 *doubleVec*의 조합입니다. 예:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*doubleVecList* 값은 평가 전 단일 *doubleVec*로 변환됩니다. 예를 들어 `v = [1,2,3]`의 경우 `avg(v)` 호출은 `avg(1,2,3)` 호출과 동일합니다. `avg(v, 7)` 호출은 `avg(1,2,3,7)` 호출과 동일합니다.

## <a name="getsampledata"></a>샘플 데이터 가져오기

자동 크기 조정 수식은 배치 서비스에서 제공한 메트릭 데이터(샘플)에서 작동합니다. 수식은 서비스에서 가져온 값에 따라 풀 크기를 늘리거나 줄입니다. 위에 설명한 서비스에 정의 변수는 해당 개체에 연결된 데이터에 액세스하는 다양한 메서드를 제공하는 개체입니다. 예를 들어, 다음 식은 최근 5분 동안의 CPU 사용률을 얻기 위한 요청을 보여 줍니다.

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

| 메서드 | 설명 |
| --- | --- |
| GetSample() | `GetSample()` 메서드는 데이터 샘플의 벡터를 반환합니다. <br/><br/>하나의 샘플은 30초 동안의 메트릭 데이터입니다. 즉 30초마다 샘플을 가져옵니다. 그러나 아래에서 설명하듯이 샘플이 수집된 시간과 해당 샘플을 수식에 사용할 수 있게 되는 시간 사이에 지연이 있습니다. 따라서 지정된 기간 동안 일부 샘플을 수식에 의한 평가에 사용할 수 없을지도 모릅니다.<ul><li>`doubleVec GetSample(double count)`<br/>수집된 가장 최근의 샘플에서 구한 샘플 수를 지정합니다.<br/><br/>`GetSample(1)` 마지막으로 사용 가능한 샘플을 반환합니다. 그러나 `$CPUPercent` 같은 메트릭의 경우 샘플이 수집된 *시기* 를 알 수 없기 때문에 이를 사용해서는 안 됩니다. 최근 샘플일 수도 있지만 시스템 문제로 인해 훨씬 오래된 샘플일 수도 있습니다. 그러한 경우 아래에 표시된 것처럼 시간 간격을 사용하는 것이 좋습니다.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>샘플 데이터 수집 기간을 지정합니다. 선택적으로 요청 시간 프레임에 있어야 하는 샘플의 백분율을 지정합니다.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` 마지막 10분에 대한 모든 샘플이 CPUPercent 기록에 있으면 20개 샘플을 반환합니다. 그러나 내역의 마지막 분을 사용할 수 없으면 샘플 18개만 반환될 것입니다. 이 경우: <br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` 샘플의 90%만 사용할 수 있으므로 실패합니다. <br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` 성공합니다.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/> 시작 시간과 종료 시간을 모두 포함하여 데이터를 수집하는 기간을 지정합니다.<br/><br/> 위에서 언급했듯이 샘플이 수집된 시간과 해당 샘플을 수식에 사용할 수 있게 되는 시간 사이에 지연이 있습니다. `GetSample` 메서드를 사용하는 경우 이러한 문제를 고려해야 합니다. 아래 `GetSamplePercent` 참조|
| GetSamplePeriod() | 기록 샘플 데이터 집합에서 가져온 샘플의 기간을 반환합니다. |
| Count() | 메트릭 기록에 있는 총 샘플 수를 반환합니다. |
| HistoryBeginTime() | 메트릭에 대해 사용 가능한 가장 오래된 데이터 샘플의 타임스탬프를 반환합니다. |
| GetSamplePercent() |지정된 시간 간격에 사용할 수 있는 샘플의 백분율을 반환합니다. 예: <br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>반환된 샘플의 백분율이 지정한 `samplePercent`보다 작은 경우 `GetSample` 메서드가 실패하기 때문에 먼저 `GetSamplePercent` 메서드를 사용하여 확인할 수 있습니다. 그런 다음 비효율적인 샘플이 존재하는 경우 자동 크기 조정 평가를 중단하지 않고 대체 작업을 수행할 수 있습니다.|

### 샘플, 샘플 비율 및 *GetSample()* 메서드

자동 크기 조정 수식의 핵심 작업은 작업 및 리소스 메트릭 데이터를 가져오고 데이터를 기반으로 풀 크기를 조정하는 것입니다. 따라서 자동 크기 조정 수식이 메트릭 데이터 또는 "샘플"과 상호 작용하는 방법을 이해해야 합니다.

**샘플**

배치 서비스는 정기적으로 작업 및 리소스 메트릭의 *샘플*을 사용하고 자동 크기 조정 수식에 사용할 수 있도록 합니다. 이러한 샘플은 배치 서비스에서 30초 마다 기록됩니다. 하지만 일반적으로 해당 샘플이 기록될 때와 자동 크기 조정 수식에 사용할 수 있을 때(읽을 수 있을 때) 사이에 지연이 발생하는 약간의 대기 시간이 존재합니다. 또한 네트워크 또는 다른 인프라 문제와 같은 다양한 원인으로 인해 샘플은 특정 기간에 기록되지 않았을 수 있습니다. 이는 "누락된" 샘플로 발생합니다.

**샘플 비율**

`samplePercent`을 `GetSample()` 메서드에 전달하거나 `GetSamplePercent()` 메서드를 호출할 경우 "비율"은 배치 서비스에서 기록한 샘플의 *가능한* 총 수와 실제로 자동 크기 조정 수식에 *사용할 수 있는* 샘플 수 간에 비교를 의미합니다.

예를 들어 10분 TimeSpan을 살펴보겠습니다. 샘플이 10분 TimeSpan 내에서 30초 마다 기록되므로 배치에 의해 기록된 샘플의 최대 총 수는 20샘플(2분 당)이었습니다. 그러나 보고 메커니즘의 고유한 대기 시간 또는 Azure 인프라 내의 몇 가지 다른 문제로 인해 읽기를 위한 자동 크기 조정 수식에 사용할 수 있는 샘플이 15밖에 없을 수 있습니다. 즉, 10분 동안 기록하는 샘플의 총 개수 중 **75%**만이 실제로 수식에 사용될 수 있습니다.

**GetSample() 및 샘플 범위**

자동 크기 조정 수식은 노드를 추가하거나 제거하여 풀을 증가시키고 축소시킵니다. 노드에 비용이 들기 때문에 수식은 충분한 데이터를 기반으로 지능형 분석 메서드를 사용해야 합니다. 따라서 수식에서 추세 형식 분석을 사용하는 것이 좋습니다. 이 형식은 수집된 샘플의 *범위*에 따라 풀을 확장하고 축소시킵니다.

이를 위해 `GetSample(interval look-back start, interval look-back end)`을 사용하여 샘플의 **벡터**를 반환합니다.

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

위의 줄이 배치에 의해 확인된 경우 다양한 샘플을 값의 벡터로 반환합니다. 예:

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

샘플의 벡터를 수집했으면 `min()`, `max()` 및 `avg()`과 같은 함수를 사용하여 수집된 범위에서 의미 있는 값을 파생할 수 있습니다.

보안을 강화하려면 특정 기간 동안 샘플의 특정 비율 보다 작은 경우 *실패*에 대한 수식 평가를 강제할 수 있습니다. 수식 평가가 실패하도록 강제하면 지정된 샘플의 비율을 사용할 수 없는 경우 배치가 수식의 추가 평가를 중단하도록 지시하여 풀 크기가 변경되지 않습니다. 평가가 성공하기 위해 샘플의 필요한 백분율을 지정하려면 `GetSample()`에 대한 세 번째 매개 변수로 지정합니다. 여기에서는 샘플의 75% 요구 사항이 지정됩니다.

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

또한 샘플 가용성에서 이전에 언급한 지연 시간으로 인해 1분 이상인 돌아보기 시작 시간으로 시간 범위를 지정하는 것이 중요합니다. 이 샘플이 시스템을 통해 전파되는 데 1분 정도가 걸리기 때문에 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 범위에 있는 샘플은 사용할 수 없는 경우가 많습니다. 다시 `GetSample()`라는 백분율 매개 변수를 사용하여 특정 샘플 비율 요구 사항을 강제할 수 있습니다.

> [AZURE.IMPORTANT] **자동 크기 조정 수식에서 `GetSample(1)`에*만* 의존하지 않는** 것이 **아주 좋습니다**. 즉, `GetSample(1)`가 기본적으로 배치 서비스에 "경과한 시간에 관계 없이 마지막 샘플을 제공하도록" 요구하기 때문입니다. 이 샘플은 단일 샘플이고 오래된 샘플이기 때문에 최근 작업 또는 리소스 상태의 큰 그림을 나타내지 않을 수 있습니다. `GetSample(1)`을 사용하는 경우 큰 문의 일부이며 수식이 사용하는 유일한 데이터 지점이 아니도록 주의합니다.

## 메트릭

수식을 정의할 때 **리소스** 및 **작업** 메트릭을 사용할 수 있습니다. 가져오고 평가한 메트릭 데이터를 기반으로 하는 풀의 전용 노드 대상 수를 조정합니다. 각 메트릭에 대한 자세한 내용은 위의 [변수](#variables) 섹션을 참조하세요.

<table>
  <tr>
    <th>메트릭</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><b>리소스</b></td>
    <td><p><b>리소스 메트릭</b>은 노드 수 뿐만 아니라 계산 노드의 CPU, 대역폭 및 메모리 사용량에 기반합니다.</p>
		<p> 이러한 서비스 정의 변수는 노드 수에 기반하여 조정하는 데 유용합니다.</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>이러한 서비스 정의 변수는 노드 리소스 사용량에 기반하여 조정하는 데 유용합니다.</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Task</b></td>
    <td><p><b>태스크 메트릭</b>은 활성, 보류 중 및 완료됨과 같은 태스크 상태를 기반으로 합니다. 다음 서비스 정의 변수는 노드 작업 메트릭에 기반하여 풀 크기를 조정하는 데 유용합니다.</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## 자동 크기 조정 수식 빌드

자동 크기 조정 수식 구축은 위의 구성 요소를 사용하고 해당 구문을 완전한 수식에 결합하여 구문을 형성함으로써 수행됩니다. 예를 들어, 여기에서는 다음을 수행하는 수식의 요구 사항을 먼저 정의함으로써 수식을 작성합니다.

1. CPU 사용량이 높은 경우 풀의 계산 노드에 대상 수를 늘립니다.
2. CPU 사용량이 낮은 경우 풀의 계산 노드에 대상 수를 줄입니다.
3. 항상 최대 노드 수를 400으로 제한합니다.

CPU 사용량이 높을 때 노드 수를 *늘리기* 위해서, 마지막 10분 동안의 최소 평균 CPU 사용량이 70% 이상인 경우, 사용자 정의 변수 ($TotalNodes)에 현재 대상 노드 수의 110%가 되는 값으로 채워진 구문을 정의합니다.

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

다음 구문은 지난 60분의 평균 CPU 사용량이 20% *이하*인 경우 동일한 변수를 노드에 있는 현재 대상 수의 90%에 설정합니다. 낮은 CPU 사용량이 낮을 때 대상 수를 줄입니다. 이 구문은 또한 위 구문의 사용자 정의 변수 *$TotalNodes*를 참조합니다.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

이제 전용 계산 노드의 대상 수를 **최대** 400으로 제한합니다.

`$TargetDedicated = min(400, $TotalNodes)`

완성된 수식은 다음과 같습니다.

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE] 자동 크기 조정 수식은 [배치 REST][rest_api] API 변수, 형식, 작업 및 함수로 구성됩니다. [배치 .NET][net_api] 라이브러리로 작업하더라도 수식 문자열에서 사용합니다.

## 자동 크기 조정을 사용하는 풀 만들기

풀을 만들 때 자동 크기 조정을 사용하려면 다음 방법 중 하나를 사용합니다.

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx)--이 Azure PowerShell cmdlet은 AutoScaleFormula 매개 변수를 사용하여 자동 크기 조정 수식을 지정합니다.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx)--풀을 만들기 위해 이 .NET 메서드가 호출되면 풀의 [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) 속성 및 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 속성을 설정하여 자동 크기 조정을 사용할 수 있게 합니다.
- [계정에 풀 추가](https://msdn.microsoft.com/library/azure/dn820174.aspx)--풀이 만들어질 때 이 REST API 요청에서 enableAutoScale 및 autoScaleFormula 요소를 사용하여 풀에 대해 자동 크기 조정을 설정합니다.

> [AZURE.IMPORTANT] 위의 방법 중 하나를 사용하여 자동 크기 조정 사용 풀을 만드는 경우 풀에 대한 *targetDedicated* 매개 변수는 지정되지 **않아야** 합니다. 또한 자동 크기 조정 사용 풀의 크기를 수동으로 조정하려는 경우(예: [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]을 사용하여) 먼저 풀에서 자동 크기 조정을 **사용하지 않도록** 다음 풀의 크기를 조정해야 합니다.

다음 코드 조각은 [배치 .NET][net_api] 라이브러리를 사용하여 자동 크기 조정 사용 풀([CloudPool][net_cloudpool]) 만들기를 보여 줍니다. 풀의 자동 크기 조정 수식은 월요일에 5의 대상 노드 수를 모든 다른 요일에 1의 대상 노드 수를 설정합니다. 또한 자동 크기 조정 간격을 30분으로 설정합니다(아래에서 [자동 크기 조정 간격](#automatic-scaling-interval) 참조). 여기와 이 문서의 다른 C# 코드 조각에서 "myBatchClient"는 [BatchClient][net_batchclient]의 적절히 초기화된 인스턴스입니다.

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### 자동 크기 조정 간격

기본적으로 배치 서비스는 자동 크기 조정 수식에 따라 풀의 크기를 **15분**마다 조정합니다. 그러나 다음 풀 속성을 사용하여 이 간격은 구성할 수 있습니다.

- REST API--[autoScaleEvaluationInterval][rest_autoscaleinterval]
- .NET API--[CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

최소 간격은 5분이고 최대 간격은 168시간입니다. 이 범위 밖의 간격이 지정되면 배치 서비스는 잘못된 요청(400) 오류를 반환합니다.

> [AZURE.NOTE] 자동 크기 조정은 현재 1분 미만의 변경 내용에 응답하지 않지만 워크로드를 실행하면 점차적으로 풀의 크기를 조정합니다.

## 풀을 만든 후 자동 크기 조정을 사용하도록 설정

이미 *targetDedicated* 매개 변수를 사용하여 계산 노드 수가 지정된 풀을 설정한 경우 나중에 기존 풀을 업데이트하여 자동으로 크기를 조정할 수 있습니다. 다음 방법 중 하나를 사용하여 이 작업을 수행할 수 있습니다.

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale]--이 .NET 메서드는 기존 풀의 ID와 풀에 적용할 자동 크기 조정 수식이 필요합니다.
- [풀에서 자동 크기 조정 사용][rest_enableautoscale]--이 REST API 요청은 URI에 기존 풀의 ID가 필요하고 요청 본문에 자동 크기 조정 수식이 필요합니다.

> [AZURE.NOTE] 풀을 만들 때 *targetDedicated* 매개 변수에 대해 값이 지정된 경우, 자동 크기 조정 수식이 평가될 때 이 값은 무시됩니다.

이 코드 조각은 [배치 .NET][net_api] 라이브러리를 사용하여 기존 풀에서 자동 크기 조정을 사용하는 것을 보여 줍니다. 기존 풀에서 수식을 사용하고 업데이트하는 것은 모두 동일한 메서드를 사용합니다. 이 기술은 자동 크기 조정을 이미 사용하고 있던 경우 지정된 풀에서 수식을 *업데이트*합니다. 코드 조각은 "mypool"이 기존 풀([CloudPool][net_cloudpool])의 ID라고 가정합니다.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## 자동 크기 조정 수식 평가

항상 응용 프로그램에서 수식을 사용하기 전에 수식을 평가하는 것이 좋습니다. 수식은 기존 풀에서 수식을 “테스트 실행”하여 평가합니다. 다음을 사용하여 이 작업을 수행합니다.

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) 또는 [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)--이러한 .NET 메서드는 기존 풀의 ID와 자동 크기 조정 수식이 포함된 문자열이 필요합니다. 호출 결과는 [AutoScaleEvaluation ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) 클래스의 인스턴스에 포함됩니다.
- [자동 크기 조정 수식 평가](https://msdn.microsoft.com/library/azure/dn820183.aspx)--이 REST API 요청의 경우 풀 ID는 URI에 지정됩니다. 자동 크기 조정 수식은 요청 본문의 *autoScaleFormula* 요소에 지정됩니다. 작업 응답에는 수식과 관련이 있을 수 있는 오류 정보가 포함됩니다.

> [AZURE.NOTE] 자동 크기 조정 수식을 평가하려면 먼저 유효한 수식을 사용하여 풀에서 자동 크기 조정을 사용해야 합니다.

[배치 .NET][net_api] 라이브러리를 사용하는 이 코드 조각에서, 이를 풀([CloudPool][net_cloudpool])에 적용하기 전에 수식을 평가합니다.

```
// First obtain a reference to the existing pool
CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
{
	// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
	string myFormula = @"
		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;
	";

	// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
	// the pool.
	AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

	if (eval.AutoScaleRun.Error == null)
	{
		// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
		// variable as evaluated by the autoscale formula.
		Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

		// Apply the formula to the pool since it evaluated successfully
		client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
	}
	else
	{
		// Evaluation failed, output the message associated with the error
		Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
	}
}
```

이 코드 조각에서 수식의 성공적인 평가는 다음과 유사하게 출력됩니다.

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## 자동 크기 조정 실행에 대한 정보 가져오기

수식이 예상대로 수행되도록 자동 크기 조정 실행의 결과를 주기적으로 검사합니다.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx)--.NET 라이브러리를 사용하는 경우 이 풀의 속성은 [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) 클래스의 인스턴스를 제공합니다. 이 클래스는 최신 자동 크기 조정 실행의 다음 속성을 제공합니다.
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [풀에 대한 정보 가져오기](https://msdn.microsoft.com/library/dn820165.aspx)--이 REST API 요청은 풀에 대한 정보를 반환하며 여기에는 최신 자동 크기 조정 실행이 포함되어 있습니다.

## <a name="examples"></a>예제 수식

풀에서 계산 리소스의 크기를 자동으로 조정하기 위해 수식을 사용할 수 있는 몇 가지 방식을 보여 주는 몇 가지 예를 살펴보겠습니다.

### 예제1: 시간 기반 조정

아마도 정해진 요일과 시간에 다음에 따라 노드 수를 늘리거나 줄임으로써 풀 크기를 조정하고자 할 것입니다.

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

이 수식은 먼저 현재 시간을 가져옵니다. 평일(1-5)에 근무 시간(오전 8시-오후 6시)인 경우, 대상 풀 크기는 20개의 노드로 설정됩니다. 그렇지 않은 경우, 풀 크기는 10에 설정됩니다.

### 예제2: 작업 기반 조정

이 예에서는 풀 크기는 큐에 있는 작업의 수에 따라 조정 됩니다. 주석과 줄바꿈은 모두 수식 문자열에 허용됩니다.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### 예제3: 병렬 작업에 대한 회계

작업의 수에 따라 풀 크기를 조정하는 또 다른 예입니다. 이 수식은 또한 풀에 대해 설정된 [MaxTasksPerComputeNode][net_maxtasks] 값을 고려합니다. [병렬 작업 실행](batch-parallel-node-tasks.md)이 풀에서 사용된 경우에 특히 유용합니다.

```
// Determine whether 70 percent of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### 예제4: 초기 풀 크기 설정

이 예제는 초기 기간 동안 풀 크기를 특정 노드 수로 설정하는 자동 크기 조정 수식이 있는 C# 코드 조각을 보여 줍니다. 그런 다음 초기 기간이 경과한 후 실행 중이고 활성화된 작업 수를 기반으로 풀 크기를 조정합니다.

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

위 코드 조각의 수식은 다음과 같습니다.

- 초기 풀 크기를 4 노드로 설정합니다.
- 풀의 수명 주기의 처음 10분 이내에는 풀 크기를 조정하지 않습니다.
- 10분 후 지난 60분 이내에 실행 중이고 활성화된 작업 수의 최대값을 가져옵니다.
  - 두 값이 모두 0이면(마지막 60분 동안 실행 중이거나 활성화된 작업이 없었음을 나타냄) 풀 크기가 0입니다.
  - 값 중 하나가 0보다 큰 경우 변경되지 않습니다.

## 다음 단계

* [동시 노드 작업으로 Azure 배치 계산 리소스 사용 극대화](batch-parallel-node-tasks.md)는 풀의 계산 노드에서 여러 작업을 동시에 실행할 수 있는 방법을 자세히 설명합니다. 자동 크기 조정 외에도 이 기능은 일부 워크로드에 대한 작업 기간을 줄여서 비용을 절약하는 데 도움이 될 수 있습니다.

* 다른 효율성 부스터의 경우 배치 응용 프로그램이 배치 서비스를 최적화하여 쿼리하도록 합니다. [효율적인 Azure 배치 서비스 쿼리](batch-efficient-list-queries.md)에서 잠재적으로 수천 개의 계산 노드 또는 작업의 상태를 쿼리할 경우 네트워크를 교차하는 데이터의 양을 제한하는 방법을 알아봅니다.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/ko-KR/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0928_2016-->