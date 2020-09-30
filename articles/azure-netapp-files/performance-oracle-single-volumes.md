---
title: Azure NetApp Files 단일 볼륨의 Oracle 데이터베이스 성능 | Microsoft Docs
description: Oracle 데이터베이스에서 Azure NetApp Files 단일 볼륨의 성능 테스트 결과에 대해 설명 합니다.
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571416"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>단일 볼륨 Azure NetApp Files Oracle 데이터베이스 성능

이 문서에서는 클라우드의 Oracle에 대 한 다음 항목을 다룹니다. 이러한 항목은 데이터베이스 관리자, 클라우드 설계자 또는 저장소 설계자에 게 특히 관심이 있을 수 있습니다.   

* OLTP (온라인 트랜잭션 처리) 워크 로드 (대부분 임의 i/o) 또는 OLAP (온라인 분석 처리) 워크 로드 (대부분 순차 i/o)를 구동 하는 경우 성능이 어떻게 되나요?   
* 일반 Linux 커널 NFS (kNFS) 클라이언트와 Oracle 자체의 직접 NFS 클라이언트 간 성능 차이는 무엇 인가요?
* 대역폭에 관심이 있는 한, 단일 Azure NetApp Files 볼륨의 성능이 충분 한가요?

## <a name="testing-environment-and-components"></a>환경 및 구성 요소 테스트

다음 다이어그램은 테스트에 사용 되는 환경을 보여 줍니다. 일관성과 단순성을 위해 Ansible 플레이 북는 테스트 평판의 모든 요소를 배포 하는 데 사용 되었습니다.

