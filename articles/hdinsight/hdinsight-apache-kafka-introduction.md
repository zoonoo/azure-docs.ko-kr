---
title: "HDInsight의 Apache Kafka 소개 - Azure | Microsoft Docs"
description: "HDInsight의 Apache Kafka에 대해 알아보세요. 이것이 무엇인지, 무엇을 하는지, 어디서 예제와 시작 정보를 찾는지에 대해 설명합니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1976c52bd7fa56bb07104e205ab3699b2dfa4c50
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>HDInsight의 Apache Kafka 소개(미리 보기)

[Apache Kafka](https://kafka.apache.org)는 실시간 스트리밍 데이터 파이프라인과 응용 프로그램을 만드는 데 사용할 수 있는 오픈 소스 분산형 스트리밍 플랫폼입니다. 또한 Kafka는 명명된 데이터 스트림을 게시하고 구독할 수 있는 메시지 대기열과 비슷한 메시지 브로커 기능을 제공합니다. HDInsight의 Kafka는 Microsoft Azure 클라우드에서 관리되고 확장성이 뛰어난 고가용성 서비스를 제공합니다.

## <a name="why-use-kafka-on-hdinsight"></a>HDInsight에서 Kafka를 사용하는 이유

Kafka에서 제공하는 기능은 다음과 같습니다.

* 게시-구독 메시징 패턴: Kafka는 Kafka 토픽에 레코드를 게시하기 위한 생산자 API를 제공합니다. 소비자 API는 토픽을 구독할 때 사용됩니다.

* 스트림 처리: Kafka는 종종 실시간 스트리밍 처리를 위해 Apache Storm 또는 Apache Spark와 함께 사용됩니다. Kafka 0.10.0.0(HDInsight 버전 3.5)은 Storm이나 Spark를 요구하지 않고 스트리밍 솔루션을 빌드할 수 있는 스트리밍 API를 도입했습니다.

* 수평 확장: Kafka는 HDInsight 클러스터의 노드에서 스트림을 분할합니다. 소비자 프로세스는 개별 파티션에 연결하여 레코드를 소비할 때 부하 분산을 제공할 수 있습니다.

* 순차적 전달: 레코드는 각 파티션 내에서 수신된 순서대로 스트림에 저장됩니다. 파티션마다 소비자 프로세스를 하나씩 연결하여 레코드가 순서대로 처리되도록 보장할 수 있습니다.

* 내결함성: 노드 간에 결함을 허용하기 위해 파티션을 복제할 수 있습니다.

* Azure Managed Disks와 통합: 관리 디스크는 HDInsight 클러스터에서 가상 컴퓨터에 사용된 디스크에 대해 더 높은 규모와 처리량을 제공합니다.

    기본적으로 관리 디스크는 HDInsight에서 Kafka에 대해 사용하도록 설정되며 HDInsight 생성 중에 노드당 사용된 디스크 수를 구성할 수 있습니다. 관리 디스크에 대한 자세한 내용은 [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

    HDInsight에서 Kafka로 관리 디스크 구성에 대한 자세한 내용은 [HDInsight에서 Kafka의 확장성 높이기](hdinsight-apache-kafka-scalability.md)를 참조하세요.

## <a name="use-cases"></a>사용 사례

* **메시징**: Kafka는 게시-구독 메시지 패턴을 지원하므로 종종 메시지 브로커로 사용됩니다.

* **활동 추적**: Kafka는 순서대로 레코드 로그를 기록하므로 활동을 추적하고 다시 만드는 데 사용할 수 있습니다. 예를 들어 웹 사이트 또는 응용 프로그램에서의 사용자 작업이 여기에 해당합니다.

* **집계**: 스트림 처리를 사용하여 결합할 서로 다른 스트림의 정보를 한데 모으고 중앙에서 이 정보를 운영 데이터로 집중적으로 처리할 수 있습니다.

* **변환**: 스트림 처리를 사용하여 여러 입력 토픽의 데이터를 하나 이상의 출력 토픽으로 결합하고 보강할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 링크를 사용하여 HDInsight에서 Apache Kafka를 사용하는 방법에 대해 알아봅니다.

* [HDInsight에서 Apache Kafka 시작](hdinsight-apache-kafka-get-started.md)

* [MirrorMaker를 사용하여 HDInsight에 Kafka 복제본 만들기](hdinsight-apache-kafka-mirroring.md)

* [HDInsight의 Kafka에서 Apache Storm 사용](hdinsight-apache-storm-with-kafka.md)

* [HDInsight의 Kafka에서 Apache Spark 사용](hdinsight-apache-spark-with-kafka.md)

* [Azure Virtual Network를 통해 Kafka에 연결](hdinsight-apache-kafka-connect-vpn-gateway.md)
