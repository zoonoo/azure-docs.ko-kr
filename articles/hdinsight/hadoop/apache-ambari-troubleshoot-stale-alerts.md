---
title: Azure HDInsight에서 아파치 암바리 부실 경고
description: HDInsight에서 아파치 암바리 부실 경고에 대한 가능한 이유와 솔루션에 대한 토론 및 분석.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539113"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 아파치 암바리 부실 경고

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

아파치 암바리 UI에서 다음과 같은 경고가 표시될 수 있습니다.

![아파치 암바리 부실 경고 예](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>원인

Ambari 에이전트는 지속적으로 많은 자원의 상태를 모니터링합니다. 특정 클러스터 *속성이* 미리 결정된 임계값 내에 있는지 여부를 알려주도록 경고를 구성할 수 있습니다. 각 리소스 검사가 실행된 후 경고 조건이 충족되면 Ambari 에이전트는 상태를 Ambari 서버에 다시 보고하고 경고를 트리거합니다. 경고 프로필의 간격에 따라 경고가 확인되지 않으면 서버는 *Ambari 서버 부실 경고 경고를 트리거합니다.*

상태 확인이 정의된 간격으로 실행되지 않는 데는 여러 가지 이유가 있습니다.

* 호스트는 사용량이 많고(CPU 사용량이 높음) Ambari 에이전트가 정시에 경고를 실행하기에 충분한 시스템 리소스를 얻을 수 없습니다.

* 클러스터는 부하가 많은 기간 동안 많은 작업이나 서비스를 실행하느라 중입니다.

* 클러스터의 소수의 호스트가 많은 구성 요소를 호스팅하므로 많은 경고를 실행하는 데 필요합니다. 구성 요소 수가 많으면 경고 작업이 예약된 간격을 놓칠 수 있습니다.

## <a name="resolution"></a>해결 방법

다음 방법을 사용하여 Ambari 오래된 경고 문제를 해결합니다.

### <a name="increase-the-alert-interval-time"></a>경고 간격 시간 늘리기

클러스터의 응답 시간 및 로드에 따라 개별 경고 간격의 값을 늘릴 수 있습니다.

1. 아파치 암바리 UI에서 **경고 탭을 선택합니다.**
1. 원하는 경고 정의 이름을 선택합니다.
1. 정의에서 **편집을**선택합니다.
1. 간격 **확인** 값을 늘인 다음 **저장을**선택합니다.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Ambari 서버 경고에 대한 경고 간격 시간 증가

1. 아파치 암바리 UI에서 **경고 탭을 선택합니다.**
1. **그룹** 드롭다운 목록에서 **AMBARI 기본값을**선택합니다.
1. **Ambari 서버 경고 경고를 선택합니다.**
1. 정의에서 **편집을**선택합니다.
1. 간격 **검사** 값을 늘립니다.
1. 간격 **승수** 값을 늘인 다음 **저장을**선택합니다.

### <a name="disable-and-reenable-the-alert"></a>경고를 비활성화하고 다시 사용할 수 있습니다.

오래된 경고를 삭제하려면 비활성화한 다음 다시 사용할 수 있습니다.

1. 아파치 암바리 UI에서 **경고 탭을 선택합니다.**
1. 원하는 경고 정의 이름을 선택합니다.
1. 정의에서 UI의 맨 오른쪽 부분에서 **사용 옵션을** 선택합니다.
1. **확인** 팝업 창에서 사용 **안 함 확인을**선택합니다.
1. 페이지에 표시된 모든 경고 "인스턴스"가 지워질 때까지 몇 초 동안 기다립니다.
1. 정의에서 UI의 맨 오른쪽 부분에서 **사용 안 함(사용 안 함)을** 선택합니다.
1. **확인** 팝업 창에서 활성화 **확인을**선택합니다.

### <a name="increase-the-alert-grace-period"></a>경고 유예 기간 늘리기

Ambari 에이전트가 구성된 경고가 일정을 놓쳤다고 보고하기 전에 유예 기간이 있습니다. 경고가 예약된 시간을 놓쳤지만 유예 기간 내에 실행된 경우 부실 경고가 생성되지 않습니다.

기본값은 `alert_grace_period` 5초입니다. /etc/ambari 에이전트/conf/ambari-agent.ini에서 이 설정을 구성할 수 있습니다. 오래된 경고가 정기적으로 발생하는 호스트의 경우 값을 10으로 늘려 보십시오. 그런 다음 Ambari 에이전트를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 언급되지 않았거나 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 더 많은 지원을 확인하십시오.

* [Azure 커뮤니티 지원에서](https://azure.microsoft.com/support/community/)Azure 전문가의 답변을 얻을 수 있습니다.

* 트위터에 [@AzureSupport](https://twitter.com/azuresupport) 연결합니다. 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출합니다. 포털 메뉴에서 도움말 **(? )을**선택하거나 **도움말 + 지원** 창을 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법을 참조하세요.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 

  구독 관리 및 청구에 대한 지원은 Microsoft Azure 구독에 포함되어 있습니다. 기술 지원은 [Azure 지원 계획을](https://azure.microsoft.com/support/plans/)통해 사용할 수 있습니다.
