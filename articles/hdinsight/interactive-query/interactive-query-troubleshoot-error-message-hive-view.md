---
title: Apache Hive 보기에 표시 되지 않는 오류 메시지-Azure HDInsight
description: Azure HDInsight 클러스터에 대 한 세부 정보가 없는 Apache Hive 보기에서 쿼리가 실패 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288944"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Hive 보기에 쿼리 오류 메시지가 표시 되지 않음

이 문서에서는 Azure HDInsight 클러스터에서 대화형 쿼리 구성 요소를 사용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Hive 뷰 쿼리 오류 메시지는 추가 정보 없이 다음과 같이 표시 됩니다.

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>원인

경우에 따라 쿼리 오류의 오류 메시지가 너무 커서 Hive 보기 주 페이지에 표시 되지 않을 수 있습니다.

## <a name="resolution"></a>해결 방법

Hive_view의 오른쪽 위 모퉁이에 있는 알림 탭을 선택 하 여 전체 Stacktrace 및 오류 메시지를 확인 합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]