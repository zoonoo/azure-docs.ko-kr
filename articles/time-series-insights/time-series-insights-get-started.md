---
title: 환경 만들기 - Azure Time Series Insights | Microsoft Docs
description: Azure Portal를 사용 하 여 새 Time Series Insights 환경을 만드는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81602369"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Azure Portal에서 Time Series Insights 환경 만들기

이 문서에서는 Azure Portal을 사용하여 새로운 Time Series Insights 환경을 만드는 방법을 설명합니다.

Time Series Insights를 사용하여 Azure IoT Hub 및 Event Hubs로 이동하는 데이터를 빠른 시간 안에 시각화하고 쿼리할 수 있으므로 대용량 시계열 데이터를 빠르게 쿼리할 수 있습니다.  이 기능은 IoT(사물 인터넷) 규모에 맞게 설계되었으며 테라바이트 단위의 데이터를 처리할 수 있습니다.

## <a name="steps-to-create-the-environment"></a>환경을 만드는 단계

다음 단계에 따라 환경을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **+ 리소스 만들기** 단추를 선택 합니다.

1. **사물 인터넷** 범주를 선택하고 **Time Series Insights**를 선택합니다.

   [![Time Series Insights 환경 만들기](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. **Time Series Insights** 페이지에서 **만들기**를 선택합니다.

1. 필수 매개 변수를 입력합니다. 다음 표에서는 각 매개 변수에 대해 설명합니다.

   [![Time Series Insights 리소스 그룹 만들기](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   설정|제안 값|Description
   ---|---|---
   환경 이름 | 고유한 이름 | 이 이름은 [Time Series 탐색기](https://insights.timeseries.azure.com)에서 해당 환경을 나타내는 데 사용됩니다.
   Subscription | 사용자의 구독 | 구독이 여러 개인 경우 이벤트 원본을 포함하는 구독을 선택합니다. Time Series Insights는 동일한 구독의 기존 Azure IoT Hub 및 이벤트 허브 리소스를 자동으로 검색할 수 있습니다.
   Resource group | 새로 만들기 또는 기존 항목 사용 | 리소스 그룹은 함께 사용되는 Azure 리소스 컬렉션입니다. 예를 들어 이벤트 허브 또는 IoT Hub를 포함하는 기존 리소스 그룹을 선택할 수 있습니다. 또는 이 리소스가 다른 리소스와 관련이 없는 경우에는 새로 만들 수 있습니다.
   위치 | 이벤트 원본에 가장 가까운 위치 | 지역 외부로 데이터를 이동할 때 지역 간 및 영역 간 대역폭 비용이 추가되고 대기 시간이 늘어나지 않도록 하기 위해 이벤트 원본 데이터를 포함하는 동일한 데이터 센터 위치를 선택하는 것이 좋습니다.
   가격 책정 계층 | S1 | 필요한 처리량을 선택합니다. 최저 비용의 시작 용량을 사용하려면 S1을 선택합니다.
   용량 | 1 | 용량은 선택한 SKU와 관련된 입력 속도, 스토리지 용량 및 비용에 적용되는 승수입니다.  환경을 만든 후 환경의 용량을 변경할 수 있습니다. 최저 비용을 원할 경우 용량 1을 선택합니다.
  
1. **만들기**를 선택하여 프로비전 프로세스를 시작합니다. 이 작업에 몇 분 정도가 소요됩니다.

1. 배포 프로세스를 모니터링하려면 **알림** 기호(종 모양)를 선택합니다.

   [![알림 보기](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. 리소스 **개요**에서 배포 구성 설정을 확인 합니다.

   [![Time Series Insights를 만들고 대시보드에 고정](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(선택 사항)** 오른쪽 위 모서리에 있는 **고정 아이콘** 을 선택 하 여 나중에 Time Series Insights 환경에 쉽게 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 환경을 보호하기 위한 [데이터 액세스 정책 정의](time-series-insights-data-access.md)

* Azure Time Series Insights 환경에 [이벤트 허브 이벤트 원본 추가](time-series-insights-how-to-add-an-event-source-eventhub.md)

* 이벤트 원본으로 [이벤트를 보냅니다](time-series-insights-send-events.md) .

* [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경을 봅니다.
