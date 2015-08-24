
<properties
	pageTitle="Azure Batch 풀에서 자동으로 계산 노드 크기 조정"
	description="자동 크기 조정은 풀에서 사용하도록 설정하고 응용 프로그램 처리에 필요한 계산 노드 수 계산에 사용되는 수식을 풀에 연결하는 방법으로 수행됩니다."
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
	ms.date="08/05/2015"
	ms.author="davidmu"/>

# Azure Batch 풀에서 자동으로 계산 노드 크기 조정

Azure Batch 풀에서 자동으로 계산 노드 크기를 조정하는 것은 응용 프로그램에서 사용하는 처리 능력을 동적으로 조정하는 것입니다. 이러한 손쉬운 조정을 통해 시간과 비용을 절약할 수 있습니다. 계산 노드 및 풀에 대한 자세한 내용은 [Azure Batch 기술 개요](batch-technical-overview.md)를 참조하세요.

자동 크기 조정은 이 기능이 풀에서 사용하도록 설정되어 있고 수식이 풀에 연결되어 있는 경우 발생합니다. 수식은 응용 프로그램 처리에 필요한 계산 노드 수를 결정하는 데 사용됩니다. 자동 크기 조정은 풀이 만들어질 때 설정할 수도 있고 나중에 기존 풀에서 설정할 수도 있습니다. 자동 크기 조정을 사용하도록 설정한 풀에서는 수식도 업데이트할 수 있습니다.

자동 크기 조정을 사용하도록 설정하면 수식에 따라 15분마다 사용할 수 있는 계산 노드 수가 조정됩니다. 수식은 주기적으로 수집되는 샘플에서 작동하지만 샘플이 수집 되는 시기와 샘플을 수식에 사용할 수 있는 시기 사이에는 지연이 발생합니다. 아래 설명된 GetSample 메서드를 사용하는 경우 이러한 문제를 고려해야 합니다.

항상 수식을 풀에 할당하기 전에 평가하는 것이 좋으며 자동 크기 조정 실행 상태를 모니터링하는 것이 중요합니다.

> [AZURE.NOTE]각 Azure Batch 계정은 처리에 사용할 수 있는 최대 계산 노드 수로 제한됩니다. 시스템은 해당 제한까지만 노드를 만들며 수식에 지정된 대상 번호에 도달하지 않을 수 있습니다.

## 수식 정의

정의하는 수식은 다음 처리 간격 동안 풀에서 사용할 수 있는 계산 노드 수를 결정하는 데 사용됩니다. 수식은 문자열 크기가 8KB를 초과할 수 없으며 세미콜론으로 구분된 문을 100개까지 포함할 수 있습니다.

수식에 있는 문은 자유 형식의 식입니다. 모든 시스템 정의 변수, 사용자 정의 변수, 상수 값 및 이러한 변수 또는 상수에서 지원되는 연산에 포함될 수 있습니다.

	VAR = Function(System defined variables, user-defined variables);

변수를 결합하여 복잡한 수식을 만들 수도 있습니다.

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### 변수

수식에는 시스템 정의 변수 및 사용자 정의 변수를 사용할 수 있습니다. 이러한 시스템 정의 변수 값을 설정하여 풀의 계산 노드를 관리할 수 있습니다.

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

샘플에 있는 계산 노드의 메트릭을 기반으로 조정을 수행하는 경우에만 이러한 시스템 정의 변수의 값을 읽을 수 있습니다.

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

수식에서 지원되는 형식은 다음과 같습니다.

- double
- doubleVec
- string
- timestamp
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

위에 나열된 형식에서 허용되는 연산은 다음과 같습니다.

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

이러한 미리 정의된 함수는 자동 크기 조정 수식을 정의하는 데 사용할 수 있습니다.

<table>
  <tr>
    <th>함수</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>doubleVecList에 있는 모든 값의 평균 값입니다.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>doubleVecList에서 만든 벡터의 길이입니다.</td>
  <tr>
    <td>double lg(double)</td>
    <td>로그 밑이 2입니다.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>구성 요소의 로그 밑이 2입니다. vec(double)가 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다.</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>자연 로그입니다.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>구성 요소의 로그 밑이 2입니다. vec(double)가 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다.</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>로그 밑이 10입니다.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>구성 요소의 로그 밑이 10입니다. vec(double)가 단일 double 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double log(double) 버전으로 간주됩니다.</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>doubleVecList의 최대값입니다.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>doubleVecList의 최소값입니다.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>doubleVecList에서 만든 벡터의 두 기준입니다.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>벡터 v의 백분위수 요소입니다.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>0.0에서 1.0 사이의 임의 값입니다.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>doubleVecList에 있는 최소값과 최대값 사이의 차이입니다.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>doubleVecList에 있는 값의 샘플 표준 편차입니다.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>자동 크기 조정 식 평가를 중지합니다.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>doubleVecList에 있는 모든 구성 요소의 합계입니다.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>매개 변수가 전달되지 않는 경우 현재 시간의 타임스탬프이며 매개 변수가 전달되는 경우 dateTime 문자열의 타임스탬프입니다. 지원되는 dateTime 형식은 W3CDTF 및 RFC1123입니다.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>시작 인덱스가 0인 벡터 v의 위치 i 요소 값입니다.</td>
  </tr>
