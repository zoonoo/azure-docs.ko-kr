---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b9c8ef0343259d5a0adb2cefa9063f9d86edbc3c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="storage-and-predictable-performance-provisioning-in-azure-cosmos-db"></a>Azure Cosmos DB의 저장소 및 예측 가능한 성능 프로비전
Azure Cosmos DB는 JSON 문서에 대한 완전히 관리되고 확장성 있는 문서 지향 NoSQL 데이터베이스 서비스입니다. Azure Cosmos DB를 사용하면 가상 컴퓨터를 임대하거나, 소프트웨어를 배포하거나, 데이터베이스를 모니터링할 필요가 없습니다. 세계적 수준의 가용성, 성능 및 데이터 보호를 제공하기 위해 Microsoft 엔지니어가 Azure Cosmos DB를 작동하고 지속적으로 모니터링합니다.  

[Azure Portal](https://portal.azure.com/)을 통해 [데이터베이스 계정](documentdb-create-account.md)을 만든 다음 [Azure Cosmos DB 컬렉션 및 데이터베이스](documentdb-create-collection.md)를 만들어서 Azure Cosmos DB를 시작할 수 있습니다. Azure Cosmos DB 데이터베이스는 SSD(반도체 드라이브) 지원 저장소 및 처리량의 단위로 제공됩니다. 이러한 저장소 단위는 데이터베이스 컬렉션을 만들 때 프로비전되며, 각 컬렉션의 예약된 처리량은 응용 프로그램의 요구에 맞게 언제든지 확장 또는 축소할 수 있습니다.

응용 프로그램이 하나 이상의 컬렉션에 대한 예약된 처리량을 초과할 경우 컬렉션당 기준에 따라 요청이 제한됩니다. 즉, 응용 프로그램 요청이 경우에 따라 성공하거나 제한될 수도 있습니다.

이 문서에서는 용량을 관리하고 데이터 저장소를 계획하는 데 사용할 수 있는 리소스와 메트릭의 개요를 제공합니다.

## <a name="database-account"></a>데이터베이스 계정
Azure 구독자는 Azure Cosmos DB 데이터베이스 계정을 하나 이상 프로비전하여 데이터베이스 리소스를 관리할 수 있습니다. 각 구독은 단일 Azure 구독과 연결됩니다.

Azure Cosmos DB 계정은 [Azure Portal](documentdb-create-account.md)을 통해서 또는 [ARM 템플릿이나 Azure CLI](documentdb-automation-resource-manager-cli.md)를 사용하여 만들 수 있습니다.

## <a name="databases"></a>데이터베이스
단일 Azure Cosmos DB 데이터베이스에 실제로 무제한 크기의 문서 저장소가 컬렉션으로 그룹화되어 포함될 수 있습니다. 컬렉션은 성능 격리를 제공합니다. 즉, 같은 데이터베이스 또는 계정에서 다른 컬렉션과 공유되지 않는 처리량으로 각 컬렉션을 프로비전할 수 있습니다. Azure Cosmos DB 데이터베이스는 크기가 탄력적이며 SSD 지원 문서 저장소 및 프로비전된 처리량이 몇 GB에서 TB 범위까지 다양합니다. 기존 RDBMS 데이터베이스와 달리 Azure Cosmos DB의 데이터베이스는 단일 컴퓨터로 범위가 지정되지 않으며, 여러 컴퓨터 또는 클러스터에 걸쳐 있을 수 있습니다.  

Azure Cosmos DB를 사용하면 응용 프로그램을 확장할 필요에 따라 컬렉션, 데이터베이스 또는 둘 다를 더 만들 수 있습니다. [Azure Portal](documentdb-create-database.md) 또는 [Azure Cosmos DB SDK](documentdb-dotnet-samples.md) 중 하나를 통해 데이터베이스를 만들 수 있습니다.   

## <a name="database-collections"></a>데이터베이스 컬렉션
각 Azure Cosmos DB 데이터베이스에 컬렉션이 하나 이상 포함될 수 있습니다. 컬렉션은 문서 저장소 및 처리에 대해 항상 사용 가능한 데이터 파티션으로 작동합니다. 각 컬렉션은 유형이 다른 스키마를 사용하여 문서를 저장할 수 있습니다. Azure Cosmos DB의 자동 인덱싱 및 쿼리 기능을 이용하여 문서를 쉽게 필터링하고 검색할 수 있습니다. 컬렉션은 문서 저장소 및 쿼리 실행 범위를 제공합니다. 컬렉션은 포함된 모든 문서에 대한 트랜잭션 도메인이기도 합니다. 컬렉션은 Azure 포털 또는 SDK를 통해 설정된 값에 따라 처리량을 할당받습니다.

컬렉션은 Azure Cosmos DB에서 하나 이상의 물리적 서버에 자동으로 분할됩니다. 컬렉션을 만들 때 초당 요청 단위 및 파티션 키 속성 면에서 프로비전된 처리량을 지정할 수 있습니다. 이 속성 값은 Azure Cosmos DB에서 파티션 간에 문서를 분산시키고 쿼리와 같은 요청을 라우팅하는 데 사용됩니다. 파티션 키 값은 저장 프로시저 및 트리거에 대한 트랜잭션 경계 역할도 수행합니다. 각 컬렉션에는 동일 계정에서 다른 컬렉션과 공유되지 않는 해당 컬렉션에 해당되는 예약된 처리량을 갖습니다. 따라서 저장소 및 처리량의 측면에서 응용 프로그램의 규모를 확장할 수 있습니다.

[Azure Portal](documentdb-create-collection.md) 또는 [DocumentDB SDK](documentdb-sdk-dotnet.md) 중 하나를 통해 컬렉션을 만들 수 있습니다.   

## <a name="request-units-and-database-operations"></a>요청 단위 및 데이터베이스 작업
컬렉션을 만들 때 초당 [RU(요청 단위)](documentdb-request-units.md) 면에서 처리량을 예약합니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 응용 프로그램 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 **RU(요청 단위)** 를 고려할 수 있습니다. 컬렉션에 저장된 항목 수 또는 동시에 실행되는 요청 수에 관계없이 1KB 문서를 읽는 데 동일한 1RU가 소비됩니다. SQL 쿼리와 같은 복잡한 작업을 포함하여 DocumentDB에 대한 모든 요청은 개발 시 결정될 수 있는 예측 가능한 RU 값을 가집니다. 응용 프로그램에 대해 지원할 문서 크기와 각 작업(읽기, 쓰기 및 쿼리)의 빈도를 알고 있는 경우 응용 프로그램 요구 사항을 충족하는 정확한 양의 처리량을 프로비전하고 성능 변화에 따라 데이터베이스를 확장 및 축소할 수 있습니다.

초당 수백 요청 단위부터 수백만 요청 단위까지 초당 100RU 블록의 처리량으로 각 컬렉션을 예약할 수 있습니다. 프로비전된 처리량은 변화하는 처리 요구에 맞고 응용 프로그램의 패턴에 액세스하는 컬렉션의 수명 동안 조정할 수 있습니다. 자세한 내용은 [Azure Cosmos DB 성능 수준](documentdb-performance-levels.md)을 참조하세요.

> [!IMPORTANT]
> 컬렉션은 청구 가능한 엔터티입니다. 비용은 소비된 총 저장소(기가바이트)와 함께 초당 요청 단위로 측정되는 컬렉션의 프로비전된 처리량에 의해 결정됩니다.
>
>

삽입, 삭제, 쿼리 또는 저장 프로시저 실행과 같은 특정 작업에서 소비하는 요청 단위 수는 몇 개인가요? 요청 단위는 요청 처리 비용의 정규화된 측정값입니다. 1KB 문서 읽기가 1RU이지만 동일한 문서를 삽입, 대체 또는 삭제하는 요청은 서비스에서 추가 처리를 사용하므로 더 많은 요청 단위가 사용됩니다. 서비스의 각 응답에는 요청에 사용된 요청 단위를 보고하는 사용자 지정 헤더(`x-ms-request-charge`)가 포함됩니다. 이 헤더는 [SDK](documentdb-sdk-dotnet.md)를 통해 액세스할 수도 있습니다. .NET SDK에서 [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge)는 [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) 개체의 속성입니다. 단일 호출을 수행하기 전에 처리량 요구를 예측하려는 경우 [Capacity Planner](documentdb-request-units.md#estimating-throughput-needs) 를 사용하여 예측을 수행할 수 있습니다.

> [!NOTE]
> 1KB 문서에 대한 1 요청 단위의 기준선은 [세션 일관성](documentdb-consistency-levels.md)이 있는 문서의 간단한 GET에 해당합니다.
>
>

Azure Cosmos DB 데이터베이스 계정에 대한 작업에 사용되는 요청 단위에 영향을 주는 여러 요인이 있습니다. 이러한 요인은 다음과 같습니다.

* 문서 크기. 문서 크기가 증가할수록 데이터를 읽거나 쓰는 데 사용되는 단위도 증가합니다.
* 속성 수. 모든 속성의 기본 인덱싱을 가정할 경우 속성 수가 증가할수록 문서를 쓰는 데 사용되는 단위가 증가합니다.
* 데이터 일관성. 강력 또는 제한된 부실 데이터 일관성 수준을 사용하는 경우 문서를 읽는 데 추가 단위가 사용됩니다.
* 인덱싱되는 속성. 각 컬렉션의 인덱스 정책에 따라 기본적으로 인덱싱되는 속성이 결정됩니다. 인덱싱되는 속성 수를 제한하면 요청 단위 사용을 줄일 수 있습니다.
* 문서 인덱싱. 기본적으로 각 문서가 자동으로 인덱싱되며, 일부 문서를 인덱싱하지 않도록 선택하면 더 적은 요청 단위가 사용됩니다.

자세한 내용은 [Azure Cosmos DB 요청 단위](documentdb-request-units.md)를 참조하세요.

예를 들어 다음 표에는 세 가지 문서 크기(1KB, 4KB 및 64KB)와 두 가지 다른 성능(500읽기/초 + 100쓰기/초 및 500읽기/초 +500 쓰기/초)에서 프로비전할 요청 단위가 나와 있습니다. 데이터 일관성은 세션에서 구성되었으며 인덱싱 정책은 None으로 설정되었습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>문서 크기</strong></p></td>
            <td valign="top"><p><strong>읽기/초</strong></p></td>
            <td valign="top"><p><strong>쓰기/초</strong></p></td>
            <td valign="top"><p><strong>요청 단위</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1,000RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3,000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1,350RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4,150RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000RU/s</p></td>
        </tr>
    </tbody>
</table>

쿼리, 저장 프로시저 및 트리거는 수행하는 작업의 복잡성에 따라 요청 단위를 사용합니다. 응용 프로그램을 개발하면서 요청 요금 헤더를 검사하면 각 작업이 요청 단위 용량을 어떻게 사용하는지 파악하는 데 도움이 됩니다.  

## <a name="choice-of-consistency-level-and-throughput"></a>일관성 수준 및 처리량 선택
기본 일관성 수준 선택은 처리량 및 대기 시간에 영향을 줍니다. 프로그래밍 방식으로 및 Azure 포털을 통해 기본 일관성 수준을 설정할 수 있습니다. 요청 단위로 일관성 수준을 재정의할 수도 있습니다. 기본 일관성 수준은 일정한 읽기/쓰기 및 고유한 쓰기 읽기를 보장하는 **세션**으로 설정됩니다. 세션 일관성은 사용자 중심 응용 프로그램에 유용하며 일관성과 성능 절충의 최적 균형을 제공합니다.    

Azure Portal에서 일관성 수준 변경에 대한 지침은 [Azure Cosmos DB 계정을 관리하는 방법](documentdb-manage-account.md#consistency)을 참조하세요. 일관성 수준에 대한 자세한 내용은 [일관성 수준 사용](documentdb-consistency-levels.md)을 참조하세요.

## <a name="provisioned-document-storage-and-index-overhead"></a>프로비전된 문서 저장소 및 인덱스 오버헤드
Azure Cosmos DB에서는 단일 파티션과 분할된 컬렉션을 모두 만들 수 있습니다. Azure Cosmos DB의 각 파티션은 최대 10GB의 SSD 지원 저장소를 지원합니다. 10GB의 문서 저장소에는 문서와 인덱스 저장소가 포함됩니다. 기본적으로 Azure Cosmos DB 컬렉션은 보조 인덱스나 스키마를 명시적으로 요구하지 않고 모든 문서를 자동으로 인덱싱하도록 구성되어 있습니다. Azure Cosmos DB를 사용하는 응용 프로그램에 따라 일반적인 인덱스 오버헤드는 2-20% 사이입니다. Azure Cosmos DB에서 사용하는 인덱싱 기술은 속성 값에 관계없이 인덱스 오버헤드가 기본 설정을 사용한 문서 크기의 80%를 초과하지 않도록 합니다.

기본적으로 모든 문서가 Azure Cosmos DB에서 자동으로 인덱싱됩니다. 그러나 [Azure Cosmos DB 인덱싱 정책](documentdb-indexing-policies.md)에서 설명한 대로 인덱스 오버헤드를 미세 조정하려면 문서를 삽입하거나 바꿀 때 특정 문서가 인덱싱되지 않도록 제거할 수 있습니다. 컬렉션 내의 모든 문서를 인덱싱에서 제외하도록 Azure Cosmos DB 컬렉션을 구성할 수 있습니다. [컬렉션의 인덱싱 정책 구성](documentdb-indexing-policies.md#CustomizingIndexingPolicy)의 설명과 같이 JSON 문서의 와일드카드가 있는 특정 속성이나 경로만 선택적으로 인덱싱하도록 Azure Cosmos DB 컬렉션을 구성할 수도 있습니다. 속성이나 문서를 제외하면 쓰기 처리량도 향상되므로 사용하는 요청 단위가 줄어듭니다.   

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB가 작동하는 방식에 대해 자세히 알아보려면 [Azure Cosmos DB에서 분할 및 크기 조정](documentdb-partition-data.md)을 참조하세요.

Azure Portal에서 성능 수준 모니터링에 대한 지침은 [Azure Cosmos DB 계정 모니터링](documentdb-monitor-accounts.md)을 참조하세요. 컬렉션에 대한 성능 수준 선택에 대한 자세한 내용은 [Azure Cosmos DB의 성능 수준](documentdb-performance-levels.md)을 참조하세요.

