
<properties
	pageTitle="Azure Batch 풀에서 자동으로 계산 노드 크기 조정 | Microsoft Azure"
	description="클라우드 풀에서 자동 크기 조정을 사용하면 풀의 계산 노드 수를 동적으로 조정합니다."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="08/26/2015"
	ms.author="davidmu"/>

# Azure Batch 풀에서 자동으로 계산 노드 크기 조정

Azure Batch 풀에서 자동으로 계산 노드 크기를 조정하는 것은 응용 프로그램에서 사용하는 처리 능력을 동적으로 조정하는 것입니다. 이러한 손쉬운 조정을 통해 시간과 비용을 절약할 수 있습니다. 계산 노드 및 풀에 대한 자세한 내용은 [Azure Batch 기술 개요](batch-technical-overview.md)를 참조하세요.

자동 크기 조정은 이 기능이 풀에서 사용하도록 설정되어 있고 수식이 풀과 연결되어 있는 경우 발생합니다. 수식은 응용 프로그램 처리에 필요한 계산 노드 수를 결정하는 데 사용됩니다. 주기적으로 수집되는 샘플에 작용하면서 풀에서 사용할 수 있는 계산 노드 수는 연결된 식에 따라 15분마다 조정됩니다.

자동 크기 조정은 풀이 만들어질 때 설정할 수도 있고 나중에 기존 풀에서 설정할 수도 있습니다. 이전에 자동 크기 조정을 사용하도록 설정한 풀에서는 수식도 업데이트할 수 있습니다. 항상 수식을 풀에 할당하기 전에 평가하는 것이 좋으며 자동 크기 조정 실행 상태를 모니터링하는 것이 중요합니다. 아래에서 이들 각각의 항목에 대해 자세히 알아봅니다.

> [AZURE.NOTE]각 Azure Batch 계정은 처리에 사용할 수 있는 최대 계산 노드 수로 제한됩니다. 시스템은 해당 제한까지만 노드를 만들기 때문에 수식에 지정된 대상 번호에 도달하지 않을 수 있습니다.

## 계산 리소스 자동 크기 조정

정의한 크기 조정 수식은 다음 처리 간격 동안 풀에서 사용할 수 있는 계산 노드 수를 결정합니다. 자동 크기 조정 수식은 요청 본문(REST API)에 있는 풀의 [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) 요소 또는 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 속성(.NET API)에 할당된 단순한 문자열 값입니다. 이 수식 문자열의 크기는 8KB를 초과할 수 없으며, 세미콜론으로 구분된 구문을 100개까지 포함할 수 있으며 줄 바꿈과 주석을 포함할 수 있습니다.

수식에 있는 문은 자유 형식의 식입니다. 모든 시스템 정의 변수, 사용자 정의 변수, 상수 값 및 이러한 변수 또는 상수에서 지원되는 연산에 포함될 수 있습니다.

	VAR = Expression(system-defined variables, user-defined variables);

