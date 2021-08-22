---
title: Apache Hive View에 오류 메시지가 표시되지 않음 - Azure HDInsight
description: Azure HDInsight 클러스터에 대한 세부 정보 없이 Apache Hive View에서 쿼리가 실패합니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2f80a966b3d326d367d376354bc83f7d1d935812
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290853"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Hive View에 쿼리 오류 메시지가 표시되지 않음

이 문서에서는 Azure HDInsight 클러스터에서 Interactive Query 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Hive View의 쿼리 오류 메시지가 추가 정보 없이 다음과 같이 표시됩니다.

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>원인

경우에 따라 쿼리 실패의 오류 메시지가 너무 커서 Hive View 메인 페이지에 표시되지 않을 수 있습니다.

## <a name="resolution"></a>해결 방법

Hive_view의 오른쪽 위 모퉁이에 있는 알림 탭을 선택하여 전체 Stacktrace와 오류 메시지를 확인합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]