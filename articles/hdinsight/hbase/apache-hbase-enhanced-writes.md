---
title: Azure HDInsight (미리 보기)의 Apache HBase에 대 한 기록 향상
description: Apache HBase 미리 로그 쓰기의 성능을 향상 시키기 위해 프리미엄 managed disks를 사용 하는 Azure HDInsight 씁니다 향상 된 기능에 간략하게 설명 합니다.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: 37e6f316a5202429396ddd2a31cb14fe61341e89
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759484"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight (미리 보기)의 Apache HBase에 대 한 기록 향상

이 문서에서 배경 정보를 제공 합니다 **향상 된 기록** Azure HDInsight에서 Apache HBase에 대 한 기능 및이 사용할 수 있는 방법을 효과적으로 개선 하기 위해 쓰기 성능을 합니다. **쓰기 향상** 사용 하 여 [관리 디스크를 Azure premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) 의 Apache HBase 쓰기 미리 로그 (WAL) 성능 향상을 위해. Apache HBase에 대 한 자세한 내용은 참조 하세요 [HDInsight에서 Apache HBase 란](apache-hbase-overview.md)합니다.

## <a name="overview-of-hbase-architecture"></a>HBase 아키텍처 개요

HBase에는 **행** 하나 이상의 구성 **열** 으로 식별 되 고는 **행 키**합니다. 여러 행을 구성 하는 **테이블**합니다. 열에 포함 **셀**에 해당 열에서 값의 타임 스탬프가 지정 된 버전입니다. 열으로 그룹화 됩니다 **열 패밀리**를 호출 하는 저장소 파일에 모든 열은 열 패밀리에 함께 저장 됩니다 **HFiles**합니다.

**지역** HBase 데이터 처리 부하 분산을 위해 사용 됩니다. HBase는 먼저 단일 지역의 테이블의 행을 저장 하 고 여러 지역에 걸쳐 행 테이블 증가에 데이터의 양을으로 분산 시킵니다. **지역 서버** 여러 지역에 대 한 요청을 처리할 수 있습니다.

## <a name="write-ahead-log-for-apache-hbase"></a>Apache HBase에 대 한 로그를 작성 합니다.

HBase에서 데이터 업데이트는 쓰기 미리 로그 (WAL)를 호출 하는 커밋 로그의 형식으로 먼저 기록 됩니다. 업데이트 WAL으로 저장 된 후 메모리 내에 쓸 때 **MemStore**합니다. 으로 디스크에 기록한 데이터를 메모리에서 최대 용량에 도달 하면를 **HFile**합니다.

경우는 **RegionServer** 작동이 중단 되거나 사용할 수 없게 MemStore, 플러시되기 전에 미리 쓰기 로그 수 업데이트를 재생 합니다. WAL을 하지 않고 경우는 **RegionServer** 충돌에 대 한 업데이트에 플러시하기 전에 **HFile**, 손실 되 고 해당 업데이트를 모두 합니다.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Apache HBase에 대 한 Azure HDInsight에서 향상 된 쓰기 기능

기록 향상 된 기능에는 미리 쓰기 로그 클라우드 저장소에 있는 사용 하 여 발생 하는 더 높은 쓰기-대기의 문제가 해결 되었습니다.  HDInsight HBase 클러스터에 대 한 기록 향상 된 기능을 연결 합니다. premium SSD 디스크 마다 RegionServer (작업자 노드)를 관리합니다. 그런 다음 계속 해 서 로그 클라우드 저장소 대신 이러한 프리미엄 managed disks에 탑재 된 Hadoop 파일 시스템 (HDFS)에 기록 작성 합니다.  프리미엄 managed disks 반도체 디스크 (Ssd)를 사용 및 내결함성을 사용 하 여 뛰어난 I/O 성능을 제공 합니다.  관리 되지 않는 디스크와 달리 저장소 단위 하나 다운 되 면 해당 영향을 주지 않습니다 동일한 가용성 집합에서 다른 저장소 단위입니다.  결과적으로, 관리 되는 디스크에는 낮은 쓰기 대기 시간 및 응용 프로그램에 대 한 더 나은 복원 력을 제공합니다. Managed disks를 Azure에 대 한 자세한 참조 [관리 디스크를 Azure 소개](../../virtual-machines/windows/managed-disks-overview.md)합니다. 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>HDInsight의 HBase에 대 한 향상 된 쓰기를 사용 하는 방법

단계에 따라 기록 향상 된 기능을 사용 하 여 새 HBase 클러스터를 만들려면 [HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md) 도달할 때까지 **3 단계, 저장소**합니다. 아래 **Metastore 설정**, 옆에 있는 확인란을 클릭 **사용 하도록 설정 (미리 보기)를 쓰는 향상**합니다. 그런 다음, 클러스터 생성에 대 한 나머지 단계를 진행 합니다.

![HDInsight의 Apache HBase에 대 한 향상 된 기록 옵션을 사용 하도록 설정](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

## <a name="other-considerations"></a>기타 고려 사항

데이터 지 속성을 유지 하는 최소 3 개의 작업자 노드를 사용 하 여 클러스터를 만듭니다. 를 만든 후에 세 개 미만의 작업자 노드로 클러스터를 확장할 수 없습니다. 

플러시 하거나 계속 해 서 로그 쓰기 데이터가 손실 되지 않도록 클러스터를 삭제 하기 전에 HBase 테이블을 비활성화 합니다.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>다음 단계

* 에 대 한 공식 Apache HBase 설명서의 [해 로그 쓰기 기능](https://hbase.apache.org/book.html#wal)합니다.