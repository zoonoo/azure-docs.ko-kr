---
title: Azure 모니터 경고 및 알림 문제 해결
description: Azure 모니터 경고 및 가능한 솔루션에 대한 일반적인 문제입니다.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132338"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Azure 모니터 경고의 문제 해결 

이 문서에서는 Azure Monitor 경고에서 일반적인 문제에 대해 설명합니다.

Azure Monitor 경고는 모니터링 데이터에 중요한 조건이 발견되면 사전에 알려줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대한 자세한 내용은 [Microsoft Azure의 경고 개요를](alerts-overview.md)참조하십시오.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>내 경고에 대한 조치 또는 알림이 예상대로 작동하지 않음

Azure Portal에서 경고가 발생했지만 일부 작업 또는 알림에 문제가 있는 경우 다음 섹션을 참조하세요.

## <a name="did-not-receive-expected-email"></a>예상 이메일을 받지 못했습니다.

Azure Portal에서 경고가 발생했지만 구성한 전자 메일을 받지 못한 경우 다음 단계를 따르십시오. 

1. ** [작업 규칙에](alerts-action-rules.md)의해 전자 메일이 표시되지 않았습니다.** 

    포털에서 실행된 경고를 클릭하여 확인하고 기록 탭에서 억제된 [작업 그룹을](action-groups.md)확인합니다. 

    ![경고 작업 규칙 억제 기록](media/alerts-troubleshoot/history-action-rule.png)

1. **작업 유형 "전자 메일 Azure 리소스 관리자 역할"입니까?**

    이 작업은 구독 범위에 있는 Azure Resource Manager 역할 할당 및 *사용자*형식에 대해서만 살펴봅니다.  리소스 수준 또는 리소스 그룹 수준이 아니라 구독 수준에서 역할을 할당했는지 확인합니다.

1. **이메일 서버와 사서함이 외부 전자 메일을 수락하고 있습니까?**

    다음 세 주소의 이메일이 차단되지 않았는지 확인합니다.
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    내부 메일 링리스트 또는 메일 목록은 외부 이메일 주소의 전자 메일을 차단하는 것이 일반적입니다. 위의 이메일 주소를 화이트리스트에 추가해야 합니다.  
    테스트하려면 메일링 리스트가 아닌 일반 직장 전자 메일 주소를 작업 그룹에 추가하고 경고가 해당 전자 메일에 도착하는지 확인합니다. 

1. **받은 편지함 규칙 또는 스팸 필터에 의해 처리된 이메일인가요?** 

    해당 이메일을 삭제하거나 사이드 폴더로 이동하는 받은 편지함 규칙이 없는지 확인합니다. 예를 들어 받은 편지함 규칙은 주제의 특정 발신자 또는 특정 단어를 잡을 수 있습니다.

    또한 다음을 확인합니다.
    
      - 이메일 클라이언트의 스팸 설정(예: Outlook, Gmail)
      - 전자 메일 서버의 발신자 제한 / 스팸 설정 / 격리 설정 (예 : Exchange, Office 365, G-suite)
      - 이메일 보안 어플라이언스의 설정(바라쿠다, 시스코 등)이 있는 경우 

1. **실수로 작업 그룹에서 구독을 취소했습니까?** 

    경고 이메일은 작업 그룹의 구독을 취소할 수 있는 링크를 제공합니다. 이 작업 그룹에서 실수로 구독을 취소했는지 확인하려면 다음 중 하나를 수행하십시오.

    1. 포털에서 작업 그룹을 열고 상태 열을 확인합니다.

    ![작업 그룹 상태 열](media/alerts-troubleshoot/action-group-status.png)

    2. 이메일에서 수신 거부 확인을 검색합니다.

    ![경고 작업 그룹에서 구독 취소](media/alerts-troubleshoot/unsubscribe-action-group.png)

    다시 구독하려면 수신취소 확인 이메일의 링크를 사용하거나 작업 그룹에서 이메일 주소를 제거한 다음 다시 추가합니다. 
 
