---
title: Azure HDInsight의 아파치 하이브 테이블에서 권한 거부 오류
description: Azure HDInsight에서 아파치 하이브 테이블을 만들려고 할 때 권한 거부 오류
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895151"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 아파치 하이브 테이블을 만들려고 할 때 권한이 오류를 거부했습니다.

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

테이블을 만들려고 할 때 다음과 같은 오류가 표시됩니다.

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

다음 HDFS 저장소 명령을 실행하면 비슷한 오류 메시지가 표시됩니다.

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>원인

아파치 하이브에서 테이블을 만드는 기능은 클러스터의 저장소 계정에 적용된 사용 권한에 의해 결정됩니다. 클러스터 저장소 계정 사용 권한이 올바르지 않으면 테이블을 만들 수 없습니다. 즉, 테이블 만들기에 대 한 올바른 레인저 정책을 가질 수 있습니다., 여전히 볼 수 있는 "권한 거부" 오류.

## <a name="resolution"></a>해결 방법

이는 사용 중인 저장소 컨테이너에 대한 충분한 사용 권한이 없기 때문에 발생합니다. Hive 테이블을 만드는 사용자는 컨테이너에 대해 읽기, 쓰기 및 실행 권한이 필요합니다. 자세한 내용은 [HDP 2.2에서 아파치 레인저를 사용하여 하이브 인증모범 사례를](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)참조하십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
