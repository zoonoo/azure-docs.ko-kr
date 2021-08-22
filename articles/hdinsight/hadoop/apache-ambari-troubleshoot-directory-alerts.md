---
title: Azure HDInsight의 Apache Ambari 디렉터리 경고
description: HDInsight의 Apache Ambari 디렉터리 경고가 가능한 원인 및 해결 방법에 대한 논의 및 분석입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 2e5e74c49b7654c2ade20e78f7098192649f9385
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300132"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari 디렉터리 경고

이 문서에서는 Azure HDInsight 클러스터와 상호작용할 때의 문제에 대한 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Ambari에서 다음과 유사한 오류가 표시됩니다.

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>원인

영향을 받는 작업자 노드에 Ambari 경고에서 언급된 디렉터리가 없습니다.

## <a name="resolution"></a>해결 방법

영향을 받는 작업자 노드에 존재하지 않는 디렉터리를 수동으로 만듭니다.

1. 관련 작업자 노드에 대한 SSH입니다.

1. 루트 사용자를 가져옵니다: `sudo su`.

1. 필요한 디렉터리를 재귀적으로 만듭니다.

1. 다음 폴더의 소유자 및 그룹을 변경합니다.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Apache Ambari UI에서 사용하지 않으면 경고를 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]