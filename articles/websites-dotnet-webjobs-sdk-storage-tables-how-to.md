<properties 
	pageTitle="WebJobs SDK를 사용하여 Azure 테이블 저장소로 작업하는 방법" 
	description="WebJobs SDK를 사용하여 Azure 테이블 저장소로 작업하는 방법에 대해 알아봅니다. 테이블을 만들고, 테이블에 엔터티를 추가하고, 기존 테이블을 읽습니다." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# WebJobs SDK를 사용하여 Azure 테이블 저장소로 작업하는 방법

이 가이드에서는 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 버전 1.x를 사용하여 Azure 저장소 테이블을 읽고 쓰는 방법을 보여 주는 C# 코드 샘플을 제공합니다.

이 가이드에서는 [저장소 계정을 가리키는 연결 문자열을 사용하여 Visual Studio에서 WebJob 프로젝트를 만드는 방법](websites-dotnet-webjobs-sdk-get-started.md).을 알고 있는 것으로 가정합니다.
		
일부 코드 조각에서는  `Table` 특성이 [수동으로 호출된](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual) 함수, 즉 트리거 특성 중 하나를 사용하지 않고 호출된 함수에서 사용됩니다. 

## 목차

-   [테이블에 엔터티를 추가하는 방법](#ingress)
-   [실시간 모니터링](#monitor)
-   [테이블에서 여러 엔터티를 읽는 방법](#multiple)
-   [테이블에서 단일 엔터티를 읽는 방법](#readone)
-   [.NET 저장소 API를 직접 사용하여 테이블로 작업하는 방법](#readone)
-   [큐 방법 문서에서 다루는 관련 항목](#queues)
-   [다음 단계](#nextsteps)

## <a id="ingress"></a> 테이블에 엔터티를 추가하는 방법

테이블에 엔터티를 추가하려면  `ICollector<T>` 또는  `IAsyncCollector<T>` 매개 변수와 함께  `Table` 특성을 사용합니다(여기서  `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table. ).

The following code sample adds `Person` 엔터티를  *Ingress*라는 테이블에.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

일반적으로  `ICollector`에서 사용되는 유형은  `TableEntity`에서 파생되거나  `ITableEntity`를 구현하지만 반드시 그런 것은 아닙니다. 다음  `Person` 클래스 중 하나는 이전  `Ingress` 메서드에 표시된 코드와 함께 작동합니다.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Azure 저장소 API로 직접 작업하려는 경우 메서드 서명에  `CloudStorageAccount` 매개 변수를 추가할 수 있습니다.

## <a id="monitor"></a> 실시간 모니터링

데이터 수신 함수는 많은 양의 데이터를 처리하는 경우가 많기 때문에 WebJobs SDK 대시보드에서는 실시간 모니터링 데이터를 제공합니다. **호출 로그** 섹션에 함수가 여전히 실행 중인지 표시됩니다.

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

**호출 세부 정보** 페이지에는 실행 중인 함수의 진행률(작성된 엔터티 수)이 보고되고 실행을 중단할 수 있는 기능을 제공합니다. 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

함수가 완료되면 **호출 세부 정보** 페이지에 작성된 행 수가 보고됩니다.

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> 테이블에서 여러 엔터티를 읽는 방법

테이블을 읽으려면  `IQueryable<T>` 매개 변수와 함께  `Table` 특성을 사용합니다(여기서 유형  `T` derives from `TableEntity`되거나  `ITableEntity`를 구현합니다.

다음 코드 샘플은  `Ingress` 테이블에서 모든 행을 읽고 기록합니다.
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a> 테이블에서 단일 엔터티를 읽는 방법

단일 테이블 엔터티에 바인딩할 때 파티션 키 및 행 키를 지정할 수 있는 추가 매개 변수 두 개가 포함된  `Table` 특성 생성자가 있습니다.

다음 코드 샘플은 큐 메시지에서 받은 파티션 키 및 행 키를 기반으로  `Person` 엔터티에 대한 테이블 행을 읽습니다.  

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


이 예제의  `Person` 클래스는  `ITableEntity`를 구현할 필요가 없습니다.

## <a id="storageapi"></a> .NET 저장소 API를 직접 사용하여 테이블로 작업하는 방법

 `CloudTable` 개체에서  `Table` 특성을 사용하여 테이블 작업을 보다 유연하게 수행할 수 있습니다.

다음 코드 샘플은  `CloudTable` 개체를 사용하여  *Ingress* 테이블에 단일 엔터티를 추가합니다. 
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

 `CloudTable` 개체를 사용하는 방법에 대한 자세한 내용은 [.NET에서 테이블 저장소를 사용하는 방법]을 참조하세요(storage-dotnet-how-to-use-tables.md). 

## <a id="queues"></a>큐 방법 문서에서 다루는 관련 항목

큐 메시지에 의해 트리거되는 테이블을 처리하는 방법 또는 테이블 처리에 특정하지 않은 WebJobs SDK 시나리오에 대한 자세한 내용은 [WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 을 참조하세요.

이 문서에서 다루는 항목은 다음과 같습니다.

* 비동기 함수
* 여러 인스턴스
* 정상 종료
* 함수 본문에 WebJobs SDK 특성 사용
* 코드에서 SDK 연결 문자열 설정
* 코드에서 WebJobs SDK 생성자 매개 변수 값 설정
* 수동으로 함수 트리거
* 로그 작성

## <a id="nextsteps"></a> 다음 단계

이 가이드에서는 Azure 테이블 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 샘플을 제공했습니다. Azure WebJob 및 WebJob SDK를 사용하는 방법에 대한 자세한 내용은 [Azure WebJob - 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.


<!--HONumber=42-->
