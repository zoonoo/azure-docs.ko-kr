---
title: 경고 및 알림 Azure Monitor 문제 해결
description: Azure Monitor 경고 및 가능한 해결 방법에 대 한 일반적인 문제
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132338"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Azure Monitor 경고 문제 해결 

이 문서에서는 경고를 Azure Monitor 하는 일반적인 문제에 대해 설명 합니다.

Azure Monitor 경고는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알려 줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대 한 자세한 내용은 [Microsoft Azure의 경고 개요](alerts-overview.md)를 참조 하세요.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>경고에 대 한 작업 또는 알림이 예상 대로 작동 하지 않음

Azure Portal에서 발생 한 경고를 볼 수 있지만 일부 작업 또는 알림에 문제가 있는 경우 다음 섹션을 참조 하십시오.

## <a name="did-not-receive-expected-email"></a>예상 전자 메일을 받지 못했습니다.

Azure Portal에서 발생 한 경고를 볼 수 있지만 구성 된 전자 메일을 받지 못한 경우 다음 단계를 수행 합니다. 

1. ** [작업 규칙](alerts-action-rules.md)에 의해 전자 메일이**표시 되지 않나요? 

    포털에서 발생 한 경고를 클릭 하 여 확인 하 고 표시 되지 않은 [작업 그룹](action-groups.md)의 기록 탭에서 다음을 확인 합니다. 

    ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)

1. **"Email Azure Resource Manager Role" 동작의 유형 인가요?**

    이 작업은 구독 범위 및 *사용자*형식의 Azure Resource Manager 역할 할당만 확인 합니다.  구독 수준에서 역할을 할당 하 고 리소스 수준 또는 리소스 그룹 수준에서 할당 하지 않았는지 확인 합니다.

1. **전자 메일 서버와 사서함이 외부 전자 메일을 수락 하 고 있습니까?**

    이 세 주소의 전자 메일이 차단 되지 않았는지 확인 합니다.
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    일반적으로 내부 메일 그룹 또는 메일 그룹은 외부 전자 메일 주소의 전자 메일을 차단 합니다. 위의 전자 메일 주소를 허용 목록 해야 합니다.  
    테스트 하려면 작업 그룹에 일반 회사 전자 메일 주소 (메일 그룹 아님)를 추가 하 고 해당 전자 메일에 경고가 도착 하는지 확인 합니다. 

1. **받은 편지함 규칙이 나 스팸 필터에 의해 전자 메일이 처리 되었습니까?** 

    해당 전자 메일을 삭제 하거나 옆쪽 폴더로 이동 하는 수신함 규칙이 없는지 확인 합니다. 예를 들어 수신함 규칙은 특정 보낸 사람이 나 특정 단어를 검색할 수 있습니다.

    또한 다음을 확인 합니다.
    
      - 전자 메일 클라이언트 (예: Outlook, Gmail)의 스팸 설정
      - 전자 메일 서버 (예: Exchange, Office 365, G suite)의 발신자 제한/스팸 설정/격리 설정
      - 전자 메일 보안 어플라이언스의 설정 (있는 경우) (예: Barracuda, Cisco) 

1. **작업 그룹에서 실수로 구독을 취소 했습니까?** 

    경고 전자 메일은 작업 그룹에서 구독을 취소할 수 있는 링크를 제공 합니다. 이 작업 그룹에서 실수로 구독을 취소 했는지 여부를 확인 하려면 다음 중 하나를 수행 합니다.

    1. 포털에서 작업 그룹을 열고 상태 열을 확인 합니다.

    ![작업 그룹 상태 열](media/alerts-troubleshoot/action-group-status.png)

    2. 전자 메일에서 구독 취소 확인을 검색 합니다.

    ![경고 작업 그룹에서 구독 취소](media/alerts-troubleshoot/unsubscribe-action-group.png)

    다시 구독 하려면 받은 구독 취소 확인 전자 메일의 링크를 사용 하거나 작업 그룹에서 전자 메일 주소를 제거한 다음 다시 추가 합니다. 
 
