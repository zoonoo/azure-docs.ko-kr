---
title: Azure HDInsight에서 Grafana 사용
description: Azure HDInsight에서 아파치 하두롭 클러스터를 사용하여 그라파나 대시보드에 액세스하는 방법을 알아보십시오.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082866"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Azure HDInsight에서 Grafana에 액세스

[Grafana](https://grafana.com/)는 널리 사용되는 오픈 소스 그래프 및 대시보드 작성기입니다. Grafana는 다양한 기능을 수행합니다. 사용자가 사용자 지정 가능하고 공유 가능한 대시보드를 만들 뿐만 아니라 템플릿/스크립팅 대시보드, LDAP 통합, 여러 데이터 원본 등도 제공합니다.

현재 Azure HDInsight에서 그라파나는 스파크, HBase, Kafka 및 대화형 쿼리 클러스터 유형으로 지원됩니다. 엔터프라이즈 보안 팩이 활성화된 클러스터에는 지원되지 않습니다.

Azure 구독이 없는 경우 시작하기 전에 [무료 계정을](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 만드세요.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop 클러스터 만들기

[Azure 포털을 사용하여 아파치 하두롭 클러스터 만들기를](../hdinsight-hadoop-create-linux-clusters-portal.md)참조하십시오. **클러스터 유형의**경우 **스파크,** **카프카,** **HBase**또는 **대화형 쿼리를**선택합니다.

## <a name="access-the-grafana-dashboard"></a>Grafana 대시보드에 액세스

1. 웹 브라우저에서 CLUSTERNAME이 클러스터의 이름인 `https://CLUSTERNAME.azurehdinsight.net/grafana/` 곳으로 이동합니다.

1. Hadoop 클러스터 사용자 자격 증명을 입력합니다.

1. 다음 예제와 같은 Grafana 대시보드가 표시됩니다.

    ![HDInsight 그라파나 웹 대시보드](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HD인사이트 그라파나 대시보드")

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 생성된 클러스터를 삭제합니다.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. 맨 위에 있는 **검색** 상자에 **HDInsight**를 입력합니다.

1. **서비스**에서 **HDInsight 클러스터**를 선택합니다.

1. 표시되는 HDInsight 클러스터 목록에서 만든 클러스터 **옆의** 을 선택합니다.

1. **삭제를 선택합니다.** **예**를 선택합니다.

## <a name="next-steps"></a>다음 단계

HDInsight를 사용하여 데이터를 분석하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight와 아파치 하이브를 사용합니다.](../hadoop/hdinsight-use-hive.md)

* [MAP을 사용하여HDInsight로 Reduce](../hadoop/hdinsight-use-mapreduce.md).

* [HDInsight에 대한 비주얼 스튜디오 하두롭 도구를 사용하여 시작 .](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
