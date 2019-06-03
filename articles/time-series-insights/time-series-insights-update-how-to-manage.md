---
title: Azure Time Series 미리 보기 프로비전 및 관리 | Microsoft Docs
description: Azure Time Series 미리 보기를 프로비전 및 관리하는 방법을 이해합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6251df2317ceff9dded92f2d829bfab0503fdf1b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237597"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series 미리 보기 프로비전 및 관리

이 문서에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Time Series Insights 미리 보기 환경을 만들고 관리하는 방법을 설명합니다.

## <a name="overview"></a>개요

Azure Time Series Insights 미리 보기 환경은 PAYG(종량제) 환경입니다.

Azure Time Series Insights 미리 보기 환경을 프로비전할 때는 두 개의 Azure 리소스를 만듭니다.

* Azure Time Series Insights 미리 보기 환경  
* Azure Storage 범용 v1 계정
  
[환경을 계획하는 방법](./time-series-insights-update-plan.md)을 알아봅니다.

>[!IMPORTANT]
> 미리 보기의 경우는 Azure Storage의 범용 v1 사용 중인지 확인 (GPv1) 계정을 합니다.

경우에 따라 각 Azure Time Series Insights 미리 보기 환경을 이벤트 원본에 연결할 수 있습니다. 자세한 내용은 [이벤트 허브 원본 추가](./time-series-insights-how-to-add-an-event-source-eventhub.md) 및 [IoT Hub 원본 추가](./time-series-insights-how-to-add-an-event-source-iothub.md)를 참조하세요. 이 단계 중에 타임스탬프 ID 속성 및 고유한 소비자 그룹을 제공합니다. 이렇게 하면 환경에서 적합한 이벤트에 액세스할 수 있습니다.

프로비저닝이 완료되면 액세스 정책 및 기타 환경 특성을 비즈니스 요구 사항에 맞게 수정할 수 있습니다.

## <a name="create-the-environment"></a>환경 만들기

다음 단계에서는 Azure Time Series Insights 미리 보기 환경을 만드는 방법을 설명합니다.

1. **SKU** 메뉴 아래의 **PAYG** 단추를 선택합니다. 환경 이름을 제공하고 사용할 구독 그룹 및 리소스 그룹을 선택합니다. 그런 다음, 환경을 호스트할 지원되는 위치를 선택합니다.

   [![Azure Time Series Insights 인스턴스를 만듭니다.](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

1. 시계열 ID를 입력합니다.

    >[!NOTE]
    > * 시계열 ID는 대/소문자를 구분하며 변경할 수 없습니다. (설정한 후에는 변경할 수 없습니다.)
    > * 시계열 ID는 최대 3개의 키로 구성될 수 있습니다.
    > * 시계열 ID 선택에 대한 자세한 내용은 [시계열 ID 선택](./time-series-insights-update-how-to-id.md)을 참조하세요.

1. Azure Storage 계정 이름을 선택하고 복제 선택을 지정하여 스토리지 계정을 만듭니다. 이렇게 하면 Azure Storage 범용 v1 계정이 자동으로 만들어집니다. 이전에 선택한 Azure Time Series Insights 미리 보기 환경과 동일한 Azure 지역에서 만들어집니다.

    [![인스턴스에 대 한 Azure storage 계정 만들기](media/v2-update-manage/manage_five.PNG)](media/v2-update-manage/manage_five.PNG#lightbox)

1. 선택적으로 이벤트 원본을 추가할 수 있습니다.

   * Time Series Insights는 [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) 및 [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md)를 옵션으로 지원합니다. 환경을 만들 때는 단일 이벤트 원본만 추가할 수 있지만 나중에 이벤트 원본을 더 추가할 수 있습니다. 모든 이벤트가 Azure Time Series Insights 미리 보기 인스턴스에 표시되도록 고유한 소비자 그룹을 만드는 것이 가장 좋습니다. 이벤트 원본을 추가할 때 기존 소비자 그룹을 선택하거나 새 소비자 그룹을 만들 수 있습니다.

   * 또한 적절한 타임스탬프 속성을 선택해야 합니다. 기본적으로 Azure Time Series Insights는 각 이벤트 원본에 대해 메시지를 큐에 넣은 시간을 사용합니다.

     > [!TIP]
     > 메시지를 큐에 넣은 시간은 일괄 처리 이벤트 또는 기록 데이터 업로드 시나리오에서 사용하기에 가장 적합한 구성 설정이 아닐 수 있습니다. 이러한 경우 타임스탬프 속성을 사용하거나 사용하지 않는 결정을 확인합니다.

     [![이벤트 원본 탭](media/v2-update-manage/manage_two.PNG)](media/v2-update-manage/manage_two.PNG#lightbox)

1. 사용자 환경이 원하는 설정으로 프로비전되었는지 확인합니다.

    [![검토 + 만들기 탭](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

## <a name="manage-the-environment"></a>환경 관리

Azure Portal을 사용하여 Azure Time Series Insights 미리 보기 환경을 관리할 수 있습니다. Azure Portal을 사용하여 S1 또는 S2 환경과 반대로 PAYG Azure Time Series Insights 미리 보기 환경을 관리하는 경우의 주요 차이점은 다음과 같습니다.

* Azure Portal **개요** 블레이드는 Azure Time Series Insights에서 변경되지 않은 상태로 유지되지만, 다음과 같은 경우는 예외입니다.
  * 용량은 해당 개념이 PAYG 환경과 관련이 없으므로 제거되었습니다.
  * 시계열 ID 속성이 추가되었습니다. 이 속성은 데이터가 분할되는 방식을 결정합니다.
  * 참조 데이터 세트가 제거되었습니다.
  * 표시된 URL은 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)로 이동합니다.
  * Azure Storage 계정 이름이 표시됩니다.

* PAYG 환경을 구성할 수 없으므로 Azure Portal **구성** 블레이드는 Azure Time Series Insights 미리 보기에서 제거되었습니다.

* 참조 데이터가 PAYG 환경의 구성 요소가 아니므로 Azure Portal **참조 데이터** 블레이드는 Azure Time Series Insights 미리 보기에서 제거되었습니다.

[![Azure portal에서 time Series Insights 미리 보기 환경](media/v2-update-manage/manage_four.PNG)](media/v2-update-manage/manage_four.PNG#lightbox)

## <a name="next-steps"></a>다음 단계

- [환경 계획](./time-series-insights-update-plan.md)을 읽어봅니다.

- 설명 하는 방법 [이벤트 허브 원본을 추가](./time-series-insights-how-to-add-an-event-source-eventhub.md)합니다.

- 구성할 [IoT hub 소스를](./time-series-insights-how-to-add-an-event-source-iothub.md)입니다.