복잡한 수식은 다음과 같은 다수의 구문 및 변수를 사용하여 만들어집니다.

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]자동 크기 조정 수식은 [배치 REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 변수, 형식, 작업 및 함수로 구성됩니다. 이는 [배치.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 라이브러리와 함께 연동되면서 수식 문자열에서 사용됩니다.

### 변수

수식에는 시스템 정의 변수와 사용자 정의 변수 모두를 사용할 수 있습니다.

이러한 **시스템 정의 변수** 값을 *가져와서* *설정*하여 풀의 계산 노드 개수를 관리합니다.

<table>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>해당 풀의 전용 계산 노드 대상 수입니다. 값은 태스크의 실제 사용에 따라 변경될 수 있습니다.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>풀에서 계산 노드가 제거되는 경우 발생하는 작업입니다. 가능한 값은 다음과 같습니다.
      <br/>
      <ul>
        <li><p><b>requeue</b> – 태스크를 즉시 종료하고 일정을 재조정하도록 작업 큐에 다시 배치합니다.</p></li>
        <li><p><b>terminate</b> – 태스크를 즉시 종료하고 작업 큐에서 제거합니다.</p></li>
        <li><p><b>taskcompletion</b> – 현재 실행 중인 태스크가 완료되기를 기다린 다음 풀에서 노드를 제거합니다.</p></li>
        <li><p><b>retaineddata</b> - 노드의 모든 로컬 태스크 보유 데이터가 정리되기를 기다린 다음 풀에서 노드를 제거합니다.</p></li>
      </ul></td>
   </tr>
</table>

이러한 **시스템 정의 변수**의 값을 *가져와서* 샘플에 있는 계산 노드의 메트릭을 기반으로 조정합니다. 이들 변수는 읽기 전용입니다.

<table>
  <tr>
    <th>변수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>평균 CPU 사용량 비율</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>사용된 시간(초) 수</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>사용된 평균 메가바이트 수</td>
  <tr>
    <td>$DiskBytes</td>
    <td>로컬 디스크에서 사용된 평균 기가바이트 수</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>읽은 바이트 수</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>쓴 바이트 수</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>수행된 디스크 읽기 작업 수</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>수행된 디스크 쓰기 작업 수</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>인바운드 바이트 수</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>아웃바운드 바이트 수</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>계산 노드 수</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>활성 상태인 태스크 수</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>실행 중 상태인 태스크 수</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>성공적으로 완료된 태스크 수</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>실패한 태스크 수</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>현재 전용 계산 노드 수</td>
  </tr>
</table>

### 형식

수식에서 지원되는 **형식**은 다음과 같습니다.

- double
- doubleVec
- string
- timestamp -- 타임스탬프는 다음의 멤버를 포함하는 복합 구조입니다.
	- year
	- month (1-12)
	- day (1-31)
	- weekday(숫자 형식, 예: 1은 월요일을 의미)
	- hour(24시간 형식, 예: 13은 1PM을 의미).
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

### 작업

이들 **연산**은 위에 나열된 형식에서 허용됩니다.

<table>
  <tr>
    <th>작업</th>
    <th>허용되는 연산자</th>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;operator> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operator>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;operator>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;operator> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>test double only (non-zero is true, zero is false)</td>
    <td>? :</td>
  </tr>
</table>

### 함수

이러한 미리 정의된 **함수**는 자동 크기 조정 수식을 정의하는 데 사용할 수 있습니다.

<table>
  <tr>
    <th>함수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>doubleVecList에 있는 모든 값의 평균 값입니다.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>doubleVecList에서 만든 벡터의 길이입니다.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>로그 밑이 2입니다.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>구성 요소의 로그 밑이 2입니다. vec(double)가 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다.</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>자연 로그입니다.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>구성 요소의 로그 밑이 2입니다. vec(double)가 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다.</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>로그 밑이 10입니다.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>구성 요소의 로그 밑이 10입니다. vec(double)가 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double log(double) 버전으로 간주됩니다.</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>doubleVecList의 최대값입니다.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>doubleVecList의 최소값입니다.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>doubleVecList에서 만든 벡터의 두 기준입니다.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>벡터 v의 백분위수 요소입니다.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>0.0에서 1.0 사이의 임의 값입니다.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>doubleVecList에 있는 최소값과 최대값 사이의 차이입니다.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>doubleVecList에 있는 값의 샘플 표준 편차입니다.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>자동 크기 조정 식 평가를 중지합니다.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>doubleVecList에 있는 모든 구성 요소의 합계입니다.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>매개 변수가 전달되지 않는 경우 현재 시간의 타임스탬프이며 매개 변수가 전달되는 경우 dateTime 문자열의 타임스탬프입니다. 지원되는 dateTime 형식은 W3CDTF 및 RFC1123입니다.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>시작 인덱스가 0인 벡터 v의 위치 i 요소 값입니다.</td>
  </tr>
</table>

위 표에 설명된 함수 중 일부는 목록을 인수로 사용할 수 있습니다. 쉼표로 구분된 목록은 *double* 및 *doubleVec*의 조합입니다. 예:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

*doubleVecList* 값은 평가 전 단일 *doubleVec*로 변환됩니다. 예를 들어 v = [1,2,3]인 경우 avg(v) 호출은 avg(1,2,3) 호출에 해당하며 avg(v, 7) 호출은 avg(1,2,3,7) 호출에 해당합니다.

### 샘플 데이터 가져오기

위에서 설명된 시스템 정의 변수는 연결된 데이터에 액세스할 메서드를 제공하는 개체입니다. 예를 들어, 다음 식은 최근 5분 동안의 CPU 사용률을 얻기 위한 요청을 보여줍니다.

	$CPUPercent.GetSample(TimeInterval_Minute*5)

이들 메서드는 샘플 데이터를 얻는 데 사용할 수 있습니다.

<table>
  <tr>
    <th>메서드</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>메트릭 기록에 있는 총 샘플 수를 반환합니다.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>데이터 샘플의 벡터를 반환합니다. 예:</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b> - 가장 최근 샘플에서 필요한 샘플 수를 지정합니다.</p>
				  <p>하나의 샘플은 5초 동안의 메트릭 데이터입니다. GetSample(1)은 사용 가능한 마지막 샘플을 반환하지만 $CPUPercent 같은 메트릭의 경우 샘플 수집 시기를 알 수 없으므로 이 메서드를 사용하지 않아야 합니다. 최근 샘플일 수도 있지만 시스템 문제로 인해 훨씬 오래된 샘플일 수도 있습니다. 아래 표시된 것처럼 시간 간격을 사용하는 것이 좋습니다.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b> – 샘플 데이터를 수집할 시간 프레임을 지정하고 선택적으로 요청 범위에 있어야 하는 샘플의 백분율을 지정합니다.</p>
          <p>마지막 10분 동안의 모든 샘플이 CPUPercent 기록에 있는 경우 $CPUPercent.GetSample(TimeInterval\_Minute*10)은 200개의 샘플을 반환해야 합니다. 마지막 1분의 기록이 아직 없는 경우 180개의 샘플만 반환됩니다.</p>
					<p>$CPUPercent.GetSample(TimeInterval\_Minute*10, 80)이 성공하면 $CPUPercent.GetSample(TimeInterval_Minute*10,95)이 실패합니다.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b> – 시작 시간과 종료 시간이 모두 포함된 데이터 수집 시간 프레임을 지정합니다.</p></li></ul>
		  <p>샘플을 수집할 때와 수식에 사용할 수 있을 때 사이 지연이 있습니다. GetSample 메서드를 사용하는 경우 이를 고려해야 합니다. 아래 GetSamplePercent를 참조하세요.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>기록 샘플 데이터 집합에서 가져온 샘플의 기간을 반환합니다.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>메트릭에 대해 사용 가능한 가장 오래된 데이터 샘플의 타임스탬프를 반환합니다.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>지정된 시간 간격에 대해 현재 기록에서 보유하고 있는 샘플의 비율을 반환합니다. 예를 들면 다음과 같습니다.</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>반환된 샘플 비율이 지정된 samplePercent보다 작은 경우 GetSample 메서드가 실패하므로 GetSamplePercent 메서드를 사용하여 샘플을 먼저 확인한 다음 충분한 샘플이 없는 경우 자동 크기 조정 평가를 중단하지 않고 대체 작업을 수행할 수 있습니다.</p></td>
  </tr>
</table>

### 메트릭

수식 메트릭을 정의할 때 리소스와 작업 **메트릭** 둘다 사용할 수 있으며 이들 메트릭은 풀에서 계산 노드를 관리하는 데 사용할 수 있습니다.

<table>
  <tr>
    <th>메트릭</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>리소스</td>
    <td><p>리소스 메트릭은 CPU 사용량, 대역폭 사용량, 메모리 사용량 및 계산 노드 수를 기반으로 합니다. 이러한 시스템 정의 변수(위 **변수**에서 설명됨)는 풀의 계산 노드를 관리하기 위해 수식에 사용됩니다.</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>이러한 시스템 정의 변수는 노드 리소스 메트릭에 기반하여 조정하는 데 사용됩니다.</p>
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
    <td>작업</td>
    <td><p>활성, 보류 중 및 완료됨과 같은 태스크 상태를 기반으로 합니다.</p>
    <p>이러한 시스템 정의 변수는 노드 작업 메트릭에 기반하여 조정하는 데 사용됩니다.</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## 자동 크기 조정 수식 빌드

자동 크기 조정 수식 구축은 위의 구성 요소를 사용하고 해당 구문을 완전한 수식에 결합하여 구문을 형성함으로써 수행됩니다. 예를 들어, 여기에서는 다음을 수행하는 수식의 요구 사항을 먼저 정의함으로써 수식을 작성합니다.

1. CPU 사용량이 높은 경우 풀의 계산 노드에 대상 수를 늘립니다.
2. CPU 사용량이 낮은 경우 풀의 계산 노드에 대상 수를 줄입니다.
3. 항상 최대 노드 수를 400으로 제한합니다.

CPU 사용량이 높을 때 노드 수를 *늘리기* 위해서, 마지막 10분 동안의 최소 평균 CPU 사용량이 70% 이상인 경우, 사용자 정의 변수 ($TotalNodes)에 현재 대상 노드 수의 110%가 되는 값으로 채워진 구문을 정의합니다.

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

다음 구문은 지난 60분의 평균 CPU 사용량이 20% *이하*인 경우 동일한 변수를 노드에 있는 현재 대상 수의 90%에 설정하여, 낮은 CPU 사용량이 낮을 때 대상 수를 줄입니다. 이 구문은 또한 위 구문의 사용자 정의 변수 *$TotalNodes*를 참조합니다.

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

이제 전용 계산 노드의 대상 수를 **최대** 400으로 제한합니다.

	$TargetDedicated = min(400, $TotalNodes)

완성된 수식은 다음과 같습니다.

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## 자동 크기 조정을 사용하는 풀 만들기

풀을 만들 때 자동 크기 조정을 사용하려면 다음 방법 중 하나를 사용합니다.

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) – 이 Azure PowerShell cmdlet은 AutoScaleFormula 매개 변수를 사용하여 자동 크기 조정 수식을 지정합니다.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) – 풀을 만들기 위해 이 .NET 메서드가 호출되면 풀의 [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) 및 [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) 속성을 설정하여 자동 크기 조정을 사용할 수 있게 됩니다.
- [계정에 풀 추가](https://msdn.microsoft.com/library/azure/dn820174.aspx) – 풀이 만들어질 때 이 REST API 요청에서 enableAutoScale 및 autoScaleFormula 요소를 사용하여 풀에 대해 자동 크기 조정을 설정합니다.

> [AZURE.NOTE]위 기술 중 하나를 사용하여 풀을 만들 때 자동 크기 조정을 설정하는 경우, 풀을 만들 때 풀의 *targetDedicated* 매개 변수를 지정하지 않습니다(지정해서는 안 됩니다). 또한 자동 크기 조정 풀의 크기를 수동으로 조정하려는 경우(예로서 [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx)를 사용하는 경우), 먼저 풀에서 자동 크기 조정을 사용하지 않도록 다음 풀의 크기를 조정해야 합니다.

다음 코드 조각은 자동 크기 조정 [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx)을 만드는 방법을 보여줍니다. 이는 수식이 노드의 대상 수를 월요일에는 5에, 일주일 내 격일에는 1에 설정하는 [배치.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 라이브러리를 사용합니다. 조각 내에서 "myBatchClient"는 다음과 같은 [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) 인스턴스를 적절하게 초기화합니다.

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## 풀을 만든 후 자동 크기 조정을 사용하도록 설정

이미 *targetDedicated* 매개 변수를 사용하여 계산 노드 수가 지정된 풀을 설정한 경우 나중에 기존 풀을 업데이트하여 자동으로 크기 조정되도록 할 수 있습니다. 이렇게 하려면 다음 방법 중 하나를 사용합니다.

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx) –이.NET 메서드는 기존 풀의 ID와 풀에 적용할 자동 크기 조정 수식이 필요합니다.
- [풀에서 자동 크기 조정 사용](https://msdn.microsoft.com/library/azure/dn820173.aspx) – 이 REST API 요청은 URI에 기존 풀의 ID가 필요하고 요청 본문에 자동 크기 조정 수식이 필요합니다.

> [AZURE.NOTE]풀을 만들 때 *targetDedicated* 매개 변수에 대해 값이 지정된 경우, 자동 크기 조정 수식이 평가될 때 이 값은 무시됩니다.

이 코드 조각은 [배치.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 라이브러리를 사용하여 기존 풀에서 자동 크기 조정을 사용하는 것을 보여줍니다. 기존 풀에서 수식을 사용하고 업데이트하는 것은 모두 동일한 메서드를 사용합니다. 이 기술은 자동 크기 조정을 이미 사용하고 있던 경우 지정된 풀에서 수식을 *업데이트*합니다. 이 조각은 "myBatchClient"가 [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) 인스턴스를 적절하게 초기화하고 있다고 가정하고, "mypool"는 기존 [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx)의 ID입니다.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## 자동 크기 조정 수식 평가

항상 응용 프로그램에서 수식을 사용하기 전에 수식을 평가하는 것이 좋습니다. 수식은 기존 풀에서 수식을 “테스트 실행”하여 평가합니다. 다음을 사용하여 이 작업을 수행합니다.

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) 또는 [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx) – 이러한 .NET 메서드는 기존 풀의 ID와 자동 크기 조정 수식이 포함된 문자열이 필요합니다. 호출 결과는 [AutoScaleEvaluation ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) 클래스의 인스턴스에 포함됩니다.
- [자동 크기 조정 수식 평가](https://msdn.microsoft.com/library/azure/dn820183.aspx) – 이 REST API 요청의 경우, 풀 ID는 URI에 지정되고 자동 크기 조정 수식은 요청 본문의 *autoScaleFormula* 요소에 지정됩니다. 작업 응답에는 수식과 관련이 있을 수 있는 오류 정보가 포함됩니다.

> [AZURE.NOTE]자동 크기 조정 수식을 평가하려면 먼저 유효한 수식을 사용하여 풀에서 자동 크기 조정을 사용해야 합니다.

[배치.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) 라이브러리를 사용하는 이 코드 조각에서, 이를 [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx)에 적용하기 전에 수식을 평가합니다.

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
				// variable as evaluated by the the autoscaling formula.
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

이 코드 조각에서 수식의 성공적인 평가는 다음과 유사하게 출력됩니다.

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## 자동 크기 조정 실행에 대한 정보 가져오기

수식에 대해 실행되어야 하는 자동 크기 조정 실행의 결과가 예상대로 수행되고 있는지 주기적으로 검사합니다. 이렇게 하려면 다음 방법 중 하나를 사용합니다.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) –.NET 라이브러리를 사용하는 경우, 이러한 풀의 속성은 다음과 같은 최신 자동 크기 조정 실행의 속성을 제공하는 [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) 클래스의 인스턴스를 제공합니다.
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [풀에 대한 정보 가져오기](https://msdn.microsoft.com/library/dn820165.aspx) – 이 REST API 요청은 풀에 대한 정보를 반환하며 이 정보에는 최신 자동 크기 조정 실행이 포함되어 있습니다.

## 예제 수식

풀에서 계산 리소스의 크기를 자동으로 조정하기 위해 수식을 사용할 수 있는 몇 가지 방식을 보여주는 몇 가지 예를 살펴보겠습니다.

### 예제 1

아마도 정해진 요일과 시간에 다음에 따라 노드 수를 늘리거나 줄임으로써 풀 크기를 조정하고자 할 것입니다.

		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;

이 수식은 먼저 현재 시간을 가져옵니다. 평일(1-5)에 근무 시간(오전 8시-오후 6시)인 경우, 대상 풀 크기는 20 개의 노드로 설정됩니다. 그렇지 않은 경우, 풀 크기는 10에 설정됩니다.

### 예 2

이 예에서는 풀 크기는 큐에 있는 작업의 수에 따라 조정 됩니다. 주석과 줄바꿈은 모두 수식 문자열에 허용됩니다.

	    // Get pending tasks for the past 15 minutes.
	    $Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
	    // If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
		// last sample point and the history average.
	    $Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
	    // If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
	    $TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
	    // The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
		// should be adjusted according to your use case.
	    $TargetDedicated = max(0,min($TargetVMs,20));
	    // Set node deallocation mode - keep nodes active only until tasks finish
	    $NodeDeallocationOption = taskcompletion;

### 예 3

작업의 수에 따라 풀 크기를 조정하는 또 다른 예에서, 이 수식은 또한 풀에 대해 설정된 [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx) 값을 고려합니다. 이는 계산 노드에서 병렬 작업 실행이 필요한 경우에 특히 유용합니다.

		// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
		$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
		$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
		// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
		// property on this pool is set to 4, adjust this number for your use case)
		$Cores = $TargetDedicated * 4;
		$ExtraVMs = ($Tasks - $Cores) / 4;
		$TargetVMs = ($TargetDedicated+$ExtraVMs);
		// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
		$TargetDedicated = max(0,min($TargetVMs,3));
		// Keep the nodes active until the tasks finish
		$NodeDeallocationOption = taskcompletion;

## 다음 단계

1. 응용 프로그램의 효율성을 완전하게 평가하려면, 계산 노드에 액세스해야 할 수 있습니다. 원격 액세스를 활용하려면 액세스하려는 노드에 사용자 계정이 추가되어야 하며 해당 노드에 대해 RDP 파일이 검색되어야 합니다.
    - 사용자 계정은 다음 방법 중 하나를 사용하여 추가합니다.
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) – 이 PowerShell cmdlet은 풀 이름, 계산 노드 이름, 계정 이름 및 암호를 매개 변수로 사용합니다.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx) – 이 .NET 메서드는 계산 노드에 대해 계정 이름 및 암호를 설정할 수 있는 [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) 클래스의 인스턴스를 만든 다음, [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx)가 인스턴스에서 호출되어 해당 노드에 사용자를 만듭니다.
        * [노드에 사용자 계정 추가](https://msdn.microsoft.com/library/dn820137.aspx) – 풀 이름 및 계산 노드는 URI에 지정되고 계정 이름 및 암호는 이 REST API요청의 요청 본문에 있는 노드로 전송됩니다.
    - RDP 파일 가져오기:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx) – 이.NET 메서드는 풀의 ID, 노드 ID, 그리고 만들려는 파일 RDP의 이름이 필요합니다.
        * [노드에서 원격 데스크톱 프로토콜 파일 가져오기](https://msdn.microsoft.com/library/dn820120.aspx) – 이 REST API 요청은 풀의 이름과 계산 노드의 이름이 필요합니다. 응답에는 RDP 파일 콘텐츠가 포함되어 있습니다.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) – 이 PowerShell cmdlet은 지정된 계산 노드에서 RDP 파일을 가져와 지정된 파일 위치 또는 스트림에 저장합니다.
2.	일부 응용 프로그램은 많은 양의 데이터를 생성하여 처리하기가 어려울 수 있습니다. 이 문제를 해결하는 한 가지 방법은 [효율적인 목록 쿼리](batch-efficient-list-queries.md)를 사용하는 것입니다.

<!---HONumber=September15_HO1-->