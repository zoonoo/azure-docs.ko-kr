---
title: Azure HDInsight 비즈니스 연속성
description: 이 문서에서는 Azure HDInsight 비즈니스 연속성 계획에 대 한 모범 사례, 단일 지역 가용성 및 최적화 옵션에 대 한 개요를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop high availability
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: beb3c54a0ab7f6f063232a1ad49744d99746c589
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893648"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight 비즈니스 연속성

Azure HDInsight 클러스터는 저장소, 데이터베이스, Active Directory, Active Directory Domain Services, 네트워킹 및 Key Vault와 같은 여러 Azure 서비스에 의존 합니다. 잘 디자인 되 고 항상 사용 가능한 내결함성 분석 응용 프로그램은 하나 이상의 이러한 서비스에서 지역 또는 지역 장애를 견딜 수 있도록 충분 한 중복성으로 설계 해야 합니다. 이 문서에서는 비즈니스 연속성 계획에 대 한 모범 사례, 단일 지역 가용성 및 최적화 옵션에 대 한 개요를 제공 합니다.

## <a name="general-best-practices"></a>일반적인 유용한 정보

이 섹션에서는 비즈니스 연속성 계획 중에 고려해 야 할 몇 가지 모범 사례를 설명 합니다.

* 재해가 발생 하는 경우 필요한 최소한의 비즈니스 기능을 결정 합니다. 예를 들어 데이터 변환 계층 (노란색으로 표시) *및* 데이터 처리 계층 (파란색으로 표시)에 대 한 장애 조치 (failover) 기능이 필요 하거나 데이터 서비스 계층에 대 한 장애 조치 (failover)만 필요 하면를 평가 합니다.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="데이터 변환 및 데이터 서비스 계층":::

* 워크 로드, 개발 수명 주기 및 부서에 따라 클러스터를 분할 합니다. 클러스터가 많을 수록 여러 비즈니스 프로세스에 영향을 주는 단일 오류가 발생할 가능성이 줄어듭니다.

* 보조 영역을 읽기 전용으로 설정 합니다. 읽기 및 쓰기 기능을 모두 사용 하는 장애 조치 지역에서는 복잡 한 아키텍처로 이어질 수 있습니다.

* 재해 발생 시 일시적 클러스터를 보다 쉽게 관리할 수 있습니다. 클러스터를 순환할 수 있는 방식으로 작업을 디자인 하 고 클러스터에서 상태를 유지 관리 하지 않습니다.

* 재해가 발생 하 고 새 지역에서 다시 시작 해야 하는 경우 일반적으로 워크 로드가 완료 되지 않습니다. Idempotent 되도록 작업을 설계 합니다.

* 클러스터를 배포 하는 동안 자동화를 사용 하 고 재해 발생 시 신속 하 고 완전 자동화 된 배포를 보장 하기 위해 클러스터 구성 설정이 최대한 스크립팅 되는지 확인 합니다.

