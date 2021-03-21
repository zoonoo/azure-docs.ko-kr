---
title: Gen 2 환경 관리-Azure 시계열 | Microsoft Docs
description: Azure Time Series Insights Gen 2 환경을 관리 하는 방법에 대해 알아봅니다.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461898"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 관리

[Azure CLI](./how-to-create-environment-using-cli.md) 또는 [Azure Portal](./how-to-create-environment-using-portal.md)를 사용 하 여 Azure Time Series Insights Gen2 환경을 만든 후에는 비즈니스 요구 사항에 맞게 액세스 정책 및 기타 환경 특성을 수정할 수 있습니다.

## <a name="manage-the-environment"></a>환경 관리

[Azure Portal](https://portal.azure.com/)를 사용 하 여 Azure Time Series Insights Gen2 환경을 관리할 수 있습니다. Azure Portal를 통해 환경을 관리 하는 경우 Gen2 환경과 Gen1 S1 또는 Gen1 S2 환경 사이에 몇 가지 주요 차이점이 있습니다.

* Azure Portal Gen2 **개요** 창에는 다음과 같은 변경 내용이 있습니다.

  * Gen2 환경에는 적용 되지 않으므로 용량이 제거 됩니다.
  * **시계열 ID** 속성이 추가 됩니다. 이 속성은 데이터가 분할되는 방식을 결정합니다.
  * 참조 데이터 세트가 제거되었습니다.
  * 표시 된 URL은 [Azure Time Series Insights 탐색기](./concepts-ux-panels.md)로 안내 합니다.
  * Azure Storage 계정 이름이 표시됩니다.

* 배율 단위는 Azure Time Series Insights Gen2 환경에 적용 되지 않으므로 Azure Portal의 **구성** 창이 제거 됩니다. 그러나 **저장소 구성을** 사용 하 여 새로 도입 된 웜 저장소를 구성할 수 있습니다.

* 참조 데이터 개념은 [TSM (시계열 모델)](./concepts-model-overview.md)로 대체 되었기 때문에 Azure Portal의 **참조 데이터** 창은 Azure Time Series Insights Gen2에서 제거 됩니다.

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Portal Azure Time Series Insights Gen2 환경":::

## <a name="next-steps"></a>다음 단계

* [스트리밍 수집 모범 사례](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) 목록 검토
* [진단 및 문제 해결](./how-to-diagnose-troubleshoot.md) 방법 이해
