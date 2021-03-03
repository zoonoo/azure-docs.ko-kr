---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop 구성 요소 및 버전에 대해 자세히 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 166c52660c9f47b8ecfce10f7e9c3b81ff067f35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699371"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight 버전

HDInsight는 클러스터에 배포 된 패키지에 Apache Hadoop 환경 구성 요소 및 HDInsight 플랫폼을 번들로 묶습니다. 자세한 내용은 [HDInsight 버전 관리의 작동 방식](hdinsight-overview-versioning.md)을 참조 하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전

이 표에는 Azure Portal에서 사용할 수 있는 HDInsight 버전 및 PowerShell, CLI 및 .NET SDK와 같은 기타 배포 방법이 나와 있습니다.

| HDInsight 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |2018년 9월 24일 | | |예 |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017년 4월 4일      | * 6 월 30 일 2021 |2021 년 6 월 30 일 |예 |

* 특정 HDInsight 3.6 클러스터 형식에 대 한 지원 기간이 연장 됩니다. [HDInsight 3.6 구성 요소 버전](hdinsight-36-component-versioning.md)을 참조 하세요.

## <a name="release-notes"></a>릴리스 정보

HDInsight의 최신 버전과 관련된 추가 릴리스 정보는 [HDInsight 릴리스 정보](hdinsight-release-notes.md)를 참조하세요.

## <a name="support-options-for-hdinsight-versions"></a>HDInsight 버전에 대 한 지원 옵션

HDInsight는 Microsoft 고객 서비스 및 지원에서 HDInsight 버전이 지원 되는 기간으로 정의 되는 표준 지원을 제공 합니다.

**지원 만료** 란 Microsoft에서 더 이상 특정 HDInsight 버전에 대한 지원을 제공하지 않음을 의미합니다. 클러스터를 만들기가 Azure Portal을 통해 더 이상 제공되지 않습니다.

**사용 중지** 란 HDInsight 버전의 기존 클러스터가 계속해서 그대로 실행됨을 의미합니다. 이 버전의 새 클러스터는 CLI 및 SDK를 포함하여 어떤 방법으로도 만들 수 없습니다. 수동 크기 조정 및 자동 크기 조정과 같은 기타 제어 평면 기능은 사용 중지 날짜 후에도 작동 하지 않을 수 있습니다. 사용 중지된 버전에는 지원이 제공되지 않습니다.

## <a name="versioning-considerations"></a>버전 관리 고려 사항
- 클러스터가 이미지를 사용 하 여 배포 되 면 해당 클러스터가 최신 이미지 버전으로 자동 업그레이드 되지 않습니다. 새 클러스터를 만들 때 최신 이미지 버전이 배포 됩니다.
- 고객은 새 HDInsight 버전을 사용할 때 응용 프로그램이 제대로 실행 되는지 테스트 하 고 유효성을 검사 해야 합니다.
- HDInsight는 사전 통지 없이 기본 버전을 변경할 권리를 보유 합니다. 버전 종속성이 있는 경우 클러스터를 만들 때 HDInsight 버전을 지정 합니다.
- Hdinsight는 HDInsight 버전을 사용 중지 하기 전에 OSS 구성 요소 버전을 사용 중지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)