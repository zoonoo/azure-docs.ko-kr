---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop 구성 요소 및 버전에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/09/2020
ms.openlocfilehash: 87c3e2439d1b4bef4a58663e3ea06d8bb7cb9b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192538"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전은?

Microsoft Azure HDInsight 및 Enterprise Security Package의 [Apache Hadoop](https://hadoop.apache.org/) 환경 구성 요소 및 버전에 대해 알아봅니다. 또한 HDInsight에서 Hadoop 구성 요소 버전을 확인하는 방법을 알아봅니다.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>각 HDInsight 버전에서 사용할 수 있는 Apache Hadoop 구성 요소

Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 2017 4 월 4 일, Azure HDInsight에서 사용 하는 기본 클러스터 버전은 3.6입니다.

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 나열되어 있습니다.

> [!NOTE]  
> HDInsight 서비스의 기본 버전은 예고 없이 변경될 수 있습니다. 버전 종속성이 있는 경우 Azure PowerShell 및 Azure 클래식 CLI를 사용하여 .NET SDK에서 클러스터를 만들 때 HDInsight 버전을 지정합니다.

| 구성 요소              | HDInsight 4.0 | HDInsight 3.6(기본값)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 및 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on ESP 대화형 쿼리) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 *(아래 참고 사항 참조) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> 시스템 성능 고려 사항으로 인해 2019년 3월에 Kafka 0.10 버전에 대한 지원이 만료되었습니다.

## <a name="check-for-current-hadoop-component-version-information"></a>현재 Hadoop 구성 요소 버전 정보 확인

