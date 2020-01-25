---
title: Azure HDInsight의 Apache Ambari 부실 경고
description: HDInsight의 부실 Apache Ambari 경고에 대 한 가능한 원인 및 해결 방법에 대 한 논의 및 분석입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722812"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari 부실 경고

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Ambari UI에서 다음 이미지와 비슷한 경고가 표시 될 수 있습니다.

![Apache Ambari 부실 경고 예제](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>원인

Ambari 에이전트는 상태 검사를 지속적으로 실행 하 여 많은 리소스의 상태를 모니터링 합니다. 각 경고는 미리 정의 된 시간 간격으로 실행 되도록 구성 됩니다. 각 경고를 실행 한 후 Ambari 에이전트는 상태를 Ambari 서버에 다시 보고 합니다. 이 시점에서 Ambari 서버는 경고가 적시에 실행 되지 않은 것을 감지 하면 "Ambari 서버 경고"를 트리거합니다. 상태 검사를 정의 된 간격으로 실행 하지 못하는 이유는 여러 가지가 있습니다.

* 호스트 사용량이 많은 경우 (높은 CPU) Ambari 에이전트가 적절 한 시간 내에 경고를 실행 하는 데 충분 한 시스템 리소스를 얻지 못할 수 있습니다.

* 부하가 많은 동안 클러스터에서 많은 작업/서비스를 실행 하는 중입니다.

* 클러스터의 호스트 수가 많으면 많은 구성 요소를 호스트할 수 있으므로 많은 경고를 실행 해야 합니다. 구성 요소 수가 클 경우 경고 작업의 예약 된 간격이 누락 될 수 있습니다.

## <a name="resolution"></a>해상도

### <a name="increase-alert-interval-time"></a>알림 간격 시간 늘리기

클러스터의 응답 시간 및 부하에 따라 개별 경고 간격의 값을 늘릴 수 있습니다.

1. Apache Ambari UI에서 **경고** 탭을 선택 합니다.
1. 원하는 경고 정의 이름을 선택 합니다.
1. 정의에서 **편집**을 선택 합니다.
1. **확인 간격** 값을 원하는 대로 수정한 다음, **저장**을 선택 합니다.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Ambari 서버 경고에 대 한 경고 간격 시간 늘리기

1. Apache Ambari UI에서 **경고** 탭을 선택 합니다.
1. **그룹** 드롭다운 목록에서 **AMBARI Default**를 선택 합니다.
1. 경고 **Ambari 서버 경고**를 선택 합니다.
1. 정의에서 **편집**을 선택 합니다.
1. **확인 간격** 값을 원하는 대로 수정 합니다.
1. **간격 승수** 값을 원하는 대로 수정 하 고 **저장**을 선택 합니다.

### <a name="disable-and-enable-the-alert"></a>경고 사용 및 사용 안 함

경고를 사용 하지 않도록 설정한 다음 다시 사용 하도록 설정 하 여 오래 된 경고를 삭제할 수 있습니다.

1. Apache Ambari UI에서 **경고** 탭을 선택 합니다.
1. 원하는 경고 정의 이름을 선택 합니다.
1. 정의에서 맨 오른쪽에 있는 **사용** 을 선택 합니다.
1. **확인** 팝업에서 **사용 안 함 확인**을 선택 합니다.
1. 페이지에 표시 된 모든 경고 "인스턴스"가 선택 취소 되 면 몇 초 정도 기다립니다.
1. 정의에서 맨 오른쪽에 있는 **사용 안 함** 을 선택 합니다.
1. **확인** 팝업에서 **사용 확인**을 선택 합니다.

### <a name="increase-alert-grace-time"></a>경고 유예 시간 늘리기

Ambari agent에서 구성 된 경고의 일정이 누락 되었음을 보고 하기 전에 유예 시간이 적용 되었습니다. 경고가 예약 된 시간을 누락 했지만 경고 유예 시간 내에 트리거된 경우에도 오래 된 경고는 발생 하지 않습니다.

기본 `alert_grace_period` 값은 5 초입니다. 이 `alert_grace_period` 설정은 `/etc/ambari-agent/conf/ambari-agent.ini`에서 구성할 수 있습니다. 부실 경고가 정기적으로 발생 하는 호스트의 경우 값을 10으로 늘립니다. 그런 다음 Ambari 에이전트를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
