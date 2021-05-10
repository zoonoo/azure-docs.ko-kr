---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop 구성 요소 및 버전에 대해 자세히 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726586"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight 버전

HDInsight는 Apache Hadoop 환경 구성 요소와 HDInsight 플랫폼을 클러스터에 배포된 패키지로 번들합니다. 자세한 내용은 [HDInsight 버전 관리의 작동 방식](hdinsight-overview-versioning.md)을 참조하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전

다음 표에는 Azure Portal 및 PowerShell, CLI 및 .NET SDK와 같은 다른 배포 방법을 통해서 사용할 수 있는 HDInsight 버전이 나열되어 있습니다.

| HDInsight 버전 | VM OS | 릴리스 날짜| 지원 유형 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |2018년 9월 24일 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |예 |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017년 4월 4일      | [기본](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | 표준 지원 만료 - 2021년 6월 30일 <br> 기본 지원 만료 - 2022년 4월 3일 |2022년 4월 4일 |예 |

\* 2021년 7월 1일부터 Microsoft는 특정 HDI 3.6 클러스터 형식에 대한 기본 지원을 제공합니다. [HDInsight 3.6 구성 요소 버전](hdinsight-36-component-versioning.md)을 참조하세요.

## <a name="release-notes"></a>릴리스 정보

HDInsight의 최신 버전과 관련된 추가 릴리스 정보는 [HDInsight 릴리스 정보](hdinsight-release-notes.md)를 참조하세요.

## <a name="support-options-for-hdinsight-versions"></a>HDInsight 버전의 지원 옵션

지원은 Microsoft 고객 지원에서 HDInsight 버전을 지원하는 기간으로 정의됩니다. HDInsight는 다음과 같은 두 가지 지원 형식을 제공합니다. 
- **표준 지원** 은 Microsoft에서 HDInsight 클러스터에 대한 업데이트 및 지원을 제공하는 기간입니다.  
    완전히 지원되는 최신 버전을 사용하여 솔루션을 빌드하는 것이 좋습니다. 
- **기본 지원** 은 Microsoft에서 HDInsight 리소스 공급자에게 제한된 서비스를 제공하는 기간입니다. HDInsight 이미지와 OSS(오픈 소스 소프트웨어) 구성 요소는 서비스되지 않습니다.   중요한 보안 픽스만 HDInsight 클러스터에서 패치됩니다.  
  Microsoft는 버전이 기본 지원 상태일 때 새 클러스터를 만들거나 새로운 솔루션을 빌드하는 것을 권장하지 않습니다. 기존 클러스터를 완전히 지원되는 최신 버전으로 마이그레이션하는 것이 좋습니다. 

**지원 만료** 란 Microsoft에서 더 이상 특정 HDInsight 버전에 대한 지원을 제공하지 않음을 의미합니다. 클러스터를 만들기가 Azure Portal을 통해 더는 제공되지 않습니다.

**사용 중지** 란 HDInsight 버전의 기존 클러스터가 계속해서 그대로 실행됨을 의미합니다. 이 버전의 새 클러스터는 CLI 및 SDK를 포함하여 어떤 방법으로도 만들 수 없습니다. 수동 스케일링 및 자동 스케일링과 같은 다른 컨트롤 플레인 기능도 사용 중지 날짜 후에는 작동하지 않을 수 있습니다. 사용 중지된 버전에는 지원이 제공되지 않습니다.

## <a name="versioning-considerations"></a>버전 관리 고려 사항
- 클러스터가 이미지와 함께 배포되면 해당 클러스터는 최신 이미지 버전으로 자동 업그레이드되지 않습니다. 새 클러스터를 만들 때 최신 이미지 버전이 배포됩니다.
- 고객은 새 HDInsight 버전을 사용할 때 애플리케이션이 제대로 실행되는지 테스트하고 유효성을 검사해야 합니다.
- HDInsight는 사전 알림 없이 기본 버전을 변경할 권리를 보유합니다. 버전 종속성이 있는 경우 클러스터를 만들 때 HDInsight 버전을 지정합니다.
- HDInsight는 HDInsight 버전을 사용 중지하기 전에 OSS 구성 요소 버전을 사용 중지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
