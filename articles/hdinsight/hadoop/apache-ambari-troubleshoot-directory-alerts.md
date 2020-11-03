---
title: Azure HDInsight의 Apache Ambari 디렉터리 경고
description: HDInsight의 Apache Ambari 디렉터리 경고에 대 한 가능한 원인 및 해결 방법에 대 한 논의 및 분석입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285534"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari 디렉터리 경고

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Ambari에서 다음과 유사한 오류가 표시 됩니다.

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>원인

영향을 받는 작업자 노드에 Ambari alert의 언급 된 디렉터리가 없습니다.

## <a name="resolution"></a>해결 방법

영향을 받는 작업자 노드에 존재 하지 않는 디렉터리를 수동으로 만듭니다.

1. 관련 작업자 노드에 대 한 SSH입니다.

1. 루트 사용자 `sudo su` 가져오기:

1. 필요한 디렉터리를 재귀적으로 만듭니다.

1. 이러한 디렉터리에 대 한 소유자 및 그룹을 변경 합니다.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Apache Ambari UI에서를 사용 하지 않도록 설정 하 고 경고를 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]