* HDInsight의 Azure 모니터링 도구를 사용 하 여 클러스터에서 비정상적인 동작을 감지 하 고 해당 하는 경고 알림을 설정 합니다. 특정 클러스터 유형의 중요 한 성능 메트릭을 수집 하는 미리 구성 된 HDInsight 클러스터 특정 관리 솔루션을 배포할 수 있습니다. 자세한 내용은 [HDInsight에 대 한 Azure 모니터링](./hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조 하세요.  

* 구독, 서비스 또는 지역에 대 한 서비스 문제, 계획 된 유지 관리, 상태 및 보안 권고에 대 한 알림을 받으려면 Azure 상태 경고를 구독 합니다. 문제 원인 및 resolute 에타를 포함 하는 상태 알림은 장애 조치 (failover) 및 장애 복구를 보다 효율적으로 실행 하는 데 도움이 됩니다. 자세한 내용은 [Azure Service Health 설명서](/azure/service-health/)를 참조 하세요.

## <a name="single-region-availability"></a>단일 지역 가용성

기본 HDInsight 시스템에는 다음과 같은 구성 요소가 있습니다. 모든 구성 요소에는 고유한 단일 지역 내결함성 메커니즘이 있습니다.

* Compute (가상 머신): Azure HDInsight 클러스터
* Metastore (s): Azure SQL Database
* 저장소: Azure Data Lake Gen2 또는 Blob storage
* 인증: Azure Active Directory, Azure Active Directory Domain Services, Enterprise Security Package
* 도메인 이름 확인: Azure DNS

Azure Key Vault 및 Azure Data Factory와 같이 사용할 수 있는 다른 선택적 서비스가 있습니다.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="데이터 변환 및 데이터 서비스 계층":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight 클러스터 (계산)

HDInsight는 99.9%의 가용성 SLA를 제공 합니다. 단일 배포에 고가용성을 제공 하기 위해 HDInsight는 기본적으로 고가용성 모드의 많은 서비스와 함께 제공 됩니다. HDInsight의 내결함성 메커니즘은 Microsoft 및 Apache OSS 에코 시스템 고가용성 서비스에서 제공 됩니다.

다음 서비스는 항상 사용 가능 하도록 설계 되었습니다.

#### <a name="infrastructure"></a>인프라

* 활성 및 대기 헤드 노드
* 여러 게이트웨이 노드
* 3 개의 사육 사 쿼럼 노드
* 장애 도메인 및 업데이트 도메인에 의해 배포 된 작업자 노드

#### <a name="service"></a>서비스

* Apache Ambari 서버
* YARN에 대 한 응용 프로그램 타임 라인 서버
* Hadoop MapReduce 용 작업 기록 서버
* Apache Livy
* HDFS
* YARN 리소스 관리자
* HBase Master

자세한 내용은 [Azure HDInsight에서 지원 되는 고가용성 서비스](./hdinsight-high-availability-components.md) 에 대 한 설명서를 참조 하세요.

비즈니스 기능에 영향을 주는 경우에는 항상 치명적인 이벤트를 발생 시킬 수 있습니다. 단일 지역에 있는 다음 서비스 중 하나 이상의 서비스 인시던트로 인해 예상 되는 비즈니스 기능이 손실 될 수도 있습니다.

### <a name="hdinsight-metastore"></a>HDInsight metastore

HDInsight는 99.99%의 SLA를 제공 하는 metastore로 [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) 를 사용 합니다. 동기 복제를 사용 하는 데이터 센터 내에서 3 개의 데이터 복제본이 유지 됩니다. 복제본이 손실 되 면 대체 복제본이 원활 하 게 처리 됩니다. [활성 지역 복제](../azure-sql/database/active-geo-replication-overview.md) 는 최대 4 개의 데이터 센터와 함께 지원 됩니다. 수동 또는 데이터 센터의 장애 조치 (failover)가 있는 경우 계층의 첫 번째 복제본이 자동으로 읽기/쓰기 가능 상태가 됩니다. 자세한 내용은 [Azure SQL Database 비즈니스 연속성](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조 하세요.

### <a name="hdinsight-storage"></a>HDInsight 저장소

HDInsight는 기본 저장소 계층으로 Azure Data Lake Storage Gen2를 권장 합니다. Azure Data Lake Storage Gen2를 포함 하 [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)은 99.9%의 SLA를 제공 합니다. HDInsight는 데이터 센터 내에 3 개의 데이터 복제본이 유지 되 고 복제가 동기식 인 LRS 서비스를 사용 합니다. 복제본이 손실 되 면 복제본이 원활 하 게 처리 됩니다.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) 99.9%의 SLA를 제공 합니다. Active Directory은 여러 수준의 내부 중복성 및 자동 복구 기능을 갖춘 글로벌 서비스입니다. 자세한 내용은 [Microsoft에서 Azure Active Directory의 안정성을 지속적으로 개선](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)하는 방법을 참조 하세요.

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) 99.9%의 SLA를 제공 합니다. Azure AD DS는 전역적으로 분산 된 데이터 센터에서 호스트 되는 고가용성 서비스입니다. 복제본 세트는 azure 지역이 오프 라인 상태가 되는 경우 지리적 재해 복구를 지 원하는 Azure AD DS의 미리 보기 기능입니다. 자세한 내용은 [복제본 집합 개념 및 기능에 대 한](../active-directory-domain-services/concepts-replica-sets.md) 자세한 내용은 Azure Active Directory Domain Services를 참조 하세요.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) 100%의 SLA를 제공 합니다. HDInsight는 도메인 이름 확인을 위해 다양 한 위치에 Azure DNS를 사용 합니다.

