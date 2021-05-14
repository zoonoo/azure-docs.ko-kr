---
title: Azure HDInsight의 Apache Ambari 부실 경고
description: HDInsight의 Apache Ambari 부실 경고의 가능한 원인 및 해결 방법에 대한 논의 및 분석입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 22985f295e504a2d73015f1dc96064f89be47bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866933"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 Apache Ambari 부실 경고

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Apache Ambari UI에서 다음과 같은 경고가 표시될 수 있습니다.

:::image type="content" source="./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png" alt-text="Apache Ambari 부실 경고 예제" border="true":::

## <a name="cause"></a>원인

Ambari 에이전트는 많은 리소스의 상태를 지속적으로 모니터링합니다. 특정 클러스터 속성이 미리 정해진 임계값 내에 있는지 여부를 알리도록 ‘경고’를 구성할 수 있습니다. 각 리소스 검사가 실행된 후, 경고 조건이 충족되면 Ambari 에이전트가 상태를 다시 Ambari 서버에 보고하고 경고를 트리거합니다. 경고 프로필의 간격에 따라 경고가 검사되지 않으면 서버가 ‘Ambari 서버 부실 경고’ 경고를 트리거합니다.

상태 검사를 정의된 간격으로 실행하지 못하는 이유에는 여러 가지가 있습니다.

* 호스트를 많이 사용하는 경우(높은 CPU 사용량) Ambari 에이전트가 제때 경고를 실행하는 데 충분한 시스템 리소스를 확보할 수 없습니다.

* 부하가 많은 기간에 클러스터에서 많은 작업 또는 서비스를 실행하는 중입니다.

* 클러스터에 있는 적은 수의 호스트가 많은 구성 요소를 호스트하고 있으므로 많은 경고를 실행해야 합니다. 구성 요소 수가 많으면 경고 작업이 예약된 간격으로 실행되지 않을 수 있습니다.

## <a name="resolution"></a>해결 방법

Ambari 부실 경고와 관련된 문제를 해결하려면 다음 방법을 시도합니다.

### <a name="increase-the-alert-interval-time"></a>경고 간격 시간 늘리기

클러스터의 응답 시간 및 부하에 따라 개별 경고 간격의 값을 늘릴 수 있습니다.

1. Apache Ambari UI에서 **경고** 탭을 선택합니다.
1. 원하는 경고 정의 이름을 선택합니다.
1. 정의에서 **편집** 을 선택합니다.
1. **검사 간격** 값을 늘린 다음 **저장** 을 선택합니다.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Ambari 서버 경고의 경고 간격 시간 늘리기

1. Apache Ambari UI에서 **경고** 탭을 선택합니다.
1. **그룹** 드롭다운 목록에서 **AMBARI 기본값** 을 선택합니다.
1. **Ambari 서버 경고** 경고를 선택합니다.
1. 정의에서 **편집** 을 선택합니다.
1. **검사 간격** 값을 늘립니다.
1. **간격 승수** 값을 늘린 다음 **저장** 을 선택합니다.

### <a name="disable-and-reenable-the-alert"></a>경고를 비활성화한 후 다시 활성화

부실 경고를 삭제하려면 비활성화한 후 다시 활성화합니다.

1. Apache Ambari UI에서 **경고** 탭을 선택합니다.
1. 원하는 경고 정의 이름을 선택합니다.
1. 정의에서 UI의 맨 오른쪽 부분에 있는 **활성화됨** 을 선택합니다.
1. **확인** 팝업 창에서 **비활성화 확인** 을 선택합니다.
1. 페이지에 표시된 모든 경고 “인스턴스”가 삭제될 때까지 몇 초 정도 기다립니다.
1. 정의에서 UI의 맨 오른쪽 부분에 있는 **비활성화됨** 을 선택합니다.
1. **확인** 팝업 창에서 **활성화 확인** 을 선택합니다.

### <a name="increase-the-alert-grace-period"></a>경고 유예 기간 늘리기

구성된 경고가 해당 일정을 누락했다고 Ambari 에이전트에서 보고하기 전에 유예 기간이 있습니다. 경고가 예약된 시간을 누락했지만 유예 기간 내에 실행된 경우 부실 경고가 생성되지 않습니다.

기본 `alert_grace_period` 값은 5초입니다. /etc/ambari-agent/conf/ambari-agent.ini에서 이 설정을 구성할 수 있습니다. 부실 경고가 정기적으로 발생하는 호스트의 경우 값을 10으로 늘립니다. 그런 다음 Ambari 에이전트를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 여기에 나오지 않았거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)에서 Azure 전문가로부터 답변을 얻습니다.

* Twitter에서 [@AzureSupport](https://twitter.com/azuresupport)에 문의합니다. 이는 사용자 환경을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절한 리소스(답변, 지원, 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출합니다. 이곳으로 이동하려면 포털 메뉴에서 도움말( **?** )을 선택하거나 **도움말 + 지원** 창을 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 

  구독 관리 및 청구에 대한 지원이 Microsoft Azure 구독에 포함되어 있습니다. 기술 지원은 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/)을 통해 이용할 수 있습니다.