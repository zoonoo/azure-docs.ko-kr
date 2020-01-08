---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대 한 개발 팁 및 세부 정보를 확인 하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435459"
---
# <a name="release-notes"></a>릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 Apache Hadoop 및 Apache Spark 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-12172019"></a>릴리스 날짜: 12/17/2019

이 릴리스는 HDInsight 3.6 및 4.0에 모두 적용 됩니다.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.


## <a name="new-features"></a>새로운 기능

### <a name="service-tags"></a>서비스 태그
서비스 태그는 azure 서비스에 대 한 네트워크 액세스를 쉽게 제한할 수 있도록 하 여 Azure virtual machines 및 Azure virtual network에 대 한 보안을 간소화 합니다. NSG (네트워크 보안 그룹) 규칙에서 서비스 태그를 사용 하 여 전역적으로 또는 Azure 지역에 따라 특정 Azure 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Azure는 각 태그를 기반으로 하는 IP 주소의 유지 관리를 제공 합니다. NSGs (네트워크 보안 그룹)에 대 한 HDInsight 서비스 태그는 상태 및 관리 서비스에 대 한 IP 주소 그룹입니다. 이러한 그룹은 보안 규칙 생성의 복잡성을 최소화 하는 데 도움이 됩니다. HDInsight 고객은 Azure Portal, PowerShell 및 REST API를 통해 서비스 태그를 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Azure HDInsight에 대 한 NSG (네트워크 보안 그룹) 서비스 태그](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags)를 참조 하세요.

### <a name="custom-ambari-db"></a>사용자 지정 Ambari DB
이제 HDInsight를 사용 하 여 Apache Ambari에 대 한 고유한 SQL DB를 사용할 수 있습니다. 이 사용자 지정 Ambari DB는 Azure Portal 또는 resource manager 템플릿을 통해 구성할 수 있습니다.  이 기능을 사용 하 여 처리 및 용량 요구 사항에 적합 한 SQL DB를 선택할 수 있습니다. 비즈니스 증가 요구 사항에 맞게 쉽게 업그레이드할 수도 있습니다. 자세한 내용은 [사용자 지정 AMBARI DB를 사용 하 여 HDInsight 클러스터 설정](hdinsight-custom-ambari-db.md)을 참조 하세요.

![사용자 지정 Ambari DB](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>사용 중단
이 릴리스에 대 한 결함 없습니다. 예정 된 결함을 준비 하려면 예정 된 [변경 내용](#upcoming-changes)을 참조 하세요.

## <a name="behavior-changes"></a>동작 변경
이 릴리스에 대 한 동작은 변경 되지 않습니다. 예정 된 동작 변경 내용을 준비 하려면 예정 된 [변경 내용](#upcoming-changes)을 참조 하세요.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행 됩니다. 

### <a name="transport-layer-security-tls-12-enforcement"></a>TLS (Transport Layer Security) 1.2 적용
TLS(전송 계층 보안) 및 SSL(Secure Sockets Layer)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다. 자세한 내용은 [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)을 참조 하세요. Azure HDInsight 클러스터는 공용 HTTPS 끝점에서 TLS 1.2 연결을 허용 하지만 이전 클라이언트와의 호환성을 위해 TLS 1.1은 계속 지원 됩니다.

다음 릴리스에서부터 TLS 1.2 연결만 수락 하도록 새 HDInsight 클러스터를 옵트인 하 고 구성할 수 있습니다. 

이후에는 6/30/2020부터 시작 하 여 Azure HDInsight는 모든 HTTPS 연결에 TLS 1.2 이상 버전을 적용 합니다. 모든 클라이언트에서 TLS 1.2 이상 버전을 처리할 수 있도록 준비 하는 것이 좋습니다.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합으로 이동
이제 HDInsight는 Azure virtual machines를 사용 하 여 클러스터를 프로 비전 합니다. Fe삼 차 2020 (정확한 날짜는 나중에 전달 됨)부터 시작 하 여 HDInsight는 Azure virtual machine scale sets를 대신 사용 합니다. [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)에 대 한 자세한 내용을 참조 하세요.

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 클러스터 노드 크기 변경 
예정 된 릴리스에서는 다음을 수행 합니다.
- ESP Spark 클러스터에 허용 되는 최소 노드 크기는 Standard_D13_V2로 변경 됩니다. 
- A 시리즈 vm은 CPU와 메모리 용량이 상대적으로 부족 하 여 ESP 클러스터 문제를 일으킬 수 있으므로 새 ESP 클러스터를 만드는 데 사용 되지 않습니다.

### <a name="hbase-20-to-21"></a>HBase 2.0 ~ 2.1
예정 된 HDInsight 4.0 릴리스에서 HBase 버전은 버전 2.0에서 2.1로 업그레이드 됩니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속 해 서 클러스터 안정성과 성능을 향상 시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
HDInsight 3.6 지원이 2020 년 12 월 31 일까 지 확장 되었습니다. 자세한 내용은 [지원 되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)에서 찾아볼 수 있습니다.

HDInsight 4.0에 대 한 구성 요소 버전이 변경 되지 않았습니다.

HDInsight 3.6의 Apache Zeppelin: 0.7.0--> 0.7.3. 

[이 문서](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)에서 최신 구성 요소 버전을 찾을 수 있습니다.

## <a name="new-regions"></a>새 지역

### <a name="uae-north"></a>아랍에미리트 북부
아랍에미리트 북부의 관리 Ip는 `65.52.252.96` 및 `65.52.252.97`입니다.
