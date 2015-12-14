<properties 
	pageTitle="DocumentDB의 성능 수준 | Microsoft Azure" 
	description="DocumentDB의 성능 수준을 통해 컬렉션별 기준에 따라 처리량을 예약하는 방법을 알아봅니다." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015" 
	ms.author="johnmac"/>

#DocumentDB의 성능 수준

이 문서에서는 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)의 성능 수준에 대해 개괄적으로 설명합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

-	성능 수준이란?
-	데이터베이스 계정에 대해 처리량은 어떻게 예약되는가?
-	성능 수준을 사용하는 방법은 무엇인가?
-	성능 수준에 따른 청구 방식은 무엇인가?

##성능 수준 소개

표준 계정으로 생성된 각 DocumentDB 컬렉션은 연관된 성능 수준과 함께 프로비전됩니다. 성능 수준은 최저에서 최고 성능까지 S1, S2 또는 S3으로 지정됩니다. 컬렉션의 성능 수준은 응용 프로그램에 예약된 요청 처리 리소스의 양을 결정합니다. 데이터베이스에서 각 컬렉션은 자주 액세스되는 컬렉션에 대해 더 많은 처리량을 지정하고 가끔 액세스되는 컬렉션에 대해 더 적은 처리량을 지정할 수 있도록 여러 성능 수준을 포함할 수 있습니다. 모든 컬렉션의 최소 성능 수준은 S1입니다.

