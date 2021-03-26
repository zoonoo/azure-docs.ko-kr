---
title: Azure Portal Azure Time Series Insights Gen2를 사용 하 여 Gen2 환경 설정 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Time Series Insights Gen2에서 환경을 설정 하는 방법을 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952850"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Time Series Insights Gen2 환경을 만듭니다.

이 문서에서는 [Azure Portal](https://portal.azure.com/)를 사용 하 여 Azure Time Series Insights Gen2 환경을 만드는 방법을 설명 합니다.

환경 프로 비전 자습서는이 과정을 안내 합니다. 올바른 시계열 ID를 선택 하 고 두 JSON 페이로드의 예제를 확인 하는 방법에 대해 알아봅니다.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>개요

Azure Time Series Insights Gen2 환경을 프로 비전 할 때 다음과 같은 Azure 리소스를 만듭니다.

* 종 량 제 가격 책정 모델을 따르는 Azure Time Series Insights Gen2 환경
* Azure Storage 계정
* 더 빠르고 무제한 쿼리를 위한 선택적 웜 저장소

> [!TIP]
>
> * [환경을 계획하는 방법](./how-to-plan-your-environment.md)을 알아봅니다.
> * [이벤트 허브 원본을 추가](./how-to-ingest-data-event-hub.md) 하는 방법 또는 [IoT hub 원본을 추가](./how-to-ingest-data-iot-hub.md)하는 방법을 참조 하세요.

다음 방법을 알게 됩니다.

1. 각 Azure Time Series Insights Gen 2 환경을 이벤트 원본에 연결 합니다. 또한 환경에서 적절 한 이벤트에 액세스할 수 있도록 타임 스탬프 ID 속성과 고유한 소비자 그룹을 제공 합니다.

1. 프로 비전이 완료 된 후 비즈니스 요구 사항에 맞게 액세스 정책 및 기타 환경 특성을 수정할 수 있습니다.

   > [!NOTE]
   > 환경을 프로 비전 할 때 첫 번째 단계는 선택 사항입니다. 이 단계를 건너뛰는 경우 나중에 데이터를 환경으로 이동 하기 시작 하 고 쿼리를 통해 액세스할 수 있도록 나중에 이벤트 원본을 환경에 연결 해야 합니다.

## <a name="create-the-environment"></a>환경 만들기

Azure Time Series Insights Gen 2 환경을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)의 *사물 인터넷* 에 Azure Time Series Insights 리소스를 만듭니다.

1. **계층** 으로 **Gen2 (L1)** 을 선택 합니다. 환경 이름을 제공 하 고 사용할 구독 그룹 및 리소스 그룹을 선택 합니다. 그런 다음 환경을 호스트할 지원 되는 위치를 선택 합니다.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Azure Time Series Insights 인스턴스를 만듭니다." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. 시계열 ID를 입력합니다.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="계속 해 서 Azure Time Series Insights 환경 구성을 만듭니다." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * 시계열 ID는 *대/소문자를 구분* 하 고 *변경할* 수 없습니다. (설정한 후에는 변경할 수 없습니다.)
   > * 시계열 Id는 최대 *3 개의* 키로 지정할 수 있습니다. 사용자 환경으로 데이터를 전송 하는 각 장치 센서를 고유 하 게 나타내는 데이터베이스의 기본 키로 간주 합니다. 속성 하나 또는 최대 세 개의 속성을 조합 하 여 사용할 수 있습니다.
   > * [시계열 ID를 선택 하는 방법](./how-to-select-tsid.md) 에 대해 자세히 알아보세요.

1. 저장소 계정 이름, 계정 종류를 선택 하 고 [복제](../storage/common/redundancy-migration.md?tabs=portal) 선택 항목을 지정 하 여 Azure Storage 계정을 만듭니다. 이렇게 하면 Azure Storage 계정이 자동으로 만들어집니다. 기본적으로 범용 [v2](../storage/common/storage-account-overview.md) 계정이 생성 됩니다. 계정은 이전에 선택한 Azure Time Series Insights Gen2 환경과 동일한 지역에 만들어집니다.
또는 새 Azure Time Series Gen2 환경을 만들 때 [Azure Resource Manager 템플릿을](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) 통해 사용자 고유의 저장소 (byos)를 가져올 수도 있습니다.

1. **(선택 사항)** 사용자 환경의 최신 데이터에 대해 더 빠르고 무제한 쿼리를 원하는 경우 환경에 웜 스토어를 사용 하도록 설정 합니다. Azure Time Series Insights Gen2 환경을 만든 후 왼쪽 탐색 창에서 **저장소 구성** 옵션을 통해 웜 저장소를 만들거나 삭제할 수도 있습니다.

1. **(선택 사항)** 이제 이벤트 소스를 추가할 수 있습니다. 인스턴스가 프로 비전 될 때까지 기다릴 수도 있습니다.

   * Azure Time Series Insights는 [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) 및 [Azure Event Hubs](./how-to-ingest-data-event-hub.md) 을 이벤트 원본 옵션으로 지원 합니다. 환경을 만들 때 단일 이벤트 원본만 추가할 수 있지만 나중에 다른 이벤트 소스를 추가할 수 있습니다.

     이벤트 원본을 추가할 때 기존 소비자 그룹을 선택 하거나 새 소비자 그룹을 만들 수 있습니다. 이벤트 원본에서 사용자 환경의 고유한 소비자 그룹을 사용 하 여 데이터를 읽도록 해야 합니다.

   * 적절 한 타임 스탬프 속성을 선택 합니다. 기본적으로 Azure Time Series Insights는 각 이벤트 원본에 대 한 메시지 큐에 대기 된 시간을 사용 합니다.

     > [!TIP]
     > 메시지 큐에 넣은 시간은 일괄 처리 이벤트 시나리오 또는 기록 데이터 업로드 시나리오에서 사용 하기에 가장 적합 하 게 구성 된 설정이 아닐 수 있습니다. 이러한 경우에는 타임 스탬프 속성을 사용할지 여부를 결정 해야 합니다.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="이벤트 원본 구성 탭" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. **검토 + 만들기** 를 선택 하 여 원하는 방식으로 환경이 프로 비전 되 고 구성 되었는지 확인 합니다.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="검토 + 만들기 탭" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>다음 단계

* [환경 계획](./how-to-plan-your-environment.md)을 읽어 Azure Time Series Insights 일반적으로 사용할 수 있는 환경 및 Gen2 환경에 대해 자세히 알아보세요.
* Azure Time Series Insights Gen2 환경에 대 한 [스트리밍 수집 이벤트 원본](./concepts-streaming-ingestion-event-sources.md) 에 대해 알아봅니다.
* [환경을 관리 하는 방법](./how-to-provision-manage.md)에 대해 자세히 알아보세요.
