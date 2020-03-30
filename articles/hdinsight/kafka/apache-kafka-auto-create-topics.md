---
title: Apache Kafka에서 자동 토픽 생성 사용 - Azure HDInsight
description: 자동으로 토픽을 만들도록 HDInsight에서 Apache Kafka를 구성하는 방법에 대해 알아봅니다. Ambari를 통해 auto.create.topics.enable을 true로 설정하거나 또는 PowerShell 또는 Resource Manager 템플릿을 통해 클러스터를 만드는 동안에 Kafka를 구성할 수 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242370"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>자동으로 토픽을 작성하기 위해 HDInsight에서 Apache Kafka를 구성하는 방법

기본적으로 HDInsight의 [아파치 카프카는](https://kafka.apache.org/) 자동 토픽 생성을 활성화하지 않습니다. [Apache Ambari](https://ambari.apache.org/)를 사용하여 기존 클러스터에 자동 토픽 작성을 사용할 수 있습니다. Azure Resource Manager 템플릿을 사용하여 새로운 Kafka 클러스터를 만들 때 자동 토픽 만들기도 사용할 수 있습니다.

## <a name="apache-ambari-web-ui"></a>Apache Ambari 웹 UI

Ambari 웹 UI를 통해 기존 클러스터에서 자동 토픽 생성을 사용할 수 있게 하려면 다음 단계를 사용합니다.

1. Azure [포털에서](https://portal.azure.com)Kafka 클러스터를 선택합니다.

1. **클러스터 대시보드에서** **Ambari 홈을**선택합니다.

    ![클러스터 대시 보드로 선택한 포털 이미지](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    메시지가 표시되면 클러스터에 대한 로그인(관리자) 자격 증명을 사용하여 인증을 받습니다. 또는 카프카 클러스터의 이름에서 `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` 바로 Amabri에 연결할 수 있습니다.

1. 페이지 왼쪽 목록에서 Kafka 서비스를 선택합니다.

    ![아파치 암바리 서비스 목록 탭](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. 페이지 중간의 Config를 선택합니다.

    ![아파치 암바리 서비스 구성 탭](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. 필터 필드에 `auto.create` 값을 입력합니다.

    ![아파치 암바리 검색 필터 필드](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    이렇게 하면 속성 목록이 필터링되고 `auto.create.topics.enable` 설정이 표시됩니다.

1. 의 값을 `auto.create.topics.enable` 변경한 다음 **저장을** `true`선택합니다. 메모를 추가한 다음 다시 **저장을** 선택합니다.

    ![auto.create.topics.enable 항목의 이미지](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Kafka 서비스를 선택하고 __다시 시작__을 선택한 후 __영향을 받는 모든 서비스 다시 시작__을 선택합니다. 메시지가 표시되면 __모두 다시 시작 확인을__선택합니다.

    ![아파치 암바리 는 영향을받는 모든 다시 시작](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> 또한 Ambari REST API를 통해 Ambari 값을 설정할 수 있습니다. 현재 구성을 검색하고 수정하는 등 여러 REST 호출을 해야 하므로 일반적으로 더 어렵습니다. 자세한 내용은 [아파치 암바리 REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) 문서를 사용하여 HDInsight 클러스터 관리를 참조하십시오.

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

Azure Resource Manager 템플릿을 사용하여 Kafka 클러스터를 만들 때 `kafka-broker`에 추가하여 직접 `auto.create.topics.enable`을 설정할 수 있습니다. 다음 JSON 코드 조각은 이 값을 `true`로 설정하는 방법을 보여줍니다.

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight에서 Apache Kafka에 대한 자동 화제 생성을 활성화하는 방법을 배웠습니다. Kafka 작업에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [Apache Kafka 로그 분석](apache-kafka-log-analytics-operations-management.md)
* [Apache Kafka 클러스터 간 데이터 복제](apache-kafka-mirroring.md)
