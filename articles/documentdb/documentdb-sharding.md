---

redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 7023e7e7f5857db345c47c9a3aa00a816e027a96
ms.lasthandoff: 03/29/2017



---
# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>DocumentDB에서 클라이언트 쪽 지원을 사용하여 데이터를 분할하는 방법
Azure DocumentDB는 [컬렉션의 자동 분할](documentdb-partition-data.md)을 지원합니다. 그러나 분할 동작을 세밀하게 제어하는 데 도움이 되는 사용 사례가 있습니다. 분할 작업에 필요한 상용구 코드 양을 줄이기 위해 여러 컬렉션에 걸쳐 규모가 확장되는 응용 프로그램을 쉽게 빌드할 수 있게 해주는 기능이 .NET, Node.js 및 Java SDK에 추가되었습니다.

이 문서에서는 .NET SDK의 클래스 및 인터페이스와 분할된 응용 프로그램을 개발하는 데 사용하는 방법을 살펴보겠습니다. Java, Node.js, Python 등 다른 SDK는 클라이언트 쪽 분할에 대해 비슷한 메서드 및 인터페이스를 지원합니다.

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>DocumentDB SDK를 사용한 클라이언트 쪽 분할
분할을 자세히 살펴보기 전에 분할과 관련된 몇 가지 기본적인 DocumentDB 개념을 정리해 보겠습니다. 모든 Azure DocumentDB 데이터베이스 계정은 각각 여러 컬렉션을 포함하는 데이터베이스 집합으로 구성되고, 각 컬렉션에는 저장 프로시저, 트리거, UDF, 문서 및 관련 첨부 파일이 포함될 수 있습니다. 컬렉션은 단일 파티션이거나 자체 분할될 수 있으며 다음과 같은 속성을 포함합니다.

* 컬렉션은 성능 격리를 제공합니다. 따라서 동일한 컬렉션 내에서 유사한 문서를 정렬하는 성능상 이점이 있습니다. 예를 들어 시계열 데이터의 경우 자주 쿼리되는 지난 달에 대한 데이터를 프로비전된 처리량이 높은 컬렉션 내에서 배치하는 반면 이전 데이터는 프로비전된 처리량이 낮은 컬렉션 내에 배치하고자 할 수 있습니다.
* ACID 트랜잭션, 즉 저장 프로시저 및 트리거는 컬렉션에 걸쳐 있지 않습니다. 트랜잭션 범위는 컬렉션 내 단일 파티션 키 값 안으로 범위가 지정됩니다.
* 컬렉션은 스키마를 적용하지 않으므로 동일한 형식이나 다른 형식의 JSON 문서에 사용할 수 있습니다.

[Azure DocumentDB SDK 1.5.x](documentdb-sdk-dotnet.md) 버전부터 데이터베이스에 대해 직접 문서 작업을 수행할 수 있습니다. 내부적으로 [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) 는 데이터베이스에 대해 지정된 PartitionResolver를 사용하여 요청을 해당 컬렉션으로 라우팅합니다.

> [!NOTE]
> REST API 2015-12-16 및 SDKs 1.6.0+에 도입된 [서버 쪽 분할](documentdb-partition-data.md)에서는 간단한 사용 사례에 대해 클라이언트 쪽 파티션 확인자 방법을 사용하지 않습니다. 그러나 클라이언트 쪽 분할은 보다 유연하므로 파티션 키 간의 성능 격리 및 병렬 처리 수준을 제어하면서 여러 파티션, 사용 범위/공간 분할 방법 및 해시로부터 결과를 읽어옵니다.
> 
> 

예를 들어 .NET에서 각 PartitionResolver 클래스는 [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) 인터페이스의 구체적 구현으로, 3개의 메서드 [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) 및 [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx)가 있습니다. LINQ 쿼리 및 ReadFeed 반복기는 내부적으로 ResolveForRead 메서드를 사용하여 요청의 파티션 키와 일치하는 모든 컬렉션을 반복합니다. 마찬가지로, 만들기 작업은 ResolveForCreate 메서드를 사용하여 만들기를 해당 파티션으로 라우팅합니다. Replace, Delete 및 Read는 이미 해당 컬렉션에 대한 참조를 포함하는 문서를 사용하기 때문에 변경할 필요가 없습니다.

