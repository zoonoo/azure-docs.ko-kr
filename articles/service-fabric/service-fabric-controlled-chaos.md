---
title: "Service Fabric 클러스터에서 비정상 상황 유도 | Microsoft Docs"
description: "오류 삽입 및 클러스터 분석 서비스 API를 사용하여 클러스터의 비정상 상황을 관리합니다."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2017
ms.author: motanv
translationtype: Human Translation
ms.sourcegitcommit: 1a9bec270650223cd40b3b60e5bc9fc7e212a207
ms.openlocfilehash: ea8f76d146bd630cc8cb0a417d8c090b656150a4


---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Service Fabric 클러스터에서 제어되는 비정상 상황 유도
클라우드 인프라와 같은 대규모 분산 시스템은 기본적으로 안정적이지 않습니다. Azure 서비스 패브릭을 사용하면 개발자들은 불안정한 인프라 위에 안정적인 서비스를 작성할 수 있습니다. 강력한 서비스를 작성하기 위해 개발자는 불안정한 인프라와 같은 결함을 유도하여 서비스의 안정성을 테스트할 수 있어야 합니다.

오류 삽입 및 클러스터 분석 서비스(오류 분석 서비스라고도 함)는 개발자에게 서비스를 테스트할 수 있도록 오류 작업을 유도하는 기능을 제공합니다. 그러나 특정 대상을 통한 오류 시뮬레이션으로는 한계가 있습니다. 테스트를 좀 더 수행하려면 비정상 상황을 사용할 수 있습니다.

비정상 상황은 장기간에 걸쳐 클러스터 전체에서 정상적 및 비정상적 인터리브 오류를 지속적으로 시뮬레이션합니다. 오류의 비율 및 종류로 비정상 상황을 구성하면 C# API 또는 PowerShell을 통해 시작 또는 중지되어 클러스터 및 서비스에서 오류를 생성할 수 있습니다.

비정상 상황이 실행되는 동안 현재 실행의 상태를 캡처하는 다양한 이벤트가 생성됩니다. 예를 들어 ExecutingFaultsEvent에는 해당 반복에서 실행되는 모든 오류가 포함됩니다. ValidationFailedEvent에는 클러스터 유효성 검사 중에 발견된 오류의 세부 정보가 포함됩니다. GetChaosReportAsync API를 호출하여 비정상 상황 실행 보고서를 가져올 수 있습니다.

## <a name="faults-induced-in-chaos"></a>비정상 상황에서 유도되는 오류
비정상 상황에서는 전체 Service Fabric 클러스터에서 오류가 생성되며 수개월 또는 수년에 걸쳐 확인된 오류가 몇 시간으로 압축됩니다. 인터리브 오류를 높은 오류 비율과 결합하면 놓치기 쉬운 특이한 사례를 발견할 수 있습니다. 이러한 비정상 상황에 대처하는 연습을 통해 서비스 코드 품질을 대폭 개선할 수 있습니다.

비정상 상황은 다음 범주에서 오류를 유도합니다.

* 노드 다시 시작
* 배포된 코드 패키지 다시 시작
* 복제본 제거
* 복제본 다시 시작
* 주 복제본 이동(구성 가능)
* 보조 복제본 이동(구성 가능)

비정상 상황에서는 지정된 기간 동안 오류 및 클러스터 유효성 검사가 여러 차례 반복해서 실행됩니다. 클러스터가 안정화되고 유효성 검사가 성공하는 데 걸리는 시간을 구성할 수 있습니다. 클러스터 유효성 검사에 오류가 있으면 비정상 상황이 생성되며 UTC 타임스탬프 및 오류 세부 정보로 ValidationFailedEvent가 유지됩니다.

예를 들어 비정상 상황 인스턴스가 1시간 동안 실행되고 최대 세 가지 오류가 동시에 발생하도록 설정되었다고 가정하겠습니다. 비정상 상황은 세 개의 오류를 유도한 다음 클러스터 상태의 유효성을 검사합니다. 또한 StopChaosAsync API를 통해 명시적으로 서비스가 중지되거나 1시간이 경과할 때까지 이전 단계를 반복합니다. 구성된 시간 내에 클러스터가 안정화되지 못한 경우처럼 반복 과정 중에 클러스터가 비정상 상태가 되면 ValidationFailedEvent가 생성됩니다 이 이벤트는 무언가 잘못되었으며 자세한 조사가 필요할 수 있음을 나타냅니다.

현재 형태에서는 비정상 상황은 안전한 오류만 유도합니다. 즉, 외부 오류가 없으면 쿼럼 손실 또는 데이터 손실이 발생하지 않습니다.

## <a name="important-configuration-options"></a>중요 구성 옵션
* **TimeToRun**: 성공적으로 완료될 때까지 비정상 상황이 실행되는 총 시간입니다. StopChaos API를 통해 TimeToRun 기간 동안 실행되기 전에 비정상 상황을 중지할 수 있습니다.
* **MaxClusterStabilizationTimeout**: 클러스터가 정상 상태가 될 때까지 기다리는 최대 시간입니다. 이 시간이 지나면 다시 검사합니다. 이를 통해 복구되는 동안 클러스터의 부하를 줄일 수는 있습니다. 수행되는 검사는 다음과 같습니다.
  * 클러스터 상태가 정상인 경우
  * 서비스 상태가 정상인 경우
  * 서비스 파티션에 대해 목표 복제본 세트 크기가 달성된 경우
  * InBuild 복사본이 없음
* **MaxConcurrentFaults**: 각 반복에서 유도되는 동시 오류의 최대 수입니다. 숫자가 클수록 비정상 상황이 더 적극적으로 실행됩니다. 따라서 장애 조치(failover)와 전환의 조합이 더 복잡해집니다. 외부 오류가 없으면 이 숫자를 얼마나 높게 구성하든 쿼럼 또는 데이터 손실이 없습니다.
* **EnableMoveReplicaFaults**: 주 복제본 또는 보조 복제본을 이동하게 만드는 오류를 설정하거나 해제합니다. 이러한 오류는 기본적으로 해제됩니다.
* **WaitTimeBetweenIterations**: 반복 사이의 대기 시간입니다(예: 오류 및 그에 해당하는 유효성 검사 후).
* **WaitTimeBetweenFaults**: 반복에서 두 개의 연속 오류 사이에 대기하는 시간입니다.

## <a name="how-to-run-chaos"></a>비정상 상황을 실행하는 방법
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```



<!--HONumber=Nov16_HO3-->