HDInsight 클러스터 버전과 연결 된 Hadoop 환경 구성 요소 버전은 HDInsight에 대 한 업데이트로 변경 될 수 있습니다. Hadoop 구성 요소를 확인하고 클러스터에 대해 사용 중인 버전을 확인하려면 Ambari REST API를 사용합니다. **GetComponentInformation** 명령은 서비스 구성 요소에 대한 정보를 검색합니다. 자세한 내용은 [Apache Ambari 설명서](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 참조하세요.

### <a name="release-notes"></a>릴리스 정보

최신 HDInsight 버전과 관련된 추가 릴리스 정보에 대해서는 [HDInsight 릴리스 정보](hdinsight-release-notes.md) 를 참조하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight 버전에 대 한 만료 및 사용 중지 지원

**지원 만료** 는 Microsoft에서 더 이상 지정 된 HDInsight 버전에 대 한 지원을 제공 하지 않음을 의미 합니다. 클러스터를 만들기 위해 Azure Portal를 통해 더 이상 사용할 수 없게 됩니다. 그러나 이러한 버전은 Azure CLI 또는 다양 한 Sdk를 사용 하 여 계속 만들 수 있습니다.

HDInsight **버전의 사용** 중지는 기존 클러스터가 그대로 계속 실행 됨을 의미 합니다. 그러나이 버전의 새 클러스터는 CLI 및 Sdk를 비롯 한 어떤 방법으로도 만들 수 없습니다. 다른 제어 평면 기능 (예: 수동 크기 조정 및 자동 크기 조정)은 버전이 사용 중지 된 후에도 작동 하지 않을 수 있습니다. 사용 중지 된 버전에는 지원을 사용할 수 없습니다.

다음 표에서는 HDInsight 버전을 보여줍니다. 알려진 경우 지원 만료 날짜와 사용 중지 날짜도 나와 있습니다.

### <a name="available-versions"></a>사용 가능한 버전

다음 표에는 Azure Portal에서 사용할 수 있는 HDInsight 버전 및 PowerShell 및 .NET SDK와 같은 기타 배포 방법이 나와 있습니다.

| HDInsight 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal에서 사용 가능 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018년 9월 24일 | | |예 |예 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017년 4월 4일 | 2020 년 12 월 31 일 |2020 년 12 월 31 일 |예 |예 |

Spark 2.1, 2.2 & Kafka 1.0 지원은 년 6 월 30 2020 일에 만료 됩니다.

> [!NOTE]  
> 버전에 대한 지원이 만료되면 Microsoft Azure Portal을 통해 사용하지 못할 수도 있습니다. 그러나 클러스터 버전은 버전 사용 중지 날짜까지 Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 명령의 `Version` 매개 변수 및 .NET SDK를 통해 계속 사용할 수 있습니다.

### <a name="retired-versions"></a>사용 중지된 버전

다음 표에는 Azure Portal **에서 사용할 수 없는 HDInsight** 버전이 나와 있습니다.

| HDInsight 버전 | HDP 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal에 대한 사용 가용성 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016년 9월 30일 |2017년 9월 5일 |2018년 6월 28일 |예 |아니요 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016년 3월 29일 |2016년 12월 29일 |2018년 1월 9일 |예 |아니요 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015년 12월 2일 |2016년 6월 27일 |2018년 7월 31일 |예 |아니요 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015년 12월 2일 |2016년 6월 27일 |2017년 7월 31일 |예 |아니요 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 또는 Windows Server 2012 R2 |2015년 2월 18일 |2016년 3월 1일 |2017년 4월 1일 |예 |아니요 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014년 6월 24일 |2015년 5월 18일 |2016년 6월 30일 |예 |아니요 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014년 2월 11일 |2014년 9월 17일 |2015년 6월 30일 |예 |아니요 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013년 10월 28일 |2014년 5월 12일 |2015년 5월 31일 |예 |아니요 |
| HDInsight 1.6 |HDP 1.1 | |2013년 10월 28일 |2014년 4월 26일 |2015년 5월 31일 |아니요 |아니요 |

> [!NOTE]  
> 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 버전 2.1 이상에 배포됩니다. HDInsight 버전 1.6 클러스터에서는 사용할 수 없습니다.

## <a name="enterprise-security-package-for-hdinsight"></a>HDInsight용 엔터프라이즈 보안 패키지

엔터프라이즈 보안은 HDInsight 클러스터에서 클러스터 워크플로 생성의 일부로 추가할 수 있는 선택적 패키지입니다. 엔터프라이즈 보안 패키지는 다음을 지원합니다.

- 인증을 위한 Active Directory와 통합

    이전에는 로컬 관리자 사용자 및 로컬 SSH 사용자를 사용 하 여 HDInsight 클러스터를 만들었습니다. 로컬 관리 사용자는 모든 파일, 폴더, 테이블 및 열에 액세스할 수 있습니다.  Enterprise Security Package에서 HDInsight를 Active Directory와 통합 하 여 역할 기반 액세스 제어를 사용 하도록 설정 합니다. 여기에는 온-프레미스 Active Directory Azure Active Directory Domain Services 포함 됩니다. 또는 IaaS 가상 컴퓨터에서 Active Directory 합니다. 클러스터의 도메인 관리자는 사용자에 게 자신의 회사 (도메인) 사용자 이름 및 암호를 사용 하도록 부여할 수 있습니다.

    자세한 내용은 다음을 참조하세요.

    - [도메인에 가입된 HDInsight 클러스터를 사용하는 Apache Hadoop 보안 소개](./domain-joined/hdinsight-security-overview.md)
    - [HDInsight에서 Azure 도메인에 가입된 Apache Hadoop 클러스터 계획](./domain-joined/apache-domain-joined-architecture.md)
    - [도메인 가입 샌드박스 환경 구성](./domain-joined/apache-domain-joined-configure.md)
    - [Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- 데이터에 대한 권한 부여

  - Hive, Spark SQL 및 Yarn 큐의 권한 부여를 위해 Apache Ranger와 통합합니다.
  - 파일 및 폴더에 대한 액세스 제어를 설정할 수 있습니다.

    자세한 내용은 다음을 참조하세요.

  - [도메인에 가입된 HDInsight에서 Apache Hive 정책 구성](./domain-joined/apache-domain-joined-run-hive.md)

- 감사 로그를 보고 액세스 및 구성된 정책을 모니터링합니다.

### <a name="supported-cluster-types"></a>지원되는 클러스터 형식

현재 다음과 같은 클러스터 형식만이 엔터프라이즈 보안 패키지를 지원합니다.

- Hadoop(HDInsight 3.6에만 해당)
- Spark
- Kafka
- HBase
- 대화형 쿼리

### <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 지원

엔터프라이즈 보안 패키지는 Azure Data Lake Storage를 기본 스토리지 및 추가 기능 스토리지 둘 다로 사용할 수 있도록 지원합니다.

### <a name="pricing-and-service-level-agreement-sla"></a>가격 책정 및 SLA (서비스 수준 계약)

엔터프라이즈 보안 패키지의 가격 책정 및 SLA에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 클러스터 버전의 서비스 수준 약정

SLA (서비스 수준 계약)는 _지원 기간_으로 정의 됩니다. 지원 기간은 HDInsight 버전이에서 `Microsoft Customer Service and Support`지 원하는 기간입니다. 버전에 전달 된 _지원 만료 날짜가_있는 경우 HDInsight 클러스터는 지원 기간을 벗어납니다. HDInsight 버전 X (최신 X + 1 버전을 사용할 수 있는 경우)에 대 한 만료는 나중에 지원 됩니다.  

- 수식 1: HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
- 수식 2: HDInsight 클러스터 버전 X+1이 Azure Portal에서 사용 가능한 날짜에 90일을 추가합니다.

사용 중지 _날짜_ 는 HDInsight에서 클러스터 버전을 만들 수 없는 날짜입니다. 2017 년 7 월 31 일부 터 사용 중지 날짜 이후에는 HDInsight 클러스터의 크기를 조정할 수 없습니다.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>클러스터에 대한 기본 노드 구성 및 가상 머신 크기

클러스터에 대해 선택할 가상 컴퓨터 Sku에 대 한 자세한 내용은 [Azure HDInsight 클러스터 구성 세부 정보](hdinsight-supported-node-configuration.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
- [Azure HDInsight 버전과 관련 된 Hortonworks 릴리스 정보](./hortonworks-release-notes.md)
