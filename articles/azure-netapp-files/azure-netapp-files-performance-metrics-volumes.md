---
title: 벤치 마크 테스트 볼륨 성능 및 NetApp Azure Files를 사용 하 여 메트릭을 | Microsoft Docs
description: 벤치 마크 테스트 볼륨의 성능 및 NetApp Azure Files를 사용 하 여 메트릭을 대 한 권장 사항을 제공 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478811"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Azure NetApp Files 파일을 사용하는 볼륨 성능 및 메트릭에 대한 벤치마크 테스트

이 문서에서는 벤치 마크 테스트 볼륨의 성능 및 NetApp Azure Files를 사용 하 여 메트릭을 대 한 권장 사항을 제공 합니다.

## <a name="overview"></a>개요

NetApp Azure Files 볼륨의 성능 특성을 이해 하려면 오픈 소스 도구를 사용할 수 있습니다 [FIO](https://github.com/axboe/fio) 일련의 다양 한 워크 로드를 시뮬레이션 하기 위해 벤치 마크를 실행 합니다. FIO는 Linux 모두에 설치할 수 있습니다 하 고 Windows 기반 운영 체제입니다.  볼륨에 대 한 IOPS 및 처리량 모두의 스냅샷을 신속 가져오려고는 탁월한 도구입니다.

### <a name="vm-instance-sizing"></a>VM 인스턴스 크기 조정

최상의 결과 테스트 하는 데 적절 하 게 크기가 지정 된 가상 머신 (VM) 인스턴스를 사용 하 고 있는지 확인 합니다. 다음 예제에서는 Standard_D32s_v3 인스턴스를 사용 합니다. VM 인스턴스 크기에 대 한 자세한 내용은 참조 하세요. [Azure에서 가상 컴퓨터 크기에 대 한 Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Windows 기반 Vm 용 하 고 [Azure에서 Linux 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Linux 기반 Vm에 대 한 합니다.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp 파일에 대 한 볼륨 크기 조정

예상 되는 성능 수준에 대 한 올바른 서비스 수준 및 볼륨 할당량 크기를 선택 하 고 있는지 확인 합니다. 참조 [NetApp Azure Files에 대 한 서비스 수준](azure-netapp-files-service-levels.md) 자세한 내용은 합니다.

### <a name="virtual-network-vnet-recommendations"></a>가상 네트워크 (VNet) 권장 사항

벤치 마크 Azure NetApp 파일과 동일한 VNet에서 테스트를 수행 해야 합니다. 아래 예제에는 권장 사항을 보여 줍니다.

![VNet 권장 사항](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO 설치

FIO는 Linux 및 Windows에 대 한 이진 형식으로 제공 됩니다. 이진 패키지 섹션 [FIO](https://github.com/axboe/fio) 선택한 플랫폼에 설치 합니다.

## <a name="fio-examples-for-iops"></a>IOPS에 대 한 FIO 예제 

이 단원의 FIO 예제에서는 다음 설정을 사용합니다.
* VM 인스턴스 크기: D32s_v3
* 용량 풀 서비스 수준 및 크기: 프리미엄 50 / TiB
* 볼륨 할당량 크기: 48 TiB

다음 예제에서는 임의 FIO 읽기 및 쓰기를 보여 줍니다.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k 크기 100% 임의 읽기를 차단합니다.

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>출력 68 k 읽기 IOPS 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k 크기 100% 임의 쓰기를 차단합니다.

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>출력 73 k 쓰기 IOPS 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>대역폭에 대 한 FIO 예제

이 섹션에서는 쇼 순차적 FIO의에서 예제를 읽고 씁니다.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k 크기 100% 순차 읽기를 차단합니다.

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>출력 11.8 표시 Gbit/s 처리량

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k 크기 100% 순차 쓰기를 차단합니다.

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>출력 12.2 Gbit/s 처리량 표시

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>볼륨 메트릭

Azure NetApp 파일 성능 데이터를 Azure Monitor 카운터를 통해 사용할 수 있습니다. 이러한 카운터는 Azure portal 및 REST API GET 요청을 통해 사용할 수 있습니다. 

다음 정보를 기록 데이터를 볼 수 있습니다.
* 평균 읽기 대기 시간 
* 평균 쓰기 대기 시간 
* 읽기 IOPS (평균)
* 쓰기 IOPS (평균)
* 볼륨 논리적 크기 (평균)
* 볼륨 스냅숏 크기 (평균)

### <a name="using-azure-monitor"></a>Azure Monitor 사용 

아래와 같이 Azure NetApp 파일 카운터 당 볼륨 별로 메트릭 페이지에서 액세스할 수 있습니다.

![Azure Monitor 메트릭](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

메트릭 페이지 것인지, NetApp, 필터링 및 관심 볼륨 카운터를 지정 하 여 Azure NetApp 파일에 대 한 Azure Monitor에서 대시보드를 만들 수도 있습니다. 

![Azure Monitor 대시보드](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API 액세스

REST API 호출을 사용 하 여 Azure NetApp 파일 카운터에 액세스할 수 있습니다. 참조 [지원 되는 Azure Monitor 메트릭: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) 용량 풀 및 볼륨에 대 한 카운터에 대 한 합니다.

다음 예제에서는 논리 볼륨 크기를 보기 위한 가져오기 URL을 보여 줍니다.

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp 파일에 대 한 성능 벤치 마크](azure-netapp-files-performance-benchmarks.md)