---
title: 경고 및 알림 Azure Monitor 문제 해결
description: Azure Monitor 경고 및 가능한 해결 방법에 대 한 일반적인 문제
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: 3e9eb9d0910e4c0e00e57eac80c09910f214db6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300776"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Azure Monitor 경고 문제 해결

이 문서에서는 경고 및 알림 Azure Monitor의 일반적인 문제에 대해 설명 합니다.

Azure Monitor 경고는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알려 줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대 한 자세한 내용은 [Microsoft Azure의 경고 개요](alerts-overview.md)를 참조 하세요.

경고가 발생할 때 발생 하거나 발생 하지 않는 문제가 발생 하는 경우 아래 문서를 참조 하세요. Azure Portal에서 "발생 된" 경고를 볼 수 있습니다.

- [Microsoft Azure에서 메트릭 경고 Azure Monitor 문제 해결](alerts-troubleshoot-metric.md)  
- [Microsoft Azure에서 Azure Monitor 로그 경고 문제 해결](alerts-troubleshoot-metric.md)

Azure Portal에 따라 경고가 발생 하지만 적절 한 알림이 발생 하지 않는 경우이 문서의 나머지 부분에 있는 정보를 사용 하 여 문제를 해결 하십시오.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>경고에 대 한 작업 또는 알림이 예상 대로 작동 하지 않음

Azure Portal에서 발생 한 경고를 볼 수 있지만 일부 작업 또는 알림에 문제가 있는 경우 다음 섹션을 참조 하십시오.

## <a name="did-not-receive-expected-email"></a>예상 전자 메일을 받지 못했습니다.

Azure Portal에서 발생한 경고를 볼 수 있지만 이에 대한 이메일을 받지 못한 경우 다음 단계를 수행합니다.

1. ** [작업 규칙](alerts-action-rules.md)에 의해 전자 메일이**표시 되지 않나요?

    포털에서 발생한 경고를 클릭하여 확인하고 표시되지 않은 [작업 그룹](action-groups.md)에 대해 기록 탭을 확인합니다.

    ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)

1. **"Email Azure Resource Manager Role" 동작의 유형 인가요?**

    이 작업은 구독 범위 및 *사용자*형식의 Azure Resource Manager 역할 할당만 확인 합니다.  리소스 수준 또는 리소스 그룹 수준이 아닌 구독 수준에서 역할을 할당했는지 확인합니다.

1. **전자 메일 서버와 사서함이 외부 전자 메일을 수락 하 고 있습니까?**

    다음 세 주소의 이메일이 차단되지 않았는지 확인합니다.
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    일반적으로 내부 메일 그룹은 외부 이메일 주소의 이메일을 차단합니다. 위의 메일 주소에서 메일을 허용 해야 합니다.  
    테스트하려면 작업 그룹에 일반 업무용 이메일 주소(메일 그룹 아님)를 추가하고 해당 이메일에 경고가 도착하는지 확인합니다.

1. **받은 편지함 규칙이 나 스팸 필터에 의해 전자 메일이 처리 되었습니까?**

    해당 이메일을 삭제하거나 다른 폴더로 이동하는 받은 편지함 규칙이 없는지 확인합니다. 예를 들어 수신함 규칙은 특정 보낸 사람이 나 특정 단어를 검색할 수 있습니다.

    다음을 확인합니다.

   - 이메일 클라이언트(Outlook, Gmail과 같은)의 스팸 설정
      - 이메일 서버(예: Exchange, Office 365, G suite)의 발신자 제한/스팸 설정/격리 설정
      - 전자 메일 보안 어플라이언스의 설정 (있는 경우) (예: Barracuda, Cisco)

1. **작업 그룹에서 실수로 구독을 취소 했습니까?**

    경고 이메일은 작업 그룹에서 수신을 거부할 수 있는 링크를 제공합니다. 이 작업 그룹에서 실수로 수신을 거부했는지 확인하려면 다음 중 하나를 수행합니다.

    1. 포털에서 작업 그룹을 열고 상태 열을 확인합니다.

    ![작업 그룹 상태 열](media/alerts-troubleshoot/action-group-status.png)

    2. 이메일에서 수신 거부 확인을 검색합니다.

    ![경고 작업 그룹에서 수신 거부됨](media/alerts-troubleshoot/unsubscribe-action-group.png)

    다시 수신하려면 받은 수신 거부 확인 이메일의 링크를 사용하거나 작업 그룹에서 이메일 주소를 제거한 다음 다시 추가합니다. 
 
