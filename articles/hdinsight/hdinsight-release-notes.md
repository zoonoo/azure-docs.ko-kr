---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535613"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-09282020"></a>릴리스 날짜: 09/28/2020

이 릴리스는 HDInsight 3.6 및 HDInsight 4.0 모두에 적용 됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새 기능
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>HDInsight 4.0를 사용 하 여 대화형 쿼리에 대 한 자동 크기 조정이 이제 일반 공급 됩니다.
대화형 쿼리 클러스터 유형에 대 한 자동 크기 조정 기능은 이제 HDInsight 4.0에 대 한 GA (일반 공급)로 제공 됩니다. 27 2020 월 27 일 이후에 만들어진 모든 대화형 쿼리 4.0 클러스터는 자동 크기 조정에 대해 GA를 지원 합니다.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>HBase 클러스터는 Premium ADLS Gen2 지원
이제 HDInsight는 HDInsight HBase 3.6 및 4.0 클러스터에 대 한 기본 저장소 계정으로 Premium ADLS Gen2 지원 합니다. 가속화 된 [쓰기](./hbase/apache-hbase-accelerated-writes.md)와 함께 HBase 클러스터에 대해 더 나은 성능을 얻을 수 있습니다.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Azure 장애 도메인의 kafka 파티션 배포
장애 도메인은 Azure 데이터 센터에 있는 기본 하드웨어의 논리적 그룹입니다. 장애 도메인마다 공통 전원과 네트워크 스위치를 공유합니다. HDInsight Kafka은 모든 파티션 복제본을 동일한 장애 도메인에 저장할 수 있습니다. 이제 HDInsight는이 릴리스에서부터 Azure 장애 도메인을 기반으로 Kafka 파티션의 자동 배포를 지원 합니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화
고객은 플랫폼 관리 키를 사용 하는 IPSec 암호화를 사용 하 여 클러스터 노드 간에 전송 중인 암호화를 사용 하도록 설정할 수 있습니다. 이 옵션은 클러스터를 만들 때 사용할 수 있습니다. [전송에서 암호화를 사용 하도록 설정 하는 방법](./domain-joined/encryption-in-transit.md)에 대 한 자세한 내용을 참조 하세요.

### <a name="encryption-at-host"></a>호스트에서 암호화
호스트에서 암호화를 사용 하도록 설정 하면 VM 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. 이 릴리스에서는 클러스터를 만들 때 **임시 데이터 디스크의 호스트에서 암호화를 사용 하도록 설정할** 수 있습니다. 호스트의 암호화는 [제한 된 지역의 특정 VM sku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal)에서만 지원 됩니다. HDInsight는 [다음 노드 구성 및 sku](./hdinsight-supported-node-configuration.md)를 지원 합니다. [호스트에서 암호화를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys)에 대 한 자세한 내용을 참조 하세요.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets로 전환
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 이 버전부터 서비스는 [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)으로 점진적으로 마이그레이션됩니다. 전체 프로세스에는 몇 개월이 걸릴 수 있습니다. 지역 및 구독이 마이그레이션된 후에는 새로 만든 HDInsight 클러스터가 고객의 작업 없이도 가상 머신 확장 집합에서 실행 됩니다. 주요 변경 내용은 필요 하지 않습니다.

## <a name="deprecation"></a>사용 중단
이 릴리스에 대 한 사용이 중단 되지 않습니다.

## <a name="behavior-changes"></a>동작 변경 내용
이 릴리스에 대 한 동작은 변경 되지 않습니다.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Spark, Hadoop 및 ML 서비스에 대해 다른 사육 사 SKU를 선택 하는 기능
현재 HDInsight는 Spark, Hadoop 및 ML 서비스 클러스터 유형에 대 한 사육 사 SKU 변경을 지원 하지 않습니다. 이/A2는 아웃 청구 노드에 대해 A2_v2 SKU를 사용 하 고 고객에 게는 요금이 부과 되지 않습니다. 향후 릴리스에서 고객은 필요에 따라 Spark, Hadoop 및 ML 서비스의 사육 사 SKU를 변경할 수 있습니다. A2_v2/A2 이외의 SKU를 포함 하는 아웃 청구 노드의 요금은 청구 됩니다. 기본 SKU는 여전히 A2_V2/A2 및 무료로 제공 됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대한 구성 요소 버전이 변경되지 않았습니다. [이 문서](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)에서 hdinsight 4.0 및 hdinsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.