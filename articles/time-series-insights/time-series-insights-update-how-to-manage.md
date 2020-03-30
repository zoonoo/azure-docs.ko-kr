---
title: 미리 보기 환경 프로비저닝 및 관리 - Azure 타임시리즈 | 마이크로 소프트 문서
description: Azure Time Series 인사이트 미리 보기 환경을 프로비전하고 관리하는 방법을 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087223"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series 미리 보기 프로비전 및 관리

이 문서에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Time Series Insights 미리 보기 환경을 만들고 관리하는 방법을 설명합니다.

## <a name="overview"></a>개요

Azure Time Series 인사이트 미리 보기 환경은 *PAYG(종량제)* 환경입니다.

Azure Time Series Insights 미리 보기 환경을 프로비전할 때 이러한 Azure 리소스를 만듭니다.

* Azure Time Series Insights 미리 보기 환경  
* Azure Storage 범용 v1 계정
* 빠르고 무제한의 쿼리를 위한 옵션 웜 스토어

> [!TIP]
> * [환경을 계획하는 방법](./time-series-insights-update-plan.md)을 알아봅니다.
> * 이벤트 허브 [소스를 추가하는](./time-series-insights-how-to-add-an-event-source-eventhub.md) 방법 또는 [IoT 허브 소스를 추가하는](./time-series-insights-how-to-add-an-event-source-iothub.md)방법에 대해 읽어보십시오.

다음 방법을 알게 됩니다.

1. **(선택 사항)** 각 Azure Time Series Insights 미리 보기 환경을 이벤트 원본과 연결합니다. 또한 환경이 적절한 이벤트에 액세스할 수 있도록 타임스탬프 ID 속성과 고유한 소비자 그룹을 제공합니다.

   > [!NOTE]
   > 이전 단계는 환경을 프로비전할 때 선택 사항입니다. 이 단계를 건너뛰는 경우 나중에 환경에서 데이터에 액세스할 수 있도록 이벤트 원본을 환경에 연결해야 합니다.

1. 프로비저닝이 완료되면 액세스 정책 및 기타 환경 특성을 비즈니스 요구 사항에 맞게 수정할 수 있습니다.

## <a name="create-the-environment"></a>환경 만들기

Azure Time series 인사이트 미리 보기 환경을 만들려면 다음을 수행하십시오.

1. **PAYG를** **계층으로**선택합니다. 환경 이름을 제공하고 사용할 구독 그룹 및 리소스 그룹을 선택합니다. 그런 다음 환경을 호스트할 지원되는 위치를 선택합니다.

   [![Azure Time Series Insights 인스턴스를 만듭니다.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 시계열 ID를 입력합니다.

    > [!NOTE]
    > * 타임시리즈 ID는 *대/소문자를 구분하고* *변경할 수 없습니다.* (설정한 후에는 변경할 수 없습니다.)
    > * 열렬 아이디는 최대 *3개의* 키일 수 있습니다.
    > * [타임시리즈 ID 를 선택하는 방법에](time-series-insights-update-how-to-id.md) 대해 자세히 알아보기

1. 저장소 계정 이름을 선택하고 복제 선택을 지정하여 Azure Storage 계정을 만듭니다. 이렇게 하면 Azure Storage 범용 v1 계정이 자동으로 만들어집니다. 계정은 이전에 선택한 Azure Time Series Insights 미리 보기 환경과 동일한 리전에서 만들어집니다.

    [![콜드 스토리지 구성](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(선택 사항)** 사용자 환경의 최신 데이터에 대해 더 빠르고 무제한의 쿼리를 원하는 경우 환경에 대한 웜 저장소를 사용하도록 설정합니다. 또한 타임시리즈 인사이트 미리 보기 환경을 만든 후 왼쪽 탐색 창의 **저장소 구성** 옵션을 통해 웜 저장소를 만들거나 삭제할 수 있습니다.

    [![웜 스토리지 구성](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(선택 사항)** 지금 이벤트 소스를 추가할 수 있습니다. 인스턴스가 프로비전될 때까지 기다릴 수도 있습니다.

   * Time Series Insights는 [Azure IoT 허브](./time-series-insights-how-to-add-an-event-source-iothub.md) 및 [Azure 이벤트 허브를](./time-series-insights-how-to-add-an-event-source-eventhub.md) 이벤트 소스 옵션으로 지원합니다. 환경을 만들 때 단일 이벤트 원본만 추가할 수 있지만 나중에 다른 이벤트 원본을 추가할 수 있습니다. 
   
     이벤트 소스를 추가할 때 기존 소비자 그룹을 선택하거나 새 소비자 그룹을 만들 수 있습니다. 모든 이벤트가 Azure Time Series Insights 미리 보기 환경에 표시되도록 고유한 소비자 그룹을 만드는 것이 가장 좋습니다.

   * 적절한 타임스탬프 속성을 선택합니다. 기본적으로 Azure Time Series Insights는 각 이벤트 원본에 대해 메시지 큐에 대기된 시간을 사용합니다.

     > [!TIP]
     > 메시지 큐에 대기된 시간은 일괄 처리 이벤트 시나리오 또는 기록 데이터 업로드 시나리오에서 사용하기에 가장 적합한 설정이 아닐 수 있습니다. 이러한 경우 타임스탬프 속성을 사용하거나 사용하지 않기로 한 결정을 확인해야 합니다.

     [![이벤트 소스 구성 탭](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 환경이 원하는 방식으로 프로비전되고 구성되었는지 확인합니다.

    [![리뷰 + 만들기 탭](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>환경 관리

Azure Portal을 사용하여 Azure Time Series Insights 미리 보기 환경을 관리할 수 있습니다. PAYG Azure 서열 인사이트 미리 보기 환경과 Azure 포털을 통해 환경을 관리할 때 염두에 두어야 할 일반적으로 사용 가능한 S1 또는 S2 환경 간에는 몇 가지 주요 차이점이 있습니다.

* Azure 포털 미리 보기 **개요** 블레이드에는 다음과 같은 변경 사항이 있습니다.

  * 용량은 PAYG 환경에 적용되지 않으므로 제거됩니다.
  * **타임시리즈 ID** 속성이 추가됩니다. 이 속성은 데이터가 분할되는 방식을 결정합니다.
  * 참조 데이터 세트가 제거되었습니다.
  * 표시된 URL은 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)로 이동합니다.
  * Azure Storage 계정 이름이 표시됩니다.

* PAYG 환경을 구성할 수 없으므로 Azure 포털의 **구성** 블레이드는 Azure Time Series Insights 미리 보기에서 제거됩니다. 그러나 **저장소 구성을** 사용하여 새로 도입된 웜 저장소를 구성할 수 있습니다.

* 참조 데이터가 PAYG 환경의 일부가 아니므로 Azure 포털의 **참조 데이터** 블레이드는 Azure Time Series Insights 미리 보기에서 제거됩니다.

[![Azure Portal의 Time Series Insights 미리 보기 환경](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [사용자 환경 [계획]을](./time-series-insights-update-plan.md)읽고 일반적으로 사용 가능한 환경 및 미리 보기 환경에 대해 자세히 알아봅니다.

- [이벤트 허브 소스를 추가하는](./time-series-insights-how-to-add-an-event-source-eventhub.md)방법에 대해 알아봅니다.

- [IoT 허브 소스를 구성합니다.](./time-series-insights-how-to-add-an-event-source-iothub.md)
