---
title: Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량 프로비전
description: Azure Cosmos 컨테이너 및 데이터베이스에 프로비전되는 처리량을 설정하는 방법을 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 7caa29807f2779ee1f52cb22de2bf95fdb9cb37e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367128"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB의 프로비전된 처리량 소개

Azure Cosmos DB에서는 데이터베이스 및 컨테이너에 프로비전된 처리량을 설정할 수 있습니다. 프로비전된 처리량에는 표준(수동) 또는 자동 크기 조정이라는 두 가지 유형이 있습니다. 이 문서에서는 프로 비전 된 처리량의 작동 방식에 대 한 개요를 제공 합니다. 

Azure Cosmos 데이터베이스는 컨테이너 세트의 관리 단위입니다. 데이터베이스는 스키마 제약 없는 컨테이너의 집합으로 구성됩니다. Azure Cosmos 컨테이너는 처리량과 스토리지 모두에 대한 확장성 단위입니다. 컨테이너는 Azure 지역 내에 있는 머신 세트에 수평적으로 분할되고 Azure Cosmos 계정과 연결된 모든 Azure 지역에 분산됩니다.

Azure Cosmos DB를 사용하면 두 가지 단위로 처리량을 프로비전할 수 있습니다.
 
- Azure Cosmos 컨테이너
- Azure Cosmos 데이터베이스

## <a name="set-throughput-on-a-container"></a>컨테이너의 처리량 설정  

Azure Cosmos 컨테이너에 프로비전된 처리량은 해당 컨테이너 전용으로 예약됩니다. 컨테이너는 항상 프로비전된 처리량을 받습니다. 컨테이너에 프로비전된 처리량은 재정적으로 SLA의 지원을 받습니다. 컨테이너에서 표준(수동) 처리량을 구성하는 방법에 대한 자세한 내용은 [Azure Cosmos 컨테이너에 처리량 프로비전](how-to-provision-container-throughput.md)을 참조하세요. 컨테이너에서 자동 크기 조정 처리량을 구성하는 방법을 알아보려면 [자동 크기 조정 처리량 프로비전](how-to-provision-autoscale-throughput.md)을 참조하세요.

컨테이너에 프로비전된 처리량을 설정하는 방법이 가장 자주 사용되는 옵션입니다. [RU(요청 단위)](request-units.md)를 사용해서 처리량을 원하는 대로 프로비전하여 컨테이너의 처리량 크기를 탄력적으로 조정할 수 있습니다. 

컨테이너에 프로비전된 처리량은 실제 파티션 간에 균등하게 분산되며, 논리 파티션을 실제 파티션 간에 균등하게 분산하는 좋은 파티션 키를 가정하여 처리량이 컨테이너의 모든 논리 파티션에도 균등하게 분산됩니다. 그러나 논리 파티션의 처리량을 선택적으로 지정할 수는 없습니다. 컨테이너에 있는 하나 이상의 논리 파티션은 실제 파티션에서 호스트되므로, 실제 파티션은 컨테이너에 배타적으로 포함되고 컨테이너에서 프로비전된 처리량을 지원합니다. 

논리 파티션에서 실행 되는 워크 로드가 기본 실제 파티션에 할당 된 처리량을 초과 하는 경우 해당 작업의 속도가 제한 될 수 있습니다. 하나의 논리 파티션이 다른 파티션 키 값 보다 많은 요청을 지나치게 많이 포함 하는 경우 _핫 파티션이_ 발생 합니다.

속도 제한이 발생하는 경우 전체 컨테이너의 프로비전된 처리량을 높이거나 작업을 다시 시도할 수 있습니다. 또한 저장소 및 요청 볼륨을 균등 하 게 분산 하는 파티션 키를 선택 해야 합니다. 분할에 대 한 자세한 내용은 [Azure Cosmos DB 분할 및 수평적 크기 조정](partitioning-overview.md)을 참조 하세요.

컨테이너에 대해 예측 가능한 성능을 원할 경우 컨테이너 세분성에서 처리량을 구성 하는 것이 좋습니다.

