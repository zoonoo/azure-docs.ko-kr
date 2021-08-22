---
title: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족합니다
description: Azure HDInsight의 Apache Hadoop 클러스터 노드 디스크 공간 문제 해결.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: 76810596eb8ec3f9880c04036149a068e9acb45f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299774"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 클러스터 노드의 디스크 공간이 부족합니다

이 문서에서는 Azure HDInsight 클러스터와 상호작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`과 유사한 오류 메시지와 함께 작업이 실패할 수 있습니다.

또는 `local-dirs usable space is below configured utilization percentage`과 유사한 Apache Ambari 경고를 받을 수 있습니다.

## <a name="cause"></a>원인

Apache Yarn 응용 프로그램 캐시가 사용 가능한 모든 디스크 공간을 사용했을 수 있습니다. Spark 응용 프로그램이 비효율적으로 실행될 가능성이 높습니다.

## <a name="resolution"></a>해결 방법

1. Ambari UI를 사용하여 디스크 공간이 부족한 노드를 확인합니다.

1. 모든 디스크 공간에 적용되는 문제가 있는 노드의 폴더를 확인합니다. 먼저 노드에 대한 SSH를 실행 한 다음 `df`를 실행하여 모든 마운트에 대한 디스크 사용량을 나열합니다. 일반적으로 이는 OSS에서 사용되는 임시 디스크인 `/mnt`입니다. 폴더에 입력한 다음 `sudo du -hs`를 입력하여 폴더 아래에 요약된 파일 크기를 표시할 수 있습니다. `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`과 유사한 폴더가 표시되는 경우, 이는 응용 프로그램이 여전히 실행되고 있음을 의미합니다. RDD 지속성 또는 중간 순서 섞기 파일이 원인일 수 있습니다.

1. 이 문제를 완화하기 위해 응용 프로그램을 중지하여 응용 프로그램에서 사용하는 디스크 공간을 해제합니다.

1. 작업자 노드에서 문제가 자주 발생하는 경우, 클러스터에서 YARN 로컬 캐시 설정을 조정할 수 있습니다.

    Ambari UI를 열고 YARN --> Configs --> Advanced으로 이동합니다.  
    사용자 지정 yarn-site.xml 섹션에 다음 2개의 속성을 추가하고 저장합니다.

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. 위의 방법으로 문제가 해결되지 않으면 응용 프로그램을 최적화합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]