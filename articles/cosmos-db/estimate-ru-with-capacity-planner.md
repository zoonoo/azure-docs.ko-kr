---
title: Azure Cosmos DB Capacity Planner를 사용하여 비용 예측
description: Azure Cosmos DB 용량 플래너를 사용하면 워크로드에 필요한 처리량(RU/s)과 비용을 추정할 수 있습니다. 이 문서에서는 새 버전의 용량 플래너를 사용하여 필요한 처리량과 비용을 추정하는 방법을 설명합니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/29/2021
ms.author: dech
ms.openlocfilehash: 6041b58d5834afe3356778207083627d7bbe10d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937008"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB Capacity Planner를 사용하여 RU/s 예측
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

적절한 크기의 프로비저닝된 처리량을 사용하여 Azure Cosmos 데이터베이스 및 컨테이너를 구성하거나 워크로드에 대한 [요청 단위(RU/초)](request-units.md)를 구성하는 것은 비용과 성능을 최적화하는 데 필수적입니다. 이 문서에서는 Azure Cosmos DB [용량 플래너](https://cosmos.azure.com/capacitycalculator/)를 사용하여 필요한 RU/s 및 워크로드 비용을 추정하는 방법을 설명합니다. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB 용량 플래너를 사용하여 처리량과 비용을 추정하는 방법

용량 플래너는 두 가지 모드에서 사용할 수 있습니다.

|**모드**  |**설명**  |
|---------|---------|
|Basic|빠르고 높은 수준의 RU/s와 비용 추정치를 제공합니다. 이 모드에서는 인덱싱 정책, 일관성, 기타 매개 변수의 기본 Azure Cosmos DB 설정을 가정합니다. <br/><br/>Azure Cosmos DB에서 실행할 잠재적 워크로드를 평가할 때 빠르고 높은 수준의 추정을 위해 기본 모드를 사용합니다. 자세한 정보는 [기본 모드를 사용하여 비용을 예측](#basic-mode)하는 방법을 참조하세요.|
|고급|인덱싱 정책, 일관성 수준 및 비용과 처리량에 영향을 미치는 기타 매개 변수와 같은 추가 설정을 튜닝할 수 있는 기능과 함께 더 자세한 RU/s와 비용 추정을 제공합니다. <br/><br/>새 프로젝트의 RU/s를 추정하거나 더 자세한 추정을 원할 때 고급 모드를 사용합니다. 자세한 정보는 [고급 모드를 사용하여 비용을 예측](#advanced-mode)하는 방법을 참조하세요.|

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>기본 모드를 사용하여 프로비저닝된 처리량과 비용 추정
기본 모드를 사용하여 워크로드를 신속하게 추정하려면 [용량 플래너](https://cosmos.azure.com/capacitycalculator/)로 이동합니다. 워크로드에 따라 다음 매개 변수를 입력합니다.

|**입력**  |**설명**  |
|---------|---------|
| API |계정의 API 형식을 선택합니다. |
|지역 수|Azure Cosmos DB는 모든 새 Azure 하위 지역에 제공됩니다. 워크로드에 필요한 지역 수를 선택합니다. 지역 수를 Cosmos 계정과 연결할 수 있습니다. 자세한 내용은 Azure Cosmos DB의 [글로벌 배포](distribute-data-globally.md)를 참조하세요.|
|다중 지역 쓰기|[다중 지역 쓰기](distribute-data-globally.md#key-benefits-of-global-distribution)를 사용하도록 설정하면 애플리케이션이 모든 Azure 지역을 읽고 쓸 수 있습니다. 다중 지역 쓰기를 사용하지 않도록 설정하면 애플리케이션이 데이터를 단일 지역에 쓸 수 있습니다. <br/><br/> 여러 다른 지역에서 대기 시간이 짧은 쓰기가 필요한 활성-활성 워크로드가 있어야 하는 경우 다중 지역 쓰기를 사용하도록 설정합니다. 예를 들어, 여러 다른 지역에서 대용량 데이터베이스에 데이터를 쓰는 IOT 워크로드입니다. <br/><br/> 다중 지역 쓰기는 99.999% 읽기 및 쓰기 가용성을 보장합니다. 다중 지역 쓰기에는 단일 쓰기 지역보다 더 많은 처리량이 필요합니다. 자세한 내용은 [단일 쓰기 지역과 다중 쓰기 지역의 RU 차이점](optimize-cost-regions.md) 문서를 참조하세요.|
|트랜잭션 저장소에 저장된 총 데이터 |단일 지역의 트랜잭션 저장소에 저장된 총 예상 데이터(GB)입니다.|
|분석 저장소에 저장된 총 데이터 | **분석 저장소 사용** 옵션을 **설정** 하는 경우 이 옵션이 표시됩니다. 단일 지역의 분석 저장소에 저장된 총 예상 데이터(GB)를 나타냅니다.  |
|항목 크기|데이터 항목(예: 문서)의 예상 크기입니다(1KB - 2MB). |
|지역별 읽기/초|초당 예상 읽기 작업 수. |
|지역당 만들기/초|초당 예상 만들기 작업 수. |
|지역당 업데이트/초|초당 예상 업데이트 작업 수. |
|지역당 삭제/초|초당 예상 삭제 작업 수. |

필요한 세부 정보를 채운 다음, **계산** 을 선택합니다. **비용 추정** 탭은 스토리지와 프로비저닝된 처리량의 총비용을 보여 줍니다. 이 탭에서 **세부 정보 표시** 링크를 확장하여 다양한 CRUD 요청에 필요한 처리량을 분석할 수 있습니다. 필드의 값을 변경할 때마다 **계산** 을 선택하여 예상 비용을 다시 계산합니다.

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="용량 플래너 기본 모드" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>고급 모드를 사용하여 프로비저닝된 처리량과 비용 추정

고급 모드를 사용하면 RU/s 추정에 영향을 주는 추가 설정을 제공할 수 있습니다. 이 옵션을 사용하려면 [용량 플래너](https://cosmos.azure.com/capacitycalculator/)로 이동하고 Azure에 사용하는 계정으로 도구에 **로그인** 합니다. 로그인 옵션은 오른쪽 모서리에서 사용할 수 있습니다.

로그인하면 기본 모드의 필드와 비교하여 추가 필드가 표시됩니다. 워크로드에 따라 추가 매개 변수를 입력합니다.

|**입력**  |**설명**  |
|---------|---------|
|API|Azure Cosmos DB는 다중 모델이며 다중 API 서비스입니다. 새 워크로드의 경우 SQL(코어) API를 선택합니다. |
|지역 수|Azure Cosmos DB는 모든 새 Azure 하위 지역에 제공됩니다. 워크로드에 필요한 지역 수를 선택합니다. 지역 수를 Cosmos 계정과 연결할 수 있습니다. 자세한 내용은 Azure Cosmos DB의 [글로벌 배포](distribute-data-globally.md)를 참조하세요.|
|다중 지역 쓰기|[다중 지역 쓰기](distribute-data-globally.md#key-benefits-of-global-distribution)를 사용하도록 설정하면 애플리케이션이 모든 Azure 지역을 읽고 쓸 수 있습니다. 다중 지역 쓰기를 사용하지 않도록 설정하면 애플리케이션이 데이터를 단일 지역에 쓸 수 있습니다. <br/><br/> 여러 다른 지역에서 대기 시간이 짧은 쓰기가 필요한 활성-활성 워크로드가 있어야 하는 경우 다중 지역 쓰기를 사용하도록 설정합니다. 예를 들어, 여러 다른 지역에서 대용량 데이터베이스에 데이터를 쓰는 IOT 워크로드입니다. <br/><br/> 다중 지역 쓰기는 99.999% 읽기 및 쓰기 가용성을 보장합니다. 다중 지역 쓰기에는 단일 쓰기 지역보다 더 많은 처리량이 필요합니다. 자세한 내용은 [단일 쓰기 지역과 다중 쓰기 지역의 RU 차이점](optimize-cost-regions.md) 문서를 참조하세요.|
|기본 일관성|Azure Cosmos DB에서는 5가지 일관성 수준을 지원하므로, 개발자가 일관성, 가용성, 대기 시간 간의 균형을 맞출 수 있습니다. 자세한 정보는 [일관성 수준](consistency-levels.md) 문서를 참조하세요. <br/><br/> 기본적으로 Azure Cosmos DB에서는 세션 일관성을 사용하여 세션에서 고유 쓰기를 읽을 수 있는 기능을 보장합니다. <br/><br/> 강력 또는 제한된 부실을 선택하려면 세션, 일관된 접두사, 최종 일관성과 비교할 때 읽기에 필요한 RU/s의 두 배가 필요합니다. 다중 지역 쓰기와의 강력한 일관성은 지원되지 않으며 강력한 일관성이 있는 단일 지역 쓰기로 자동으로 기본 설정됩니다. |
|인덱싱 정책|기본적으로 Azure Cosmos DB는 유연하고 효율적인 쿼리를 위해 모든 항목의 [모든 속성을 인덱싱](index-policy.md)합니다(**자동** 인덱싱 정책에 매핑). <br/><br/> **해제** 를 선택하면 속성이 인덱싱되지 않습니다. 그러면 쓰기의 RU 비용이 최저가 됩니다. [포인트 읽기](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)(키 값 조회) 및/또는 쓰기만 수행하고 쿼리는 수행하지 않으려는 경우 **해제** 정책을 선택합니다. <br/><br/> 사용자 지정 인덱싱 정책을 사용하면 쓰기 처리량과 스토리지를 낮추기 위해 인덱스에서 특정 속성을 포함하거나 제외할 수 있습니다. 자세한 정보는 [인덱싱 정책](index-overview.md)과 [샘플 인덱싱 정책](how-to-manage-indexing-policy.md#indexing-policy-examples) 문서를 참조하세요.|
|트랜잭션 저장소에 저장된 총 데이터 |단일 지역의 트랜잭션 저장소에 저장된 총 예상 데이터(GB)입니다.|
|분석 저장소에 저장된 총 데이터 | **분석 저장소 사용** 옵션을 **설정** 하는 경우 이 옵션이 표시됩니다. 단일 지역의 분석 저장소에 저장된 총 예상 데이터(GB)를 나타냅니다.  |
|워크로드 모드|워크로드 볼륨이 일정하면 **일정** 을 선택합니다. <br/><br/> 시간이 지남에 따라 워크로드 볼륨이 변경되면 **변수** 를 선택합니다.  예를 들어 특정 날짜 또는 한 달 동안입니다. <br/><br/> 가변 워크로드 옵션을 선택하면 다음 설정을 사용할 수 있습니다.<ul><li>최대 시간 비율: 워크로드에 최대(가장 높은) 처리량이 필요한 한 달의 시간 백분율입니다. <br/><br/> 예를 들어, 평일 오전 9시부터 오후 6시까지의 업무 시간 동안 작업이 많은 워크로드가 있는 경우 최대 시간의 백분율은 최대 45시간/730시간/월 = ~6%입니다.<br/><br/></li><li>사용량 최대 시 지역당 읽기/초 - 사용량 최대 시 초당 예상되는 읽기 수입니다.</li><li>사용량 최대 시 지역당 쓰기/초 - 사용량 최대 시 초당 예상되는 쓰기 수입니다.</li><li>사용량이 적을 때 지역당 읽기/초 - 사용량이 적은 기간에 초당 예상되는 읽기 수입니다.</li><li>사용량이 적을 때 지역당 쓰기/초 - 사용량이 적은 기간에 초당 예상되는 쓰기 수입니다.</li></ul>사용량 최대 간격과 사용량이 적은 간격을 사용하면 [프로비저닝된 처리량을 프로그래매틱 방식으로 스케일링](set-throughput.md#update-throughput-on-a-database-or-a-container) 업하고 다운로드하여 비용을 최적화할 수 있습니다.|
|항목 크기|데이터 항목(예: 문서)의 크기입니다(1KB - 2MB). <br/><br/>더 정확한 추정을 위해 **샘플 업로드(JSON)**  문서도 작성할 수 있습니다.<br/><br/>워크로드에 같은 컨테이너에 있는 여러 형식의 항목(다른 JSON 콘텐츠 포함)이 있으면 여러 JSON 문서를 업로드하고 추정치를 가져올 수 있습니다. **새 항목 추가** 단추를 사용하여 여러 샘플 JSON 문서를 추가합니다.|
|지역별 읽기/초|초당 예상 읽기 작업 수. |
|지역당 만들기/초|초당 예상 만들기 작업 수. |
|지역당 업데이트/초|초당 예상 업데이트 작업 수. |
|지역당 삭제/초|초당 예상 삭제 작업 수. |

**추정치 저장** 단추를 사용하여 현재 추정치를 포함하는 CSV 파일도 다운로드할 수 있습니다.

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="용량 플래너 고급 모드" border="true":::

Azure Cosmos DB 용량 플래너에 표시된 가격은 처리량과 스토리지의 퍼블릭 가격 책정 요율을 기반으로 한 추정치입니다. 모든 가격은 미국 달러 단위로 표시됩니다. 지역별 모든 요금을 보려면 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.  

## <a name="estimating-throughput-for-queries"></a>쿼리의 처리량 추정

Azure Cosmos 용량 계산기는 워크로드의 포인트 읽기(문서와 같은 단일 항목을 ID와 파티션 키 값으로 읽기)와 쓰기를 가정합니다. 쿼리에 필요한 처리량을 추정하려면 Cosmos 컨테이너의 대표 데이터 세트에서 쿼리를 실행하고 [RU 요금을 확보](find-request-unit-charge.md)합니다. RU 요금에 초당 실행할 것으로 예상되는 쿼리 수를 곱하여 필요한 총 RU/s를 얻습니다. 

예를 들어, 워크로드에 초당 100회 실행되는 ``SELECT * FROM c WHERE c.id = 'Alice'`` 쿼리가 필요하고 쿼리의 RU 요금이 10RU이면, 해당 요청을 처리하기 위해 총 100개의 쿼리/초 * 10RU/쿼리 = 1000RU/s가 필요합니다. 워크로드에서 발생하는 읽기 또는 쓰기에 필요한 RU/s에 해당 RU/s를 추가합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 가격 책정 모델](how-pricing-works.md)에 관해 자세히 알아봅니다.
* 새 [Cosmos 계정, 데이터베이스 및 컨테이너](create-cosmosdb-resources-portal.md)를 만듭니다.
* [프로비저닝된 처리량 비용을 최적화](optimize-cost-throughput.md)하는 방법을 알아봅니다.
* [예약 용량으로 비용을 최적화](cosmos-db-reserved-capacity.md)하는 방법을 알아봅니다.