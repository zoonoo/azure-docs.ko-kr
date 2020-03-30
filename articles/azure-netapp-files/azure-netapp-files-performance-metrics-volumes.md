---
title: 권장 성능 벤치마크 테스트 - Azure NetApp 파일
description: Azure NetApp 파일을 사용하여 볼륨 성능 및 메트릭에 대한 벤치마크 테스트 권장 사항에 대해 알아봅니다.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551525"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 벤치마크 테스트 추천 사항

이 문서에서는 Azure NetApp 파일을 사용하는 볼륨 성능 및 메트릭에 대한 벤치마크 테스트 권장 사항을 제공합니다.

## <a name="overview"></a>개요

Azure NetApp 파일 볼륨의 성능 특성을 이해하려면 오픈 소스 도구 [FIO를](https://github.com/axboe/fio) 사용하여 일련의 벤치마크를 실행하여 다양한 워크로드를 시뮬레이션할 수 있습니다. FIO는 리눅스와 윈도우 기반 운영 체제 모두에 설치할 수 있습니다.  볼륨에 대한 IOPS 및 처리량의 빠른 스냅샷을 얻을 수 있는 훌륭한 도구입니다.

### <a name="vm-instance-sizing"></a>VM 인스턴스 크기 조정

최상의 결과를 얻으려면 테스트를 수행하기에 적절한 크기의 VM(가상 컴퓨터) 인스턴스를 사용하고 있는지 확인합니다. 다음 예제는 Standard_D32s_v3 인스턴스를 사용합니다. VM 인스턴스 크기에 대한 자세한 내용은 Windows 기반 [VM용 Azure의 Windows 가상 컴퓨터](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 크기 및 Linux 기반 VM용 [Azure의 Linux 가상 컴퓨터용 크기를](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조하십시오.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp 파일 볼륨 크기 조정

예상 성능 수준에 대해 올바른 서비스 수준 및 볼륨 할당량 크기를 선택해야 합니다. 자세한 내용은 [Azure NetApp 파일에 대한 서비스 수준을](azure-netapp-files-service-levels.md) 참조하십시오.

### <a name="virtual-network-vnet-recommendations"></a>가상 네트워크(VNet) 권장 사항

Azure NetApp 파일과 동일한 VNet에서 벤치마크 테스트를 수행해야 합니다. 아래 예제에서는 권장 사항을 보여 줍니다.

![VNet 권장 사항](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO 설치

FIO는 리눅스와 윈도우 모두 바이너리 형식으로 사용할 수 있습니다. [FIO의](https://github.com/axboe/fio) 바이너리 패키지 섹션을 따라 선택한 플랫폼에 설치하십시오.

## <a name="fio-examples-for-iops"></a>IOPS에 대한 FIO 예제 

이 섹션의 FIO 예제에서는 다음 설정을 사용합니다.
* VM 인스턴스 크기: D32s_v3
* 용량 풀 서비스 수준 및 크기: 프리미엄 / 50 TiB
* 볼륨 할당량 크기: 48 TiB

다음 예제는 FIO 임의 읽기 및 쓰기를 보여 준다.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO : 8k 블록 크기 100 % 무작위 읽기

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>출력 : 68k 읽기 IOPS 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO : 8k 블록 크기 100 % 무작위 쓰기

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>출력 : 73k 쓰기 IOPS 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>대역폭에 대한 FIO 예제

이 섹션의 예제는 FIO 순차읽기 및 쓰기를 보여 준다.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO : 64k 블록 크기 100 % 순차 읽기

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>출력: 11.8Gbit/s 처리량 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO : 64k 블록 크기 100 % 순차 적글

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>출력: 12.2Gbit/s 처리량 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>볼륨 메트릭

Azure NetApp 파일 성능 데이터는 Azure 모니터 카운터를 통해 사용할 수 있습니다. 카운터는 Azure 포털 및 REST API GET 요청을 통해 사용할 수 있습니다. 

다음 정보에 대한 기록 데이터를 볼 수 있습니다.
* 평균 읽기 대기 시간 
* 평균 쓰기 대기 시간 
* IOPS 읽기(평균)
* IOPS 쓰기(평균)
* 볼륨 논리 크기(평균)
* 볼륨 스냅샷 크기(평균)

### <a name="using-azure-monitor"></a>Azure Monitor 사용 

아래와 같이 메트릭 페이지에서 볼륨별로 Azure NetApp 파일 카운터에 액세스할 수 있습니다.

![Azure 모니터 메트릭](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

메트릭 페이지로 이동, NetApp에 대 한 필터링 및 관심 있는 볼륨 카운터를 지정 하 여 Azure NetApp 파일에 대 한 Azure 모니터에서 대시보드를 만들 수 있습니다. 

![Azure Monitor 대시보드](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure 모니터 API 액세스

REST API 호출을 사용하여 Azure NetApp 파일 카운터에 액세스할 수 있습니다. [Azure 모니터를 사용 하 고 지원 되는 메트릭을 참조: Microsoft.NetApp/netAppAccounts/용량 풀/볼륨](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) 용량 풀 및 볼륨에 대 한 카운터에 대 한 볼륨입니다.

다음 예제에서는 논리 볼륨 크기를 보기 위한 GET URL을 보여 주며 있습니다.

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp Files의 성능 벤치마크](azure-netapp-files-performance-benchmarks.md)