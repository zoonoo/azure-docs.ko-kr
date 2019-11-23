---
title: Plan your Preview environment - Azure Time Series Insights | Microsoft Docs
description: Learn how to plan your Azure Time Series Insights Preview environment.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9fb2dcf2c05b709340f8e9ae549bab5756e6abf2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420319"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights 미리 보기 환경 계획

This article describes best practices to plan and get started quickly by using Azure Time Series Insights Preview.

> [!NOTE]
> For best practices to plan a general availability Time Series Insights instance, see [Plan your Azure Time Series Insights general availability environment](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>계획 및 준비 모범 사례

Best practices surrounding planning for and preparing your environment are described further in the following articles:

* What you get when you [provision a Time Series Insights Preview environment](#the-preview-environment).
* What your [Time Series IDs and Timestamp properties are](#configure-time-series-ids-and-timestamp-properties).
* What the new [Time Series Model is](#understand-the-time-series-model), and how to build your own.
* How to [send events efficiently in JSON](#shape-your-events).
* Time Series Insights [business disaster recovery options](#business-disaster-recovery).

Azure Time Series Insights employs a pay-as-you-go business model. 요금 및 용량에 대한 자세한 내용은 [Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

## <a name="the-preview-environment"></a>The preview environment

Time Series Insights 미리 보기 환경을 프로비전할 때는 다음 두 개의 Azure 리소스를 만듭니다.

* Azure Time Series Insights 미리 보기 환경
* Azure Storage 범용 V1 계정

As part of the provisioning process, you specify whether you want to enable a warm store. Warm store provides you with a tiered query experience. When enabled, you must specify a retention period between 7 and 30 days. Queries executed within the warm store retention period generally provide faster response times. When a query spans over the warm store retention period, it's served from cold store.

Queries on warm store are free, while queries on cold store incur costs. It's important to understand your query patterns and plan your warm store configuration accordingly. We recommend that interactive analytics on the most recent data reside in your warm store and pattern analysis and long-term trends reside in cold.

> [!NOTE]
> We currently support a maximum of 1,000 properties with warm store.

시작하려면 다음 세 개의 추가 항목이 필요합니다.

* [시계열 모델](./time-series-insights-update-tsm.md)
* [Time Series Insights에 연결된 이벤트 원본](./time-series-insights-how-to-add-an-event-source-iothub.md)
* 모델에 매핑되고 유효한 JSON 형식인, [이벤트 원본으로 흐르는 이벤트](./time-series-insights-send-events.md)

## <a name="review-preview-limits"></a>Review preview limits

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure Time Series IDs and Timestamp properties

새로운 Time Series Insights 환경을 만들려면 시계열 ID를 선택합니다. 이렇게 하면 데이터의 논리 파티션으로 작동합니다. 설명한 대로, 시계열 ID가 준비되었는지 확인합니다.

> [!IMPORTANT]
> Time Series IDs *can't be changed later*. 최종 선택하고 처음 사용하기 전에 각 항목을 확인합니다.

You can select up to three keys to uniquely differentiate your resources. 자세한 내용은 [시계열 ID 선택 모범 사례](./time-series-insights-update-how-to-id.md) 및 [스토리지 및 수신](./time-series-insights-update-storage-ingress.md)을 참조하세요.

The **Timestamp** property is also important. 이벤트 원본을 추가할 때 이 속성을 지정할 수 있습니다. 각 이벤트 원본에는 시간 경과에 따라 이벤트 원본을 추적하는 데 사용되는 선택적 타임스탬프 속성이 있습니다. 타임스탬프 값은 대/소문자를 구분하며 각 이벤트 원본의 개별 사양에 맞는 형식이어야 합니다.

> [!TIP]
> 이벤트 원본에 대한 형식 및 구문 분석 요구 사항을 확인합니다.

이 값을 비워 두면 이벤트 원본의 이벤트를 큐에 넣는 시간이 이벤트 타임스탬프로 사용됩니다. 기록 데이터 또는 일괄 처리 이벤트를 전송하는 경우 타임스탬프 속성을 사용자 지정하는 것이 기본 이벤트를 큐에 넣는 시간보다 더 유용합니다. For more information, read about how to [add event sources in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>시계열 모델 이해

이제 Time Series Insights 환경의 시계열 모델을 구성할 수 있습니다. 새 모델을 사용하면 IoT 데이터를 쉽게 찾아서 분석할 수 있습니다. 시계열 데이터의 큐레이션, 유지 관리 및 보강을 가능하게 하며, 소비자가 바로 사용할 수 있는 데이터 세트를 준비하는 데 도움이 됩니다. The model uses Time Series IDs, which map to an instance that associates the unique resource with variables, known as types, and hierarchies. 새로운 [시계열 모델](./time-series-insights-update-tsm.md)에 대해 읽어 보세요.

모델은 동적이므로 언제든지 빌드할 수 있습니다. 빠르게 시작하려면 데이터를 Time Series Insights에 푸시하기 전에 빌드 및 업로드합니다. 모델을 빌드하려면 [시계열 모델 사용](./time-series-insights-update-how-to-tsm.md)을 참조하세요.

많은 고객의 경우, 시계열 모델은 이미 구현된 기존 자산 모델 또는 ERP 시스템에 매핑됩니다. 기존 모델이 없는 경우, 빠르게 시작하고 실행하도록 사전 빌드된 사용자 환경이 [제공](https://github.com/Microsoft/tsiclient)됩니다. 모델이 어떻게 도움이 되는지를 확인하려면 [샘플 데모 환경](https://insights.timeseries.azure.com/preview/demo)을 살펴보세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Time Series Insights에 이벤트를 보내는 방법을 확인할 수 있습니다. 이벤트를 효율적으로 잘 비정규화하는 것이 좋습니다.

경험상 좋은 방법:

* Store metadata in your Time Series Model.
* Ensure that Time Series Mode, instance fields, and events include only necessary information, such as a Time Series ID or Timestamp property.

자세한 내용은 [이벤트 셰이핑](./time-series-insights-send-events.md#supported-json-shapes)을 참조하세요.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- Review [Azure Advisor](../advisor/advisor-overview.md) to plan out your business recovery configuration options.
- Read more about [storage and ingress](./time-series-insights-update-storage-ingress.md) in the Time Series Insights Preview.
- Learn about [data modeling](./time-series-insights-update-tsm.md) in the Time Series Insights Preview.