## <a name="multi-region-cost-and-complexity-optimizations"></a>다중 지역 비용 및 복잡성 최적화

지역 간 고가용성 재해 복구를 사용 하 여 비즈니스 연속성을 향상 하려면 복잡성이 높고 비용이 높은 아키텍처 디자인이 필요 합니다. 다음 표에서는 총 소유 비용을 늘릴 수 있는 몇 가지 기술 영역에 대해 자세히 설명 합니다.

### <a name="cost-optimizations"></a>비용 최적화

|영역|비용 에스컬레이션 원인|최적화 전략|
|----|------------------------|-----------------------|
|데이터 스토리지|보조 지역의 주 데이터/테이블 복제|큐 레이트 데이터만 복제|
|데이터 송신|아웃 바운드 교차 지역 데이터 전송은 한 가격으로 제공 됩니다. 대역폭 가격 책정 지침 검토|큐 레이트 데이터만 복제 하 여 지역 송신 공간 줄이기|
|클러스터 계산|보조 지역의 추가 HDInsight 클러스터/s|자동화 된 스크립트를 사용 하 여 기본 실패 후 보조 계산을 배포 합니다. 자동 크기 조정을 사용 하 여 보조 클러스터 크기를 최소로 유지 합니다. 저렴 한 VM Sku를 사용 합니다. VM Sku가 할인 될 수 있는 지역에 보조 데이터베이스를 만듭니다.|
|인증 |보조 지역의 다중 사용자 시나리오에서 추가 Azure AD DS 설치가 발생 합니다.|보조 지역에서 다중 사용자 설정이 사용 되지 않도록 합니다.|

### <a name="complexity-optimizations"></a>복잡성 최적화

|영역|복잡성 에스컬레이션의 원인|최적화 전략|
|----|------------------------|-----------------------|
|읽기 쓰기 패턴 |주 및 보조 복제본을 모두 읽고 쓸 수 있도록 요구 |보조 데이터베이스를 읽기 전용으로 디자인|
|0 RPO & RTO |데이터 손실 (RPO = 0) 및 가동 중지 시간 0 (RTO = 0) 요구 |장애 조치 (failover)에 필요한 구성 요소 수를 줄이는 방식으로 RPO 및 RTO를 설계 합니다.|
|비즈니스 기능 |보조에서 주 서버의 전체 비즈니스 기능 요구 |보조에서 비즈니스 기능의 완전 한 최소 하위 집합을 실행할 수 있는지 평가 합니다.|
|연결 |주 데이터베이스의 모든 업스트림 및 다운스트림 시스템이 보조 데이터베이스에도 연결 하도록 요구|보조 연결을 최소 필수 하위 집합으로 제한 합니다.|

## <a name="next-steps"></a>다음 단계

이 문서에서 설명 하는 항목에 대 한 자세한 내용은 다음을 참조 하세요.

* [Azure HDInsight 비즈니스 연속성 아키텍처](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 항상 사용 가능한 솔루션 아키텍처 사례 연구](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight의 Apache Hive 및 HiveQL 무엇 인가요?](./hadoop/hdinsight-use-hive.md)
