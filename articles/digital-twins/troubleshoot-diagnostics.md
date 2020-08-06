---
title: 진단 설정
titleSuffix: Azure Digital Twins
description: 진단 설정을 사용 하 여 로깅을 사용 하도록 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809404"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins 문제 해결: 진단 로깅

Azure Digital Twins는 리소스의 상태에 대 한 정보를 제공 하는 서비스 인스턴스에 대 한 [메트릭을](troubleshoot-metrics.md) 수집 합니다. 이러한 메트릭을 사용 하 여 Azure Digital Twins 서비스의 전반적인 상태와 연결 된 리소스를 평가할 수 있습니다. 이러한 사용자 지향 통계는 azure 지원에 문의 하지 않고도 문제에 대 한 근본 원인 분석을 수행 하는 데 도움이 되는 Azure Digital Twins의 기능을 확인 하는 데 도움이 됩니다.

이 문서에서는 Azure Digital Twins 인스턴스에서 메트릭 데이터에 대 한 **진단 로깅을** 설정 하는 방법을 보여 줍니다. 이러한 로그를 사용 하 여 서비스 문제를 해결할 수 있습니다.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Azure Portal를 사용 하 여 진단 설정 켜기

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Digital twins 인스턴스로 이동 합니다. 포털 검색 표시줄에 이름을 입력 하 여 찾을 수 있습니다. 

2. 메뉴에서 **진단 설정** 을 선택 하 고 **진단 설정을 추가**합니다.

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="진단 설정 페이지 및 추가할 단추를 보여 주는 스크린샷":::

3. 뒤에 나오는 페이지에서 다음 값을 입력 합니다.
     * **진단 설정 이름**: 진단 설정에 이름을 지정 합니다.
     * **범주 세부 정보**: 모니터링할 작업을 선택 하 고 확인란을 선택 하 여 해당 작업에 대 한 진단을 사용 하도록 설정 합니다. 진단 설정이 보고할 수 있는 작업은 같습니다.
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
     * **대상 세부 정보**: 로그를 보낼 위치를 선택 합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.
        - Log Analytics에 보내기
        - 스토리지 계정에 보관
        - 이벤트 허브로 스트림

        대상 선택에 필요한 경우 추가 세부 정보를 입력 하 라는 메시지가 표시 될 수 있습니다.  
    
4. 새 설정을 저장합니다. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="진단 설정 페이지 및 추가할 단추를 보여 주는 스크린샷":::

새 설정은 약 10분 후에 적용됩니다. 그 후에는 해당 인스턴스에 대 한 **진단 설정** 페이지에서 로그가 구성 된 대상에 다시 표시 됩니다. 

## <a name="next-steps"></a>다음 단계

* 진단을 구성 하는 방법에 대 한 자세한 내용은 [*Azure 리소스에서 로그 데이터 수집 및 사용*](../azure-monitor/platform/platform-logs-overview.md)을 참조 하세요.
* Azure Digital Twins 메트릭에 대 한 자세한 내용은 [*문제 해결: Azure Monitor 사용 하 여 메트릭 보기*](troubleshoot-metrics.md)를 참조 하세요.
