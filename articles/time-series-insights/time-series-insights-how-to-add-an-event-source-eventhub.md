---
title: Event Hubs 이벤트 원본 추가-Azure Time Series Insights | Microsoft Docs
description: Time Series Insights 환경에 Azure Event Hubs 이벤트 원본을 추가하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 021ac5fccf4d694895ab9941bd46dd2388f49af9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407450"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Time Series Insights 환경에 이벤트 허브 이벤트 원본 추가

이 문서에서는 Azure Portal을 사용하여 Azure Event Hubs에서 데이터를 읽는 이벤트 원본을 Azure Time Series Insights 환경에 추가하는 방법을 다룹니다.

> [!NOTE]
> 이 문서에서 설명 하는 단계는 Time Series Insights GA 및 Time Series Insights 미리 보기 환경에 모두 적용 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Time Series Insights 환경 만들기](./time-series-insights-update-create-environment.md)에 설명 된 대로 Time Series Insights 환경을 만듭니다.
- 이벤트 허브를 만듭니다. 읽기 [Azure Portal를 사용 하 여 Event Hubs 네임 스페이스 및 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조 하세요.
- 이벤트 허브에는 전송된 활성 메시지 이벤트가 있어야 합니다. [.NET Framework를 사용 하 여 Azure Event Hubs에 이벤트를 전송](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)하는 방법을 알아봅니다.
- Time Series Insights 환경에서 사용할 수 있는 전용 소비자 그룹을 이벤트 허브에 만듭니다. 각 Time Series Insights 이벤트 원본에는 다른 소비자와 공유되지 않은 전용 소비자 그룹 자체가 있어야 합니다. 여러 판독기가 동일한 소비자 그룹의 이벤트를 사용 하는 경우 모든 판독기에 오류가 발생할 수 있습니다. 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [Event Hubs 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)를 참조 하세요.

### <a name="add-a-consumer-group-to-your-event-hub"></a>이벤트 허브에 소비자 그룹 추가

애플리케이션은 소비자 그룹을 사용하여 Azure Event Hubs의 데이터를 풀합니다. 이벤트 허브에서 데이터를 안정적으로 읽으려면이 Time Series Insights 환경 에서만 사용 되는 전용 소비자 그룹을 제공 합니다.

이벤트 허브에 새 소비자 그룹을 추가하려면

1. [Azure Portal](https://portal.azure.com)에서 이벤트 허브 네임 스페이스의 **개요** 창에서 이벤트 허브 인스턴스를 찾아서 엽니다. **엔터티 > Event Hubs** 선택 하거나 **이름**아래에서 인스턴스를 찾습니다.

    [![이벤트 허브 네임 스페이스를 엽니다.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. 이벤트 허브 인스턴스에서 **엔터티 > 소비자 그룹**을 선택 합니다. 그런 다음 **+ 소비자 그룹** 을 선택 하 여 새 소비자 그룹을 추가 합니다. 

   [![이벤트 허브-소비자 그룹 추가](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   그렇지 않으면 기존 소비자 그룹을 선택 하 고 다음 섹션으로 건너뜁니다.

1. **소비자 그룹** 페이지에서 **이름**에 대해 고유한 새 값을 입력합니다.  Time Series Insights 환경에서 새 이벤트 원본을 만들 때 이 동일한 이름을 사용합니다.

1. **만들기**를 선택합니다.

## <a name="add-a-new-event-source"></a>새 이벤트 원본 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기존 Time Series Insights 환경을 찾습니다. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, Time Series Insights 환경을 선택합니다.

1. **이벤트 원본**을 선택 하 고 **추가**를 선택 합니다.

   [![이벤트 원본에서 추가 단추를 선택합니다.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. 이 Time Series Insights 환경에 고유한 **이벤트 원본 이름** 값 (예:)을 입력 `Contoso-TSI-GA-Event-Hub-ES`합니다.

1. **원본**에서 **이벤트 허브**를 선택합니다.

1. **가져오기 옵션**에서 적합한 값을 선택합니다.

   * 구독 중 하나에 기존 이벤트 허브가 있는 경우 **사용 가능한 구독의 이벤트 허브 사용**을 선택합니다. 이 옵션이 가장 쉬운 방법입니다.

     [![이벤트 원본 가져오기 옵션을 선택 합니다.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  다음 표에서는 **사용 가능한 구독의 이벤트 허브 사용** 옵션에 필요한 속성을 설명합니다.

       [![구독 및 이벤트 허브 세부 정보](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | 속성 | Description |
       | --- | --- |
       | Subscription | 원하는 이벤트 허브 인스턴스 및 네임 스페이스가 속한 구독입니다. |
       | 이벤트 허브 네임스페이스 | 원하는 이벤트 허브 인스턴스가 속한 이벤트 허브 네임 스페이스입니다. |
       | 이벤트 허브 이름 | 원하는 이벤트 허브 인스턴스의 이름입니다. |
       | 이벤트 허브 정책 값 | 원하는 공유 액세스 정책을 선택 합니다. 이벤트 허브 **구성** 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **읽기** 사용 권한이 *반드시* 있어야 합니다. |
       | 이벤트 허브 정책 키 | 선택한 이벤트 허브 정책 값으로 미리 채워집니다. |

    * 이벤트 허브가 구독 외부에 있거나 고급 옵션을 선택하려는 경우 **수동으로 이벤트 허브 설정 제공**을 선택합니다.

       다음 표에서는 **수동으로 이벤트 허브 설정 제공** 옵션에 필요한 속성을 설명합니다.
 
       | 속성 | Description |
       | --- | --- |
       | 구독 ID | 원하는 이벤트 허브 인스턴스 및 네임 스페이스가 속한 구독입니다. |
       | Resource group | 원하는 이벤트 허브 인스턴스 및 네임 스페이스가 속한 리소스 그룹입니다. |
       | 이벤트 허브 네임스페이스 | 원하는 이벤트 허브 인스턴스가 속한 이벤트 허브 네임 스페이스입니다. |
       | 이벤트 허브 이름 | 원하는 이벤트 허브 인스턴스의 이름입니다. |
       | 이벤트 허브 정책 값 | 원하는 공유 액세스 정책을 선택 합니다. 이벤트 허브 **구성** 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **읽기** 사용 권한이 *반드시* 있어야 합니다. |
       | 이벤트 허브 정책 키 | Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. 기본 키 또는 보조 키를 여기에 입력합니다. |

    * 두 옵션 모두 다음 구성 옵션을 공유 합니다.

       | 속성 | Description |
       | --- | --- |
       | 이벤트 허브 소비자 그룹 | 이벤트 허브에서 이벤트를 읽는 소비자 그룹입니다. 이벤트 원본에 대한 전용 소비자 그룹을 사용하는 것이 좋습니다. |
       | 이벤트 직렬화 형식 | 현재, JSON이 사용 가능한 유일한 직렬화 형식입니다. 이벤트 메시지는이 형식 이어야 합니다. 그렇지 않으면 데이터를 읽을 수 없습니다. |
       | 타임스탬프 속성 이름 | 이 값을 확인하려면 이벤트 허브로 전송되는 메시지 데이터의 메시지 형식을 이해해야 합니다. 이 값은 이벤트 타임스탬프로 사용하려는 메시지 데이터에 있는 특정 이벤트 속성의 **이름**입니다. 이 값은 대/소문자를 구분합니다. 이 값을 비워 두면 이벤트 원본의 **이벤트를 큐에 넣는 시간**이 이벤트 타임스탬프로 사용됩니다. |

1. 이벤트 허브에 추가한 전용 Time Series Insights 소비자 그룹 이름을 추가합니다.

1. **만들기**를 선택합니다.

   이벤트 원본이 생성 된 후 Time Series Insights에서 자동으로 사용자 환경에 대 한 스트리밍 데이터를 시작 합니다.

## <a name="next-steps"></a>다음 단계

* 데이터를 보호하기 위한 [데이터 액세스 정책 정의](time-series-insights-data-access.md)

* 이벤트 원본으로 [이벤트를 보냅니다](time-series-insights-send-events.md) .

* [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경에 액세스
