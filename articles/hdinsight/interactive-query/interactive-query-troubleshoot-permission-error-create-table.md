---
title: Azure HDInsight의 Apache Hive 테이블에 대한 권한 거부 오류
description: Azure HDInsight에서 Apache Hive 테이블을 만들려고 할 때의 권한 거부 오류
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: bd9f4f6da3ee82947192041cefe40261a182341b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290650"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive 테이블을 만들려고 할 때의 권한 거부 오류

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

테이블을 만들려고 할 때 다음과 같은 오류가 표시됩니다.

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

다음 HDFS 스토리지 명령을 실행하는 경우 유사한 오류 메시지가 표시됩니다.

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>원인

Apache Hive에서 테이블을 만드는 기능은 클러스터의 스토리지 계정에 적용되는 권한에 따라 결정됩니다. 클러스터 스토리지 계정 권한이 잘못된 경우에는 테이블을 만들 수 없습니다. 즉, 테이블 생성을 위한 레인저 정책이 있어도 “권한 거부” 오류가 표시될 수 있습니다.

## <a name="resolution"></a>해결 방법

해당 문제는 사용 중인 스토리지 컨테이너에 대한 권한이 없기 때문에 발생합니다. Hive 테이블을 만드는 사용자는 컨테이너에 대한 읽기, 쓰기, 실행 권한이 필요합니다. 자세한 내용은 [HDP 2.2에서 Apache 레인저를 사용하는 Hive 권한 부여에 대한 모범 사례](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]