1. **단일 이메일 주소로 가는 많은 이메일로 인해 제한된 평가를 받았습니까?** 

    이메일은 각 이메일 주소로 매시간 100개 이하의 이메일로 [제한됩니다.](alerts-rate-limiting.md) 이 임계값을 통과하면 추가 이메일 알림이 삭제됩니다.  이메일 주소의 요금이 일시적으로 제한되었음을 나타내는 메시지를 받았는지 확인합니다. 
 
   ![이메일 요금 제한](media/alerts-troubleshoot/email-paused.png)

   속도 제한 없이 대량의 알림을 받으려면 웹후크, 논리 앱, Azure 함수 또는 자동화 런북과 같은 다른 작업을 사용하는 것이 좋습니다. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>예상되는 SMS, 음성 통화 또는 푸시 알림을 받지 못했습니다.

포털에서 해고된 경고를 볼 수 있지만 SMS, 음성 통화 또는 푸시 알림을 받지 못한 경우 다음 단계를 따르십시오. 

1. **작업 [규칙에](alerts-action-rules.md)의해 작업이 억제되나요?** 

    포털에서 실행된 경고를 클릭하여 확인하고 기록 탭에서 억제된 [작업 그룹을](action-groups.md)확인합니다. 

    ![경고 작업 규칙 억제 기록](media/alerts-troubleshoot/history-action-rule.png)

   의도하지 않은 경우 작업 규칙을 수정, 비활성화 또는 삭제할 수 있습니다.
 
1. **SMS / 음성 : 전화 번호가 정확합니까?**

   국가 코드 또는 전화 번호의 오타에 대한 SMS 작업을 확인합니다. 
 
1. **SMS / 음성 : 당신은 속도 제한 되었습니까?** 

    SMS 및 음성 통화는 전화 번호당 5분마다 1회 이하의 알림으로 제한됩니다. 이 임계값을 통과하면 알림이 삭제됩니다. 

      - 음성 통화 - 통화 내역을 확인하고 이전 5분 동안 Azure에서 다른 통화가 있었는지 확인합니다. 
      - SMS - 전화 번호가 속도 제한이 있음을 나타내는 메시지가 SMS 기록을 확인합니다. 

    속도 제한 없이 대량의 알림을 받으려면 웹후크, 논리 앱, Azure 함수 또는 자동화 런북과 같은 다른 작업을 사용하는 것이 좋습니다. 
 
1. **SMS: 실수로 작업 그룹에서 탈퇴했습니까?**

    SMS 기록을 열고 이 특정 작업 그룹(disable action_group_short_name 회신 사용) 또는 모든 작업 그룹(STOP 회신 사용)에서 SMS 배달을 옵트아웃했는지 확인합니다. 다시 구독하려면 관련 SMS 명령(action_group_short_name 활성화 또는 시작)을 보내거나 작업 그룹에서 SMS 작업을 제거한 다음 다시 추가합니다.  자세한 내용은 [작업 그룹의 SMS 경고 동작을](alerts-sms-behavior.md)참조하십시오.

1. **실수로 휴대 전화의 알림을 차단 했습니까?**

   대부분의 휴대폰을 사용하면 특정 전화 번호 또는 단축 코드에서 통화 또는 SMS를 차단하거나 특정 앱(예: Azure 모바일 앱)의 푸시 알림을 차단할 수 있습니다. 휴대폰에서 알림을 실수로 차단했는지 확인하려면 휴대폰 운영 체제 및 모델에 대한 설명서를 검색하거나 다른 전화 및 전화 번호로 테스트합니다. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>트리거할 다른 유형의 작업이 예상되었지만 

포털에서 해고된 경고가 표시되지만 구성된 작업이 트리거되지 않은 경우 다음 단계를 따르십시오. 

1. **작업 규칙에 의해 작업이 억제되었습니까?** 

    포털에서 실행된 경고를 클릭하여 확인하고 기록 탭에서 억제된 [작업 그룹을](action-groups.md)확인합니다. 

    ![경고 작업 규칙 억제 기록](media/alerts-troubleshoot/history-action-rule.png)
 
    의도하지 않은 경우 작업 규칙을 수정, 비활성화 또는 삭제할 수 있습니다. 

