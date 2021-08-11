---
title: Azure HDInsight 비즈니스 연속성
description: 이 문서에서는 Azure HDInsight 비즈니스 연속성 계획의 모범 사례, 단일 지역 가용성, 최적화 옵션에 관한 개요를 제공합니다.
keywords: hadoop high availability
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933315"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight 비즈니스 연속성

Azure HDInsight 클러스터는 스토리지, 데이터베이스, Active Directory, Active Directory Domain Services, 네트워킹, Key Vault 등의 많은 Azure 서비스를 사용합니다. 잘 디자인되고, 가용성이 뛰어나고, 내결함성이 있는 분석 애플리케이션은 이러한 서비스 중 하나 이상에서 국가별 또는 지역 중단을 견딜 수 있는 충분한 중복을 가지도록 디자인해야 합니다. 이 문서에서는 비즈니스 연속성 계획의 모범 사례, 단일 지역 가용성, 최적화 옵션에 관한 개요를 제공합니다.

## <a name="general-best-practices"></a>일반적인 유용한 정보

이 섹션에서는 비즈니스 연속성 계획 중에 고려해야 할 몇 가지 모범 사례를 설명합니다.

* 재해가 발생하는 경우 필요한 최소한의 비즈니스 기능을 결정하고 필요한 이유를 확인합니다. 예를 들어, 데이터 변환 계층(노란색으로 표시) ‘및’ 데이터 서비스 계층(파란색으로 표시)에 대한 장애 조치(failover) 기능이 필요한지, 아니면 데이터 서비스 계층에 대한 장애 조치만 필요한지 평가합니다.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="데이터 변환 및 데이터 서비스 계층":::

* 워크로드, 개발 수명 주기, 부서에 따라 클러스터를 분할합니다. 클러스터가 많을수록 여러 가지 비즈니스 프로세스에 영향을 주는 단일 대규모 실패가 발생할 가능성이 감소합니다.

* 보조 지역을 읽기 전용으로 설정합니다. 읽기 및 쓰기 기능을 둘 다 사용하는 장애 조치 지역은 복잡한 아키텍처가 될 수 있습니다.

* 재해 발생 시 임시 클러스터를 더 쉽게 관리할 수 있습니다. 클러스터를 순환할 수 있고 클러스터에서 상태를 유지 관리하지 않는 방식으로 워크로드를 디자인합니다.

* 종종 워크로드는 재해 발생 시 완료되지 않은 상태이므로 새 지역에서 다시 시작해야 합니다. 실제로 Idempotent가 되도록 워크로드를 디자인합니다.

* 클러스터를 배포하는 동안 자동화를 사용하고 재해 발생 시 신속하고 완전 자동화된 배포를 보장하기 위해 클러스터 구성 설정이 최대한 스크립팅되도록 합니다.

* HDInsight에서 Azure 모니터링 도구를 사용하여 클러스터에서 비정상적인 동작을 탐지하고 해당하는 경고 알림을 설정합니다. 특정 클러스터 유형의 중요한 성능 메트릭을 수집하는 미리 구성된 HDInsight 클러스터 관련 관리 솔루션을 배포할 수 있습니다. 자세한 내용은 [HDInsight에 대한 Azure 모니터링](./hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조하세요.  

* 구독, 서비스 또는 지역과 관련된 서비스 문제, 계획된 유지 관리, 상태, 보안 공지에 관한 알림을 받으려면 Azure 상태 경고를 구독합니다. 문제 원인과 해결 ETA를 포함하는 상태 알림은 장애 조치와 장애 복구를 더 효율적으로 실행하는 데 도움이 됩니다. 자세한 내용은 [Azure Service Health 설명서](../service-health/index.yml)를 참조하세요.

## <a name="single-region-availability"></a>단일 지역 가용성

기본 HDInsight 시스템에는 다음 구성 요소가 있습니다. 모든 구성 요소에는 고유한 단일 지역 내결함성 메커니즘이 있습니다.

* 컴퓨팅(가상 머신): Azure HDInsight 클러스터
* 메타스토어: Azure SQL Database
* 스토리지: Azure Data Lake Gen2 또는 Blob Storage
* 인증: Azure Active Directory, Azure Active Directory Domain Services, Enterprise Security Package
* 도메인 이름 확인: Azure DNS

Azure Key Vault, Azure Data Factory와 같은 다른 선택적 서비스를 사용할 수 있습니다.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight 구성 요소":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight 클러스터(컴퓨팅)

HDInsight는 99.9%의 가용성 SLA를 제공합니다. 단일 배포에서 고가용성을 제공하기 위해 HDInsight는 기본적으로 고가용성 모드에 있는 많은 서비스와 함께 제공됩니다. HDInsight의 내결함성 메커니즘은 Microsoft 및 Apache OSS 에코시스템 고가용성 서비스에서 둘 다 제공됩니다.

다음 서비스는 고가용성을 제공하도록 디자인되었습니다.

#### <a name="infrastructure"></a>인프라

* 활성 및 대기 헤드 노드
* 여러 게이트웨이 노드
* Zookeeper 쿼럼 노드 3개
* 장애 도메인과 업데이트 도메인에서 배포된 작업자 노드

#### <a name="service"></a>서비스

* Apache Ambari 서버
* YARN용 애플리케이션 타임라인 서버
* Hadoop MapReduce용 작업 기록 서버
* Apache Livy
* HDFS
* YARN Resource Manager
* HBase Master

자세한 내용은 [Azure HDInsight에서 지원하는 고가용성 서비스](./hdinsight-high-availability-components.md) 설명서를 참조하세요.

비즈니스 기능에 영향을 주는 치명적인 이벤트가 항상 발생하는 것은 아닙니다. 단일 지역에 있는 다음 서비스 중 하나 이상의 서비스 인시던트로 인해 필요한 비즈니스 기능이 손실될 수도 있습니다.

### <a name="hdinsight-metastore"></a>HDInsight 메타스토어

HDInsight는 99.99%의 SLA를 제공하는 메타스토어로 [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)를 사용합니다. 동기 복제를 사용하여 데이터 센터 내에서 세 개의 데이터 복제본이 유지됩니다. 복제본이 손실되면 대체 복제본이 원활하게 제공됩니다. [활성 지역 복제](../azure-sql/database/active-geo-replication-overview.md)는 최대 네 개의 데이터 센터에서 기본적으로 지원됩니다. 수동 또는 데이터 센터를 통해 장애 조치가 수행되면 계층 구조의 첫 번째 복제본이 자동으로 읽기/쓰기 가능 상태가 됩니다. 자세한 내용은 [Azure SQL Database 비즈니스 연속성](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조하세요.

### <a name="hdinsight-storage"></a>HDInsight 스토리지

HDInsight는 기본 스토리지 계층으로 Azure Data Lake Storage Gen2를 권장합니다. Azure Data Lake Storage Gen2를 포함한 [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)는 99.9%의 SLA를 제공합니다. HDInsight는 데이터 센터 내에서 세 개의 데이터 복제본이 유지되고 복제가 동기화되는 LRS 서비스를 사용합니다. 복제본이 손실되면 복제본이 원활하게 제공됩니다.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/)는 99.9%의 SLA를 제공합니다. Active Directory는 여러 수준의 내부 중복과 자동 복구 기능을 제공하는 글로벌 서비스입니다. 자세한 내용은 [Microsoft에서 Azure Active Directory의 안정성을 지속적으로 개선](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)하는 방법을 참조하세요.

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure AD DS(Active Directory Domain Services)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)는 99.9%의 SLA를 제공합니다. Azure AD DS는 전 세계에 분산된 데이터 센터에서 호스트되는 고가용성 서비스입니다. 복제본 세트는 Azure 지역이 오프라인으로 전환되는 경우 지리적 재해 복구를 가능하게 하는 Azure AD DS의 미리 보기 기능입니다. 자세한 내용은 [Azure Active Directory Domain Services에 대한 복제본 세트 개념 및 기능](../active-directory-domain-services/concepts-replica-sets.md)을 참조하세요.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/)는 100%의 SLA를 제공합니다. HDInsight는 도메인 이름 확인을 위해 다양한 위치에서 Azure DNS를 사용합니다.

