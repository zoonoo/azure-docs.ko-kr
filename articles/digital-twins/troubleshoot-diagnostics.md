---
title: 진단 설정
titleSuffix: Azure Digital Twins
description: 진단 설정을 사용 하 여 로깅을 사용 하도록 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612404"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins 문제 해결: 진단 로깅

Azure Digital Twins는 리소스의 상태에 대 한 정보를 제공 하는 서비스 인스턴스에 대 한 [메트릭을](troubleshoot-metrics.md) 수집 합니다. 이러한 메트릭을 사용 하 여 Azure Digital Twins 서비스의 전반적인 상태와 연결 된 리소스를 평가할 수 있습니다. 이러한 사용자 지향 통계는 azure 지원에 문의 하지 않고도 문제에 대 한 근본 원인 분석을 수행 하는 데 도움이 되는 Azure Digital Twins의 기능을 확인 하는 데 도움이 됩니다.

이 문서에서는 Azure Digital Twins 인스턴스에서 메트릭 데이터에 대 한 **진단 로깅을** 설정 하는 방법을 보여 줍니다. 이러한 로그를 사용 하 여 서비스 문제를 해결 하 고 진단 설정을 구성 하 여 Azure Digital Twins 메트릭을 다른 대상으로 보낼 수 있습니다. 진단 설정 만들기에서 이러한 설정에 대 한 자세한 내용을 참조 [*하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보낼*](../azure-monitor/platform/diagnostic-settings.md)수 있습니다.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Azure Portal를 사용 하 여 진단 설정 켜기

Azure Digital Twins 인스턴스에 대 한 진단 설정을 사용 하도록 설정 하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Digital twins 인스턴스로 이동 합니다. 포털 검색 표시줄에 이름을 입력 하 여 찾을 수 있습니다. 

2. 메뉴에서 **진단 설정** 을 선택 하 고 **진단 설정을 추가**합니다.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="진단 설정 페이지 및 추가할 단추를 보여 주는 스크린샷":::

3. 뒤에 나오는 페이지에서 다음 값을 입력 합니다.
     * **진단 설정 이름**: 진단 설정에 이름을 지정 합니다.
     * **범주 세부 정보**: 모니터링할 작업을 선택 하 고 확인란을 선택 하 여 해당 작업에 대 한 진단을 사용 하도록 설정 합니다. 진단 설정이 보고할 수 있는 작업은 같습니다.
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        이러한 옵션에 대 한 자세한 내용은 아래의 [*범주 세부 정보*](#category-details) 섹션을 참조 하세요.
     * **대상 세부 정보**: 로그를 보낼 위치를 선택 합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.
        - Log Analytics에 보내기
        - 스토리지 계정에 보관
        - 이벤트 허브로 스트림

        대상 선택에 필요한 경우 추가 세부 정보를 입력 하 라는 메시지가 표시 될 수 있습니다.  
    
4. 새 설정을 저장합니다. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="진단 설정 페이지 및 추가할 단추를 보여 주는 스크린샷":::

새 설정은 약 10분 후에 적용됩니다. 그 후에는 해당 인스턴스에 대 한 **진단 설정** 페이지에서 로그가 구성 된 대상에 다시 표시 됩니다. 

## <a name="category-details"></a>범주 세부 정보

진단 설정을 설정할 때 **범주 세부 정보** 에서 선택할 수 있는 로그 범주에 대 한 자세한 내용은 다음과 같습니다.

| 로그 범주 | Description |
| --- | --- |
| ADTModelsOperation | 모델과 관련 된 모든 API 호출을 기록 합니다. |
| ADTQueryOperation | 쿼리와 관련 된 모든 API 호출을 기록 합니다. |
| ADTEventRoutesOperation | 이벤트 경로와 관련 된 모든 API 호출을 기록 하 고 Azure Digital Twins에서 Event Grid, Event Hubs 및 Service Bus와 같은 끝점 서비스로 이벤트를 송신 합니다. |
| ADTDigitalTwinsOperation | Azure Digital Twins와 관련 된 모든 API 호출을 기록 합니다. |

각 로그 범주는 쓰기, 읽기, 삭제 및 동작의 작업으로 구성 됩니다.  이러한 REST API 호출은 다음과 같이 호출 됩니다.

| 이벤트 유형 | REST API 작업 |
| --- | --- |
| 쓰기 | PUT 및 PATCH |
| 읽기 | GET |
| DELETE | Delete |
| 작업 | POST |

다음은 각 범주에 기록 되는 작업 및 해당 [Azure Digital Twins REST API 호출](https://docs.microsoft.com/rest/api/azure-digitaltwins/) 의 포괄적인 목록입니다. 

>[!NOTE]
> 각 로그 범주에는 여러 개의 작업/REST API 호출이 포함 됩니다. 아래 표에서 각 로그 범주는 다음 로그 범주가 나열 될 때까지 그 아래의 모든 작업/REST API 호출에 매핑됩니다. 

| 로그 범주 | 작업(Operation) | REST API 호출 및 기타 이벤트 |
| --- | --- | --- |
| ADTModelsOperation | DigitalTwins/모델/쓰기 | 디지털 쌍 모델 업데이트 API |
|  | DigitalTwins/모델/읽기 | Id 및 List Api를 통해 가져오는 디지털 쌍 모델 |
|  | DigitalTwins/모델/삭제 | 디지털 쌍 모델 삭제 API |
|  | DigitalTwins/모델/작업 | 디지털 쌍 모델 API 추가 |
| ADTQueryOperation | DigitalTwins/쿼리/동작 | 쿼리 Twins API |
| ADTEventRoutesOperation | DigitalTwins/eventroutes/write | 이벤트 경로 API 추가 |
|  | DigitalTwins/eventroutes/읽기 | Id로 Get 이벤트 경로 및 목록 Api |
|  | DigitalTwins/eventroutes/삭제 | 이벤트 경로 Delete API |
|  | DigitalTwins/eventroutes/작업 | API 호출이 아닌 끝점 서비스에 이벤트를 게시 하는 동안 오류가 발생 했습니다. |
| ADTDigitalTwinsOperation | DigitalTwins/DigitalTwins/write | Digital Twins 추가, 관계 추가, 업데이트, 업데이트 구성 요소 |
|  | DigitalTwins/DigitalTwins/읽기 | 디지털 쌍 Id로 가져오기, 구성 요소 가져오기, Id로 관계 가져오기, 들어오는 관계 나열, 관계 나열 |
|  | DigitalTwins/DigitalTwins/delete | 디지털 쌍 삭제, 관계 삭제 |
|  | DigitalTwins/DigitalTwins/작업 | 디지털 쌍 송신 구성 요소 원격 분석, 원격 분석 보내기 |

## <a name="next-steps"></a>다음 단계

* 진단을 구성 하는 방법에 대 한 자세한 내용은 [*Azure 리소스에서 로그 데이터 수집 및 사용*](../azure-monitor/platform/platform-logs-overview.md)을 참조 하세요.
* Azure Digital Twins 메트릭에 대 한 자세한 내용은 [*문제 해결: Azure Monitor 사용 하 여 메트릭 보기*](troubleshoot-metrics.md)를 참조 하세요.
* 메트릭에 대 한 경고를 사용 하도록 설정 하는 방법을 보려면 [*문제 해결: 경고 설정*](troubleshoot-alerts.md)을 참조 하세요.