1. **웹후크가 트리거되지 않았습니까?**

    1. **원본 IP 주소가 차단되었습니까?**
    
       웹후크가 호출되는 [IP 주소를](action-groups.md#action-specific-information) 화이트리스트로 나열합니다.

    1. **웹후크 끝점이 제대로 작동합니까?**

       구성한 웹후크 끝점이 올바른지, 끝점이 올바르게 작동하는지 확인합니다. 웹후크 로그를 확인하거나 코드를 계측하여 조사할 수 있습니다(예: 들어오는 페이로드 로그). 

    1. **슬랙 또는 마이크로소프트 팀을 호출합니까?**  
    이러한 각 끝점은 특정 JSON 형식을 기대합니다. 대신 논리 앱 작업을 구성하려면 [다음 지침을](action-groups-logic-app.md) 따릅니다.

    1. **웹후크가 응답하지 않거나 반환된 오류가 되었습니까?** 

        웹후크 응답의 시간 제한 기간은 10초입니다. 웹후크 호출은 다음 HTTP 상태 코드가 반환될 때 최대 두 번 다시 시도됩니다: 408, 429, 503, 504, 또는 HTTP 끝점이 응답하지 않는 경우. 10초 후에 첫 번째 다시 시도가 발생합니다. 두 번째 및 최종 재시도는 100초 후에 수행됩니다. 두 번째 다시 시도가 실패하면 모든 작업 그룹에 대해 엔드포인트가 30분 동안 다시 호출되지 않습니다.

## <a name="action-or-notification-happened-more-than-once"></a>작업 또는 알림이 두 번 이상 발생했습니다. 

경고(예: 전자 메일 또는 SMS)에 대한 알림을 두 번 이상 받았거나 경고의 작업(예: 웹후크 또는 Azure 함수)이 여러 번 트리거된 경우 다음 단계를 따르십시오. 

1. **정말 같은 경고인가요?** 

    경우에 따라 비슷한 경고가 여러 번 동시에 발생합니다. 따라서 동일한 경고가 작업을 여러 번 트리거한 것처럼 보일 수 있습니다. 예를 들어 이벤트 상태 필드를 필터링하지 않음으로써 이벤트가 시작되었을 때와 이벤트가 완료된 시기(성공 또는 실패)를 모두 발생하도록 활동 로그 경고 규칙을 구성할 수 있습니다. 

    이러한 작업 또는 알림이 다른 경고에서 왔는지 확인하려면 타임스탬프 및 경고 ID 또는 상관 관계 ID와 같은 경고 세부 정보를 검사합니다. 또는 포털에서 해고된 경고 목록을 확인합니다. 이 경우 경고 규칙 논리를 조정하거나 경고 소스를 구성해야 합니다. 

1. **작업이 여러 작업 그룹에서 반복됩니까?** 

    경고가 발생하면 각 작업 그룹이 독립적으로 처리됩니다. 따라서 트리거된 작업 그룹(예: 전자 메일 주소)이 여러 작업 그룹에 나타나면 작업 그룹당 한 번 호출됩니다. 

    트리거된 작업 그룹을 확인하려면 경고 기록 탭을 확인합니다. 경고 규칙에 정의된 작업 그룹과 작업 규칙에 의해 경고에 추가된 작업 그룹이 모두 표시됩니다. 

    ![여러 작업 그룹에서 반복되는 작업](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>작업 또는 알림에 예기치 않은 콘텐츠가 있습니다.

경고를 받았지만 일부 필드가 누락되었거나 올바르지 않은 것으로 판단되면 다음 단계를 따르세요. 

1. **작업에 대한 올바른 형식을 선택했습니까?** 

    각 작업 유형(전자 메일, 웹후크 등)에는 기본 형식, 레거시 형식 및 [최신 공통 스키마 형식의](alerts-common-schema.md)두 가지 형식이 있습니다. 작업 그룹을 만들 때 작업그룹별로 원하는 형식을 지정합니다. 

    예를 들어 웹후크 작업의 경우: 

    ![웹후크 작업 스키마 옵션](media/alerts-troubleshoot/webhook.png)

    작업 수준에서 지정된 형식이 예상되는 형식인지 확인합니다. 예를 들어 경고(웹후크, 함수, 논리 앱 등)에 응답하여 하나의 형식을 예상하지만 나중에 다른 형식을 지정한 작업에서 코드를 개발했을 수 있습니다.  

    또한 [활동 로그 경고,](activity-log-alerts-webhook.md) [로그 검색 경고(응용](alerts-log-webhook.md) 프로그램 인사이트 및 로그 분석 모두), [메트릭 경고,](alerts-metric-near-real-time.md#payload-schema) [공통 경고 스키마](alerts-common-schema-definitions.md)및 더 이상 사용되지 않음된 클래식 메트릭 경고에 대한 페이로드 형식(JSON)을 [확인합니다.](alerts-webhooks.md)

 
1. **활동 로그 경고: 활동 로그에서 정보를 사용할 수 있습니까?** 

    [활동 로그 경고는](activity-log-alerts.md) Azure 리소스 생성, 업데이트 또는 삭제, 서비스 상태 및 리소스 상태 이벤트 또는 Azure Advisor 및 Azure Policy의 결과와 같이 Azure 활동 로그에 기록된 이벤트를 기반으로 하는 경고입니다. 활동 로그에 따라 경고를 받았지만 필요한 필드가 없거나 올바르지 않은 경우 먼저 활동 로그 자체의 이벤트를 확인합니다. Azure 리소스가 해당 활동 로그 이벤트에서 찾고 있는 필드를 작성하지 않은 경우 해당 필드는 해당 경고에 포함되지 않습니다. 

## <a name="action-rule-is-not-working-as-expected"></a>작업 규칙이 예상대로 작동하지 않습니다. 

포털에서 실행된 경고가 표시되지만 관련 작업 규칙이 예상대로 작동하지 않는 경우 다음 단계를 따르십시오. 

1. **작업 규칙이 활성화되어 있습니까?** 

    작업 규칙 상태 열을 확인하여 관련 작업 역할이 활성화되어 있는지 확인합니다. 

    ![그래픽](media/alerts-troubleshoot/action-rule-status.png) 

    활성화되지 않은 경우 작업 규칙을 선택하고 활성화를 클릭하여 활성화를 활성화할 수 있습니다. 

1. **서비스 상태 경고입니까?** 

    서비스 상태 경고(서비스 모니터 = "서비스 상태")는 작업 규칙의 영향을 받지 않습니다. 

1. **경고에 대한 작업 규칙이 작동했습니까?** 

    작업 규칙이 포털에서 실행된 경고를 클릭하여 경고를 처리했는지 확인하고 기록 탭을 확인합니다.

    다음은 모든 작업 그룹을 억제하는 작업 규칙의 예입니다. 
 
     ![경고 작업 규칙 억제 기록](media/alerts-troubleshoot/history-action-rule.png)

    다음은 다른 작업 그룹을 추가하는 작업 규칙의 예입니다.

    ![여러 작업 그룹에서 반복되는 작업](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **작업 규칙 범위 및 필터가 실행된 경고와 일치합니까?** 

    작업 규칙이 실행되었어야 하지만 그렇지 않았거나 해고되지 않았어야 한다고 생각되는 경우 작업 규칙 범위 및 필터 조건을 주의 깊게 검토하여 실행된 경고의 속성과 비교합니다. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>해고된 경고의 경고 ID를 찾는 방법

특정 해고된 경고에 대한 사례를 열 때(예: 전자 메일 알림을 받지 못한 경우) 경고 ID를 제공해야 합니다. 

이를 찾으려면 다음 단계를 따르십시오.

1. Azure 포털에서 해고된 경고 목록으로 이동하여 특정 경고를 찾습니다. 필터를 사용하여 필터를 찾을 수 있습니다. 

1. 경고를 클릭하여 경고 세부 정보를 엽니다. 

1. 첫 번째 탭(요약 탭)의 경고 필드에서 아래로 스크롤하여 찾을 때까지 복사합니다. 이 필드에는 사용할 수 있는 "클립보드로 복사" 도우미 버튼도 포함되어 있습니다.  

    ![경고 ID 찾기](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Azure 포털에서 작업 규칙을 만들거나 업데이트하거나 삭제하는 문제

[작업 규칙을](alerts-action-rules.md)만들거나 업데이트하거나 삭제하는 동안 오류가 발생하면 다음 단계를 따르십시오. 

1. **사용 권한 오류가 발생했습니까?**  

     [모니터링 기여자 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)또는 작업 규칙 및 경고와 관련된 특정 권한이 있어야 합니다.

1. **작업 규칙 매개 변수를 확인했습니까?**  

    작업 [규칙 설명서](alerts-action-rules.md)또는 [작업 규칙 PowerShell Set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) 명령을 확인합니다. 


## <a name="next-steps"></a>다음 단계
- 로그 경고를 사용하는 경우 [로그 경고 문제 해결도 참조합니다.](alert-log-troubleshoot.md)
- [Azure 포털로](https://portal.azure.com) 돌아가 위의 지침으로 문제를 해결했는지 확인합니다. 
