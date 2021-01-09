---
title: Azure Cosmos DB에서 프로 비전 된 처리량과 서버 리스 서버를 선택 하는 방법
description: 프로 비전 된 처리량을 선택 하 고 워크 로드에 대해 서버 리스를 선택 하는 방법에 대해 알아봅니다.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 3f5c3400f319a3f9d5f1544457b009f90d479634
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049833"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>프로 비전 된 처리량과 서버 리스 서버 중에서 선택 하는 방법
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB는 [프로 비전 된 처리량](set-throughput.md) 및 [서버](serverless.md)를 사용 하지 않는 두 가지 용량 모드로 제공 됩니다. 두 모드 모두에서 정확히 동일한 데이터베이스 작업을 수행할 수 있지만 이러한 작업에 대해 비용이 청구 되는 방식은 매우 다릅니다. 다음 비디오에서는 이러한 모드와 이러한 모드의 여러 가지 작업 유형에 대 한 주요 차이점을 설명 합니다.

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

## <a name="detailed-comparison"></a>자세히 비교

| 조건 | 프로비전된 처리량 | 서버를 사용하지 않음 |
| --- | --- | --- |
| 상태 | 일반 공급 | 미리 보기 |
| 가장 적합한 용도 | 예측 가능한 성능이 필요한 지속적인 트래픽이 있는 워크 로드 | 간헐적 이거나 예기치 않은 트래픽이 있고 평균 대 최고 트래픽 비율이 낮은 워크 로드 |
| 작동 방식 | 각 컨테이너에 대해 초당 [요청 단위로](request-units.md) 표현 된 처리량을 프로 비전 합니다. 초 마다이 요청 단위를 데이터베이스 작업에 사용할 수 있습니다. 프로 비전 된 처리량을 수동으로 업데이트 하거나 자동 [크기 조정을](provision-throughput-autoscale.md)사용 하 여 자동으로 조정할 수 있습니다. | 용량을 프로 비전 하지 않고도 컨테이너에 대해 데이터베이스 작업을 실행할 수 있습니다. |
| 지역 배포 | 사용 가능 (Azure 지역 수 제한 없음) | 사용할 수 없음 (서버 리스 계정은 1 개의 Azure 지역 에서만 실행할 수 있음) |
| 컨테이너당 최대 스토리지 | 제한 없음 | 50GB |
| 성능 | SLA가 적용 되는 지점 읽기 및 쓰기에 대해 10 밀리초의 대기 시간 < | SLO에서 적용 되는 쓰기에 대 한 지점 읽기 및 < 30ms 대기 시간 < |
| 청구 모델 | 청구는 사용된 RU 수에 관계없이 프로비저닝된 초당 RU의 시간당 기준으로 수행됩니다. | 청구는 데이터베이스 작업에서 사용 하는 RUs의 양에 따라 시간당으로 수행 됩니다. |

> [!IMPORTANT]
> 서버 리스 제한 중 일부는 서버 리스가 일반 공급 되 고 **사용자 의견** 을 확인 하는 데 도움이 되는 경우 줄어들 또는 제거 될 수 있습니다. 서버를 사용 하지 않는 환경에 대해 설명 하 고이에 대해 자세히 알려주세요 [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="estimating-your-expected-consumption"></a>예상 사용량 예측

일부 경우에는 지정 된 워크 로드에 대해 프로 비전 된 처리량 또는 서버를 사용 하지 않는 것이 확실 하지 않을 수 있습니다. 이 결정에 도움이 되도록 전체 **예상 사용량** 을 예측할 수 있습니다. 여기에는 한 달 동안 소비할 수 있는 총 RUs 수가 나와 있습니다. [여기](plan-manage-costs.md#estimating-serverless-costs)에 표시 된 표를 참조 하 여이를 예측할 수 있습니다.

**예제 1**: 작업은 최대 500 r u/s로 버스트 되 고 한 달 동안 총 2000만 RUs를 사용 합니다.

- 프로 비전 된 처리량 모드에서는 월별 비용 ($0.008 * 5 * 730 = **$29.20** )에 대해 500 r u/초를 사용 하 여 컨테이너를 프로 비전 합니다.
- 서버 리스 모드에서는 사용 된 RUs에 대해 요금을 지불 합니다. $0.25 * 20 = **$5.00**

**예제 2**: 워크 로드는 최대 500 r u/s로 버스트 되 고 한 달 동안 총 2억5000만 RUs를 사용 합니다.

- 프로 비전 된 처리량 모드에서는 월별 비용 ($0.008 * 5 * 730 = **$29.20** )에 대해 500 r u/초를 사용 하 여 컨테이너를 프로 비전 합니다.
- 서버 리스 모드에서는 사용 된 RUs에 대해 요금을 지불 합니다. $0.25 * 250 = **$62.50**

이러한 예제는 저장소 비용을 고려 하지 않으며 두 모드 간에 동일 합니다.

> [!NOTE]
> 앞의 예제에 표시 된 비용은 데모용 으로만 사용 됩니다. 최신 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB에서 프로 비전 처리량](set-throughput.md) 에 대해 자세히 알아보세요.
- 서버를 사용 하지 않는 [Azure Cosmos DB](serverless.md) 에 대해 자세히 알아보세요.
- [요청 단위의](request-units.md) 개념에 대해 알아보기