## <a name="multi-region-cost-and-complexity-optimizations"></a>다중 지역 비용 및 복잡성 최적화

지역 간 고가용성 재해 복구를 사용하여 비즈니스 연속성을 향상하려면 복잡성과 비용이 더 높은 아키텍처 디자인이 필요합니다. 다음 표에서는 총 소유 비용을 늘릴 수 있는 몇 가지 기술 영역을 자세히 설명합니다.

### <a name="cost-optimizations"></a>비용 최적화

|영역|비용 에스컬레이션의 원인|최적화 전략|
|----|------------------------|-----------------------|
|데이터 스토리지|보조 지역의 주 데이터/테이블 복제|큐레이팅된 데이터만 복제|
|데이터 송신|아웃바운드 지역 간 데이터 전송은 대가가 따릅니다. 대역폭 가격 책정 지침 검토|큐레이팅된 데이터만 복제하여 지역 송신 공간 줄이기|
|클러스터 컴퓨팅|보조 지역의 추가 HDInsight 클러스터/초|자동화된 스크립트를 사용하여 주 실패 후 보조 컴퓨팅을 배포합니다. 자동 스케일링을 사용하여 보조 클러스터 크기를 최솟값으로 유지합니다. 더 저렴한 VM SKU를 사용합니다. VM SKU가 할인될 수 있는 지역에 보조 복제본을 만듭니다.|
|인증 |보조 지역의 다중 사용자 시나리오로 인해 추가 Azure AD DS 설정 발생|보조 지역에서 다중 사용자 설정을 방지합니다.|

### <a name="complexity-optimizations"></a>복잡성 최적화

|영역|복잡성 에스컬레이션의 원인|최적화 전략|
|----|------------------------|-----------------------|
|읽기 쓰기 패턴 |주 및 보조 지역에서 둘 다 읽고 쓸 수 있도록 요구 |보조 지역을 읽기 전용으로 디자인합니다.|
|Zero RPO & RTO |데이터 손실(RPO=0) 및 가동 중지 시간 없음(RTO=0) 요구 |장애 조치에 필요한 구성 요소 수를 줄이는 방식으로 RPO와 RTO를 디자인합니다.|
|비즈니스 기능 |보조 지역에서 주 지역의 전체 비즈니스 기능 요구 |보조 지역에서 비즈니스 기능 중 최소한의 중요한 하위 집합을 실행할 수 있는지 평가합니다.|
|연결 |주 지역의 모든 업스트림 및 다운스트림 시스템이 보조 지역에도 연결되도록 요구|보조 연결을 최소한의 중요한 하위 집합으로 제한합니다.|

## <a name="next-steps"></a>다음 단계

이 문서에서 설명한 항목에 관해 자세히 알아보려면 다음을 참조하세요.

* [Azure HDInsight 비즈니스 연속성 아키텍처](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 고가용성 솔루션 아키텍처 사례 연구](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight의 Apache Hive 및 HiveQL이란?](./hadoop/hdinsight-use-hive.md)