1. **단일 전자 메일 주소로 이동 하는 많은 전자 메일 때문에 제한 된 등급을가지고 있나요?**

    이메일 주소마다 시간당 이메일 100개 이하로 [제한됩니다](alerts-rate-limiting.md). 이 임계값을 초과하면 추가 이메일 알림이 삭제됩니다.  이메일 주소가 일시적으로 제한되었음을 나타내는 메시지를 받았는지 확인합니다. 
 
   ![이메일 비율 제한됨](media/alerts-troubleshoot/email-paused.png)

   비율 제한 없이 많은 양의 알림을 받으려면 비율 제한이 없는 웹후크, 논리 앱, Azure 함수 또는 자동화 Runbook과 같은 다른 작업을 사용하는 것이 좋습니다. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>예상 SMS, 음성 통화 또는 푸시 알림을 받지 못했습니다.

포털에서 발생한 경고가 표시되지만, 이에 대해 구성한 SMS, 음성 통화 또는 푸시 알림이 수신되지 않은 경우 다음 단계를 수행합니다. 

1. **작업 [규칙](alerts-action-rules.md)에 의해 동작이 무시 되었습니까?**

    포털에서 발생한 경고를 클릭하여 확인하고 표시되지 않은 [작업 그룹](action-groups.md)에 대해 기록 탭을 확인합니다. 

    ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)

   의도하지 않은 경우 작업 규칙을 수정하거나 사용하지 않도록 설정하거나 삭제할 수 있습니다.
 
1. **SMS/voice: 전화 번호가 정확한 가요?**

   SMS 작업에서 국가 번호 또는 전화 번호가 정확한지 확인합니다.
 
1. **SMS/voice: 요금이 제한적 입니까?**

    SMS 및 음성 통화는 전화 번호마다 5분당 알림 1개 이하로 속도가 제한됩니다. 이 임계값을 전달하면 알림이 삭제됩니다.

      - 음성 통화 – 통화 기록을 확인하고 이전 5분 내에 Azure에서 다른 통화가 있었는지 확인합니다.
      - SMS - 전화 번호에 속도 제한이 설정되어 있음을 알리는 메시지가 SMS 기록에 있는지 확인합니다.

    속도 제한 없이 많은 양의 알림을 받으려면 속도 제한이 없는 웹후크, 논리 앱, Azure 함수 또는 자동화 Runbook과 같은 다른 작업을 사용하는 것이 좋습니다. 
 
1. **SMS: 작업 그룹에서 실수로 구독을 취소 했습니까?**

    SMS 기록을 열고,이 특정 작업 그룹 (action_group_short_name 회신 사용 안 함) 또는 모든 작업 그룹 (중지 회신 사용)에서 SMS 배달을 옵트아웃 했는지 확인 합니다. 다시 구독하려면 관련 SMS 명령(action_group_short_name 사용 또는 시작)을 보내거나 작업 그룹에서 SMS 작업을 제거한 다음, 다시 추가합니다.  자세한 내용은 [작업 그룹의 SMS 경고 동작](alerts-sms-behavior.md)을 참조하세요.

1. **실수로 휴대폰에서 알림을 차단 했습니까?**

   대부분의 휴대폰에서는 특정 전화 번호 또는 짧은 코드에서 통화 또는 SMS를 차단하거나 특정 앱(예: Azure 모바일 앱)의 푸시 알림을 차단할 수 있습니다. 휴대폰에서 알림을 실수로 차단했는지 확인하려면 휴대폰 운영 체제 및 모델에 해당하는 설명서를 검색하거나 다른 전화기 및 전화 번호를 사용하여 테스트합니다.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>트리거할 다른 동작 형식이 필요 하지만 
   
포털에서 발생한 경고를 볼 수 있지만 구성된 작업이 트리거되지 않은 경우 다음 단계를 수행합니다.

1. **작업 규칙에 의해 동작이 무시 되었습니까?**

    포털에서 발생한 경고를 클릭하여 확인하고 표시되지 않은 [작업 그룹](action-groups.md)에 대해 기록 탭을 확인합니다.

    ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)
 
    의도하지 않은 경우 작업 규칙을 수정하거나 사용하지 않도록 설정하거나 삭제할 수 있습니다.

