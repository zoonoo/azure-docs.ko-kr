---
title: Azure Time Series 미리 보기 프로비전 및 관리 | Microsoft Docs
description: Azure Time Series 미리 보기를 프로비전 및 관리하는 방법을 이해합니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47c96cb14f2e466d02d57f73fb2e66bc1f44a311
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989922"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series 미리 보기 프로비전 및 관리

이 문서에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Time Series Insights 미리 보기 환경을 만들고 관리하는 방법을 설명합니다.

## <a name="overview"></a>개요

Azure Time Series Insights 미리 보기 환경은 PAYG(종량제) 환경입니다.

Azure Time Series Insights 미리 보기 환경을 프로 비전 할 때 다음과 같은 Azure 리소스를 만듭니다.

* Azure Time Series Insights 미리 보기 환경  
* Azure Storage 범용 v1 계정
* 더 빠르고 무제한 쿼리를 위한 선택적 웜 저장소
  
[환경을 계획하는 방법](./time-series-insights-update-plan.md)을 알아봅니다.

각 Azure Time Series Insights 미리 보기 환경을 이벤트 원본에 연결 합니다. 자세한 내용은 [이벤트 허브 원본 추가](./time-series-insights-how-to-add-an-event-source-eventhub.md) 및 [IoT Hub 원본 추가](./time-series-insights-how-to-add-an-event-source-iothub.md)를 참조하세요. 이 단계 중에 타임스탬프 ID 속성 및 고유한 소비자 그룹을 제공합니다. 이렇게 하면 환경에서 적합한 이벤트에 액세스할 수 있습니다.

> [!NOTE]
> 이전 단계는 시계열 미리 보기 환경을 만드는 동안 프로 비전 워크플로에서 선택 사항입니다. 그러나 데이터를 해당 환경에서 이동 하기 시작할 수 있도록 환경에 이벤트 원본을 연결 해야 합니다.

프로비저닝이 완료되면 액세스 정책 및 기타 환경 특성을 비즈니스 요구 사항에 맞게 수정할 수 있습니다.

## <a name="create-the-environment"></a>환경 만들기

Azure Time Series Insights 미리 보기 환경을 만들려면 다음을 수행 합니다.

1. **SKU** 메뉴에서 **PAYG** 단추를 선택 합니다. 환경 이름을 제공 하 고 사용할 구독 그룹 및 리소스 그룹을 선택 합니다. 그런 다음 환경을 호스트할 지원 되는 위치를 선택 합니다.

   [![Azure Time Series Insights 인스턴스를 만듭니다.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. 시계열 ID를 입력합니다.

    >[!NOTE]
    > * 시계열 ID는 대/소문자를 구분하며 변경할 수 없습니다. (설정한 후에는 변경할 수 없습니다.)
    > * 시계열 ID는 최대 3개의 키로 구성될 수 있습니다.
    > * 시계열 ID 선택에 대한 자세한 내용은 [시계열 ID 선택](./time-series-insights-update-how-to-id.md)을 참조하세요.

1. Azure Storage 계정 이름을 선택하고 복제 선택을 지정하여 스토리지 계정을 만듭니다. 이렇게 하면 Azure Storage 범용 v1 계정이 자동으로 만들어집니다. 계정은 이전에 선택한 Azure Time Series Insights 미리 보기 환경과 동일한 지역에 만들어집니다.

    [![인스턴스에 대 한 Azure storage 계정을 만듭니다.](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(선택 사항)** 사용자 환경의 최신 데이터에 대해 더 빠르고 무제한 쿼리를 원하는 경우 환경에 웜 스토어를 사용 하도록 설정 합니다. Time Series Insights 미리 보기 환경을 만든 후 왼쪽 탐색 창에서 **저장소 구성** 옵션을 통해 웜 저장소를 만들거나 삭제할 수도 있습니다.

1. **(선택 사항)** 이제 이벤트 소스를 추가할 수 있습니다. 또는 인스턴스가 프로 비전 될 때까지 기다릴 수 있습니다.

   * Time Series Insights는 [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) 및 [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) 을 이벤트 원본 옵션으로 지원 합니다. 환경을 만들 때 단일 이벤트 원본만 추가할 수 있지만 나중에 다른 이벤트 소스를 추가할 수 있습니다. 이벤트 원본을 추가할 때 기존 소비자 그룹을 선택 하거나 새 소비자 그룹을 만들 수 있습니다. 모든 이벤트가 Azure Time Series Insights 미리 보기 환경에 표시 되도록 고유한 소비자 그룹을 만드는 것이 가장 좋습니다.

   * 적절 한 타임 스탬프 속성을 선택 합니다. 기본적으로 Azure Time Series Insights는 각 이벤트 원본에 대 한 메시지 큐에 대기 된 시간을 사용 합니다.

     > [!TIP]
     > 메시지 큐에 넣은 시간은 일괄 처리 이벤트 시나리오 또는 기록 데이터 업로드 시나리오에서 사용 하기에 가장 적합 하 게 구성 된 설정이 아닐 수 있습니다. 이러한 경우에는 타임 스탬프 속성을 사용할지 여부를 결정 해야 합니다.

     [![이벤트 원본 탭](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. 원하는 설정을 사용 하 여 환경을 프로 비전 했는지 확인 합니다.

    [![검토 + 만들기 탭](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>환경 관리

Azure Portal을 사용하여 Azure Time Series Insights 미리 보기 환경을 관리할 수 있습니다. Azure Portal를 통해 관리 하는 경우 종 량 제 Azure Time Series Insights 미리 보기 환경과 일반적으로 사용 가능한 S1 또는 S2 환경 사이에 몇 가지 주요 차이점이 있습니다.

* Azure Portal **개요** 블레이드는 Azure Time Series Insights에서 변경되지 않은 상태로 유지되지만, 다음과 같은 경우는 예외입니다.
  * 종 량 제 환경에는 적용 되지 않으므로 용량이 제거 됩니다.
  * 시계열 ID 속성이 추가 됩니다. 이 속성은 데이터가 분할되는 방식을 결정합니다.
  * 참조 데이터 세트가 제거되었습니다.
  * 표시된 URL은 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)로 이동합니다.
  * Azure Storage 계정 이름이 표시됩니다.

* PAYG 환경을 구성할 수 없기 때문에 Azure Portal의 **구성** 블레이드가 Azure Time Series Insights 미리 보기에서 제거 됩니다. 그러나 **저장소 구성을** 사용 하 여 새로 도입 된 웜 저장소를 구성할 수 있습니다.

* 참조 데이터가 종 량 제 환경의 일부가 아니기 때문에 Azure Portal의 **참조 데이터** 블레이드가 Azure Time Series Insights 미리 보기에서 제거 되었습니다.

[Azure Portal에서 Time Series Insights 미리 보기 환경 ![](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [환경 계획](./time-series-insights-update-plan.md)을 읽어 Time Series Insights 일반적으로 사용 가능한 환경 및 미리 보기 환경에 대해 자세히 알아보세요.

- [이벤트 허브 원본을 추가](./time-series-insights-how-to-add-an-event-source-eventhub.md)하는 방법에 대해 알아봅니다.

- [IoT hub 원본을](./time-series-insights-how-to-add-an-event-source-iothub.md)구성 합니다.
