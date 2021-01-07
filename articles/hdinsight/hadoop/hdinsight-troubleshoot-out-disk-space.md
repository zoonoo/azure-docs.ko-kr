---
title: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족 합니다.
description: Azure HDInsight의 클러스터 노드 디스크 공간 문제 Apache Hadoop 문제 해결
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: f1a994ad07980c67e37d00bffb7e605ed610bb08
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289078"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족 합니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

다음과 유사한 오류 메시지와 함께 작업이 실패할 수 있습니다. `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

또는와 유사한 Apache Ambari 경고를 받을 수 있습니다 `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>원인

Apache Yarn 응용 프로그램 캐시가 사용 가능한 모든 디스크 공간을 사용 했을 수 있습니다. Spark 응용 프로그램이 비효율적으로 실행 될 가능성이 높습니다.

## <a name="resolution"></a>해결 방법

1. Ambari UI를 사용 하 여 디스크 공간이 부족 하 게 되는 노드를 확인 합니다.

1. 문제가 노드의 모든 디스크 공간에 적용 되는 폴더를 확인 합니다. 먼저 노드에 대 한 SSH를 실행 한 다음 `df` 를 실행 하 여 모든 탑재에 대 한 디스크 사용량을 나열 합니다. 일반적으로이는 `/mnt` OSS에서 사용 되는 임시 디스크입니다. 폴더에를 입력 한 다음를 입력 `sudo du -hs` 하 여 폴더 아래에 요약 된 파일 크기를 표시할 수 있습니다. 와 유사한 폴더가 표시 되는 경우 `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` 이는 응용 프로그램이 여전히 실행 되 고 있음을 의미 합니다. RDD 지 속성 또는 중간 순서 섞기 파일이 원인일 수 있습니다.

1. 이 문제를 완화 하기 위해 응용 프로그램에서 사용 하는 디스크 공간을 해제 하는 응용 프로그램을 중지 합니다.

1. 작업자 노드에서 문제가 자주 발생 하는 경우 클러스터에서 YARN 로컬 캐시 설정을 튜닝할 수 있습니다.

    YARN--> Configs--> Advanced로 이동 하 여 Ambari UI를 엽니다.  
    사용자 지정 yarn-site.xml 섹션에 다음 2 개의 속성을 추가 하 고 저장 합니다.

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. 위의 방법으로 문제가 해결 되지 않으면 응용 프로그램을 최적화 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]