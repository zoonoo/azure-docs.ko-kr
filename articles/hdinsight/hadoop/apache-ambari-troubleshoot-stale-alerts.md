---
title: Azure HDInsight의 Apache Ambari 부실 경고
description: HDInsight의 Apache Ambari 오래 된 경고에 대 한 가능한 원인 및 해결 방법에 대 한 논의 및 분석입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77539113"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari 부실 경고

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제점

Apache Ambari UI에서 다음과 같은 경고가 표시 될 수 있습니다.

![Apache Ambari 부실 경고 예제](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>원인

Ambari 에이전트는 많은 리소스의 상태를 지속적으로 모니터링 합니다. 특정 클러스터 속성이 미리 정해진 임계값 내에 있는지 여부를 알리도록 *경고* 를 구성할 수 있습니다. 각 리소스 확인을 실행 한 후 경고 조건이 충족 되 면 Ambari 에이전트가 상태를 다시 Ambari 서버에 보고 하 고 경고를 트리거합니다. 경고 프로필의 간격에 따라 경고가 확인 되지 않으면 서버가 *Ambari 서버 부실 경고* 경고를 트리거합니다.

상태 검사를 정의 된 간격으로 실행 하지 못하는 이유는 여러 가지가 있습니다.

* 호스트의 사용량이 많은 경우 (높은 CPU 사용량) Ambari 에이전트가 경고를 실행 하는 데 충분 한 시스템 리소스를 가져올 수 없습니다.

* 부하가 높은 기간 동안 클러스터에서 많은 작업 또는 서비스를 실행 하는 중입니다.

* 클러스터에 적은 수의 호스트가 많은 구성 요소를 호스팅하고 있으므로 많은 경고를 실행 하는 데 필요 합니다. 구성 요소 수가 클 경우 경고 작업의 예약 된 간격이 누락 될 수 있습니다.

## <a name="resolution"></a>해결 방법

Ambari 오래 된 경고와 관련 된 문제를 해결 하려면 다음 방법을 시도 하십시오.

### <a name="increase-the-alert-interval-time"></a>경고 간격 시간 늘리기

클러스터의 응답 시간 및 부하에 따라 개별 경고 간격의 값을 늘릴 수 있습니다.

1. Apache Ambari UI에서 **경고** 탭을 선택 합니다.
1. 원하는 경고 정의 이름을 선택 합니다.
1. 정의에서 **편집**을 선택 합니다.
1. **확인 간격** 값을 늘리고 **저장**을 선택 합니다.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Ambari 서버 경고에 대 한 경고 간격 시간 늘리기

1. Apache Ambari UI에서 **경고** 탭을 선택 합니다.
1. **그룹** 드롭다운 목록에서 **AMBARI Default**를 선택 합니다.
1. **Ambari 서버 경고** 경고를 선택 합니다.
1. 정의에서 **편집**을 선택 합니다.
1. **확인 간격** 값을 늘립니다.
1. **간격 승수** 값을 늘리고 **저장**을 선택 합니다.

### <a name="disable-and-reenable-the-alert"></a>경고를 사용 하지 않도록 설정 하 고 다시 활성화

오래 된 경고를 삭제 하려면 사용 하지 않도록 설정한 다음 다시 활성화 합니다.

1. Apache Ambari UI에서 **경고** 탭을 선택 합니다.
1. 원하는 경고 정의 이름을 선택 합니다.
1. 정의에서 UI의 맨 오른쪽 부분에 있는 **사용** 을 선택 합니다.
1. **확인** 팝업 창에서 **사용 안 함 확인**을 선택 합니다.
1. 페이지에 표시 된 모든 경고 "인스턴스"를 지울 때까지 몇 초 정도 기다립니다.
1. 정의에서 UI의 맨 오른쪽 부분에 있는 **사용 안 함** 을 선택 합니다.
1. **확인** 팝업 창에서 **사용 확인**을 선택 합니다.

### <a name="increase-the-alert-grace-period"></a>경고 유예 기간 늘리기

Ambari 에이전트가 구성 된 경고가 해당 일정을 누락 하는 것으로 보고 하기 전에 유예 기간이 있습니다. 경고가 예약 된 시간을 누락 했지만 유예 기간 내에 실행 된 경우 오래 된 경고가 생성 되지 않습니다.

기본값은 `alert_grace_period` 5 초입니다. /Etc/ambari-agent/conf/ambari-agent.ini에서이 설정을 구성할 수 있습니다. 오래 된 경고가 정기적으로 발생 하는 호스트의 경우 값을 10으로 늘립니다. 그런 다음 Ambari 에이전트를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

여기에서 문제를 언급 하지 않았거나 해결할 수 없는 경우 다음 채널 중 하나를 방문 하 여 더 많은 지원을 받을 수 있습니다.

* Azure [커뮤니티 지원](https://azure.microsoft.com/support/community/)에서 azure 전문가의 답변을 받으세요.

* Twitter를 사용 하 여 연결 [@AzureSupport](https://twitter.com/azuresupport) 합니다. 사용자 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출 합니다. 가져오려면 포털 메뉴에서 도움말 (**?**)을 선택 하거나 **도움말 + 지원** 창을 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 

  구독 관리 및 청구에 대 한 지원이 Microsoft Azure 구독에 포함 되어 있습니다. 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 통해 제공 됩니다.