다음 이미지는 실제 파티션이 컨테이너의 논리 파티션을 하나 이상 호스트하는 방법을 보여줍니다.

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="컨테이너의 논리 파티션을 하나 이상 호스트 하는 실제 파티션" border="false":::

## <a name="set-throughput-on-a-database"></a>데이터베이스의 처리량 설정

Azure Cosmos 데이터베이스의 처리량을 프로비전하는 경우 데이터베이스의 모든 컨테이너(공유 데이터베이스 컨테이너라고 함) 간에 처리량이 공유됩니다. 단, 데이터베이스의 특정 컨테이너에 프로비전된 처리량을 지정한 경우는 예외입니다. 컨테이너 간에 데이터베이스 수준 프로비전된 처리량을 공유하는 것은 머신 클러스터에 데이터베이스를 호스트하는 것과 비슷합니다. 데이터베이스 내의 모든 컨테이너가 머신에 제공되는 리소스를 공유하므로 당연히 특정 컨테이너에 대한 예상 성능이 제공되지 않습니다. 데이터베이스에서 프로비전된 처리량을 구성하는 방법을 알아보려면 [Azure Cosmos 데이터베이스에 프로비전된 처리량 구성](how-to-provision-database-throughput.md)을 참조하세요. 데이터베이스에서 자동 크기 조정 처리량을 구성하는 방법을 알아보려면 [자동 크기 조정 처리량 프로비전](how-to-provision-autoscale-throughput.md)을 참조하세요.

데이터베이스 내 모든 컨테이너가 프로비전된 처리량을 공유하므로, Azure Cosmos DB는 해당 데이터베이스의 특정 컨테이너에 대해 예측 가능한 처리량을 보장하지 않습니다. 특정 컨테이너가 받을 수 있는 처리량은 다음 조건에 따라 다릅니다.

* 컨테이너 수
* 다양한 컨테이너에 대해 선택한 파티션 키
* 컨테이너의 다양한 논리 파티션에 분산되는 워크로드 

특정 컨테이너의 전용 처리량으로 지정하지 않고 여러 컨테이너 간에 처리량을 공유하려는 경우 데이터베이스의 처리량을 구성하는 것이 좋습니다. 

다음 예제에서는 데이터베이스 수준에서 처리량을 프로비전하는 것이 좋은 경우를 보여 줍니다.

* 데이터베이스에 프로비전된 처리량을 컨테이너 집합 간에 공유하는 방법은 다중 테넌트 애플리케이션에 유용합니다. 각 사용자를 고유한 Azure Cosmos 컨테이너로 나타낼 수 있습니다.

* 데이터베이스에 프로비전된 처리량을 컨테이너 세트 간에 공유하는 방법은 VM 클러스터에 호스트된 MongoDB, Cassandra 등의 NoSQL 데이터베이스를 마이그레이션하거나 온-프레미스 물리적 서버에서 Azure Cosmos DB로 마이그레이션하는 경우에 유용합니다. Azure Cosmos 데이터베이스에 구성된 프로비전된 처리량은 MongoDB 또는 Cassandra 클러스터의 컴퓨팅 용량과 논리적으로 상응하지만 더 비용 효과적이고 탄력적인 것으로 생각하면 됩니다.  

프로비전된 처리량을 가진 데이터베이스 내에서 생성된 모든 컨테이너는 [파티션 키](partitioning-overview.md)를 사용하여 만들어야 합니다. 데이터베이스 내 컨테이너에 할당된 처리량은 지정된 시점에 해당 컨테이너의 모든 논리 파티션 간에 분산됩니다. 데이터베이스에 구성된 프로비전된 처리량을 공유하는 컨테이너가 있는 경우 처리량을 특정 컨테이너 또는 논리 파티션에 선택적으로 적용할 수 없습니다. 

논리 파티션의 워크로드가 특정 논리 파티션에 할당된 처리량보다 많은 양을 사용하는 경우 작업 속도가 제한됩니다. 속도 제한이 발생하는 경우 전체 데이터베이스의 처리량을 높이거나 작업을 다시 시도할 수 있습니다. 분할에 대한 자세한 내용은 [논리 파티션](partitioning-overview.md)을 참조하세요.

