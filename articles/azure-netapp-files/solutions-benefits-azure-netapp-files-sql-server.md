---
title: SQL Server 배포에 대 한 Azure NetApp Files 사용의 이점 | Microsoft Docs
description: SQL Server 배포에 대 한 Azure NetApp Files 사용에 대 한 자세한 비용 분석 성능 이점을 보여 줍니다.
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
ms.date: 02/08/2021
ms.author: b-juche
ms.openlocfilehash: 7bbedae3d2dd080819b295d815bec68309916a6e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803696"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>SQL Server 배포에 대 한 Azure NetApp Files 사용의 이점

Azure NetApp Files는 블록 저장소 솔루션에 비해 TCO (총 소유 비용)를 SQL Server 줄일 수 있습니다.  블록 저장소를 사용 하는 경우 가상 컴퓨터에서 i/o 및 디스크 작업에 대 한 대역폭에 대 한 제한이 적용 됩니다. 네트워크 대역폭 제한은 해당 Azure NetApp Files에 대해서만 적용 됩니다.  즉, Azure NetApp Files에 VM 수준 i/o 제한이 적용 되지 않습니다. 이러한 i/o 제한이 없으면 Azure NetApp Files에 연결 된 작은 가상 머신에서 실행 되는 SQL Server는 훨씬 더 큰 가상 머신에서 실행 되 SQL Server 수 있습니다. 인스턴스 크기를 조정 하면 계산 비용이 이전 price 태그의 25%로 줄어듭니다.  *Azure NetApp Files를 사용 하 여 계산 비용을 줄일 수 있습니다.*  

그러나 계산 비용은 SQL Server 라이선스 비용에 비해 작습니다.  Microsoft SQL Server [라이선스](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) 는 실제 코어 수에 연결 됩니다. 따라서 인스턴스 크기를 줄이면 소프트웨어 라이선스에 대 한 비용 절감을 훨씬 더 많이 야기 합니다. *Azure NetApp Files를 사용 하 여 소프트웨어 라이선스 비용을 줄일 수 있습니다.*

저장소 자체의 비용은 데이터베이스의 실제 크기에 따라 가변적입니다. 선택한 저장소에 관계 없이 용량에는 관리 디스크 또는 파일 공유 여부와 상관 없이 비용이 포함 됩니다.  데이터베이스 크기가 늘어나고 저장소 비용이 증가 함에 따라 저장소는 TCO에 기여 하 여 전반적인 비용에 영향을 줍니다.  이와 같이 어설션이 다음과 같이 조정 됩니다. *Azure NetApp Files를 사용 하 여 SQL Server 배포 비용을 줄일 수* 있습니다. 

이 문서에서는 SQL Server 배포에 대 한 Azure NetApp Files 사용에 대 한 자세한 비용 분석 및 성능상의 이점을 보여 줍니다. 더 작은 인스턴스를 사용 하는 경우 데이터베이스 작업을 수행 하는 데 충분 한 CPU가 있어야 하는 것은 아닙니다. 대규모 인스턴스의 경우에는 *대부분의 경우 더 작은 인스턴스는 Azure NetApp Files 때문에 더 큰 디스크 기반 항목 보다 훨씬 더* 성능이 훨씬 더 우수 합니다. 

## <a name="detailed-cost-analysis"></a>자세한 비용 분석 

이 섹션의 두 가지 그래픽 집합에는 TCO 예제가 나와 있습니다.  최적의 가격을 얻기 위해 관리 디스크의 수와 유형, Azure NetApp Files 서비스 수준 및 각 시나리오의 용량을 선택 했습니다.  각 그래픽은 그룹화 된 컴퓨터 (예: 관리 디스크를 사용 하는 D64과 비교 하 여 Azure NetApp Files D16)로 구성 되며 각 컴퓨터 유형에 대해 가격은 세분화 됩니다.  

첫 번째 그래픽 집합은 TiB 데이터베이스 크기를 사용 하 여 솔루션의 전체 비용을 D64, D32에 대 한 D8 및 D16에 대 한 D4와 D16s_v3 비교 하 여 보여 줍니다. 각 구성에 대 한 예상 IOPs는 녹색 또는 노란색 줄로 표시 되 고 오른쪽 Y 축에 해당 합니다.