1. **Webhook가 트리거되지 않나요?**

    1. **원본 IP 주소가 차단 되었습니까?**
    
       Webhook가 호출 되는 [IP 주소](action-groups.md#action-specific-information) 를 허용 목록에 추가 합니다.

    1. **Webhook 끝점이 제대로 작동 하나요?**

       구성한 웹후크 엔드포인트가 올바르고 엔드포인트가 제대로 작동하는지 확인합니다. 웹후크 로그를 확인하거나 해당 코드를 계측하여 조사할 수 있습니다(예: 들어오는 페이로드 기록).

    1. **여유 시간 또는 Microsoft 팀을 호출 하나요?**  
    이러한 각 엔드포인트에는 특정 JSON 형식이 필요합니다. [이러한 지침](action-groups-logic-app.md)을 따라 대신 논리 앱 작업을 구성합니다.

    1. **Webhook가 응답 하지 않거나 오류가 반환 되었습니까?** 

        웹후크 응답의 시간 제한은 10초입니다. 웹후크 호출은 HTTP 상태 코드 408, 429, 503, 504가 반환되거나 HTTP 엔드포인트가 응답하지 않으면 최대 2번 다시 시도됩니다. 10초 후에 첫 번째 다시 시도가 발생합니다. 100초 후에 두 번째 및 마지막 다시 시도가 발생합니다. 두 번째 다시 시도가 실패하면 모든 작업 그룹에 대해 엔드포인트가 30분 동안 다시 호출되지 않습니다.

## <a name="action-or-notification-happened-more-than-once"></a>작업 또는 알림이 두 번 이상 발생 했습니다. 

경고에 대한 알림(예: 이메일 또는 SMS)을 두 번 이상 받았거나 경고 동작(예: 웹후크 또는 Azure 함수)이 여러 번 트리거된 경우 다음 단계를 수행합니다. 

1. **정말 동일한 경고 입니까?** 

    경우에 따라 유사한 경고가 여러 개 동시에 발생합니다. 따라서 동일한 경고가 작업을 여러 번 트리거한 것처럼 보일 수 있습니다. 예를 들어 이벤트 상태 필드를 필터링하지 않고 이벤트가 시작될 때와 완료(성공 또는 실패)될 때 모두 발생되도록 활동 로그 경고 규칙을 구성할 수 있습니다. 

    이러한 작업 또는 알림이 다른 경고에서 발생했는지 확인하려면 해당 타임스탬프 및 경고 ID 또는 해당 상관 관계 ID와 같은 경고 세부 정보를 확인합니다. 또는 포털에서 발생한 경고 목록을 확인합니다. 이 경우 경고 규칙 논리를 조정 하거나 경고 원본을 구성 해야 합니다. 

1. **작업은 여러 작업 그룹에서 반복 됩니까?** 

    경고가 발생하면 각 작업 그룹은 독립적으로 처리됩니다. 따라서 여러 개의 트리거된 작업 그룹에 작업(예: 이메일 주소)이 표시되면 작업 그룹당 한 번씩 호출됩니다. 

    트리거된 작업 그룹을 확인하려면 경고 기록 탭을 확인합니다. 경고 규칙에 정의된 작업 그룹과 작업 규칙에 의해 경고에 추가된 작업 그룹를 모두 볼 수 있습니다. 

    ![여러 작업 그룹에서 반복 되는 작업](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>작업 또는 알림에 예기치 않은 콘텐츠가 있습니다.

경고를 받았지만 일부 필드가 누락되었거나 잘못된 것으로 판단되면 다음 단계를 수행합니다. 

1. **작업에 올바른 형식을 선택 했습니까?** 

    각 작업 유형 (메일, 웹 후크 등)에는 두 가지 형식, 즉 기본, 기존 형식 및 [최신 공용 스키마 형식이](alerts-common-schema.md)있습니다. 작업 그룹을 만들 때 작업별로 원하는 형식을 지정합니다. 작업 그룹의 서로 다른 작업은 서로 다른 형식을 사용할 수 있습니다. 

    예를 들어 webhook 작업의 경우: 

    ![webhook 동작 스키마 옵션](media/alerts-troubleshoot/webhook.png)

    작업 수준에 지정된 형식이 원하는 형식인지 확인합니다. 예를 들어 어떤 형식을 기대하며 경고에 응답하는 코드(웹후크, 함수, 논리 앱 등)를 개발했지만 나중에 해당 사용자 또는 다른 사용자가 다른 형식을 지정했습니다.  

    또한 [활동 로그 경고](activity-log-alerts-webhook.md), [로그 검색 경고](alerts-log-webhook.md)(Application Insights 및 Log Analytics 모두), [메트릭 경고](alerts-metric-near-real-time.md#payload-schema), [일반적인 경고 스키마](alerts-common-schema-definitions.md) 및 사용되지 않는 [클래식 메트릭 경고](alerts-webhooks.md)에 대해 페이로드 형식(JSON)을 확인합니다.

 
1. **활동 로그 경고: 활동 로그에서 정보를 사용할 수 있나요?** 

    [활동 로그 경고](activity-log-alerts.md) 는 azure 리소스 생성, 업데이트 또는 삭제, 서비스 상태 및 리소스 상태 이벤트, Azure Advisor 및 Azure Policy의 결과 등 Azure 활동 로그에 기록 된 이벤트를 기반으로 하는 경고입니다. 활동 로그를 기반으로 경고를 받았지만 필요한 일부 필드가 누락되거나 잘못된 경우 활동 로그 자체의 이벤트를 먼저 확인합니다. Azure 리소스가 활동 로그 이벤트에서 찾고 있는 필드를 작성하지 않은 경우 해당 필드는 해당 경고에 포함되지 않습니다. 

## <a name="action-rule-is-not-working-as-expected"></a>작업 규칙이 예상대로 작동하지 않음 

포털에서 발생한 경고를 볼 수 있지만 관련된 작업 규칙이 예상대로 작동하지 않는 경우 다음 단계를 수행합니다. 

1. **작업 규칙을 사용할 수 있나요?** 

    작업 규칙 상태 열을 확인하여 관련 작업 역할이 사용하도록 설정되어 있는지 확인합니다. 

    ![그래픽](media/alerts-troubleshoot/action-rule-status.png) 

    사용하도록 설정되지 않은 경우 작업 규칙을 선택하고 사용을 클릭하여 작업 규칙을 사용하도록 설정할 수 있습니다. 

1. **서비스 상태 경고 입니까?** 

    서비스 상태 경고(모니터링 서비스 = “Service Health”)는 작업 규칙의 영향을 받지 않습니다. 

1. **작업 규칙이 경고에 대해 작동 했습니까?** 

    포털에서 발생한 경고를 클릭하여 작업 규칙이 처리되었는지 확인하고, 기록 탭을 살펴봅니다.

    다음은 모든 작업 그룹을 표시하지 않는 작업 규칙의 예제입니다. 
 
     ![경고 작업 규칙 비 표시 기록](media/alerts-troubleshoot/history-action-rule.png)

    다음은 다른 작업 그룹을 추가하는 작업 규칙의 예제입니다.

    ![여러 작업 그룹에서 반복 되는 작업](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **작업 규칙 범위와 필터가 발생 한 경고와 일치 합니까?** 

    작업 규칙이 발생해야 하는데 발생하지 않았거나 발생하지 말아야 하는데 발생한 경우 작업 규칙 범위 및 필터 조건과 발생한 경고의 속성을 주의 깊게 검토합니다. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>발생 한 경고의 경고 ID를 찾는 방법

발생 한 특정 경고에 대 한 사례를 열 때 (예: 전자 메일 알림을 받지 않은 경우) 경고 ID를 제공 해야 합니다. 

이를 찾으려면 다음 단계를 수행 합니다.

1. Azure Portal에서 발생 한 경고 목록으로 이동 하 여 특정 경고를 찾습니다. 필터를 사용 하 여 찾을 수 있습니다. 

1. 경고를 클릭 하 여 경고 세부 정보를 엽니다. 

1. 첫 번째 탭의 경고 필드 (요약 탭)를 찾아서 복사할 때까지 아래로 스크롤합니다. 이 필드에는 사용할 수 있는 "클립보드로 복사" 도우미 단추도 포함 되어 있습니다.  

    ![경고 ID 찾기](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Azure Portal에서 작업 규칙을 생성, 업데이트 또는 삭제 하는 데 문제가 있습니다.

[작업 규칙](alerts-action-rules.md)을 만들거나 업데이트 하거나 삭제 하는 동안 오류가 발생 한 경우 다음 단계를 수행 합니다. 

1. **권한 오류가 수신 되었습니까?**  

     [모니터링 참여자 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)또는 작업 규칙 및 경고와 관련 된 특정 사용 권한이 있어야 합니다.

1. **작업 규칙 매개 변수를 확인 했나요?**  

    [작업 규칙 설명서](alerts-action-rules.md)또는 [작업 규칙 PowerShell AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) 명령을 확인 합니다. 


## <a name="next-steps"></a>다음 단계
- 로그 경고를 사용 하는 경우 [로그 경고 문제 해결](alert-log-troubleshoot.md)도 참조 하세요.
- [Azure Portal](https://portal.azure.com) 로 돌아가서 위의 지침에 따라 문제를 해결 했는지 확인 합니다. 