각 성능 수준에는 연관된 RU(요청 단위) 비율 한도가 있습니다. 이러한 한도는 성능 수준에 따라 컬렉션에 대해 예약되는 처리량이며, 해당 컬렉션에서만 배타적으로 사용할 수 있습니다. 컬렉션은 [Azure 클래식 포털](http://portal.azure.com) 또는 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 통해 만들 수 있습니다. DocumentDB API를 사용하면 컬렉션의 성능 수준을 지정할 수 있습니다.

컬렉션 성능 수준|예약된 처리량
---|---
S1|250 RU/초
S2|1000 RU/초
S3|2500 RU/초

DocumentDB에서는 쿼리, UDF(사용자 정의 함수)를 포함하는 쿼리, 저장 프로시저 및 트리거를 포함해서 다양한 데이터베이스 작업을 수행할 수 있습니다. 이러한 각 작업과 연관된 처리 비용은 해당 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 응용 프로그램 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

> [AZURE.NOTE]성능 수준은 요청 단위로 측정됩니다. 각 성능 수준에는 초당 비율로 연관된 최대 요청 단위가 있습니다. 컬렉션의 성능 수준은 API 또는 [Azure 클래식 포털](https://portal.azure.com/)을 통해 조정할 수 있습니다.

##컬렉션에 대한 성능 수준 설정
컬렉션을 만든 다음에는 지정된 성능 수준을 기반으로 RU에 대한 전체 할당이 해당 컬렉션에 대해 예약됩니다. 예를 들어 컬렉션이 S3 –로 설정된 경우 컬렉션은 2, 500 RUs/초를 처리할 수 있습니다. 각 컬렉션은 지정된 처리량 및 10GB 데이터베이스 저장소를 예약합니다. 컬렉션 비용은 선택한 성능 수준(S1, S2, S3)에 따라 달라집니다. DocumentDB는 용량 예약을 기준으로 작동하며, 컬렉션을 만들면 응용 프로그램에 처리량 및 데이터베이스 저장소가 예약되고, 실제로 사용된 저장소 및 처리량에 관계없이 이러한 예약된 항목에 따라 비용이 청구됩니다.

컬렉션을 만든 다음에는 DocumentDB SDK 또는 Azure 클래식 포털을 통해 성능 수준을 수정할 수 있습니다.

> [AZURE.IMPORTANT]DocumentDB 표준 컬렉션은 시간 단위로 요금이 청구되며, 생성되는 각 컬렉션은 최소 1시간 사용량을 기준으로 청구됩니다.

한 시간 내에 컬렉션의 성능 수준을 조정할 경우, 해당 시간 중에 설정된 가장 높은 성능 수준을 기준으로 요금이 청구됩니다. 예를 들어 오전 8:53분에 컬렉션의 성능 수준을 늘린 경우, 오전 8:00부터 새로운 수준에 대한 요금이 청구됩니다. 마찬가지로, 오전 8:53분에 성능 수준을 낮춘 경우, 새 요금은 오전 9:00부터 적용됩니다.

요청 단위는 설정된 성능 수준을 기준으로 각 컬렉션에 대해 예약됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 컬렉션에서 프로비전된 요청 속도(또는 성능 수준)를 초과하는 응용 프로그램은 해당 비율이 컬렉션에 예약된 수준 이하로 떨어질 때까지 조정됩니다. 응용 프로그램에 더 높은 처리량 수준이 필요한 경우 각 컬렉션에 대한 성능 수준을 높일 수 있습니다.

> [AZURE.NOTE]응용 프로그램이 하나 이상의 컬렉션에 대한 성능 수준을 초과할 경우 컬렉션당 기준에 따라 요청이 제한됩니다. 즉, 응용 프로그램 요청이 경우에 따라 성공하거나 제한될 수도 있습니다.

##성능 수준 작업
DocumentDB 컬렉션을 사용하면 응용 프로그램의 쿼리 패턴 및 성능 요구를 기준으로 데이터를 분할할 수 있습니다. DocumentDB를 사용한 데이터 분할에 대한 자세한 내용은 [데이터 분할 설명서](documentdb-partition-data.md)를 참조하세요. DocumentDB의 자동 인덱싱 및 쿼리 지원을 통해 동일한 컬렉션 내에서 이기종 문서를 수집하는 것이 상당히 일반적입니다. 개별 컬렉션을 사용해야 할지 여부를 결정할 때는 다음과 같은 주요 사항을 고려합니다.

- 쿼리 - 컬렉션은 쿼리 실행을 위한 범위입니다. 문서 집합에 대해 쿼리를 수행해야 할 경우, 가장 효율적인 읽기 패턴은 문서를 단일 컬렉션에 배치하는 것으로부터 시작됩니다.
- 트랜잭션 - 컬렉션은 저장 프로시저 및 트리거에 대한 트랜잭션 도메인입니다. 모든 트랜잭션은 단일 컬렉션으로 범위가 지정됩니다. 
- 성능 - 컬렉션에는 연관된 성능 수준이 포함됩니다. 이렇게 하면 각 컬렉션이 예약된 RU를 통해 예측 가능한 성능을 갖게 됩니다. 액세스 빈도에 따라 다양한 성능 수준으로 서로 다른 컬렉션에 데이터를 할당할 수 있습니다.

> [AZURE.IMPORTANT]응용 프로그램에서 생성된 컬렉션 수를 기반으로 전체 표준 요금이 청구됩니다.

응용 프로그램은 큰 저장소 또는 처리량 요구 사항이 있지 않으면 적은 수의 컬렉션을 사용하는 것이 좋습니다. 새 컬렉션 작성을 위한 응용 프로그램 패턴을 잘 이해합니다. 응용 프로그램 외부에서 처리되는 관리 작업으로 컬렉션 생성을 예약하도록 선택할 수 있습니다. 마찬가지로 컬렉션에 대한 성능 수준을 조정하면 컬렉션에 대해 비용이 청구되는 시간 비율이 변경됩니다. 응용 프로그램에서 이를 동적으로 조정할 경우에는 컬렉션 성능 수준을 모니터링해야 합니다.

##Azure 포털을 사용하여 성능 수준 변경

Azure 포털은 컬렉션의 성능 수준을 관리할 때 사용할 수 있는 한 가지 옵션입니다. Azure 클래식 포털에서 컬렉션의 성능 수준을 변경하려면 이 단계를 수행합니다.

1. 브라우저에서 [**Azure 포털**](https://portal.azure.com)로 이동합니다.
2. 왼쪽에 있는 점프 모음에서 **찾아보기**를 클릭합니다.
3. **찾아보기** 허브의 **필터 기준** 레이블에서 **DocumentDB 계정**를 클릭합니다.
4. **DocumentDB 계정** 블레이드에서 원하는 컬렉션을 포함하는 DocumentDB 계정을 클릭합니다.
5. **DocumentDB 계정** 블레이드에서 **데이터베이스** 렌즈 아래로 스크롤하고 원하는 컬렉션을 포함하는 데이터베이스를 클릭합니다. 
6. 새로 연 **데이터베이스** 블레이드에서 아래의 **컬렉션** 필터로 스크롤하고 원하는 컬렉션을 선택합니다.
7. 끝으로 **컬렉션** 블레이드 내의 **사용량** 필터에서 **가격 책정 계층** 타일을 찾아 클릭합니다.
8. **가격 책정 계층 선택** 블레이드에서 원하는 성능 수준을 클릭한 다음 블레이드의 맨 아래에서 **선택**을 클릭합니다. 

>[AZURE.NOTE]컬렉션의 성능 수준을 변경하면 최대 2분이 걸릴 수 있습니다.

![가격 책정 계층 변경][1]

##.NET SDK을 사용하여 성능 수준 변경

컬렉션의 성능 수준 변경에 대한 다른 옵션은 SDK를 통하는 것입니다. 이 섹션에서는 [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx)를 사용한 컬렉션 성능 수준 변경에 대해서만 설명하지만 다른 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)의 경우에도 프로세스는 유사합니다. .NET SDK을 처음 사용하는 경우 [시작 자습서](documentdb-get-started.md)를 참조하세요.

다음은 제품 유형을 변경하기 위한 코드 조각입니다.

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
	                          .Where(r => r.ResourceLink == "collection selfLink")    
	                          .AsEnumerable()
	                          .SingleOrDefault();
	                          
	//Change the user mode to All
	offer.OfferType = "S3";
	                    
	//Now persist these changes to the database by replacing the original resource
	Offer updated = await client.ReplaceOfferAsync(offer);

offer 메서드에 대한 자세한 내용 및 추가 예제를 보려면 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx)을 방문하세요.

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

##다음 단계

Azure DocumentDB에서 가격 책정 및 데이터 관리에 대해 자세히 알아보려면 다음 리소스를 참조하세요.
 
- [DocumentDB 가격 책정](http://azure.microsoft.com/pricing/details/documentdb/)
- [DocumentDB 용량 관리](documentdb-manage.md) 
- [DocumentDB에서 데이터 모델링](documentdb-modeling-data.md)
- [DocumentDB에서 데이터 분할](documentdb-partition-data.md)

DocumentDB에 대해 자세히 알아보려면 Azure DocumentDB [설명서](http://azure.microsoft.com/documentation/services/documentdb/)를 참조하세요.

[1]: ./media/documentdb-performance-levels/img1.png

<!---HONumber=AcomDC_1203_2015-->