</table>

표에 설명된 함수 중 일부는 목록을 인수로 사용할 수 있습니다. 쉼표로 구분된 목록은 double 및 doubleVec의 조합입니다. 예:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

doubleVecList 값은 평가 전 단일 doubleVec로 변환됩니다. 예를 들어 v = [1,2,3]인 경우 avg(v) 호출은 avg(1,2,3) 호출에 해당하며 avg(v, 7) 호출은 avg(1,2,3,7) 호출에 해당합니다.

### 샘플 데이터

시스템 정의 변수는 연결된 데이터에 액세스할 메서드를 제공하는 개체입니다. 예를 들어, 다음 식은 최근 5분 동안의 CPU 사용률을 얻기 위한 요청을 보여줍니다.

	$CPUPercent.GetSample(TimeInterval_Minute*5)

샘플 데이터를 얻는 데 사용할 수 있는 메서드는 다음과 같습니다.

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
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b> – 시작 시간과 종료 시간이 모두 포함된 데이터 수집 시간 프레임을 지정합니다.</p></li></ul></td>
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
    <td><p>지정된 시간 간격에 대해 현재 기록에서 보유하고 있는 샘플의 비율을 반환합니다. 예:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b> - 반환된 샘플 비율이 지정된 samplePercent보다 작은 경우 GetSample 메서드가 실패하므로 GetSamplePercent 메서드를 사용하여 샘플을 먼저 확인한 다음 충분한 샘플이 없는 경우 자동 크기 조정 평가를 중단하지 않고 대체 작업을 수행할 수 있습니다.</p></td>
  </tr>
</table>

### 메트릭

수식에 정의할 수 있는 메트릭은 다음과 같습니다.

<table>
  <tr>
    <th>메트릭</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>리소스</td>
    <td><p>CPU 사용량, 대역폭 사용량, 메모리 사용량 및 계산 노드 수를 기반으로 합니다. 위에 설명된 이러한 시스템 변수는 수식에 사용되어 풀의 계산 노드를 관리합니다.</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>이러한 시스템 변수는 노드 메트릭 기반 조정에 사용됩니다.</p>
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
    <p>이 예에서는 지난 10분 동안의 최소 평균 CPU 사용량이 70%를 초과하는 경우 풀의 계산 노드 수를 현재 대상 노드 수의 110%로 설정하는 데 사용되는 수식을 보여줍니다.</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>이 예에서는 지난 60분 동안의 평균 CPU 사용량이 20% 미만인 경우 풀의 계산 노드 수를 현재 대상 노드 수의 90%로 설정하는 데 사용되는 수식을 보여줍니다.</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>이 예에서는 대상 전용 계산 노드 수를 최대 400으로 설정합니다.</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>작업</td>
    <td><p>활성, 보류 중 및 완료됨과 같은 태스크 상태를 기반으로 합니다.</p>
    <p>이러한 시스템 변수는 태스크 메트릭 기반 조정에 사용됩니다.</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>이 예에서는 샘플의 70%가 지난 15분 동안 기록되었는지 여부를 검색하는 수식을 보여줍니다. 지난 15분 동안 기록된 것이 아닌 경우 마지막 샘플을 사용합니다. 활성 태스크 수와 일치하도록 계산 노드 수를 증가시키려고 하며 최대값은 3입니다. 풀의 MaxTasksPerVM 속성이 4로 설정되어 있기 때문에 활성 태스크 수의 1/4로 노드 수를 설정합니다. 또한 Deallocation 옵션을 "taskcompletion"으로 설정하여 작업이 완료될 때까지 컴퓨터를 유지합니다.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $NodeDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## 자동 크기 조정 수식 평가

항상 응용 프로그램에서 수식을 사용하기 전에 수식을 평가하는 것이 좋습니다. 수식은 기존 풀에서 테스트를 실행하여 평가합니다. 다음 방법 중 하나를 사용하여 이 작업을 수행합니다.

