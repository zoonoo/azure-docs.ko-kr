---
title: Azure Cosmos DB 용량 플래너를 사용하여 비용 예측
description: Azure Cosmos DB 용량 플래너를 사용하면 워크로드에 필요한 처리량과 비용을 예측할 수 있습니다. 이 문서에서는 새 버전의 용량 플래너를 사용하여 필요한 처리량과 비용을 예측하는 방법에 대해 설명합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707631"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Azure 코스모스 DB 용량 플래너를 사용하여 RU/s 추정

Azure Cosmos 데이터베이스 및 컨테이너를 적절한 양의 프로비저닝된 처리량 또는 [요청 단위(RU/s)로](request-units.md)구성하는 것은 비용과 성능을 최적화하는 데 필수적입니다. 이 문서에서는 Azure Cosmos DB [용량 플래너를](https://cosmos.azure.com/capacitycalculator/) 사용하여 워크로드에 필요한 RU/s 및 비용을 예측하는 방법을 설명합니다. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB 용량 플래너를 사용하여 처리량 및 비용을 추정하는 방법

용량 플래너는 두 가지 모드에서 사용할 수 있습니다.

|**모드**  |**설명**  |
|---------|---------|
|Basic|빠른 높은 수준의 RU/s 및 비용 견적을 제공합니다. 이 모드는 인덱싱 정책, 일관성 및 기타 매개 변수에 대한 기본 Azure Cosmos DB 설정을 가정합니다. <br/><br/>Azure Cosmos DB에서 실행할 잠재적인 워크로드를 평가할 때 빠른 상위 수준 추정에 기본 모드를 사용합니다.|
|고급|비용 및 처리량에 영향을 주는 인덱싱 정책, 일관성 수준 및 기타 매개 변수와 같은 추가 설정을 조정할 수 있는 더 자세한 RU/s 및 비용 견적을 제공합니다. <br/><br/>새 프로젝트에 대한 RU/s를 예측하거나 더 자세한 추정을 원할 때 고급 모드를 사용합니다. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>기본 모드를 사용하여 프로비저닝된 처리량 및 비용 예측
기본 모드를 사용하여 워크로드에 대한 빠른 추정을 얻으려면 [용량 플래너로](https://cosmos.azure.com/capacitycalculator/)이동하십시오. 워크로드에 따라 다음 매개 변수를 입력합니다. 

|**입력**  |**설명**  |
|---------|---------|
|지역 수|Azure Cosmos DB는 모든 새 Azure 하위 지역에 제공됩니다. 워크로드에 필요한 지역 수를 선택합니다. 원하는 수의 지역을 Cosmos 계정과 연결할 수 있습니다. 자세한 내용은 Azure Cosmos DB의 [전역 배포를](distribute-data-globally.md) 참조하십시오.|
|다중 지역 쓰기|[다중 지역 쓰기를](distribute-data-globally.md#key-benefits-of-global-distribution)사용하도록 설정하면 응용 프로그램이 모든 Azure 지역을 읽고 쓸 수 있습니다. 다중 지역 쓰기를 사용하지 않도록 설정하면 응용 프로그램이 단일 지역에 데이터를 쓸 수 있습니다. <br/><br/> 다른 리전에서 낮은 대기 시간 쓰기가 필요한 활성 워크로드가 예상되는 경우 다중 리전 쓰기를 사용하도록 설정합니다. 예를 들어 다른 지역의 높은 볼륨으로 데이터베이스에 데이터를 기록하는 IOT 워크로드입니다. <br/><br/> 다중 영역 쓰기는 99.999%의 읽기 및 쓰기 가용성을 보장합니다. 다중 영역 쓰기는 단일 쓰기 영역과 비교할 때 더 많은 처리량이 필요합니다. 자세한 내용은 단일 및 다중 쓰기 지역 문서에 [대한 루가 어떻게 다른지](optimize-cost-regions.md) 참조하세요.|
|저장된 총 데이터(지역별)|단일 리전에서 GB에 저장된 총 예상 데이터입니다.|
|항목 크기|1KB에서 2MB에 이르는 데이터 항목(예: 문서)의 예상 크기입니다. |
|지역별 읽기/초|초당 예상되는 읽기 수입니다. |
|지역별 쓰기/초|초당 예상되는 쓰기 수입니다. |

필요한 세부 정보를 입력한 후 **계산을**선택합니다. **비용 예측** 탭에는 저장소 및 프로비전된 처리량의 총 비용이 표시됩니다. 이 탭의 **세부정보 표시** 링크를 확장하여 읽기 및 쓰기 요청에 필요한 처리량을 분석할 수 있습니다. 필드 값을 변경할 때마다 **계산을** 선택하여 예상 비용을 다시 계산합니다. 

![용량 플래너 기본 모드](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>고급 모드를 사용하여 프로비저닝된 처리량 및 비용 예측

고급 모드를 사용하면 RU/s 추정치에 영향을 주는 더 많은 설정을 제공할 수 있습니다. 이 옵션을 사용하려면 [용량 플래너로](https://cosmos.azure.com/capacitycalculator/) 이동하여 Azure에 사용하는 계정으로 도구에 로그인합니다. 로그인 옵션은 오른쪽 모서리에 있습니다. 

로그인한 후에는 기본 모드의 필드와 비교하여 추가 필드를 볼 수 있습니다. 워크로드에 따라 추가 매개 변수를 입력합니다. 

|**입력**  |**설명**  |
|---------|---------|
|API|Azure 코스모스 DB는 다중 모델 및 다중 API 서비스입니다. 새 워크로드의 경우 SQL(코어) API를 선택합니다. |
|지역 수|Azure Cosmos DB는 모든 새 Azure 하위 지역에 제공됩니다. 워크로드에 필요한 지역 수를 선택합니다. 원하는 수의 지역을 Cosmos 계정과 연결할 수 있습니다. 자세한 내용은 Azure Cosmos DB의 [전역 배포를](distribute-data-globally.md) 참조하십시오.|
|다중 지역 쓰기|[다중 지역 쓰기를](distribute-data-globally.md#key-benefits-of-global-distribution)사용하도록 설정하면 응용 프로그램이 모든 Azure 지역을 읽고 쓸 수 있습니다. 다중 지역 쓰기를 사용하지 않도록 설정하면 응용 프로그램이 단일 지역에 데이터를 쓸 수 있습니다. <br/><br/> 다른 리전에서 낮은 대기 시간 쓰기가 필요한 활성 워크로드가 예상되는 경우 다중 리전 쓰기를 사용하도록 설정합니다. 예를 들어 다른 지역의 높은 볼륨으로 데이터베이스에 데이터를 기록하는 IOT 워크로드입니다. <br/><br/> 다중 영역 쓰기는 99.999%의 읽기 및 쓰기 가용성을 보장합니다. 다중 영역 쓰기는 단일 쓰기 영역과 비교할 때 더 많은 처리량이 필요합니다. 자세한 내용은 단일 및 다중 쓰기 지역 문서에 [대한 루가 어떻게 다른지](optimize-cost-regions.md) 참조하세요.|
|기본 일관성|Azure Cosmos DB는 5가지 일관성 수준을 지원하므로 개발자가 일관성, 가용성 및 대기 시간 장단점 간의 균형을 맞출 수 있습니다. 자세한 내용은 일관성 [수준](consistency-levels.md) 문서를 참조하세요. <br/><br/> 기본적으로 Azure Cosmos DB는 세션 일관성을 사용하여 세션에서 자신의 쓰기를 읽을 수 있습니다. <br/><br/> 강하거나 경계가 있는 부실을 선택하면 세션, 일관된 접두사 및 최종 일관성과 비교할 때 읽기에 필요한 RU/s의 두 배가 필요합니다. 다중 영역 쓰기에 대한 강력한 일관성은 지원되지 않으며 강력한 일관성을 가진 단일 지역 쓰기로 자동으로 기본설정됩니다. |
|인덱싱 정책|기본적으로 Azure Cosmos DB는 유연하고 효율적인 **쿼리(자동** 인덱싱 정책에 대한 맵)를 위해 모든 항목의 [모든 속성을 인덱싱합니다.](index-policy.md) <br/><br/> **을 선택하면**속성이 인덱싱되지 않습니다. 이렇게 하면 쓰기에 대한 RU 요금이 가장 낮습니다. [포인트 읽기(키](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) 값 조회) 및/또는 쓰기만 수행되고 쿼리가 없는 경우 정책을 **선택합니다.** <br/><br/> 사용자 지정 인덱싱 정책을 사용하면 쓰기 처리량 및 저장소를 낮추기 위해 인덱스에서 특정 속성을 포함하거나 제외할 수 있습니다. 자세한 내용은 [인덱싱 정책](index-overview.md) 및 [샘플 인덱싱 정책](how-to-manage-indexing-policy.md#indexing-policy-examples) 문서를 참조하세요.|
|저장된 총 데이터(지역별)|단일 리전에서 GB에 저장된 총 예상 데이터입니다.|
|워크로드 모드|워크로드 볼륨이 일정한 경우 **정상을** 선택합니다. <br/><br/> 시간에 따라 워크로드 볼륨이 변경되면 **변수를** 선택합니다.  예를 들어, 특정 일 또는 한 달 동안. <br/><br/> 가변 워크로드 옵션을 선택하는 경우 다음 설정을 사용할 수 있습니다.<ul><li>피크 시간대 시간 비율: 워크로드에 최고(최고) 처리량이 필요한 월의 시간 비율입니다. <br/><br/> 예를 들어 평일 오전 9시부터 오후 6시 근무시간 동안 활동이 많은 워크로드가 있는 경우 피크 시간대의 시간 비율은 최대 시간 45시간 / 730시간/ 월 = ~6%입니다.<br/><br/></li><li>최대 시간대의 지역별 읽기/초 - 최대 시간동안 예상되는 초당 읽기 횟수입니다.</li><li>최고점에 있는 지역당 쓰기/초 – 피크시 초당 예상되는 쓰기 수입니다.</li><li>지역별 읽기/초 피크 오프 - 사용량이 가장 많은 시간대에 예상되는 초당 읽기 횟수입니다.</li><li>지역별 쓰기/초 피크 오프 – 사용량이 가장 많은 동안 초당 예상되는 쓰기 수입니다.</li></ul>피크 및 비수기 간격을 사용하면 [프로비저닝된 처리량을 그에](set-throughput.md#update-throughput-on-a-database-or-a-container) 따라 프로그래밍 방식으로 확장하여 비용을 최적화할 수 있습니다.|
|항목 크기|데이터 항목(예: 문서)의 크기로 1KB에서 2MB에 이내로 표시됩니다. <br/><br/>보다 정확한 추정을 위해 **샘플(JSON)** 문서를 업로드할 수도 있습니다.<br/><br/>워크로드에 동일한 컨테이너에 여러 유형의 항목(다른 JSON 콘텐츠)이 있는 경우 여러 JSON 문서를 업로드하고 예상 을 얻을 수 있습니다. 새 **항목 추가** 단추를 사용하여 여러 샘플 JSON 문서를 추가합니다.|

**예상 값 저장** 단추를 사용하여 현재 예상 을 포함하는 CSV 파일을 다운로드할 수도 있습니다. 

![용량 플래너 고급 모드](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Azure Cosmos DB 용량 플래너에 표시된 가격은 처리량 및 저장소에 대한 공개 가격 책정 요금을 기반으로 하는 예상 가격입니다. 모든 가격은 미국 달러로 표시됩니다. 지역별 모든 요금을 보려면 [Azure Cosmos DB 가격 페이지를](https://azure.microsoft.com/pricing/details/cosmos-db/) 참조하십시오.  

## <a name="estimating-throughput-for-queries"></a>쿼리처리량 예측

Azure Cosmos 용량 계산기는 포인트 읽기(예: 문서, ID 및 파티션 키 값)를 가정하고 워크로드에 대해 씁니다. 쿼리에 필요한 처리량을 추정하려면 Cosmos 컨테이너의 대표 데이터 집합에서 쿼리를 실행하고 [RU 요금을 가져옵니다.](find-request-unit-charge.md) RU 요금을 초당 실행하려는 쿼리 수에 곱하여 필요한 총 RU/s를 얻습니다. 

예를 들어 워크로드에 초당 100회 실행되는 ``SELECT * FROM c WHERE c.id = 'Alice'`` 쿼리가 필요하고 쿼리의 RU 요금이 10RU인 경우 이러한 요청을 제공하기 위해 총 100개의 쿼리/초 * 10RU/쿼리 = 1000 RU/s가 필요합니다. 워크로드에서 발생하는 모든 읽기 또는 쓰기에 필요한 RU/s에 이러한 RU/s를 추가합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 코스모스 DB의 가격 모델에](how-pricing-works.md)대해 자세히 알아보십시오.
* 새 [Cosmos 계정, 데이터베이스 및 컨테이너를](create-cosmosdb-resources-portal.md)만듭니다.
* [프로비저닝된 처리량 비용을 최적화하는](optimize-cost-throughput.md)방법에 대해 알아봅니다.
* [예약된 용량으로 비용을 최적화하는](cosmos-db-reserved-capacity.md)방법에 대해 알아봅니다.

