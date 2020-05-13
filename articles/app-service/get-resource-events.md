---
title: Azure App Service에서 리소스 이벤트 가져오기
description: App Service 앱에서 활동 로그 및 Event Grid를 통해 리소스 이벤트를 가져오는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124740"
---
# <a name="get-resource-events-in-azure-app-service"></a>Azure App Service에서 리소스 이벤트 가져오기

Azure App Service는 리소스의 상태와 상태를 모니터링 하는 기본 제공 도구를 제공 합니다. 리소스 이벤트는 기본 웹 앱 리소스에 대 한 변경 내용을 이해 하 고 필요에 따라 조치를 취하는 데 도움이 됩니다. 이벤트의 예로는 인스턴스 크기 조정, 응용 프로그램 설정 업데이트, 웹 앱 다시 시작 등이 있습니다. 이 문서에서는 [Azure 활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) 를 확인 하 고 [Event Grid](https://docs.microsoft.com/azure/event-grid/) 를 사용 하도록 설정 하 여 App Service 웹 앱과 관련 된 리소스 이벤트를 모니터링 하는 방법을 알아봅니다.

> [!NOTE]
> Event Grid와 통합 App Service **미리 보기**상태입니다. [자세한 내용은 알림을 참조 하세요.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Azure 활동 로그 보기
Azure 활동 로그에는 구독의 리소스에서 수행 된 작업에서 내보내는 리소스 이벤트가 포함 됩니다. Azure Portal 및 Azure Resource Manager 템플릿의 사용자 동작은 모두 활동 로그에서 캡처된 이벤트에 적용 됩니다. 

Azure 활동 로그는 다음과 같은 App Service 세부 정보를 기록 합니다.
- 리소스에 대해 수행 된 작업 (예: App Service 계획)
- 작업을 시작한 사람
- 작업이 발생한 시간
- 작업의 상태
- 작업을 조사 하는 데 도움이 되는 속성 값

### <a name="what-can-you-do-with-azure-activity-logs"></a>Azure 활동 로그로 무엇을 할 수 있나요?

Azure 활동 로그는 Azure Portal, PowerShell, REST API 또는 CLI를 사용 하 여 쿼리할 수 있습니다. 저장소 계정, 이벤트 허브 및 Log Analytics로 로그를 보낼 수 있습니다. Power BI에서 분석 하거나 경고를 만들어 리소스 이벤트에 대 한 업데이트를 유지할 수도 있습니다.

[Azure 활동 로그 이벤트를 보고 검색 합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>활동 로그를 Event Grid에 제공

활동 로그는 사용자 기반 이지만 사용자 동작 및 자동화 된 이벤트를 모두 기록 하는 새로운 [Event Grid](https://docs.microsoft.com/azure/event-grid/) App Service (미리 보기)와 통합 됩니다. Event Grid를 사용 하 여 이벤트에 반응 하도록 처리기를 구성할 수 있습니다. 예를 들어 Event Grid를 사용하여, 새 사진이 Blob Storage 컨테이너에 추가될 때마다 이미지 분석을 실행하도록 서버를 사용하지 않는 함수를 즉시 트리거합니다.

또는 Event Grid와 Logic Apps를 사용하여, 코드를 작성할 필요 없이 어디서든 데이터를 처리할 수 있습니다. Event Grid는 데이터 원본과 이벤트 처리기를 연결합니다. 예를 들어 Event Grid를 사용하여, 새 사진이 Blob Storage 컨테이너에 추가될 때마다 이미지 분석을 실행하도록 서버를 사용하지 않는 함수를 즉시 트리거합니다.

### <a name="supported-event-types"></a>지원되는 이벤트 형식
| 이벤트 유형 |설명|
| -----------| ------------- |
| Microsoft.web/sites | Webapp |
| BackupOperationCompleted |Webapp 백업이 완료 되었습니다.|
| BackupOperationFailed | Webapp를 백업 하지 못했습니다.|
| RestoreOperationStarted |백업에서 복원 시작|
| RestoreOperationCompleted |백업에서 복원이 완료 되었습니다.|
| RestoreOperationFailed |백업에서 복원 하지 못했습니다.|
| SlotSwapStarted |슬롯 교환이 시작 되었습니다.|
| SlotSwapCompleted |슬롯 교환이 성공적으로 완료 됨|
| SlotSwapFailed |슬롯 교환 실패|
| SlotSwapWithPreviewStarted |미리 보기가 있는 슬롯 교환 시작|
| SlotSwapWithPreviewCancelled |미리 보기가 있는 슬롯 교환 실패|
| AppUpdated | |
| 해야만 | Webapp이 다시 시작 되었습니다. |
| 중지됨 | Webapp가 중지 되었습니다. |
| ChangedAppSettings | Webapp의 앱 설정이 변경 되었습니다. |
| - | - |
| Microsoft 웹/serverfarms | (App Service 계획) |
| 기타 업데이트 | App service 계획이 업데이트 되었습니다. 이벤트 개체는 변경 된 속성에 대 한 세부 정보를 포함 합니다. |
| 강화/규모 축소 | App service 계획이 확장 또는 축소 됩니다. 이벤트 개체는 인스턴스 수를 포함 합니다.|


## <a name="next-steps"></a><a name="nextsteps"></a> 다음 단계
* [Azure Monitor를 사용 하 여 로그 쿼리](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service에서 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