또한 SDK에는 [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 및 [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx)를 통해 2개의 Canonical 분할 기법인 해시와 범위 조회를 지원하는 두 클래스가 포함되어 있습니다. 이러한 클래스를 사용하여 응용 프로그램에 분할 논리를 쉽게 추가할 수 있습니다.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>분할 논리 추가 및 PartitionResolver 등록
다음은 [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 를 만들고 데이터베이스에 대한 DocumentClient에 등록하는 방법을 보여 주는 코드 조각입니다.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>파티션에 문서 만들기
PartitionResolver를 등록한 후에는 아래와 같이 데이터베이스를 만들고 직접 쿼리할 수 있습니다. 이 예제에서 SDK는 PartitionResolver를 사용하여 UserId를 추출하고 해시한 다음 이 값을 사용하여 만들기 작업을 올바른 컬렉션으로 라우팅합니다.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>파티션에 대한 쿼리 만들기
데이터베이스와 파티션 키를 전달하여 [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) 메서드로 쿼리할 수 있습니다. 쿼리는 데이터베이스 내에서 파티션 키에 매핑되는 모든 컬렉션의 단일 결과 집합을 반환합니다.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>데이터베이스의 모든 컬렉션에 대한 쿼리 만들기
데이터베이스 내의 모든 컬렉션을 쿼리하고, 파티션 키 인수를 건너뛰어 아래와 같이 결과를 열거할 수도 있습니다.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>해시 파티션 확인자
해시 분할에서는 분할이 해시 함수 값을 기준으로 지정되기 때문에 여러 분할 간에 요청 및 데이터를 고르게 배포할 수 있습니다. 이 방법은 일반적으로 많은 수의 고유 클라이언트에서 생성 또는 소비되는 데이터를 분할하는 데 사용되며, 사용자 프로필, 카탈로그 항목 및 IoT("사물 인터넷") 원격 분석 데이터를 저장하는 데 유용합니다. 해시 분할도 컬렉션 내에서 DocumentDB의 서버 쪽 분할 지원에 사용됩니다.

**해시 분할:**
![해시 분할을 통해 파티션 간에 요청을 균등하게 분배하는 방법을 보여 주는 다이어그램](media/documentdb-sharding/partition-hash.png)

*N*개 컬렉션에 걸친 간단한 해시 파티션 구성표는 문서를 받아서 *hash(d) mod N*을 계산하여 문서가 배치된 컬렉션을 확인합니다. 그러나 이 간단한 기술의 문제는 새 컬렉션을 추가하거나 컬렉션을 제거하는 경우 거의 모든 데이터의 순서를 다시 섞어야 하기 때문에 제대로 작동하지 않는다는 것입니다. [일관된 해시](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) 는 컬렉션을 추가하거나 제거하는 동안 필요한 데이터 이동량을 최소화하는 해시 체계를 구현하여 이 문제를 해결하는 잘 알려진 알고리즘입니다.

[HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) 클래스는 [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx) 인터페이스에 지정된 해시 함수를 통해 일관된 해시 링을 작성하는 논리를 구현합니다. 기본적으로는 HashPartitionResolver는 MD5 해시 함수를 사용하지만 사용자 고유의 해시 구현으로 대체할 수 있습니다. HashPartitionResolver는 컬렉션 간의 보다 균일한 문서 분포를 위해 내부적으로 각 컬렉션에 대한 해시 링 내에 16개의 해시, 즉 "가상 노드"를 만들지만 클라이언트 쪽 계산량과 데이터 왜곡도의 균형을 위해 이 개수를 조정할 수 있습니다.

**HashPartitionResolver를 사용한 일관된 해시:**
![HashPartitionResolver가 해시 링을 만드는 방법을 보여 주는 다이어그램](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>범위 파티션 확인자
범위 분할에서는 분할 키가 특정 범위 내에 있는지 여부에 따라 분할이 지정됩니다. 이 분할은 일반적으로 타임스탬프 속성(예: 2015년 4월 1일부터 2015년 4월 14일 사이의 eventTime)을 사용한 분할에 사용됩니다. [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) 클래스는 Range\<T\>와 컬렉션 자체 링크 간의 매핑을 유지 관리하는 데 도움이 됩니다. 

[Range\<T\>](https://msdn.microsoft.com/library/azure/mt126048.aspx)는 문자열이나 숫자와 같은, IComparable\<T\> 및 IEquatable\<T\>를 구현하는 모든 형식의 범위를 관리하는 간단한 클래스입니다. 읽기 및 만들기에 대해 임의 범위를 전달할 수 있으며, 확인자는 요청된 범위와 교차하는 파티션 범위를 식별하여 모든 후보 컬렉션을 식별합니다. 이 기능은 시계열 데이터에 대한 범위 쿼리를 수행할 때 유용할 수 있습니다.

**범위 분할:**  

![ 범위 분할을 통해 파티션 간에 요청을 균등하게 분배하는 방법을 보여 주는 다이어그램](media/documentdb-sharding/partition-range.png)  

범위 분할의 특수한 사례는 범위가 단일 불연속 값인 경우로, "조회 분할"이라고도 합니다. 이 분할은 일반적으로 지역별 분할(예: 노르웨이, 덴마크 및 스웨덴이 포함된 스칸디나비아 분할) 또는 다중 테넌트 응용 프로그램의 테넌트 분할에 사용됩니다.

## <a name="samples"></a>샘플
이러한 PartitionResolvers를 사용하고 확장하여 다음과 같은 특정 사용 사례에 맞게 사용자 고유의 확인자를 구현하는 방법에 대한 코드 조각이 포함된 [DocumentDB 분할 샘플 GitHub 프로젝트](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)를 살펴보겠습니다. 

* GetPartitionKey에 대해 임의 람다 식을 지정하고 복합 분할 키를 구현하거나 다양한 유형의 개체를 다르게 분할하는 데 사용하는 방법
* 수동 조회 테이블을 사용하여 분할을 수행하는 간단한 [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) 를 만드는 방법. 이 패턴은 일반적으로 지역, 테넌트 ID 또는 응용 프로그램 이름과 같은 불연속 값을 기반으로 하는 분할에 사용됩니다.
* 새 컬렉션에 대해 등록해야 하는 명명 체계, IndexingPolicy 및 저장 프로시저를 정의하는 템플릿을 기반으로 하여 자동으로 컬렉션을 만드는 [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) 를 만드는 방법
* 이전 컬렉션이 가득 찰 때 단순히 새 컬렉션을 만드는 덜 체계적인 [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) 를 만드는 방법
* 프로세스 간 및 종료 간에 공유할 수 있도록 PartitionResolver 상태를 JSON으로 직렬화 및 역직렬화하는 방법. 구성 파일 또는 DocumentDB 컬렉션에 이러한 상태를 저장할 수 있습니다.
* 일관된 해시를 기준으로 분할된 데이터베이스에 파티션을 동적으로 추가 및 제거하기 위한 [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) 클래스. 이 클래스는 내부적으로 [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) 를 사용하여 마이그레이션 동안 4가지 모드, 즉 이전 파티션 구성표에서 읽기(ReadCurrent), 새 파티션 구성표(ReadNext), 둘 다의 결과 병합(ReadBoth) 또는 마이그레이션 중 사용할 수 없음(None) 중 하나로 읽기 및 쓰기를 라우팅합니다.

샘플은 오픈 소스이며, 다른 DocumentDB 개발자에게 도움이 되는 정보와 함께 끌어오기 요청을 제출하는 것이 좋습니다. 참여하는 방법에 대한 지침은 [참여 지침](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) 을 참조하세요.  

> [!NOTE]
> 컬렉션 만들기는 DocumentDB에 의해 속도가 제한되므로 여기에 표시된 일부 샘플 메서드는 완료하는 데 몇 분 정도 걸릴 수 있습니다.
> 
> 

## <a name="faq"></a>FAQ
**DocumentDB에서 서버 쪽 분할을 지원하나요?**

예, DocumentDB에서 [서버 쪽 분할](documentdb-partition-data.md)을 지원합니다. DocumentDB는 또한 보다 고급 사용 사례로 클라이언트 쪽 분할 해결 프로그램을 통해 클라이언트 쪽 분할을 지원합니다.

**서버 쪽 및 클라이언트 쪽 분할을 언제 사용해야 하나요?**
대부분의 사용 사례에서는 분할 데이터 및 라우팅 요청의 관리 작업을 처리하므로 서버 쪽 분할을 사용하는 것이 좋습니다. 그러나 범위 분할이 필요하거나 파티션 키의 서로 다른 값 간에 성능 격리를 위해 특수화된 사용 사례가 있는 경우 클라이언트 쪽 분할이 최선의 방법일 수 있습니다.

**내 파티션 구성표에 컬렉션을 추가하거나 제거하려면 어떻게 해야 하나요?**

다시 분할을 구현하는 방법의 예는 샘플 프로젝트의 DocumentClientHashPartitioningManager 구현을 참조하세요.

**내 파티션 구성표를 유지하거나 다른 클라이언트와 공유하려면 어떻게 해야 하나요?**

파티셔너 상태를 JSON으로 직렬화한 다음 구성 파일이나 DocumentDB 컬렉션 내에 저장할 수 있습니다. 예제는 샘플 프로젝트의 RunSerializeDeserializeSample 메서드를 참조하세요.

**다양한 분할 기법을 연결하려면 어떻게 해야 하나요?**

내부적으로 하나 이상의 기존 확인자를 사용하는 고유한 IPartitionResolver를 구현하여 PartitionResolver를 연결할 수 있습니다. 예제는 샘플 프로젝트의 TransitionHashPartitionResolver를 참조하세요.

## <a name="references"></a>참조
* [DocumentDB에서 서버 쪽 분할](documentdb-partition-data.md)
* [DocumentDB 컬렉션 및 성능 수준](documentdb-performance-levels.md)
* [GitHub의 분할 코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [MSDN의 DocumentDB .NET SDK 설명서](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB .NET 샘플(영문)](https://github.com/Azure/azure-documentdb-net)
* [성능 팁에 대한 DocumentDB 블로그](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)


