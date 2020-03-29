---
title: Apache HBase에 대한 Azure HDInsight 가속 쓰기
description: 아파치 HBase 쓰기 미리 로그의 성능을 향상시키기 위해 프리미엄 관리 디스크를 사용하는 Azure HDInsight 가속 쓰기 기능에 대한 개요를 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764609"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Apache HBase에 대한 Azure HDInsight 가속 쓰기

이 문서에서는 Azure HDInsight의 아파치 HBase에 대한 **가속 쓰기** 기능에 대한 배경 및 쓰기 성능을 향상시키는 데 효과적으로 사용할 수 있는 방법을 제공합니다. **가속 쓰기는** [Azure 프리미엄 SSD 관리 디스크를](../../virtual-machines/linux/disks-types.md#premium-ssd) 사용하여 아파치 HBase 쓰기 미리 쓰기 로그(WAL)의 성능을 향상시킵니다. 아파치 HBase에 대한 자세한 내용은 [HDInsight에서 아파치 HBase무엇을](apache-hbase-overview.md)참조하십시오.

## <a name="overview-of-hbase-architecture"></a>HBase 아키텍처 개요

HBase에서 **행은** 하나 이상의 **열로** 구성되며 **행 키로**식별됩니다. 여러 행이 **테이블을**구성합니다. 열에는 해당 열에 있는 값의 타임스탬프 버전인 **셀이**포함되어 있습니다. 열은 **열 패밀리로**그룹화되고 열 패밀리의 모든 열은 **HFiles**라는 저장소 파일에 함께 저장됩니다.

HBase의 **영역은** 데이터 처리 부하의 균형을 맞추는 데 사용됩니다. HBase는 먼저 테이블의 행을 단일 리전에 저장합니다. 행은 테이블의 데이터 양이 증가함에 따라 여러 지역에 분산됩니다. **리전 서버는** 여러 리전에 대한 요청을 처리할 수 있습니다.

## <a name="write-ahead-log-for-apache-hbase"></a>아파치 HBase에 대 한 미리 로그 쓰기

HBase는 먼저 WAL(미리 쓰기 로그)이라고 하는 커밋 로그 유형에 데이터 업데이트를 씁니다. 업데이트가 WAL에 저장되면 메모리 **내 MemStore에**기록됩니다. 메모리의 데이터가 최대 용량에 도달하면 **디스크에 HFile**로 기록됩니다.

MemStore를 플러시하기 전에 **RegionServer가** 충돌하거나 사용할 수 없게 되면 미리 쓰기 로그를 사용하여 업데이트를 재생할 수 있습니다. WAL이 없으면 **HFile에**대한 업데이트를 플러시하기 전에 **RegionServer가** 충돌하면 이러한 모든 업데이트가 손실됩니다.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>아파치 H베이스에 대한 Azure HDInsight의 가속 쓰기 기능

가속 쓰기 기능은 클라우드 저장소에 있는 미리 쓰기 로그를 사용하여 발생하는 쓰기 대기 시간 증가 문제를 해결합니다.  HDInsight 아파치 HBase 클러스터의 가속 쓰기 기능은 모든 RegionServer(작업자 노드)에 프리미엄 SSD 관리 디스크를 연결합니다. 그런 다음 클라우드 스토리지 대신 이러한 프리미엄 관리 디스크에 탑재된 HADoop 파일 시스템(HDFS)에 미리 쓰기 로그를 작성합니다.  프리미엄 관리 디스크는 솔리드 스테이트 디스크(SSD)를 사용하며 내결함성을 갖춘 우수한 I/O 성능을 제공합니다.  관리되지 않는 디스크와 달리 한 저장소 단위가 다운되면 동일한 가용성 집합의 다른 저장소 장치에는 영향을 주지 않습니다.  따라서 관리 디스크는 쓰기 대기 시간이 적고 응용 프로그램에 더 나은 복원력을 제공합니다. Azure 관리 디스크에 대한 자세한 내용은 [Azure 관리 디스크 소개를](../../virtual-machines/windows/managed-disks-overview.md)참조하십시오.

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>HDInsight에서 HBase에 대한 가속 쓰기를 활성화하는 방법

가속 쓰기 기능을 사용하여 새 HBase 클러스터를 만들려면 **3단계 저장소에**도달할 때까지 [HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md) 단계를 따릅니다. **메타스토어 설정에서**HBase 가속 쓰기 사용 옆의 확인란을 **선택합니다.** 그런 다음 클러스터 만들기를 위한 나머지 단계를 계속합니다.

![HDInsight 아파치 HBase에 대한 가속 쓰기 옵션 사용](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>기타 고려 사항

데이터 내구성을 유지하려면 최소 3개의 작업자 노드가 있는 클러스터를 만듭니다. 생성된 후에는 클러스터를 3개 미만의 작업자 노드로 축소할 수 없습니다.

클러스터를 삭제하기 전에 HBase 테이블을 플러시하거나 비활성화하여 미리 쓰기 로그 데이터가 손실되지 않도록 합니다.

```
flush 'mytable'
```

```
disable 'mytable'
```

클러스터를 축소할 때 유사한 단계를 따르십시오: 테이블을 플러시하고 테이블을 비활성화하여 들어오는 데이터를 중지합니다. 클러스터를 3개 미만으로 축소할 수 없습니다.

다음 단계를 수행하면 성공적인 축소가 보장되고 복제되지 않거나 임시 파일로 인해 네임노드가 안전 모드로 전환될 가능성을 방지할 수 있습니다.

네임노드가 축소 된 후 안전 모드로 전환하는 경우, 하드 디스크 명령을 사용하여 복제되지 않는 블록을 다시 복제하고 안전 모드에서 하드 디스크를 가져옵니다. 이렇게 다시 복제하면 HBase를 성공적으로 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [앞서 로그 쓰기 기능에](https://hbase.apache.org/book.html#wal) 대한 공식 아파치 HBase 문서
* 가속 쓰기를 사용하도록 HDInsight 아파치 HBase 클러스터를 업그레이드하려면 [아파치 HBase 클러스터를 새 버전으로 마이그레이션을](apache-hbase-migrate-new-version.md)참조하십시오.