1. **단일 전자 메일 주소로 이동 하는 많은 전자 메일 때문에 제한 된 등급을가지고 있나요?** 

    전자 메일은 각 전자 메일 주소에 대해 매시간 100 전자 메일의 [속도로 제한](alerts-rate-limiting.md) 됩니다. 이 임계값을 전달 하면 추가 전자 메일 알림이 삭제 됩니다.  전자 메일 주소가 일시적으로 제한 되어 있음을 나타내는 메시지를 받았는지 확인 합니다. 
 
   ![전자 메일 전송률이 제한 됨](media/alerts-troubleshoot/email-paused.png)

   요금 제한 없이 많은 양의 알림을 받으려면 웹 후크, 논리 앱, Azure 함수 또는 자동화 runbook과 같은 다른 작업을 사용 하는 것이 좋습니다 .이는 어떤 비율도 제한 되지 않습니다. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>예상 SMS, 음성 통화 또는 푸시 알림을 받지 못했습니다.

포털에서 발생 한 경고를 볼 수 있지만 구성 된 SMS, 음성 통화 또는 푸시 알림이 수신 되지 않은 경우 다음 단계를 수행 합니다. 

1. **작업 [규칙](alerts-action-rules.md)에 의해 동작이 무시 되었습니까?** 

    포털에서 발생 한 경고를 클릭 하 여 확인 하 고 표시 되지 않은 [작업 그룹](action-groups.md)의 기록 탭에서 다음을 확인 합니다. 

    ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)

   의도 하지 않은 경우에는 작업 규칙을 수정 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다.
 
1. **SMS/voice: 전화 번호가 정확한 가요?**

   SMS 작업에서 국가 코드 또는 전화 번호의 오타를 확인 합니다. 
 
1. **SMS/voice: 요금이 제한적 입니까?** 

    SMS 및 음성 통화는 전화 번호 당 5 분 마다 한 번만 알림으로 제한 됩니다. 이 임계값을 전달 하면 알림이 삭제 됩니다. 

      - 음성 통화 – 통화 기록을 확인 하 고 이전 5 분 내에 Azure에서 다른 호출을 했는지 확인 합니다. 
      - SMS-회사에서 전화 번호의 요금을 제한 했음을 나타내는 메시지를 확인 합니다. 

    요금 제한 없이 많은 양의 알림을 받으려면 웹 후크, 논리 앱, Azure 함수 또는 자동화 runbook과 같은 다른 작업을 사용 하는 것이 좋습니다 .이는 어떤 비율도 제한 되지 않습니다. 
 
1. **SMS: 작업 그룹에서 실수로 구독을 취소 했습니까?**

    SMS 기록을 열고,이 특정 작업 그룹 (action_group_short_name 회신 사용 안 함) 또는 모든 작업 그룹 (중지 회신 사용)에서 SMS 배달을 옵트아웃 했는지 확인 합니다. 다시 구독 하려면 관련 SMS 명령 (action_group_short_name을 사용 하도록 설정 또는 시작)을 보내거나 작업 그룹에서 SMS 작업을 제거한 다음 다시 추가 합니다.  자세한 내용은 [작업 그룹에서 SMS 경고 동작](alerts-sms-behavior.md)을 참조 하세요.

1. **실수로 휴대폰에서 알림을 차단 했습니까?**

   대부분의 휴대폰에서는 특정 전화 번호 또는 짧은 코드에서 호출 또는 SMS를 차단 하거나 특정 앱에서의 푸시 알림 (예: Azure 모바일 앱)을 차단할 수 있습니다. 휴대폰에서 알림을 실수로 차단 했는지 확인 하려면 휴대폰 운영 체제 및 모델에 해당 하는 설명서를 검색 하거나 다른 전화 번호와 전화 번호를 사용 하 여 테스트 합니다. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>트리거할 다른 동작 형식이 필요 하지만 

포털에서 발생 한 경고를 볼 수 있지만 구성 된 작업을 트리거하지 않은 경우 다음 단계를 수행 합니다. 

1. **작업 규칙에 의해 동작이 무시 되었습니까?** 

    포털에서 발생 한 경고를 클릭 하 여 확인 하 고 표시 되지 않은 [작업 그룹](action-groups.md)의 기록 탭에서 다음을 확인 합니다. 

    ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)
 
    의도 하지 않은 경우에는 작업 규칙을 수정 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다. 

