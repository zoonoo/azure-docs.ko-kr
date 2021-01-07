---
title: Azure Stream Analytics 클러스터 크기 조정
description: Azure Stream Analytics 클러스터의 크기를 확장 및 축소하는 방법을 알아봅니다.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 0763e56de6c72a36d39b17d153db6fc4d7dd821a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946085"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Azure Stream Analytics 클러스터 크기 조정

Stream Analytics 클러스터의 용량은 SU(스트리밍 단위)로 측정됩니다. 실행 중인 모든 작업에 할당된 SU의 합계가 클러스터의 용량을 초과하지 않는 한, 여러 작업을 동일한 클러스터에서 병렬로 실행할 수 있습니다.

스트리밍 워크로드의 크기와 일치하도록 클러스터의 용량을 확장하거나 축소할 수 있습니다. 클러스터의 크기를 조정하는 데에는 시간이 걸리며, 빈번한 크기 조정은 적절하지 않습니다. 사용하려는 정확한 수의 SU를 사용하여 클러스터를 계획하고 프로비저닝하는 것이 좋습니다.

## <a name="change-the-scale-of-your-cluster"></a>클러스터의 크기 조정 변경

1. Azure Portal에서 Stream Analytics 클러스터를 찾아 선택합니다.

1. **개요** 섹션에서 **크기 조정**를 선택합니다. 클러스터에 할당된 SU의 수를 확인할 수 있습니다. 선택기를 사용하여 필요에 따라 SU를 늘리거나 줄일 수 있습니다.

   ![클러스터 크기 조정](./media/scale-cluster/scale-cluster.png)

크기 조정 작업은 현재 실행 중인 작업에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

이제 Stream Analytics 클러스터를 확장하고 축소하는 방법을 배웠습니다. 다음으로 프라이빗 엔드포인트를 관리하고 작업의 크기를 자동으로 조정하는 방법을 알아볼 수 있습니다.

* [Azure Stream Analytics 클러스터에서 프라이빗 엔드포인트 관리](private-endpoints.md)
* [Azure Stream Analytics 클러스터에서 작업 관리](manage-jobs-cluster.md)