![Oracle 테스트 환경](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>가상 머신 구성

테스트에서는 가상 컴퓨터에 대해 다음 설정을 사용 했습니다.
* 운영 체제:   
    RedHat Enterprise Linux 7.8 (wle-ora01)
* 인스턴스 유형:   
    테스트에 사용 된 두 가지 모델 D32s_v3 및 D64s_v3
* 네트워크 인터페이스 수:   
    서브넷 3에 배치 되는 1 개  
* 디스크만   
    Oracle 이진 파일 및 OS가 단일 프리미엄 디스크에 배치 되었습니다.

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files 구성
테스트에서는 다음 Azure NetApp Files 구성을 사용 했습니다.   

* 용량 풀 크기:  
    구성 된 다양 한 크기의 풀: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* 서비스 수준:  
    Ultra (128 MiB/s 대역폭의 대역폭 1 TiB 할당 된 볼륨 용량)
* 볼륨:  
    1 ~ 2 개의 볼륨 테스트가 평가 되었습니다.

### <a name="workload-generator"></a>워크 로드 생성기 

테스트를 사용 하 여 생성 된 작업은 SLOB 2.5.4.2입니다. SLOB (약간의 Oracle 벤치 마크)는 SLOB로 버퍼링 된 물리적 i/o 워크 로드를 사용 하 여 i/o 하위 시스템을 스트레스 및 테스트 하기 위해 설계 된 Oracle 공간의 잘 알려진 워크 로드 생성기입니다.  

SLOB 2.5.4.2는 플러그형 데이터베이스 (PDB)를 지원 하지 않습니다. 이와 같이 변경 내용이 및 스크립트에 추가 되어 `setup.sh` `runit.sh` PDB 지원을 추가 했습니다.  

테스트에 사용 된 SLOB 변수는 다음 섹션에 설명 되어 있습니다.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>워크 로드 80% SELECT, 20% 업데이트 | 임의 i/o – `slob.conf` 변수   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>워크 로드 100% SELECT | 순차 i/o – `slob.conf` 변수

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>데이터베이스

테스트에 사용 되는 Oracle 버전 Oracle Database Enterprise Edition 19.3.0.0입니다.

Oracle 매개 변수는 다음과 같습니다.  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

SLOB 데이터베이스에 대해 PDB를 만들었습니다.

다음 다이어그램에서는 네 개의 SLOB 사용자 스키마를 호스팅하기 위해 생성 된 600 GB 크기 (각각 20 개의 데이터 파일, 30gb)가 있는 PERFIO 라는 테이블 스페이스를 보여 줍니다. 각 사용자 스키마의 크기는 125입니다.

![Oracle 데이터베이스](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>성능 메트릭

응용 프로그램에서 경험 하는 IO 성능을 보고 하는 것이 목표입니다. 따라서이 문서의 모든 다이어그램은 자동 워크 로드 리포지토리 (AWR) 보고서를 통해 Oracle 데이터베이스에서 보고 하는 메트릭을 사용 합니다. 다이어그램에 사용 되는 메트릭은 다음과 같습니다.   

* **평균 IO 요청/초**   
    부하 프로필 섹션에서 평균 읽기 IO 요청 수/초 및 평균 쓰기 IO 요청 수/초의 합계에 해당 합니다.
* **평균 IO m b/초**   
    부하 프로필 섹션에서 평균 읽기 IO m b/초 및 평균 쓰기 IO m b/초의 합계에 해당 합니다.
* **평균 읽기 대기 시간**   
    Oracle Wait 이벤트의 평균 대기 시간 (마이크로초)에 해당 합니다.
* **스레드/스키마 수**   
    사용자 스키마 당 SLOB 스레드 수에 해당 합니다.

## <a name="performance-measurement-results"></a>성능 측정 결과  

이 섹션에서는 성능 측정 결과에 대해 설명 합니다.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS 클라이언트 및 Oracle Direct NFS

이 시나리오는 Azure VM Standard_D32s_v3 (Intel E5-2673 v4 @ 2.30 g h z)에서 실행 되 고 있습니다. 작업은 75% SELECT 및 25% 업데이트, 대부분 임의 i/o, ~ 7.5%의 데이터베이스 버퍼 적중 작업입니다. 

다음 다이어그램과 같이 Oracle DNFS 클라이언트는 일반 Linux kNFS 클라이언트 보다 최대 2.8 x 처리량을 제공 합니다.  

![Oracle Direct NFS와 비교 된 Linux kNFS 클라이언트](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

다음 다이어그램에서는 읽기 작업의 대기 시간 곡선을 보여 줍니다. 이 컨텍스트에서 kNFS 클라이언트에 대 한 병목 현상은 클라이언트와 NFS 서버 (Azure NetApp Files 볼륨) 간에 설정 된 단일 NFS TCP 소켓 연결입니다.  

![Linux kNFS 클라이언트와 Oracle Direct NFS 대기 시간 곡선 비교](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS 클라이언트는 수백 개의 TCP 소켓 연결을 만들 수 있기 때문에 더 많은 IO 요청/초를 푸시 하 여 병렬 처리를 활용할 수 있었습니다. [Azure NetApp Files 구성](#anf_config)에 설명 된 대로 할당 된 용량에 대 한 추가 TiB는 추가로 128mib/s의 대역폭을 허용 합니다. DNFS는 topped의 처리량을 1 GiB/s로 제한 합니다 .이는 8 TiB 용량 선택에 의해 적용 되는 한도입니다. 용량을 더 많이 지정 하면 더 많은 처리량이 구동 될 것입니다.

처리량은 고려 사항 중 하나일 뿐입니다. 또 다른 고려 사항은 사용자 환경에 영향을 주는 기본에 영향을 주는 대기 시간입니다. 다음 다이어그램에서 볼 수 있듯이, DNFS를 사용 하는 것 보다 더 빨리 kNFS를 사용 하 여 대기 시간이 증가 합니다. 

![Linux kNFS 클라이언트와 Oracle Direct NFS 읽기 대기 시간 비교](../media/azure-netapp-files/performance-oracle-read-latency.png)  

히스토그램은 데이터베이스 대기 시간에 대 한 뛰어난 통찰력을 제공 합니다. 다음 다이어그램에서는 가장 높은 동시성 데이터 요소 (32 스레드/스키마)에서 DNFS를 사용 하는 동안 기록 된 "db 파일 순차적 읽기"의 큐브 뷰에서 전체 뷰를 제공 합니다. 다음 다이어그램에 표시 된 것 처럼 모든 읽기 작업의 47%는 512 마이크로초와 1000 마이크로초 사이에서 적용 되 고, 모든 읽기 작업의 90%는 2 밀리초 미만의 대기 시간으로 제공 되었습니다.

![Oracle Direct NFS 히스토그램과 비교 된 Linux kNFS 클라이언트](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

결론적으로, NFS에서 Oracle 데이터베이스 인스턴스의 성능을 향상 시킬 때 DNFS가 반드시 있어야 하는 것은 분명 합니다.

### <a name="single-volume-performance-limits"></a>단일 볼륨 성능 제한

이 섹션에서는 임의 i/o 및 순차적 i/o를 사용 하는 단일 볼륨의 성능 제한에 대해 설명 합니다. 

#### <a name="random-io"></a>임의 i/o

DNFS는 8TB Azure NetApp Files 성능 할당량으로 제공 되는 것 보다 훨씬 더 많은 대역폭을 사용할 수 있습니다. Azure NetApp Files 볼륨 용량을 변경 하는 16 개의 TiB으로 늘리면 볼륨 대역폭의 양이 1024 s s/s에서 2048 MiB/s로 증가 합니다. 

다음 다이어그램에서는 80% select 및 20% 업데이트 워크 로드에 대 한 구성 및 데이터베이스 버퍼 적중률을 8%로 보여 줍니다. SLOB는 단일 볼륨을 20만 NFS i/o 요청 수/초에 대해 구동 했습니다. 각 작업이 8 KiB 크기를 고려 하 여 테스트 중인 시스템에서 ~ 20만 IO 요청/초 또는 1600 MiB/s를 제공할 수 있습니다.
 
![Oracle DNFS 처리량](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

다음 읽기 대기 시간 곡선 다이어그램은 읽기 처리량이 증가 하 고 대기 시간이 1 밀리초 줄 아래로 부드럽게 증가 하는 것을 보여 주며 ~ 1.3 밀리초의 평균 읽기 지연 시간에서 165000 평균 읽기 IO 요청 수에 대 한 곡선의 knee 적중 합니다.  이 값은 Azure 클라우드에서 거의 모든 다른 기술과의 i/o 요금 취지로에 대 한 놀라운 대기 시간 값입니다. 

![Oracle DNFS 대기 시간 곡선](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>순차 i/o  

다음 다이어그램에 표시 된 것 처럼 모든 i/o가 임의로 수행 되는 것은 아닙니다. 예를 들어, get 할 수 있는 것 처럼 많은 대역폭이 필요한 워크 로드와 같이 RMAN 백업 또는 전체 테이블 검색을 고려 합니다.  앞에서 설명한 것과 동일한 구성을 사용 하지만 볼륨이 32 TiB으로 크기 조정 된 경우, 다음 다이어그램에서는 단일 Oracle DB 인스턴스가 3900 m b/초를 초과 하 여 Azure NetApp Files 볼륨의 성능 할당량 32 TB (128 m b/s * 32 = 4096 MB/s)와 매우 근접 함을 보여 줍니다.

![Oracle DNFS i/o](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

요약 하면 Azure NetApp Files Oracle 데이터베이스를 클라우드로 이동 하는 데 도움이 됩니다. 데이터베이스에서 요구할 때 성능을 제공 합니다. 언제 든 지 볼륨 할당량의 크기를 동적으로 disruptively 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files의 성능 벤치마크 테스트 추천 사항](azure-netapp-files-performance-metrics-volumes.md)
- [Linux용 성능 벤치마크](performance-benchmarks-linux.md)