1. **Webhook가 트리거되지 않나요?**

    1. **원본 IP 주소가 차단 되었습니까?**
    
       허용 목록 webhook가 호출 되는 [IP 주소](action-groups.md#action-specific-information) 입니다.

    1. **Webhook 끝점이 제대로 작동 하나요?**

       구성한 webhook 끝점이 올바르고 끝점이 제대로 작동 하는지 확인 합니다. Webhook 로그를 확인 하거나 코드를 계측 하 여 조사할 수 있습니다 (예: 들어오는 페이로드 기록). 

    1. **여유 시간 또는 Microsoft 팀을 호출 하나요?**  
    이러한 각 끝점에는 특정 JSON 형식이 필요 합니다. 대신 논리 앱 작업을 구성 하려면 [다음 지침](action-groups-logic-app.md) 을 따르세요.

    1. **Webhook가 응답 하지 않거나 오류가 반환 되었습니까?** 

        Webhook 응답의 제한 시간은 10 초입니다. 웹 후크 호출은 408, 429, 503, 504 또는 HTTP 끝점이 응답 하지 않을 때 다음 HTTP 상태 코드가 반환 될 때 최대 두 번까지 다시 시도 됩니다. 10초 후에 첫 번째 다시 시도가 발생합니다. 두 번째 및 마지막 다시 시도는 100 초 후에 발생 합니다. 두 번째 다시 시도가 실패하면 모든 작업 그룹에 대해 엔드포인트가 30분 동안 다시 호출되지 않습니다.

## <a name="action-or-notification-happened-more-than-once"></a>작업 또는 알림이 두 번 이상 발생 했습니다. 

경고에 대 한 알림 (예: 전자 메일 또는 SMS)을 두 번 이상 받았거나 경고 동작 (예: webhook 또는 Azure function)이 여러 번 트리거된 경우 다음 단계를 수행 합니다. 

1. **정말 동일한 경고 입니까?** 

    경우에 따라 여러 유사한 경고가 동시에 발생 합니다. 따라서 동일한 경고가 작업을 여러 번 트리거한 것 처럼 보일 수 있습니다. 예를 들어 이벤트가 시작 될 때와 이벤트 상태 필드를 필터링 하지 않고 완료 (성공 또는 실패) 할 때 모두 실행 되도록 활동 로그 경고 규칙을 구성할 수 있습니다. 

    이러한 작업 또는 알림이 다른 경고에서 발생 했는지 확인 하려면 해당 타임 스탬프 및 경고 id 또는 해당 상관 관계 id와 같은 경고 세부 정보를 확인 합니다. 또는 포털에서 발생 한 경고 목록을 확인 합니다. 이 경우 경고 규칙 논리를 조정 하거나 경고 원본을 구성 해야 합니다. 

1. **작업은 여러 작업 그룹에서 반복 됩니까?** 

    경고가 발생 하면 각 작업 그룹은 독립적으로 처리 됩니다. 따라서 여러 개의 트리거된 작업 그룹에 작업 (예: 전자 메일 주소)이 표시 되 면 작업 그룹당 한 번씩 호출 됩니다. 

    트리거된 작업 그룹을 확인 하려면 경고 기록 탭을 선택 합니다. 경고 규칙에 정의 된 작업 그룹 및 작업 규칙에 따라 경고에 추가 된 작업 그룹이 표시 됩니다. 

    ![여러 작업 그룹에서 반복 되는 작업](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>작업 또는 알림에 예기치 않은 콘텐츠가 있습니다.

경고를 받았지만 일부 필드가 없거나 잘못 된 것으로 판단 되 면 다음 단계를 수행 합니다. 

1. **작업에 올바른 형식을 선택 했습니까?** 

    각 작업 유형 (메일, 웹 후크 등)에는 두 가지 형식, 즉 기본, 기존 형식 및 [최신 공용 스키마 형식이](alerts-common-schema.md)있습니다. 작업 그룹을 만들 때 작업 당 원하는 형식을 지정 합니다. 작업 그룹의 다른 작업에는 다른 형식이 있을 수 있습니다. 

    예를 들어 webhook 작업의 경우: 

    ![webhook 동작 스키마 옵션](media/alerts-troubleshoot/webhook.png)

    동작 수준에 지정 된 형식이 필요한 지 여부를 확인 합니다. 예를 들어 경고 (웹 후크, 함수, 논리 앱 등)에 응답 하는 코드를 개발 했을 수 있습니다. 단, 한 가지 형식이 필요 하지만 사용자나 다른 사용자가 다른 형식을 지정 했습니다.  

    또한 [활동 로그 경고](activity-log-alerts-webhook.md)에 대 한 페이로드 형식 (JSON), [로그 검색 경고](alerts-log-webhook.md) (Application Insights 및 log analytics 모두), [메트릭 경고](alerts-metric-near-real-time.md#payload-schema), [일반적인 경고 스키마](alerts-common-schema-definitions.md)및 더 이상 사용 되지 않는 [클래식 메트릭 경고](alerts-webhooks.md)를 확인 합니다.

 
1. **활동 로그 경고: 활동 로그에서 정보를 사용할 수 있나요?** 

    [활동 로그 경고](activity-log-alerts.md) 는 azure 리소스 만들기, 업데이트 또는 삭제, 서비스 상태 및 리소스 상태 이벤트, Azure Advisor 및 Azure Policy의 결과 등 Azure 활동 로그에 기록 된 이벤트를 기반으로 하는 경고입니다. 활동 로그를 기반으로 경고를 받았지만 필요한 일부 필드가 누락 되거나 잘못 된 경우 먼저 활동 로그 자체의 이벤트를 확인 합니다. Azure 리소스가 활동 로그 이벤트에서 찾고 있는 필드를 작성 하지 않은 경우 해당 필드는 해당 경고에 포함 되지 않습니다. 

## <a name="action-rule-is-not-working-as-expected"></a>작업 규칙이 예상 대로 작동 하지 않습니다. 

포털에서 발생 한 경고를 볼 수 있지만 관련 작업 규칙이 예상 대로 작동 하지 않는 경우 다음 단계를 수행 합니다. 

1. **작업 규칙을 사용할 수 있나요?** 

    작업 규칙 상태 열을 확인 하 여 관련 작업 역할이 사용 하도록 설정 되어 있는지 확인 합니다. 

    ![그래픽](media/alerts-troubleshoot/action-rule-status.png) 

    사용 하도록 설정 되지 않은 경우 작업 규칙을 선택 하 고 사용을 클릭 하 여 해당 규칙을 사용 하도록 설정할 수 있습니다. 

1. **서비스 상태 경고 입니까?** 

    서비스 상태 경고 (monitor service = "Service Health")는 작업 규칙의 영향을 받지 않습니다. 

1. **작업 규칙이 경고에 대해 작동 했습니까?** 

    포털에서 발생 한 경고를 클릭 하 여 작업 규칙이 경고를 처리 했는지 확인 하 고 기록 탭을 확인 합니다.

    다음은 모든 작업 그룹을 억제 하는 작업 규칙의 예입니다. 
 
     ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)

    다른 작업 그룹을 추가 하는 작업 규칙의 예는 다음과 같습니다.

    ![여러 작업 그룹에서 반복 되는 작업](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **작업 규칙 범위와 필터가 발생 한 경고와 일치 합니까?** 

    작업 규칙이 발생 했지만 발생 하지 않았거나 발생 하지 않은 것으로 생각 되는 경우 작업 규칙 범위 및 필터 조건 및 발생 한 경고의 속성을 주의 깊게 검토 합니다. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>발생 한 경고의 경고 id를 찾는 방법

발생 한 특정 경고에 대 한 사례를 열 때 (예: 전자 메일 알림을 받지 않은 경우) 경고 id를 제공 해야 합니다. 

이를 찾으려면 다음 단계를 수행 합니다.

1. Azure Portal에서 발생 한 경고 목록으로 이동 하 여 특정 경고를 찾습니다. 필터를 사용 하 여 찾을 수 있습니다. 

1. 경고를 클릭 하 여 경고 세부 정보를 엽니다. 

1. 첫 번째 탭의 경고 필드 (요약 탭)를 찾아서 복사할 때까지 아래로 스크롤합니다. 이 필드에는 사용할 수 있는 "클립보드로 복사" 도우미 단추도 포함 되어 있습니다.  

    ![경고 id 찾기](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Azure Portal에서 작업 규칙을 생성, 업데이트 또는 삭제 하는 데 문제가 있습니다.

[작업 규칙](alerts-action-rules.md)을 만들거나 업데이트 하거나 삭제 하는 동안 오류가 발생 한 경우 다음 단계를 수행 합니다. 

1. **권한 오류가 수신 되었습니까?**  

     [모니터링 참여자 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)또는 작업 규칙 및 경고와 관련 된 특정 사용 권한이 있어야 합니다.

1. **작업 규칙 매개 변수를 확인 했나요?**  

    [작업 규칙 설명서](alerts-action-rules.md)또는 [작업 규칙 PowerShell AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) 명령을 확인 합니다. 


## <a name="next-steps"></a>다음 단계
- 로그 경고를 사용 하는 경우 [로그 경고 문제 해결](alert-log-troubleshoot.md)도 참조 하세요.
- [Azure Portal](https://portal.azure.com) 로 돌아가서 위의 지침에 따라 문제를 해결 했는지 확인 합니다. 
