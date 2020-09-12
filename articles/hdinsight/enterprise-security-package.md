---
title: Azure HDInsight에 대 한 Enterprise Security Package
description: Azure HDInsight의 Enterprise Security Package 구성 요소 및 버전에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 91fa6a8da555d0b0cc79b262a83306c1f72aa68a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567098"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Azure HDInsight에 대 한 Enterprise Security Package

엔터프라이즈 보안은 HDInsight 클러스터에서 클러스터 워크플로 생성의 일부로 추가할 수 있는 선택적 패키지입니다. 엔터프라이즈 보안 패키지는 다음을 지원합니다.

* 인증을 위한 Active Directory와 통합

    이전에는 로컬 관리자 사용자 및 로컬 SSH 사용자를 사용 하 여 HDInsight 클러스터를 만들었습니다. 로컬 관리 사용자는 모든 파일, 폴더, 테이블 및 열에 액세스할 수 있습니다.  Enterprise Security Package에서 HDInsight를 Azure Active Directory Domain Services와 통합 하 여 역할 기반 액세스 제어를 사용 하도록 설정 합니다.

    자세한 내용은 다음을 참조하세요.

    * [도메인에 가입된 HDInsight 클러스터를 사용하는 Apache Hadoop 보안 소개](./domain-joined/hdinsight-security-overview.md)

    * [HDInsight에서 Azure 도메인에 가입된 Apache Hadoop 클러스터 계획](./domain-joined/apache-domain-joined-architecture.md)

    * [도메인 가입 샌드박스 환경 구성](./domain-joined/apache-domain-joined-configure.md)

    * [Azure Active Directory Domain Services를 사용하여 도메인에 가입된 HDInsight 클러스터 구성](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* 데이터에 대한 권한 부여

  * Hive, Spark SQL 및 Yarn 큐의 권한 부여를 위해 Apache Ranger와 통합합니다.
  * 파일 및 폴더에 대한 액세스 제어를 설정할 수 있습니다.

    자세한 내용은 [도메인에 가입 된 HDInsight에서 Apache Hive 정책 구성](./domain-joined/apache-domain-joined-run-hive.md) 을 참조 하세요.

* 감사 로그를 보고 액세스 및 구성된 정책을 모니터링합니다.

## <a name="supported-cluster-types"></a>지원되는 클러스터 형식

현재 다음과 같은 클러스터 형식만이 엔터프라이즈 보안 패키지를 지원합니다.

* Hadoop(HDInsight 3.6에만 해당)
* Spark
* Kafka
* HBase
* 대화형 쿼리

## <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 지원

엔터프라이즈 보안 패키지는 Azure Data Lake Storage를 기본 스토리지 및 추가 기능 스토리지 둘 다로 사용할 수 있도록 지원합니다.

## <a name="pricing-and-service-level-agreement-sla"></a>가격 책정 및 SLA (서비스 수준 계약)

엔터프라이즈 보안 패키지의 가격 책정 및 SLA에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
* [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
* [Azure HDInsight 버전과 관련된 Hortonworks 릴리스 정보](./hortonworks-release-notes.md)
* [HDInsight의 Apache 구성 요소](./hdinsight-component-versioning.md)