[![TiB 데이터베이스 크기를 사용 하는 솔루션의 전체 비용을 보여 주는 그래픽입니다. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


두 번째 그래픽 집합은 50-TiB 데이터베이스를 사용 하 여 전체 비용을 보여 줍니다. 비교는 Azure NetApp Files와 D64 비교 (예: 블록에의 한 D16 비교)입니다. 

[![50 TiB 데이터베이스 크기를 사용 하 여 전체 비용을 보여 주는 그래픽입니다. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>성능 및 많은 it  

상당한 비용 절감 어설션을 제공 하려면 많은 성능이 필요 합니다. 일반적인 Azure 인벤토리 지원의 가장 큰 인스턴스는 8만 디스크 IOPS를 예로 듭니다. 단일 Azure NetApp Files 볼륨은 8만 데이터베이스 IOPS를 달성할 수 있으며 D16 같은 인스턴스는 동일한를 사용할 수 있습니다. 일반적으로 25600 디스크 IOPS를 지 원하는 D16는 25% D64 크기입니다.  D64s_v3는 8만 디스크 IOPS를 지원할 수 있으며, 그에 따라 뛰어난 상위 수준 비교 지점을 제공 합니다.

D16s_v3 Azure NetApp Files 볼륨을 8만 데이터베이스 IOPS로 설정할 수 있습니다. SSB (SQL Storage 벤치 마크) 벤치마킹 도구에 의해 검증 된 대로 D16 인스턴스는 D64 인스턴스에서 디스크에 도달할 수 있는 것 보다 더 큰 워크 로드 125%를 달성 했습니다.  도구에 대 한 자세한 내용은 [SSB 테스트 도구](#ssb-testing-tool) 섹션을 참조 하세요.

TiB의 작업 집합 크기와 80% 읽기, 20%의 업데이트 SQL Server 워크 로드를 사용 하 여 D 인스턴스 클래스에서 대부분의 인스턴스 성능 기능을 측정 했습니다. D2 및 D64 인스턴스 자체가 테스트에서 제외 되었으므로 대부분은 그렇지 않습니다. 이전은 가속화 된 네트워킹을 지원 하지 않기 때문에 중단 되었으며, 후자는 비교 지점 이기 때문입니다. D4s_v3, D8s_v3, D16s_v3 및 D32s_v3의 제한을 각각 이해 하려면 다음 그래프를 참조 하십시오.  Managed disk storage 테스트는 그래프에 표시 되지 않습니다. 비교 값은 D 클래스 인스턴스 유형에 대 한 [Azure 가상 머신 제한 테이블](../virtual-machines/dv3-dsv3-series.md) 에서 직접 그려집니다.

Azure NetApp Files를 사용 하는 경우 D 클래스의 각 인스턴스는 인스턴스의 디스크 성능 기능을 두 배 더 크게 충족 하거나 초과할 수 있습니다.  *Azure NetApp Files를 사용 하 여 소프트웨어 라이선스 비용을 크게 줄일 수 있습니다.*  

* 75% CPU 사용률의 D4는 D16의 디스크 기능과 일치 합니다.  
    * D16 속도는 25600 디스크 IOPS로 제한 됩니다.  
* 75% CPU 사용률의 D8이 D32의 디스크 기능과 일치 합니다.  
    * D32 속도는 51200 디스크 IOPS로 제한 됩니다.  
* 55%의 CPU 사용률이 D64의 디스크 기능과 일치 D16.  
    * D64 속도는 8만 디스크 IOPS로 제한 됩니다.  
* D32 15%의 CPU 사용률이 D64의 디스크 기능과도 일치 합니다.  
    * 위에 명시 된 D64는 8만 디스크 IOPS로 제한 됩니다.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU 제한 테스트-성능 및 처리 능력

다음 다이어그램에서는 S3B CPU 제한 테스트를 요약 합니다.

![Azure NetApp Files에 대 한 단일 인스턴스 SQL Server의 평균 CPU 비율을 보여 주는 다이어그램입니다.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

확장성은 스토리의 일부일 뿐입니다. 다른 파트는 대기 시간입니다.  적은 수의 가상 컴퓨터에서 훨씬 더 많은 i/o 속도를 얻을 수 있는 한 가지 작업을 수행 하는 것은 아래와 같이 적은 수의 단일 대기 시간으로이 작업을 수행 하는 것입니다.  

* D4는 2.3 밀리초의 Azure NetApp Files에 대해 26000 IOPS를 합니다.  
* D8은 2.0 밀리초의 Azure NetApp Files에 대 한 51000 IOPS입니다.  
* D16는 2.8 밀리초의 Azure NetApp Files에 88000 IOPS를 합니다.
* D32는 2.4 밀리초의 Azure NetApp Files에 8만 IOPS를 합니다.  

### <a name="s3b-per-instance-type-latency-results"></a>S3B 인스턴스당 형식 대기 시간 결과

다음 다이어그램에서는 Azure NetApp Files에 대 한 단일 인스턴스 SQL Server의 대기 시간을 보여 줍니다.

![Azure NetApp Files에 대 한 단일 인스턴스 SQL Server의 대기 시간을 보여 주는 다이어그램입니다.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB 테스트 도구 
 
[TPC-E](http://www.tpc.org/tpce/) 벤치마킹 도구는 의도적으로 *저장소* 대신 *계산* 을 과부하 합니다. 이 섹션에 표시 된 테스트 결과는 SQL Storage 벤치 마크 (SSB) 라는 스트레스 테스트 도구를 기반으로 합니다.  SQL Server Storage 벤치 마크는 SQL Server 데이터베이스에 대해 대규모 SQL 실행을 구동 하 여 [SLOB2 Oracle 벤치마킹 도구](https://kevinclosson.net/slob/)와 유사한 OLTP 워크 로드를 시뮬레이션할 수 있습니다. 

SSB 도구는 Azure 가상 머신 내에서 실행 되는 SQL Server 데이터베이스에 직접 설명 된 문을 실행 하는 SELECT 및 UPDATE 기반 워크 로드를 생성 합니다.  이 프로젝트의 경우 SSB 워크 로드는 사용자 수 당 15 분의 중간 지점 10 개 또는 12 개를 사용 하 여 1 ~ 100 SQL Server 사용자를 심어줍니다 합니다.  이러한 실행의 모든 성능 메트릭은 perfmon의 관점에서 반복성 SSB는 시나리오 마다 3 회 실행 되었습니다. 

테스트 자체가 80% SELECT 및 20% UPDATE 문으로 구성 되었으므로 90% 무작위 읽기가 가능 합니다.  SSB 생성 된 데이터베이스 자체의 크기는 1000입니다. 사용자 테이블당 15 개의 사용자 테이블과 900만 행 당 8192 바이트로 구성 됩니다. 

SSB 벤치 마크는 오픈 소스 도구입니다.  [SQL Storage 벤치 마크 GitHub 페이지](https://github.com/NetApp/SQL_Storage_Benchmark.git)에서 무료로 사용할 수 있습니다.  


## <a name="in-summary"></a>요약  

Azure NetApp Files를 사용 하면 총 소유 비용을 대폭 줄이는 동시에 SQL server 성능을 향상 시킬 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files를 사용 하는 솔루션 아키텍처 – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

