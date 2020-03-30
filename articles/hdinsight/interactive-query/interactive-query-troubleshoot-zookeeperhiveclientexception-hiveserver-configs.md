---
title: 아파치 하이브 제플린 통역 오류 - Azure HDInsight
description: 아파치 제플린 하이브 JDBC 인터프리터는 Azure HDInsight의 잘못된 URL을 가리키고 있다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895053"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>시나리오: 아파치 하이브 제플린 통역사가 Azure HDInsight에서 사육사 오류를 제공합니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 하이브 LLAP 클러스터에서 Zeppelin 인터프리터는 쿼리를 실행하려고 할 때 다음과 같은 오류 메시지를 제공합니다.

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>원인

제플린 하이브 JDBC 인터프리터가 잘못된 URL을 가리키고 있습니다.

## <a name="resolution"></a>해결 방법

1. Hive 구성 요소 요약으로 이동하여 "Hive JDBC URL"을 클립보드에 복사합니다.

1. `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`로 이동합니다.

1. JDBC 설정 편집: 1단계에서 복사된 Hive JDBC URL로 hive.url 값을 업데이트합니다.

1. 저장한 다음 쿼리를 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
