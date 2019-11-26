---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대 한 개발 팁 및 세부 정보를 확인 하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 54e28a9d434500915aa8cc8e07ade3592e5aa96c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185611"
---
# <a name="release-notes"></a>릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 Apache Hadoop 및 Apache Spark 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

## <a name="release-date-11072019"></a>릴리스 날짜: 11/07/2019

이 릴리스는 HDInsight 3.6 및 4.0에 모두 적용 됩니다.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 버전 관리 문서](hdinsight-component-versioning.md)를 참조하세요.


## <a name="new-features"></a>새로운 기능

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (기타) (미리 보기)

Ambari (HDInsight Identity Broker)를 사용 하면 사용자가 MFA (multi-factor authentication)를 사용 하 여 Apache에 로그인 하 고 Azure Active Directory Domain Services (AAD)에서 암호 해시가 없어도 필요한 Kerberos 티켓을 가져올 수 있습니다. 현재는 ARM 템플릿을 통해 배포 된 클러스터에 대해서만 사용할 수 있습니다.

### <a name="kafka-rest-api-proxy-preview"></a>Kafka Rest API 프록시 (미리 보기)

Kafka Rest API 프록시는 보안 AAD 권한 부여 및 OAuth 프로토콜을 통해 Kafka 클러스터와 함께 항상 사용 가능한 REST 프록시를 한 번의 클릭으로 배포 합니다. 

### <a name="auto-scale"></a>자동 크기 조정

Azure HDInsight에 대 한 자동 크기 조정은 이제 Apache Spark 및 Hadoop 클러스터 유형의 모든 지역에서 일반 공급 됩니다. 이 기능을 사용 하면 보다 비용 효율적이 고 생산적인 방식으로 빅 데이터 분석 워크 로드를 관리할 수 있습니다. 이제 HDInsight 클러스터의 사용을 최적화 하 고 필요한 만큼만 비용을 지불할 수 있습니다.

요구 사항에 따라 부하 기반 자동 크기 조정과 일정 기반 자동 크기 조정 중에서 선택할 수 있습니다. 일정 기반 자동 크기 조정에서는 미리 정의 된 일정에 따라 클러스터 크기를 변경할 수 있는 반면 부하 기반 자동 크기 조정은 현재 리소스 요구에 따라 클러스터 크기를 확장 및 축소할 수 있습니다. 

HBase 및 LLAP 워크 로드에 대 한 자동 크기 조정 지원도 공개 미리 보기입니다. 자세한 내용은 [Azure HDInsight 클러스터 자동 크기 조정](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters)을 참조 하세요.

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Apache HBase에 대 한 HDInsight 가속 쓰기 

가속 쓰기는 Azure 프리미엄 SSD 관리 디스크를 사용하여 Apache HBase WAL(Write Ahead Log)의 성능을 개선합니다. 자세한 내용은 [Apache HBase에 대 한 Azure HDInsight 가속 쓰기](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes)를 참조 하세요.

### <a name="custom-ambari-db"></a>사용자 지정 Ambari DB

이제 HDInsight는 고객이 자신의 Ambari 용 SQL DB를 사용할 수 있도록 새 용량을 제공 합니다. 이제 고객은 Ambari에 적합 한 SQL DB를 선택 하 고 고유한 비즈니스 성장 요구 사항에 따라 쉽게 업그레이드할 수 있습니다. 배포는 Azure Resource Manager 템플릿을 사용 하 여 수행 됩니다. 자세한 내용은 [사용자 지정 AMBARI DB를 사용 하 여 HDInsight 클러스터 설정](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db)을 참조 하세요.

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>이제 HDInsight에서 F 시리즈 virtual machines를 사용할 수 있습니다.

F 시리즈 Vm (가상 머신)은 간단한 처리 요구 사항으로 HDInsight를 시작 하는 데 적합 합니다. 시간당 가격이 더 낮은 F 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다. 자세한 내용은 [Azure HDInsight 클러스터에 적합 한 VM 크기 선택](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size)을 참조 하세요.

## <a name="deprecation"></a>사용 중단

### <a name="g-series-virtual-machine-deprecation"></a>G 시리즈 가상 머신 사용 중단
이 릴리스에서 G 시리즈 Vm은 더 이상 HDInsight에서 제공 되지 않습니다.

### <a name="dv1-virtual-machine-deprecation"></a>Dv1 가상 컴퓨터 사용 중단
이 릴리스에서는 HDInsight에서 Dv1 Vm을 사용 하는 것이 더 이상 사용 되지 않습니다. Dv1에 대 한 고객 요청은 자동으로 Dv2에 제공 됩니다. Dv1 및 Dv2 Vm 간에는 가격 차이가 없습니다.

## <a name="behavior-changes"></a>동작 변경

### <a name="cluster-managed-disk-size-change"></a>클러스터 관리 디스크 크기 변경
HDInsight는 클러스터에 관리 되는 디스크 공간을 제공 합니다. 이 릴리스에서는 새로 만든 클러스터의 각 노드에 대 한 관리 디스크 크기가 128 GB로 변경 되었습니다.

## <a name="upcoming-changes"></a>예정된 변경
향후 릴리스에서는 다음과 같이 변경 됩니다. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합으로 이동
이제 HDInsight는 Azure virtual machines를 사용 하 여 클러스터를 프로 비전 합니다. 12 월부터 HDInsight는 Azure virtual machine scale sets를 대신 사용 합니다. [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)에 대 한 자세한 내용을 참조 하세요.

### <a name="hbase-20-to-21"></a>HBase 2.0 ~ 2.1
예정 된 HDInsight 4.0 릴리스에서 HBase 버전은 버전 2.0에서 2.1로 업그레이드 됩니다.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>A 시리즈 가상 컴퓨터의 ESP 클러스터 사용 중단
A 시리즈 Vm은 비교적 낮은 CPU와 메모리 용량으로 인해 ESP 클러스터 문제를 일으킬 수 있습니다. 향후 릴리스에서는 A 시리즈 Vm이 새 ESP 클러스터를 만드는 데 사용 되지 않습니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속 해 서 클러스터 안정성과 성능을 향상 시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대 한 구성 요소 버전은 변경 되지 않습니다. HDInsight 4.0 ad HDInsight 3.6의 최신 구성 요소 버전은 [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)에서 찾을 수 있습니다.