- [IPoolManager.EvaluateAutoScale 메서드](https://msdn.microsoft.com/library/azure/dn931617.aspx) 또는 [IPoolManager.EvaluateAutoScaleAsync 메서드](https://msdn.microsoft.com/library/azure/dn931545.aspx) – 이러한 .NET 메서드에는 기존 풀의 이름과 자동 크기 조정 수식이 포함된 문자열이 필요합니다. 호출 결과는 [AutoScaleEvaluation 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) 인스턴스에 포함됩니다.
- [자동 크기 조정 수식 평가](https://msdn.microsoft.com/library/azure/dn820183.aspx) – 이 REST 작업의 경우 풀 이름은 URI에 지정되고 자동 크기 조정 수식은 요청 본문의 autoScaleFormula 요소에 지정됩니다. 작업 응답에는 수식과 관련이 있을 수 있는 오류 정보가 포함됩니다.

## 자동 크기 조정을 사용하는 풀 만들기

다음 방법 중 하나로 풀을 만듭니다.

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) – 이 cmdlet은 AutoScaleFormula 매개 변수를 사용하여 자동 크기 조정 수식을 지정합니다.
- [IPoolManager.CreatePool 메서드](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx) – 풀을 만들기 위해 이 .NET 메서드가 호출되면 풀에서 [ICloudPool.AutoScaleEnabled 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) 및 [ICloudPool.AutoScaleFormula 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx)이 자동 크기 조정을 사용하도록 설정됩니다.
- [계정에 풀 추가](https://msdn.microsoft.com/library/azure/dn820174.aspx) – 풀이 만들어질 때 이 REST API에서 enableAutoScale 및 autoScaleFormula 요소를 사용하여 풀에 대해 자동 크기 조정을 설정합니다.

> [AZURE.NOTE]풀이 만들어질 때 위에서 언급한 리소스를 사용하여 자동 크기 조정을 설정하는 경우 풀의 targetDedicated 매개 변수가 사용되지 않습니다.

## 풀을 만든 후 자동 크기 조정을 사용하도록 설정

이미 targetDedicated 매개 변수를 사용하여 계산 노드 수가 지정된 풀을 설정한 경우 나중에 기존 풀을 업데이트하여 자동으로 크기 조정되도록 할 수 있습니다. 이 작업을 수행하려면 다음 방법 중 하나를 사용합니다.

- [IPoolManager.EnableAutoScale 메서드](https://msdn.microsoft.com/library/azure/dn931709.aspx) – 이 .NET 메서드에는 기존 풀의 이름과 자동 크기 조정 수식이 필요합니다.
- [자동 크기 조정 사용/사용 안 함](https://msdn.microsoft.com/library/azure/dn820173.aspx) – 이 REST API의 경우 URI에 기존 풀의 이름이 필요하고 요청 본문에 자동 크기 조정 수식이 필요합니다.

> [AZURE.NOTE]자동 크기 조정 수식이 평가되는 경우 풀을 만들 때 targetDedicated 매개 변수에 대해 지정한 값은 무시됩니다.

## 자동 크기 조정 실행에 대한 정보 가져오기

자동 크기 조정 실행 결과를 정기적으로 확인해야 합니다. 이렇게 하려면 다음 방법 중 하나를 사용합니다.

- [ICloudPool.AutoScaleRun 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx) – .NET 라이브러리를 사용하는 경우 풀의 이 속성이 [AutoScaleRun 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) 인스턴스를 제공하며, 이 클래스는 [AutoScaleRun.Error 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx), [AutoScaleRun.Results 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) 및 [AutoScaleRun.Timestamp 속성](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)을 제공합니다.
- [풀에 대한 정보 가져오기](https://msdn.microsoft.com/library/dn820165.aspx) – 이 REST API는 풀에 대한 정보를 반환하며 이 정보에는 최신 자동 크기 조정 실행이 포함되어 있습니다.

## 다음 단계

1.	응용 프로그램의 효율성을 완전하게 평가하려면 계산 노드에 액세스해야 할 수 있습니다. 원격 액세스를 활용하려면 액세스하려는 계산 노드에 사용자 계정이 추가되어야 하며 해당 노드에서 RDP 파일이 검색되어야 합니다. 사용자 계정은 다음 방법 중 하나를 사용하여 추가합니다.

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) – 이 cmdlet은 풀 이름, 계산 노드 이름, 계정 이름 및 암호를 매개 변수로 사용합니다.
	- [IVM.CreateUser 메서드](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx) – 이 .NET 메서드는 계산 노드에 대해 계정 이름 및 암호를 설정할 수 있는 [IUser 인터페이스](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx) 인스턴스를 만듭니다.
	- [노드에 사용자 계정 추가](https://msdn.microsoft.com/library/dn820137.aspx) – 풀 이름 및 계산 노드는 URI에 지정되고 계정 이름 및 암호는 이 REST API의 요청 본문에 있는 노드로 전송됩니다.

		RDP 파일 가져오기:

	- [IVM.GetRDPFile 메서드](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx) – 이 .NET 메서드에는 만들 RDP 파일 이름이 필요합니다.
	- [노드에서 원격 데스크톱 프로토콜 파일 가져오기](https://msdn.microsoft.com/library/dn820120.aspx) – 이 REST API에는 풀의 이름과 계산 노드의 이름이 필요합니다. 응답에는 RDP 파일 콘텐츠가 포함되어 있습니다.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) – 이 cmdlet은 지정된 계산 노드에서 RDP 파일을 가져와 지정된 파일 위치 또는 스트림에 저장합니다.
2.	일부 응용 프로그램은 많은 양의 데이터를 생성하여 처리하기가 어려울 수 있습니다. 이 문제를 해결하는 한 가지 방법은 [효율적인 목록 쿼리](batch-efficient-list-queries.md)를 사용하는 것입니다.

<!---HONumber=August15_HO7-->