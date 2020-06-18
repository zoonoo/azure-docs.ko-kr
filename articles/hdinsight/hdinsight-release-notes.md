---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: b7489c49b7469feacfd5b982615419741d286998
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849708"
---
# <a name="release-notes"></a>릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-01092020"></a>릴리스 날짜: 2020년 1월 9일

이 릴리스는 HDInsight 3.6 및 4.0 둘 다에 적용됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래의 변경 내용이 표시되지 않으면 며칠 후에 해당 지역에서 릴리스가 라이브될 때까지 기다려 주세요.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.

## <a name="new-features"></a>새로운 기능
### <a name="tls-12-enforcement"></a>TLS 1.2 적용
TLS(전송 계층 보안) 및 SSL(Secure Sockets Layer)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다. [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)에 대해 자세히 알아봅니다. HDInsight는 퍼블릭 HTTP 엔드포인트에서 TLS 1.2를 사용하지만 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원됩니다. 

이 릴리스에서 고객은 퍼블릭 클러스터 엔드포인트를 통한 모든 연결에 대해서만 TLS 1.2를 옵트인(opt in)할 수 있습니다. 이를 지원하기 위해 새 속성 **minSupportedTlsVersion**이 도입되었으며 클러스터를 만드는 동안 이 속성을 지정할 수 있습니다. 이 속성을 설정하지 않으면 클러스터는 현재 동작처럼 TLS 1.0, 1.1 및 1.2를 계속 지원합니다. 고객은 이 속성의 값을 "1.2"로 설정할 수 있습니다. 이렇게 하면 클러스터는 TLS 1.2 이상만 지원합니다. 자세한 내용은 [전송 계층 보안](./transport-layer-security.md)을 참조하세요.

### <a name="bring-your-own-key-for-disk-encryption"></a>디스크 암호화를 위한 사용자 고유 키 가져오기
HDInsight의 모든 관리 디스크는 Azure SSE(스토리지 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. 이 릴리스부터는 디스크 암호화에 대해 BYOK(Bring Your Own Key)를 수행하고 Azure Key Vault를 사용하여 관리할 수 있습니다. BYOK 암호화는 추가 비용 없이 클러스터를 만드는 동안 진행되는 1단계 구성입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가합니다. 자세한 내용은 [고객 관리형 키 디스크 암호화](https://docs.microsoft.com/azure/hdinsight/disk-encryption)를 참조하세요.

## <a name="deprecation"></a>사용 중단
이 릴리스의 사용 중단은 없습니다. 예정된 사용 중단을 대비하려면 [예정된 변경](#upcoming-changes)을 참조하세요.

## <a name="behavior-changes"></a>동작 변경 내용
이 릴리스에 대한 동작 변경은 없습니다. 예정된 변경을 대비하려면 [예정된 변경](#upcoming-changes)을 참조하세요.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다. 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>HDInsight 3.6 Spark 클러스터의 Spark 2.1 및 2.2 사용 중단
2020년 7월 1일부터 고객은 HDInsight 3.6의 Spark 2.1 및 2.2를 사용하여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 3.6의 Spark 2.3으로 전환하는 것이 좋습니다.

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>HDInsight 4.0 Spark 클러스터의 Spark 2.3 사용 중단
2020년 7월 1일부터 고객은 HDInsight 4.0의 Spark 2.3을 사용하여 새 Spark 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Spark 2.4로 전환하는 것이 좋습니다.

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>HDInsight 4.0 Kafka 클러스터의 Kafka 1.1 사용 중단
2020년 7월 1일부터 고객은 HDInsight 4.0의 Kafka 1.1을 사용하여 새 Kafka 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지하기 위해 2020년 6월 30일까지 HDInsight 4.0의 Kafka 2.1로 전환하는 것이 좋습니다.

### <a name="hbase-20-to-216"></a>HBase 2.0-2.1.6
예정된 HDInsight 4.0 릴리스에서 HBase 버전은 버전 2.0에서 2.1.6로 업그레이드됩니다.

### <a name="spark-240-to-244"></a>Spark 2.4.0-2.4.4
예정된 HDInsight 4.0 릴리스에서 Spark 버전은 버전 2.4.0에서 2.4.4로 업그레이드됩니다.

### <a name="kafka-210-to-211"></a>Kafka 2.1.0-2.1.1
예정된 HDInsight 4.0 릴리스에서 Kafka 버전은 버전 2.1.0에서 2.1.1로 업그레이드됩니다.

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>헤드 노드에는 최소 4코어 VM이 필요합니다. 
HDInsight 클러스터의 고가용성 및 안정성을 보장하기 위해 헤드 노드에는 최소 4코어 VM이 필요합니다. 2020년 4월 6일부터 고객은 새 HDInsight 클러스터에 대한 헤드 노드로 4코어 이상의 VM만 선택할 수 있습니다. 기존 클러스터는 정상적으로 계속 실행됩니다. 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 클러스터 노드 크기 변경 
예정된 릴리스에서 ESP Spark 클러스터에 허용되는 최소 노드 크기가 Standard_D13_V2로 변경됩니다. A 시리즈 VM은 비교적 낮은 CPU와 메모리 용량 때문에 ESP 클러스터 이슈를 유발할 수 있습니다. A 시리즈 VM은 새 ESP 클러스터를 만드는 데 사용되지 않습니다.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets로 전환
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 예정된 릴리스에서 HDInsight는 Azure Virtual Machine Scale Sets를 대신 사용합니다. Azure Virtual Machine Scale Sets에 대해 자세히 알아보세요.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대한 구성 요소 버전이 변경되지 않았습니다. HDInsight 4.0 및 HDInsight 3.6의 최신 구성 요소 버전을 여기에서 찾을 수 있습니다.

