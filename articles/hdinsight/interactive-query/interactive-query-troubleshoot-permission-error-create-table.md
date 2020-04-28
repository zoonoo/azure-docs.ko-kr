---
title: Azure HDInsight의 Apache Hive 테이블에 대 한 사용 권한 거부 오류
description: Azure HDInsight에서 Apache Hive 테이블을 만들려고 할 때 사용 권한 거부 오류가 발생 함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895151"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 Apache Hive 테이블을 만들려고 할 때 권한 거부 오류가 발생 함

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

테이블을 만들려고 할 때 다음과 같은 오류가 표시 됩니다.

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

다음 HDFS 저장소 명령을 실행 하는 경우 유사한 오류 메시지가 표시 됩니다.

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>원인

Apache Hive에서 테이블을 만드는 기능은 클러스터의 저장소 계정에 적용 되는 사용 권한에 따라 결정 됩니다. 클러스터 저장소 계정 사용 권한이 잘못 된 경우에는 테이블을 만들 수 없습니다. 즉, 테이블을 만들기 위한 올바른 레인저 정책이 있을 수 있으며 여전히 "권한 거부" 오류가 표시 됩니다.

## <a name="resolution"></a>해결 방법

이 문제는 사용 중인 저장소 컨테이너에 대 한 권한이 부족 하기 때문에 발생 합니다. Hive 테이블을 만드는 사용자에 게는 컨테이너에 대 한 읽기, 쓰기 및 실행 권한이 필요 합니다. 자세한 내용은 [HDP 2.2에서 Apache 레인저를 사용 하는 Hive 권한 부여에 대 한 모범 사례](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
