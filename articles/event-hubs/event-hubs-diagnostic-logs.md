---
title: 진단 로그 설정 - Azure Event Hub | Microsoft Docs
description: Azure에서 이벤트 허브의 활동 로그 및 진단 로그를 설정하는 방법을 배웁니다.
ms.topic: article
ms.date: 06/13/2021
ms.openlocfilehash: d981f1d01579719ecd055307f8a9877be94072d9
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060810"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure 이벤트 허브에 대한 진단 로그 설정

Azure Event Hubs에 대해 다음 두 가지 유형의 로그를 볼 수 있습니다.

* **[활동 로그](../azure-monitor/essentials/platform-logs-overview.md)** : 이러한 로그에는 작업에 대해 수행된 작업 관련 정보가 포함됩니다. 로그는 항상 켜져 있습니다. Azure Portal의 왼쪽 창에서 이벤트 허브 네임스페이스에 대한 **활동 로그** 를 선택하여 활동 로그 항목을 볼 수 있습니다. 예를 들면 다음과 같습니다. "네임스페이스 만들기 또는 업데이트", "이벤트 허브 만들기 또는 업데이트"

    ![Event Hubs 네임스페이스에 대한 활동 로그](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[진단 로그](../azure-monitor/essentials/platform-logs-overview.md)** : 진단 로그는 API를 사용하거나 언어 SDK의 관리 클라이언트를 통해 네임스페이스에 대해 수행된 작업 및 동작에 대한 자세한 정보를 제공합니다. 
    
    다음 섹션에서는 Event Hubs 네임스페이스에 대한 진단 로그를 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="enable-diagnostic-logs"></a>진단 로그 활성화
진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)에서 Event Hubs 네임스페이스로 이동합니다. 
2. 왼쪽 창의 **모니터링** 에서 **진단 설정** 을 선택한 다음 **+ 진단 설정 추가** 를 선택합니다. 

    ![진단 설정 페이지 - 진단 설정 추가](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. **범주 세부 정보** 섹션에서 사용하도록 설정할 **진단 로그 형식** 을 선택합니다. 이러한 범주에 대한 자세한 내용은 이 문서의 뒷부분에 있습니다. 
5. **대상 세부 정보** 섹션에서 스토리지 계정, 이벤트 허브 또는 Log Analytics 작업 영역 등 원하는 보관 대상(대상)을 설정합니다.

    ![진단 설정 추가 페이지](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  도구 모음에서 **저장** 을 선택하여 진단 설정을 저장합니다.

    새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 창에 로그가 나타납니다.

    진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/essentials/platform-logs-overview.md)를 참조하세요.

## <a name="diagnostic-logs-categories"></a>진단 로그 범주
[!INCLUDE [event-hubs-diagnostic-log-schema](../../includes/event-hubs-diagnostic-log-schema.md)]



## <a name="next-steps"></a>다음 단계
- [Event Hubs 소개](./event-hubs-about.md)
- [Event Hubs 샘플](sdks.md)
- Event Hubs 시작
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
