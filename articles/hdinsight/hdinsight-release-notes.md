---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006537"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-07132020"></a>릴리스 날짜: 07/13/2020

이 릴리스는 HDInsight 3.6 및 4.0 둘 다에 적용됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새 기능
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Microsoft Azure에 대 한 고객 Lockbox 지원
Azure HDInsight는 이제 Azure 고객 Lockbox을 지원 합니다. 고객이 고객 데이터 액세스 요청을 검토 하 고 승인 하거나 거부할 수 있는 인터페이스를 제공 합니다. 지원 요청 시 Microsoft 엔지니어가 고객 데이터에 액세스 해야 할 때 사용 됩니다. 자세한 내용은 [Microsoft Azure에 대 한 고객 Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview)를 참조 하세요.

### <a name="service-endpoint-policies-for-storage"></a>저장소에 대 한 서비스 끝점 정책
이제 고객은 HDInsight 클러스터 서브넷에서 서비스 끝점 정책 (9 월)을 사용할 수 있습니다. [Azure 서비스 끝점 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)에 대해 자세히 알아보세요.

## <a name="deprecation"></a>사용 중단
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark 클러스터의 Spark 2.1 및 2.2 사용 중단
7 월 1 2020부터 고객은 HDInsight 3.6에서 Spark 2.1 및 2.2을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 3.6의 Spark 2.3으로 전환하는 것이 좋습니다.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark 클러스터의 Spark 2.3 사용 중단
7 월 1 2020부터 고객이 HDInsight 4.0의 Spark 2.3을 사용 하 여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Spark 2.4로 전환하는 것이 좋습니다.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka 클러스터의 Kafka 1.1 사용 중단
2020년 7월 1일부터 고객은 HDInsight 4.0의 Kafka 1.1을 사용하여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Kafka 2.1로 전환하는 것이 좋습니다.

## <a name="behavior-changes"></a>동작 변경 내용
에 주의 해야 하는 동작이 변경 되지 않습니다.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Spark, Hadoop 및 ML 서비스에 대해 다른 사육 사 SKU를 선택 하는 기능
현재 HDInsight는 Spark, Hadoop 및 ML 서비스 클러스터 유형에 대 한 사육 사 SKU 변경을 지원 하지 않습니다. 이/A2는 아웃 청구 노드에 대해 A2_v2 SKU를 사용 하 고 고객에 게는 요금이 부과 되지 않습니다. 향후 릴리스에서 고객은 필요에 따라 Spark, Hadoop 및 ML 서비스의 사육 사 SKU를 변경할 수 있습니다. A2_v2/A2 이외의 SKU를 포함 하는 아웃 청구 노드의 요금은 청구 됩니다. 기본 SKU는 여전히 A2_V2/A2 및 무료로 제공 됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Hive 웨어하우스 커넥터 문제를 수정 했습니다.
이전 릴리스의 Hive 웨어하우스 커넥터 유용성에 문제가 있습니다. 문제가 해결 되었습니다. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>수정 된 Zeppelin 노트북에서 선행 제로 문제를 자릅니다.
Zeppelin가 문자열 형식에 대 한 테이블 출력의 앞에 오는 0을 잘못 자릅니다. 이 릴리스에서는이 문제가 해결 되었습니다.

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대한 구성 요소 버전이 변경되지 않았습니다. [이 문서](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)에서 hdinsight 4.0 및 hdinsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.
