---
title: Azure Cosmos DB에서 프로비전된 처리량과 서버리스 중에서 선택하는 방법
description: 워크로드에 대한 프로비저닝된 처리량과 서버리스 중에서 선택하는 방법을 알아봅니다.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 057004a7a322505b137c04b76c3407e4c36f6f89
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378201"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>프로비저닝된 처리량과 서버리스 중에서 선택하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 [프로비저닝된 처리량](set-throughput.md) 및 [서버리스](serverless.md)라는 두 가지 다른 용량 모드로 제공됩니다. 두 모드 모두에서 정확히 동일한 데이터베이스 작업을 수행할 수 있지만 이러한 작업에 대해 비용이 청구되는 방식은 크게 다릅니다. 다음 비디오에서는 이러한 모드 간의 핵심 차이점과 이러한 모드가 다양한 유형의 워크로드에 어떻게 적합한지 설명합니다.

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>자세한 비교

| 조건 | 프로비전된 처리량 | 서버를 사용하지 않음 |
| --- | --- | --- |
| 가장 적합한 용도 | 예측 가능한 성능이 필요한 지속적인 트래픽이 있는 워크로드 | 간헐적이거나 예측할 수 없는 트래픽과 평균-최고 트래픽 비율이 낮은 워크로드 |
| 작동 방식 | 각 컨테이너에 대해 초당 [요청 단위](request-units.md) 표시되는 처리량을 프로비저닝합니다. 요청 단위 크기인 1초 간격을 데이터베이스 작업에 사용할 수 있습니다. 프로비저닝된 처리량은 수동으로 업데이트하거나 [자동 스케일링](provision-throughput-autoscale.md)을 사용하여 자동으로 조정할 수 있습니다. | 용량을 프로비저닝할 필요 없이 컨테이너에 대해 데이터베이스 작업을 실행합니다. |
| 지역 배포 | 사용 가능(Azure 지역 수에 제한 없음) | 사용할 수 없음(서버리스 계정은 1개 Azure 지역에서만 실행할 수 있음) |
| 컨테이너당 최대 스토리지 | 제한 없음 | 50GB |
| 성능 | SLA에 해당되는 지점 읽기 및 쓰기에 대해 10ms 미만 대기 시간 | SLO에 해당되는 지점 읽기에 대해 10ms 미만, 쓰기에 대해 30ms 미만 대기 시간 |
| 청구 모델 | 청구는 사용된 RU 수에 관계없이 프로비저닝된 초당 RU의 시간당 기준으로 수행됩니다. | 데이터베이스 작업에서 사용하는 CPU 양에 대해 시간 단위로 비용이 청구됩니다. |

## <a name="estimating-your-expected-consumption"></a>예상 사용량 예측

경우에 따라 지정된 워크로드에 대해 프로비저닝된 처리량 또는 서버리스 중에서 어떤 방식을 선택해야 하는지 여부가 명확하지 않을 수 있습니다. 이 결정에 도움이 주기 위해 전체 **예상 사용량** 을 예측할 수 있습니다. 이 크기는 한 달 동안 사용할 수 있는 총 CPU 수입니다([여기](plan-manage-costs.md#estimating-serverless-costs)에 표시된 표를 토대로 추정할 수 있음).

**예제 1**: 워크로드가 최대 500RU/s로 버스트되고 한 달 동안 총 20,000,000RU를 사용할 것으로 예상됩니다.

- 프로비저닝된 처리량 모드에서는 월 비용 $0.008*5*730 = **$29.20** 로 500 RU/s의 컨테이너를 프로비저닝합니다.
- 서버리스 모드에서는 사용된 RU에 대해 $0.25*20 = **$5.00** 요금을 지불하게 됩니다.

**예제 2**: 워크로드가 최대 500RU/s로 버스트되고 한 달 동안 총 250,000,000RU를 사용할 것으로 예상됩니다.

- 프로비저닝된 처리량 모드에서는 월 비용 $0.008*5*730 = **$29.20** 로 500 RU/s의 컨테이너를 프로비저닝합니다.
- 서버리스 모드에서는 사용된 RU에 대해 $0.25*250 = **$62.50** 요금을 지불하게 됩니다.

이러한 예제는 스토리지 비용을 고려하지 않으며 두 모드 간에 동일합니다.

> [!NOTE]
> 앞의 예제에 표시된 비용은 데모용으로만 사용됩니다. 최신 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 프로비저닝 처리량](set-throughput.md)에 대해 자세히 알아보기
- [Azure Cosmos DB 서버리스](serverless.md)에 대해 자세히 알아보기
- [요청 단위](request-units.md) 개념에 대해 알아보기
