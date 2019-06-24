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
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870884"
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

* 100% 임의 8 3:n2}kib/ 읽기
* 100 %8 3:n2}kib/ 임의 쓰기
* 100 %64kib 순차적 읽기
* 100 %64 3:n2}kib/ 순차 쓰기
* 50 %64 3:n2}kib/ 순차적 읽기, 50 %64 3:n2}kib/ 순차 쓰기
* 8 3:n2}kib/ 임의 읽기는 50%, 50 %8 3:n2}kib/ 임의 쓰기

## <a name="bandwidth"></a>대역폭

Azure NetApp 파일 제공 여러 [서비스 수준](azure-netapp-files-service-levels.md)합니다. 각 서비스 수준 (볼륨 할당량) 프로 비전 된 용량 TiB 당 대역폭의 다양 한 크기를 제공합니다. 대역폭 제한은 볼륨에 대 한 서비스 수준 및 볼륨 할당량의 조합을 기반으로 하는 프로 비전 됩니다. 참고 대역폭 제한은 실현할 수 있는 처리량의 실제 크기를 결정 하는 데 하나의 요소입니다.  

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

테스트 Vm과 Azure NetApp 파일 볼륨 간의 거리 I/O 성능에 영향을 줍니다.  아래 차트에는 서로 다른 두 개의 Vm에 대 한 대기 시간 응답 곡선 및 IOPS를 비교합니다.  Vm 집합이 하나 Azure NetApp 파일 거의 이며 다른 집합은 제거 합니다.  참고 추가 Vm 집합에 대 한 대기 시간이 증가 지정 된 병렬 처리 수준에서 수행 되는 IOPS의 양에 영향을 줍니다.  그럼에도 불구 하 고 아래 그림과 같이 볼륨에 대해 읽기 300,000 개의 IOPS를 초과할 수 있습니다. 

![대기 시간 연구](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>요약

대기 시간에 민감한 워크 로드 (데이터베이스)에 1 밀리초 응답 시간이 있을 수 있습니다. 트랜잭션 성능 단일 볼륨에 대 한 300 k 개 IOPS를 수 있습니다.

처리량에 민감한 응용 프로그램 (스트리밍 및 이미징) 4.5GiB 수 / 초 처리량입니다.
