---
title: Azure Site Recovery를 사용하여 보조 사이트에 VMM 클라우드의 Hyper-V VM 복제에 대한 테스트 결과 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 보조 사이트에 VMM 클라우드의 Hyper-V VM 복제에 대한 성능 테스트에 대해 정보를 제공합니다.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377217"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>보조 사이트로 Hyper-V 복제에 대한 테스트 결과


이 문서에서는 보조 데이터 센터에 System Center VMM(Virtual Machine Manager) 클라우드의 Hyper-V VM을 복제하는 경우 성능 테스트의 결과를 제공합니다.

## <a name="test-goals"></a>테스트 목표

테스트의 목표는 안정적인 상태로 복제하는 동안 Site Recovery가 수행하는 방법에 대해 검사하는 것입니다.

- 안정적 상태 복제는 VM이 초기 복제를 완료하고 델타 변경 내용을 동기화하는 경우에 발생합니다.
- 안정적 상태를 사용하여 성능을 측정하는 것은 예상치 못한 중단이 발생하지 않는 한 대부분의 VM에서의 상태가 안정적 상태이기 때문에 중요합니다.
- 테스트 배포는 각 사이트에서 VMM 서버를 사용하는 두 개의 온-프레미스 사이트로 구성됩니다. 이 테스트 배포는 기본 사이트 역할을 하는 본사 및 보조 또는 복구 사이트 역할을 하는 지점을 포함하여 일반적으로 본사/지점 배포를 대표합니다.

## <a name="what-we-did"></a>수행한 내용

테스트 통과에서 한 내용은 다음과 같습니다.

1. VMM 템플릿을 사용하여 VM을 만들었습니다.
2. VM을 시작하고 12시간 동안 기준 성능 메트릭을 캡처했습니다.
3. 기본 및 복구 VMM 서버에 클라우드를 생성했습니다.
4. 원본 및 복구 클라우드 간의 매핑을 비롯하여 Site Recovery에서 복제를 구성했습니다.
5. VM에 대한 보호를 설정하고 초기 복제를 완료하도록 허용했습니다.
6. 시스템에 안정화될 때까지 대기.
7. 12시간 동안 예상된 복제 상태로 모든 VM이 유지되는 12시간 동안 성능 메트릭을 캡처했습니다.
8. 기본 성능 메트릭과 복제 성능 메트릭 간의 델타를 측정했습니다.


## <a name="primary-server-performance"></a>기본 서버 성능