공유 처리량 데이터베이스의 컨테이너는 해당 데이터베이스에 할당된 처리량(RU/s)을 공유합니다. 데이터베이스에 최소 400RU/s를 포함하는 최대 4개의 컨테이너를 사용할 수 있습니다. 표준(수동) 프로비전된 처리량을 사용하면 처음 4개 이후의 새 컨테이너 각각에 최소 100RU/s가 추가로 필요합니다. 예를 들어 8개의 컨테이너가 있는 공유 처리량 데이터베이스를 사용하는 경우 데이터베이스에 대한 최소 RU/s는 800RU/s입니다. 자동 크기 조정 프로 비전 된 처리량을 사용 하면 자동 크기 조정 최대 4000 r u/초 (400-4000 r u/초 사이 크기 조정)를 사용 하 여 데이터베이스에 최대 25 개의 컨테이너를 포함할 수 있습니다.

> [!NOTE]
> 2020년 2월에 공유 처리량 데이터베이스에 최대 25개의 컨테이너를 포함하여 보다 원활히 컨테이너 전체에서 처리량을 공유할 수 있도록 하는 변경 내용이 도입되었습니다. 처음 25개 컨테이너 이후에는 데이터베이스의 공유 처리량과는 별개인 [전용 처리량을 사용하여 프로비전](#set-throughput-on-a-database-and-a-container)된 경우에만 데이터베이스에 더 많은 컨테이너를 추가할 수 있습니다.<br>
Azure Cosmos DB 계정에 컨테이너가 25개 이상인 공유 처리량 데이터베이스가 이미 포함되어 있는 경우 동일한 Azure 구독의 해당 계정 및 다른 모든 계정은 이 변경에서 제외됩니다. 사용자 의견 또는 문의 사항이 있는 경우 [제품 고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. 

워크로드에 데이터베이스의 모든 컬렉션을 삭제하고 다시 만드는 작업이 포함된 경우에는 컬렉션을 만들기 전에 빈 데이터베이스를 삭제하고 새 데이터베이스를 다시 만드는 것이 좋습니다. 다음 이미지는 데이터베이스 내 여러 컨테이너에 속하는 하나 이상의 논리 파티션을 실제 파티션에 호스트할 수 있다는 것을 보여줍니다.

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="컨테이너의 논리 파티션을 하나 이상 호스트 하는 실제 파티션" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>데이터베이스 및 컨테이너의 처리량 설정

두 모델을 결합할 수 있습니다. 데이터베이스와 컨테이너의 처리량을 둘 다 프로비전할 수 있습니다. 다음 예제에서는 Azure Cosmos 데이터베이스 및 컨테이너에 표준(수동) 프로비전된 처리량을 프로비전하는 방법을 보여 줍니다.

* 이름이 *Z*이고 프로비전된 처리량이 *"K"* RU인 Azure Cosmos 데이터베이스를 만들 수 있습니다. 
* 다음으로, 데이터베이스 내에서 5개 컨테이너 *A*, *B*, *C*, *D*, *E*를 만듭니다. 컨테이너 B를 만들 때 **이 컨테이너에 전용 처리량 프로비전** 옵션을 사용하도록 설정하고 이 컨테이너에서 프로비전된 처리량 *"P"* RU를 명시적으로 구성해야 합니다. 데이터베이스 및 컨테이너를 만들 때만 공유 및 전용 처리량을 구성할 수 있습니다. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="컨테이너의 논리 파티션을 하나 이상 호스트 하는 실제 파티션":::

* *"K"* RU 처리량은 4개의 컨테이너 *A*, *C*, *D*, *E* 간에 공유됩니다. *A*, *C*, *D* 또는 *E*에 사용 가능한 정확한 처리량은 변동합니다. 각 개별 컨테이너의 처리량에 대한 SLA는 없습니다.
* 컨테이너 *B*는 항상 *"P"* RU 처리량을 보장받을 수 있으며 SLA가 지원됩니다.

> [!NOTE]
> 프로비전된 처리량이 할당된 컨테이너는 공유 데이터베이스 컨테이너로 변환할 수 없습니다. 반대로 공유 데이터베이스 컨테이너는 전용 처리량을 사용하도록 변환할 수 없습니다.

## <a name="update-throughput-on-a-database-or-a-container"></a>데이터베이스 또는 컨테이너에서 처리량 업데이트

Azure Cosmos 컨테이너 또는 데이터베이스를 만든 후 프로비전된 처리량을 업데이트할 수 있습니다. 데이터베이스 또는 컨테이너에 구성할 수 있는 최대 프로비전된 처리량에는 제한이 없습니다.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> 현재 프로 비전 된 처리량

Azure Portal에서 또는 Sdk를 사용 하 여 컨테이너 또는 데이터베이스의 프로 비전 된 처리량을 검색할 수 있습니다.

* .NET SDK의 [컨테이너. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) .
* Java SDK의 [CosmosContainer 처리량](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput?view=azure-java-stable&preserve-view=true) 입니다.

이러한 메서드의 응답에는 컨테이너 또는 데이터베이스에 대해 [프로 비전 된 최소 처리량](concepts-limits.md#storage-and-database-operations) 도 포함 됩니다.

* .NET SDK의 [ThroughputResponse](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput?view=azure-dotnet&preserve-view=true) .
* Java SDK에서 [ThroughputResponse ()를 처리](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput?view=azure-java-stable&preserve-view=true) 합니다.

실제 최소/s는 계정 구성에 따라 다를 수 있습니다. 그러나 일반적으로 다음의 최대값입니다.

* 400RU/s 
* 현재 저장소 (GB) * 10 r u/초
* 데이터베이스 또는 컨테이너/100에 프로 비전 된 최고 r u/초
* 컨테이너 수 * 100 r u/초 (공유 처리량 데이터베이스만)

### <a name="changing-the-provisioned-throughput"></a>프로 비전 된 처리량 변경

Azure Portal 또는 Sdk를 사용 하 여 컨테이너 또는 데이터베이스의 프로 비전 된 처리량을 확장할 수 있습니다.

* .NET SDK의 [컨테이너. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) .
* Java SDK의 [ReplaceThroughput CosmosContainer.](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput?view=azure-java-stable&preserve-view=true)

**프로 비전 된 처리량을 줄이면** [최소한](#current-provisioned-throughput)의 작업을 수행할 수 있습니다.

**프로 비전 된 처리량을 높이**는 경우 대부분의 시간에 작업이 즉시 수행 됩니다. 그러나 필요한 리소스를 프로 비전 하는 시스템 태스크로 인해 작업에 시간이 더 오래 걸릴 수 있습니다. 이 경우이 작업이 진행 되는 동안 프로 비전 된 처리량을 수정 하려고 하면 다른 크기 조정 작업이 진행 되 고 있음을 설명 하는 오류 메시지와 함께 HTTP 423 응답이 생성 됩니다.

> [!NOTE]
> 프로 비전 된 처리량이 크게 증가 해야 하는 매우 큰 수집 작업을 계획 하는 경우 크기 조정 작업에 SLA가 없고 이전 단락에서 설명한 것 처럼 증가 하는 경우 시간이 오래 걸릴 수 있다는 점에 유의 하세요. 작업을 시작 하기 전에 미리 계획 하 고 크기 조정을 시작 하 고 아래 방법을 사용 하 여 진행률을 확인 하는 것이 좋습니다.

[현재 프로 비전 된 처리량](#current-provisioned-throughput) 을 읽고 다음을 사용 하 여 크기 조정 진행률을 프로그래밍 방식으로 확인할 수 있습니다.

* .NET SDK의 [IsReplacePending ThroughputResponse.](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending?view=azure-dotnet&preserve-view=true)
* Java SDK의 [isReplacePending ()을 ThroughputResponse.](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending?view=azure-java-stable&preserve-view=true)

[Azure Monitor 메트릭을](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) 사용 하 여 리소스에서 프로 비전 된 처리량 (r u/초) 및 저장소의 기록을 볼 수 있습니다.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> 높은 저장소/낮은 처리량 프로그램

위의 [현재 프로 비전 된 처리량](#current-provisioned-throughput) 섹션에 설명 된 대로 컨테이너 또는 데이터베이스에서 프로 비전 할 수 있는 최소 처리량은 여러 가지 요인에 따라 달라 집니다. 그 중 하나는 현재 저장 된 데이터의 양입니다. Azure Cosmos DB는 저장소의 GB 당 최소 처리량이 10 r u/초까지 적용 됩니다.

많은 양의 데이터를 저장 해야 하지만 처리량 요구 사항이 적은 경우에는이 문제가 발생할 수 있습니다. 이러한 시나리오를 보다 잘 활용 하기 위해 Azure Cosmos DB에는 적격 계정에 대해 10에서 1 사이의 GB/GB 제약 조건을 줄이는 **"높은 저장소/낮은 처리량" 프로그램이** 도입 되었습니다.

현재 계정에 1TB가 넘는 데이터를 포함 하는 하나 이상의 컨테이너 또는 공유 처리량 데이터베이스가 있어야 합니다. 이 프로그램에 참여 하 고 모든 자격을 평가 하려면 [이 설문 조사](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u)를 작성 해야 합니다. 그러면 Azure Cosmos DB 팀이 후속 작업을 진행 하 고 온 보 딩을 진행 합니다.

## <a name="comparison-of-models"></a>모델 비교
다음 표에서는 데이터베이스 및 컨테이너에 대한 표준(수동) 처리량 프로비전을 비교합니다. 

|**매개 변수**  |**데이터베이스 표준(수동) 처리량**  |**컨테이너 표준(수동) 처리량**|**데이터베이스 자동 크기 조정 처리량** | **컨테이너 크기 자동 조정 처리량**|
|---------|---------|---------|---------|---------|
|진입점(최소 RU/s) |400RU/s. 처음 4개의 컨테이너 후 각 추가 컨테이너에 최소 100RU/s 필요</li> |400| 400~4,000RU/s 범위에서 자동 크기 조정. 컨테이너당 최소 RU/s 없이 최대 25개의 컨테이너 사용 가능</li> | 400~4,000RU/s 범위에서 자동 크기 조정.|
|컨테이너당 최소 RU/s|100|400|--|400~4,000RU/s 범위에서 자동 크기 조정|
|최대 RU|데이터베이스에서 무제한|컨테이너에서 무제한|데이터베이스에서 무제한|컨테이너에서 무제한
|특정 컨테이너에 할당 또는 제공되는 RU|보장되지 않습니다. 지정된 컨테이너에 할당되는 RU는 속성에 따라 다릅니다. 속성은 처리량, 워크로드 분산 및 컨테이너 수를 공유하는 컨테이너 파티션 키의 선택 항목일 수 있습니다. |컨테이너에 구성된 모든 RU는 컨테이너에만 배타적으로 예약됩니다.|보장되지 않습니다. 지정된 컨테이너에 할당되는 RU는 속성에 따라 다릅니다. 속성은 처리량, 워크로드 분산 및 컨테이너 수를 공유하는 컨테이너 파티션 키의 선택 항목일 수 있습니다. |컨테이너에 구성된 모든 RU는 컨테이너에만 배타적으로 예약됩니다.|
|컨테이너의 최대 스토리지|무제한.|제한 없음|제한 없음|제한 없음|
|컨테이너의 논리 파티션당 최대 처리량|10,000RU/s|10,000RU/s|10,000RU/s|10,000RU/s|
|컨테이너의 논리 파티션당 최대 스토리지(데이터 + 인덱스)|20GB|20GB|20GB|20GB|

## <a name="next-steps"></a>다음 단계

* [논리 파티션](partitioning-overview.md)에 대해 자세히 알아봅니다.
* [Azure Cosmos 컨테이너에서 표준(수동) 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스에서 표준(수동) 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스 또는 컨테이너에서 자동 크기 조정 처리량을 프로비전](how-to-provision-autoscale-throughput.md)하는 방법을 알아봅니다.
