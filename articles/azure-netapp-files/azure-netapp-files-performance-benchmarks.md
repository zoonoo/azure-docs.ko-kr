---
title: Azure NetApp 파일에 대 한 성능 벤치 마크 | Microsoft Docs
description: 볼륨 수준에서 Azure NetApp 파일에 대 한 성능 벤치 마크 테스트의 결과 설명합니다.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449497"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Azure NetApp Files의 성능 벤치마크

이 문서에서는 볼륨 수준에서 Azure NetApp 파일에 대 한 성능 벤치 마크 테스트의 결과 설명 합니다. 

## <a name="sample-application-used-for-the-tests"></a>테스트에 사용 되는 샘플 응용 프로그램

NetApp Azure Files를 사용 하 여 샘플 응용 프로그램을 사용 하 여 성능 테스트 실행 되지 않았습니다. 응용 프로그램에는 다음과 같은 특징이 있습니다. 

* Linux 기반 응용 프로그램을 클라우드에 대해 구축
* 필요에 따라 계산 능력을 높이기 위해 추가 가상 머신 (Vm)를 사용 하 여 선형으로 확장할 수 있습니다.
* Data lake의 신속한 접근성 필요
* 에 임의 및 경우에 따라 순차 I/O 패턴 
    * 패턴을 임의 많은 양의 I/O에 대 한 짧은 대기 시간을 필요합니다. 
    * 순차 패턴에는 많은 양의 대역폭이 필요합니다. 

## <a name="about-the-workload-generator"></a>워크 로드 생성기에 대 한

결과는 Vdbench 요약 파일에서 제공 됩니다. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) 저장소 성능을 유효성 검사에 대 한 디스크 I/O 워크 로드를 생성 하는 명령줄 유틸리티입니다. 클라이언트-서버 사용 되는 구성은 확장성입니다.  단일 혼합된 마스터/클라이언트 및 14 전담된 클라이언트 Vm이 포함 됩니다.

## <a name="about-the-tests"></a>테스트에 대 한

테스트 응답 시간과 샘플 응용 프로그램을 가질 수 있는 제한 한도까지 해당 곡선을 식별 하도록 설계 되었습니다.  

다음 테스트를 실행 합니다. 

* 100 %8-3:n2}kib/ 임의 읽기
* 100 %8 3:n2}kib/ 임의 쓰기
* 100 %64-3:n2}kib/ 순차적 읽기
* 100 %64 3:n2}kib/ 순차 쓰기
* 50 %64-3:n2}kib/ 순차 읽기, 50 %64 3:n2}kib/ 순차 쓰기
* 50 %8-3:n2}kib/ 임의 읽기, 50 %8 3:n2}kib/ 임의 쓰기

## <a name="bandwidth"></a>대역폭

Azure NetApp 파일 제공 여러 [서비스 수준](azure-netapp-files-service-levels.md)합니다. 각 서비스 수준 (볼륨 할당량) 프로 비전 된 용량 TiB 당 대역폭의 다양 한 크기를 제공합니다. 대역폭 제한은 볼륨에 대 한 서비스 수준 및 볼륨 할당량의 조합을 기반으로 하는 프로 비전 됩니다. 대역폭 제한은 실현할 수 있는 처리량의 실제 크기를 결정 하는 데 하나의 비율입니다.  

현재, 4, 500 MiB는 테스트에서 단일 볼륨에 대 한 작업에 의해 달성 되었는지는 최대 처리량입니다.  프리미엄 서비스 수준으로 70.31 TiB의 볼륨 할당량을 아래 계산 당이 처리량이를 실현 하는 데 충분 한 대역폭을 준비 합니다. 

![대역폭 수식](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![할당량 및 서비스 수준](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>처리량 집약적 워크 로드

처리량 테스트 Vdbench 12xD32s V3 storage Vm의 조합을 사용 합니다. 테스트의 샘플 볼륨 다음 처리량 수치가 지원 됩니다.

![처리량 테스트](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>O 집약적 워크 로드

I/O 테스트 Vdbench 12xD32s V3 storage Vm의 조합을 사용 합니다. 다음 I/O 번호를 달성 하는 테스트의 샘플 볼륨:

![I/O 테스트](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>대기 시간

테스트 Vm과 Azure NetApp 파일 볼륨 간의 거리 I/O 성능에 영향을 줍니다.  아래 차트에는 서로 다른 두 개의 Vm에 대 한 대기 시간 응답 곡선 및 IOPS를 비교합니다.  Vm 집합이 하나 Azure NetApp 파일 거의 이며 다른 집합은 제거 합니다.  추가 Vm 집합에 대 한 대기 시간이 증가 지정 된 병렬 처리 수준에서 수행 되는 IOPS의 크기에 영향을 줍니다.  그럼에도 불구 하 고 아래 그림과 같이 볼륨에 대해 읽기 300,000 개의 IOPS를 초과할 수 있습니다. 

![대기 시간 연구](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>요약

대기 시간에 민감한 워크 로드 (데이터베이스)에 1 밀리초 응답 시간이 있을 수 있습니다. 트랜잭션 성능 단일 볼륨에 대 한 300 k 개 IOPS를 수 있습니다.

처리량에 민감한 응용 프로그램 (스트리밍 및 이미징) 4.5GiB 수 / 초 처리량입니다.

## <a name="example-scripts"></a>예제 스크립트

다음 예제 스크립트는 데모 목적 으로만 합니다.  프로덕션 용도로 사용할 수는 없습니다.  

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