* Hyper-V 복제본(Site Recovery에 사용됨)은 주 서버의 최소 저장소 오버헤드로 로그 파일에 변경 내용을 비동기적으로 추적합니다.
* Hyper-V 복제본은 추적을 위해 IOPS 오버헤드를 최소화하도록 자체 관리되는 메모리 캐시를 활용합니다. 메모리에서 VHDX에 쓰기를 저장하고 복구 사이트로 로그를 보내기 전에 해당 로그 파일에 이를 플러시합니다. 쓰기가 미리 결정된 제한에 도달하는 경우에도 디스크 플러시가 발생합니다.
* 아래 Graph는 복제에 대한 안정적인 상태 IOPS 오버헤드를 보여줍니다. 복제로 인한 IOPS 오버헤드는 약 5%로 상당히 낮음을 볼 수 있습니다.

  ![기본 결과](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V 복제본은 디스크 성능을 최적화하기 위해 기본 서버의 메모리를 사용합니다. 다음 Graph에서 볼 수 있듯이 기본 클러스터에서 모든 서버의 메모리 오버헤드는 한계입니다. 표시되는 메모리 오버헤드는 Hyper-V 서버에서 설치된 총 메모리에 비해 복제를 통해 사용되는 메모리의 비율입니다.

![기본 결과](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V 복제본에는 최소 CPU 오버헤드가 있습니다. Graph에서 볼 수 있듯이 복제 오버헤드는 2~3% 범위 이내입니다.

![기본 결과](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>보조 서버 성능

Hyper-V 복제본은 복구 서버에 적은 양의 메모리를 사용하여 저장소 작업의 수를 최적화합니다. Graph는 복구 서버에서 메모리 사용량을 요약합니다. 표시되는 메모리 오버헤드는 Hyper-V 서버에서 설치된 총 메모리에 비해 복제를 통해 사용되는 메모리의 비율입니다.

![보조 결과](./media/hyper-v-vmm-performance-results/IC744916.png)

복구 사이트에서 I/O 작업 양은 기본 사이트에서 쓰기 작업의 수에 따라 다릅니다. 기본 사이트에서 총 I/O 작업 및 쓰기 작업과 비교하여 복구 사이트에서 총 I/O 작업을 살펴 보겠습니다. Graph는 복구 사이트에서 총 IOPS를 보여줍니다.

* 기본에서 쓰기 IOPS의 약 1.5배입니다.
* 기본 사이트에서 총 IOPS의 약 37%입니다.

![보조 결과](./media/hyper-v-vmm-performance-results/IC744917.png)

![보조 결과](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>네트워크 사용률에 미치는 영향

기존 대역폭은 초당 5Gb인데 반해 기본 및 보조 노드(압축 사용 시) 간에 네트워크 대역폭은 초당 평균 275Mb가 사용됩니다.

![결과 네트워크 사용률](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>VM 성능에 미치는 영향

중요한 고려 사항은 가상 머신에서 실행되는 프로덕션 워크로드에서 복제에 미치는 영향입니다. 기본 사이트가 복제에 대해 적절하게 프로비전되면 워크로드에 아무런 영향이 없습니다. Hyper-V 복제본의 간단한 추적 메커니즘을 사용하면 가상 머신에서 실행되는 워크로드가 안정적 상태 복제 동안 영향을 받지 않습니다. 이 내용은 다음 Graph에서 설명됩니다.

이 Graph는 복제를 사용하기 전과 후에 다른 워크로드를 실행하는 가상 머신을 통해 수행되는 IOPS를 보여 줍니다. 둘 사이에 차이가 없음을 확인할 수 있습니다.

![복제본 효과 결과](./media/hyper-v-vmm-performance-results/IC744920.png)

다음 Graph는 복제를 사용하기 전과 후에 다른 워크로드를 실행하는 가상 머신의 처리량을 보여 줍니다. 해당 복제에 심각한 영향이 없음을 확인할 수 있습니다.

![복제본 효과에 대한 결과](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>결론

결과는 Hyper-V 복제본과 관련하여 Site Recovery가 대량 클러스터에 대해 최소한의 오버헤드로 확장 가능함을 명백하게 보여 줍니다. Site Recovery는 간단한 배포, 복제, 관리 및 모니터링을 제공합니다. Hyper-V 복제본은 성공적인 복제 크기 조정에 대해 필요한 인프라를 제공합니다. 

## <a name="test-environment-details"></a>테스트 환경 세부 정보

### <a name="primary-site"></a>기본 사이트

* 기본 사이트에는 470개의 가상 머신을 실행하는 5개의 Hyper-V 서버를 포함하는 클러스터가 있습니다.
* VM은 다른 작업을 수행하며 모두 Site Recovery 보호가 설정되어 있습니다.
* 클러스터 노드에 대한 저장소는 iSCSI SAN을 통해 제공됩니다. 모델 – Hitachi HUS130.
* 각 클러스터는 서버의 각각 1Gbps인 네트워크 카드(NIC)가 4개 있습니다.
* 네트워크 카드 중 2개는 iSCSI 프라이빗 네트워크에 연결되며 다른 2개는 외부 엔터프라이즈 네트워크에 연결됩니다. 외부 네트워크 중 하나는 클러스터 통신 전용으로 예약됩니다.

![기본 하드웨어 요구 사항](./media/hyper-v-vmm-performance-results/IC744922.png)

| 서버 | RAM | Model | 프로세서 | 프로세서 수 | NIC | 소프트웨어 |
| --- | --- | --- | --- | --- | --- | --- |
| 클러스터의 Hyper-V 서버: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25의 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |IGbps 4개 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM 서버 |2 | | |2 |1Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>보조 사이트

* 보조 사이트는 6개의 노드 장애 조치 클러스터가 있습니다.
* 클러스터 노드에 대한 저장소는 iSCSI SAN을 통해 제공됩니다. 모델 – Hitachi HUS130.

![기본 하드웨어 사양](./media/hyper-v-vmm-performance-results/IC744923.png)

| 서버 | RAM | Model | 프로세서 | 프로세서 수 | NIC | 소프트웨어 |
| --- | --- | --- | --- | --- | --- | --- |
| 클러스터의 Hyper-V 서버: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |IGbps 4개 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM 서버 |2 | | |2 |1Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>서버 작업

* 테스트를 위해 엔터프라이즈 고객 시나리오에서 일반적으로 사용되는 작업을 선택했습니다.
* 시뮬레이션을 위해 표에 요약된 워크로드 특성과 함께 [IOMeter](http://www.iometer.org) 를 사용합니다.
* 모든 IOMeter 프로파일은 작업에 대한 최악의 쓰기 패턴을 시뮬레이션하기 위해 임의의 바이트를 쓰도록 설정됩니다.

| 워크로드 | I/O 크기(KB) | 액세스 % | 쓰기 % | 미해결 I/O | I/O 패턴 |
| --- | --- | --- | --- | --- | --- |
| 파일 서버 |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |모두 100% 임의 |
| SQL Server (볼륨 1)<br />SQL Server (볼륨 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% 임의<br />100% 순차 |
| Exchange |32 |100% |67% |8 |100% 임의 |
| 워크스테이션/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |둘 다 100% 임의 |
| 웹 파일 서버 |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |모두 75% 임의 |

### <a name="vm-configuration"></a>VM 구성

* 기본 클러스터의 470개의 VM입니다.
* VHDX 디스크를 사용하는 모든 VM입니다.
* 표에 요약된 작업을 실행하는 VM입니다. 모두 VMM 템플릿으로 생성되었습니다.

| 작업 | VM 수 | 최소 RAM(GB) | 최대 RAM(GB) | VM당 논리 디스크 크기(GB) | 최대 IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| 파일 서버 |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| 웹 서버 |149 |.5 |1 |80 |6 |
| 전체 |470 | | |96.83TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery 설정

* Site Recovery는 온-프레미스 간 보호로 구성됨
* VMM 서버에는 Hyper-V 클러스터 서버 및 해당 VM을 비롯하여 4개의 클라우드로 구성됩니다.

| 기본 VMM 클라우드 | 보호된 VM | 복제 빈도 | 추가 복구 지점 |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15분 |없음 |
| PrimaryCloudRpo30s |47 |30초 |없음 |
| PrimaryCloudRpo30sArp1 |47 |30초 |1 |
| PrimaryCloudRpo5m |235 |5분 |없음 |

### <a name="performance-metrics"></a>성능 메트릭

이 표는 배포 시 측정된 성능 메트릭 및 카운터를 요약합니다.

| 메트릭 | 카운터 |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` 프로세서 시간ation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| 사용 가능한 메모리 |\Memory\사용 가능한 MB |
| IOPS |\PhysicalDisk(_Total)\디스크 전송/초 |
| VM 읽기(IOPS) 작업/초 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM 쓰기(IOPS) 작업/초 |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM 읽기 처리량 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM 쓰기 처리량 |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## <a name="next-steps"></a>다음 단계

[복제 설정](hyper-v-vmm-disaster-recovery.md)
