---
title: Azure NetApp 파일에 대한 성능 벤치마크 테스트 결과 | 마이크로 소프트 문서
description: 볼륨 수준에서 Azure NetApp 파일에 대한 성능 벤치마크 테스트 결과를 설명합니다.
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881748"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Azure NetApp Files의 성능 벤치마크 테스트 결과

이 문서에서는 볼륨 수준에서 Azure NetApp 파일에 대한 성능 벤치마크 테스트 결과를 설명합니다. 

## <a name="sample-application-used-for-the-tests"></a>테스트에 사용되는 샘플 응용 프로그램

성능 테스트는 Azure NetApp 파일을 사용하는 샘플 응용 프로그램으로 실행되었습니다. 응용 프로그램에는 다음과 같은 특성이 있습니다. 

* 클라우드용으로 제작된 Linux 기반 애플리케이션
* 추가된 VM(가상 머신)으로 선형으로 확장하여 필요에 따라 컴퓨팅 성능을 높일 수 있습니다.
* 데이터 레이크의 신속한 접근성 필요
* 때때로 임의적이고 때로는 순차적 인 I / O 패턴이 있습니다. 
    * 임의 의 패턴은 많은 양의 I/O에 대해 낮은 대기 시간이 필요합니다. 
    * 순차적 패턴에는 많은 양의 대역폭이 필요합니다. 

## <a name="about-the-workload-generator"></a>워크로드 생성기 정보

결과는 Vdbench 요약 파일에서 발생합니다. [Vdbench는](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) 스토리지 성능 의 유효성을 검사하기 위해 디스크 I/O 워크로드를 생성하는 명령줄 유틸리티입니다. 사용되는 클라이언트-서버 구성은 확장 할 수 있습니다.  여기에는 단일 혼합 마스터/클라이언트 와 14개의 전용 클라이언트 VM이 포함됩니다.

## <a name="about-the-tests"></a>테스트 에 대해

이 테스트는 샘플 응용 프로그램에 있을 수 있는 제한과 한계까지 구부러지는 응답 시간을 식별하도록 설계되었습니다.  

다음 테스트가 실행되었습니다. 

* 100% 8-KiB 랜덤 읽기
* 100% 8-KiB 랜덤 쓰기
* 100% 64-KiB 순차 읽기
* 100% 64-KiB 순정 쓰기
* 50% 64-KiB 순차 읽기, 50% 64-KiB 순차 쓰기
* 50% 8-KiB 랜덤 읽기, 50% 8-KiB 랜덤 쓰기

## <a name="bandwidth"></a>대역폭

Azure NetApp 파일은 여러 [서비스 수준을](azure-netapp-files-service-levels.md)제공합니다. 각 서비스 수준은 프로비저닝된 용량(볼륨 할당량)의 TiB당 서로 다른 양의 대역폭을 제공합니다. 볼륨의 대역폭 제한은 서비스 수준과 볼륨 할당량의 조합에 따라 프로비전됩니다. 대역폭 제한은 실현될 실제 처리량을 결정하는 한 가지 요소일 뿐입니다.  

현재 4,500MiB는 테스트에서 단일 볼륨에 대해 워크로드에 의해 달성된 가장 높은 처리량입니다.  프리미엄 서비스 수준에서 70.31 TiB의 볼륨 할당량은 아래 계산에 따라 이 처리량을 실현하기에 충분한 대역폭을 프로비저닝합니다. 

![대역폭 공식](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![할당량 및 서비스 수준](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>처리량 집약적인 워크로드

처리량 테스트는 Vdbench와 12xD32s V3 스토리지 VM의 조합을 사용했습니다. 테스트의 샘플 볼륨은 다음과 같은 처리량 번호를 달성했습니다.

![처리량 테스트](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/O 집약적 워크로드

I/O 테스트는 Vdbench와 12xD32s V3 스토리지 VM의 조합을 사용했습니다. 테스트의 샘플 볼륨은 다음과 같은 I/O 숫자를 달성했습니다.

![I/O 테스트](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>대기 시간

테스트 VM과 Azure NetApp 파일 볼륨 간의 거리는 I/O 성능에 영향을 미칩니다.  아래 차트는 두 개의 서로 다른 VM 집합에 대한 IOPS와 대기 시간 응답 곡선을 비교합니다.  한 세트의 VM은 Azure NetApp 파일 근처에 있으며 다른 집합은 더 멀리 떨어져 있습니다.  추가 VM 집합에 대한 대기 시간이 증가하면 지정된 병렬 처리 수준에서 달성되는 IOPS 양에 영향을 미칩니다.  그럼에도 불구하고 볼륨에 대한 읽기는 아래 그림과 같이 300,000 IOPS를 초과할 수 있습니다. 

![대기 시간 스터디](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>요약

대기 시간에 민감한 워크로드(데이터베이스)에는 1밀리초의 응답 시간이 있을 수 있습니다. 트랜잭션 성능은 단일 볼륨에 대해 300k IOPS를 초과할 수 있습니다.

처리량에 민감한 응용 프로그램(스트리밍 및 이미징용)에는 4.5GiB/s 처리량이 있을 수 있습니다.

## <a name="example-scripts"></a>예제 스크립트

다음 예제 스크립트는 데모용으로만 사용됩니다.  생산 목적으로 사용할 수 없습니다.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
