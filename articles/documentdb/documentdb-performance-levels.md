---
title: DocumentDB의 성능 수준 | Microsoft Docs
description: DocumentDB의 성능 수준을 통해 컬렉션별 기준에 따라 처리량을 예약하는 방법을 알아봅니다.
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: mimig

---
# DocumentDB의 성능 수준
이 문서에서는 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)의 성능 수준에 대해 개괄적으로 설명합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* 성능 수준이란?
* 데이터베이스 계정에 대해 처리량은 어떻게 예약되는가?
* 성능 수준을 사용하는 방법은 무엇인가?
* 성능 수준에 따른 청구 방식은 무엇인가?

## 성능 수준 소개
표준 계정으로 생성된 각 DocumentDB 컬렉션은 연관된 성능 수준과 함께 프로비전됩니다. 데이터베이스에서 각 컬렉션은 자주 액세스되는 컬렉션에 대해 더 많은 처리량을 지정하고 가끔 액세스되는 컬렉션에 대해 더 적은 처리량을 지정할 수 있도록 여러 성능 수준을 포함할 수 있습니다. DocumentDB는 사용자 정의 성능 수준과 미리 정의된 성능 수준을 모두 지원합니다.

각 성능 수준에는 연관된 [RU(요청 단위)](documentdb-request-units.md) 속도 제한이 있습니다. 이러한 한도는 성능 수준에 따라 컬렉션에 대해 예약되는 처리량이며, 해당 컬렉션에서만 배타적으로 사용할 수 있습니다.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p>세부 정보</p></td>
            <td valign="top"><p>처리량 제한</p></td>
            <td valign="top"><p>저장소 제한</p></td>
            <td valign="top"><p>버전</p></td>
             <td valign="top"><p>API</p></td>            
        </tr>
        <tr>
            <td valign="top"><p>사용자 정의 성능</p></td>
            <td valign="top"><p>저장소는 GB 단위 사용량으로 측정됩니다.</p><p>처리량 단위는 초당 100RU입니다.</p></td>
            <td valign="top"><p>무제한. 기본값: 초당 400~250,000개 요청 단위(요청 시 더 높아질 수 있음)</p></td>
            <td valign="top"><p>무제한. 기본값: 250G(요청 시 더 높아질 수 있음) </p></td>
            <td valign="top"><p>V2</p></td>
             <td valign="top"><p>API 2015-12-16 이상</p></td>  
        </tr>
        <tr>
            <td valign="top"><p>미리 정의된 성능</p></td>
            <td valign="top"><p>예약 저장소 10GB</p><p>S1 = 초당 250RU, S2 = 초당 1,000RU, S3 = 초당 2,500RU</p></td>
            <td valign="top"><p>초당 2,500RU</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
             <td valign="top"><p>모두</p></td>  
        </tr>        
    </tbody>
</table>                


DocumentDB에서는 쿼리, UDF(사용자 정의 함수)를 포함하는 쿼리, 저장 프로시저 및 트리거를 포함해서 다양한 데이터베이스 작업을 수행할 수 있습니다. 이러한 각 작업과 연관된 처리 비용은 해당 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 응용 프로그램 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

컬렉션은 [Microsoft Azure 포털](https://portal.azure.com), [REST API](https://msdn.microsoft.com/library/azure/mt489078.aspx) 또는 모든 [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)를 통해 만들 수 있습니다. DocumentDB API를 사용하면 컬렉션의 성능 수준을 지정할 수 있습니다.

> [!NOTE]
> 컬렉션의 성능 수준은 API 또는 [Microsoft Azure 포털](https://portal.azure.com/)을 통해 조정할 수 있습니다. 성능 수준 변경은 3 분 이내에 완료될 것으로 예상됩니다.
> 
> 

## 컬렉션에 대한 성능 수준 설정
컬렉션을 만든 다음에는 지정된 성능 수준을 기반으로 RU에 대한 전체 할당이 해당 컬렉션에 대해 예약됩니다.

DocumentDB는 사용자 정의 성능 수준과 미리 정의된 성능 수준 둘 다에서 처리량 예약을 기준으로 작동합니다. 컬렉션을 만들면 해당 처리량을 실제로 얼마나 사용하는지에 관계없이 응용 프로그램용으로 처리량이 예약되며 예약된 처리량에 따라 요금이 청구됩니다. 사용자 정의 성능 수준을 사용할 때는 사용량을 기준으로 저장소를 측정하지만 미리 정의된 성능 수준을 사용할 때는 컬렉션을 만들 때 10GB의 저장소가 예약됩니다.

컬렉션을 만든 다음에는 DocumentDB SDK 또는 Azure 클래식 포털을 통해 성능 수준을 수정할 수 있습니다.

> [!IMPORTANT]
> DocumentDB 표준 컬렉션은 시간 단위로 요금이 청구되며, 생성되는 각 컬렉션은 최소 1시간 사용량을 기준으로 청구됩니다.
> 
> 

한 시간 내에 컬렉션의 성능 수준을 조정할 경우, 해당 시간 중에 설정된 가장 높은 성능 수준을 기준으로 요금이 청구됩니다. 예를 들어 오전 8:53분에 컬렉션의 성능 수준을 늘린 경우, 오전 8:00부터 새로운 수준에 대한 요금이 청구됩니다. 마찬가지로, 오전 8:53분에 성능 수준을 낮춘 경우, 새 요금은 오전 9:00부터 적용됩니다.

요청 단위는 설정된 성능 수준을 기준으로 각 컬렉션에 대해 예약됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 컬렉션에서 프로비전된 요청 속도(또는 성능 수준)를 초과하는 응용 프로그램은 해당 비율이 컬렉션에 예약된 수준 이하로 떨어질 때까지 조정됩니다. 응용 프로그램에 더 높은 처리량 수준이 필요한 경우 각 컬렉션에 대한 성능 수준을 높일 수 있습니다.

> [!NOTE]
> 응용 프로그램이 하나 이상의 컬렉션에 대한 성능 수준을 초과할 경우 컬렉션당 기준에 따라 요청이 제한됩니다. 즉, 응용 프로그램 요청이 경우에 따라 성공하거나 제한될 수도 있습니다. 요청 트래픽이 급증하는 것을 처리하기 위해 제한하는 경우 적은 수의 재시도를 추가하는 것이 좋습니다.
> 
> 

## 성능 수준 작업
DocumentDB 컬렉션을 사용하면 응용 프로그램의 쿼리 패턴 및 성능 요구를 기준으로 데이터를 그룹화할 수 있습니다. DocumentDB의 자동 인덱싱 및 쿼리 지원을 통해 동일한 컬렉션 내에서 이기종 문서를 수집하는 것이 상당히 일반적입니다. 개별 컬렉션을 사용해야 할지 여부를 결정할 때는 다음과 같은 주요 사항을 고려합니다.

* 쿼리 - 컬렉션은 쿼리 실행을 위한 범위입니다. 문서 집합에 대해 쿼리를 수행해야 할 경우, 가장 효율적인 읽기 패턴은 문서를 단일 컬렉션에 배치하는 것으로부터 시작됩니다.
* 트랜잭션 - 모든 트랜잭션은 단일 컬렉션 내에 포함되도록 범위가 지정됩니다. 단일 저장 프로시저 또는 트리거 내에서 업데이트해야 하는 문서가 있는 경우에는 같은 컬렉션 내에 해당 문서를 저장해야 합니다. 구체적으로 설명하자면, 컬렉션 내의 파티션 키가 트랜잭션 경계로 사용됩니다. 자세한 내용은 [DocumentDB의 분할](documentdb-partition-data.md)을 참조하세요.
* 성능 격리 - 컬렉션에는 성능 수준이 연관되어 있습니다. 이렇게 하면 각 컬렉션이 예약된 RU를 통해 예측 가능한 성능을 갖게 됩니다. 액세스 빈도에 따라 다양한 성능 수준으로 서로 다른 컬렉션에 데이터를 할당할 수 있습니다.

> [!IMPORTANT]
> 응용 프로그램에서 생성된 컬렉션 수를 기반으로 전체 표준 요금이 청구됩니다.
> 
> 

응용 프로그램은 큰 저장소 또는 처리량 요구 사항이 있지 않으면 적은 수의 컬렉션을 사용하는 것이 좋습니다. 새 컬렉션 작성을 위한 응용 프로그램 패턴을 잘 이해합니다. 응용 프로그램 외부에서 처리되는 관리 작업으로 컬렉션 생성을 예약하도록 선택할 수 있습니다. 마찬가지로 컬렉션에 대한 성능 수준을 조정하면 컬렉션에 대해 비용이 청구되는 시간 비율이 변경됩니다. 응용 프로그램에서 이를 동적으로 조정할 경우에는 컬렉션 성능 수준을 모니터링해야 합니다.

## <a id="changing-performance-levels-using-the-azure-portal"></a>S1, S2, S3에서 사용자 정의 성능으로 변경
미리 정의된 처리량 수준을 사용하여 Azure 포털에서 사용자 정의 처리량 수준으로 변경하려면 다음 단계를 수행합니다. 사용자 정의 처리량 수준을 사용하여 요구에 맞게 처리량을 조정할 수 있습니다. 그리고 S1 계정을 사용 중인 경우 단 몇 번의 클릭으로 기본 처리량을 250RU/s에서 400RU/s로 늘릴 수 있습니다.

미리 정의된 사용자 정의 처리량과 관련된 가격 책정 변경에 대한 자세한 내용은 블로그 게시물 [DocumentDB: 새 가격 책정 옵션 사용에 대해 알아야 하는 모든 항목](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

1. 브라우저에서 [**Azure 포털**](https://portal.azure.com)로 이동합니다.
2. **찾아보기** -> **DocumentDB 계정**을 클릭한 다음 수정할 DocumentDB 계정을 선택합니다.
3. **데이터베이스** 렌즈에서 수정할 데이터베이스를 선택한 다음 **데이터베이스** 블레이드에서 수정할 컬렉션을 선택합니다. 미리 정의된 처리량을 사용하는 계정은 S1, S2 또는 S3의 가격 책정 계층을 보유합니다.
   
      ![S1 컬렉션과 데이터베이스 블레이드의 스크린샷](./media/documentdb-performance-levels/documentdb-change-performance-S1.png)
4. **컬렉션** 블레이드에서 **자세히**를 클릭한 다음 위쪽 막대에서 **설정**을 클릭합니다.
5. **설정** 블레이드에서 **가격 책정 계층**을 클릭하면 각 플랜에 대한 월별 비용 예상액이 **가격 책정 계층 선택** 블레이드에 표시됩니다. 사용자 정의 처리량을 변경하려면 **표준** 및 **선택**을 차례로 클릭하여 변경 내용을 저장합니다.
   
      ![DocumentDB 설정의 스크린샷 및 가격 책정 계층 블레이드 선택](./media/documentdb-performance-levels/documentdb-change-performance.png)
6. **설정** 블레이드로 돌아가면 **가격 책정 계층**이 **표준**으로 변경되어 있으며 **처리량(RU/s)** 상자에 기본값인 400이 표시됩니다. 처리량을 400~10,000개 RU/s([요청 단위](documentdb-request-units.md)/초) 사이로 설정합니다. 월별 예상 비용을 제공하기 위해 페이지 아래쪽의 **가격 책정 요약**이 자동으로 업데이트됩니다. **확인**을 클릭하여 변경 내용을 저장합니다.
   
    ![처리량 값을 변경하는 위치를 보여 주는 설정 블레이드의 스크린 샷](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)
7. **데이터베이스** 블레이드로 다시 돌아가면 컬렉션의 새로운 처리량을 확인할 수 있습니다.
   
    ![수정된 컬렉션과 데이터베이스 블레이드의 스크린샷](./media/documentdb-performance-levels/documentdb-change-performance-confirmation.png)

더 많은 처리량(10,000RU/s 초과) 또는 더 많은 저장소(10GB 초과)가 필요하다고 판단되는 경우 분할된 컬렉션을 만들 수 있습니다. 분할된 컬렉션을 만들려면 [컬렉션 만들기](documentdb-create-collection.md)를 참조하세요.

> [!NOTE]
> 컬렉션의 성능 수준을 변경하면 최대 2분이 걸릴 수 있습니다.
> 
> 

## .NET SDK을 사용하여 성능 수준 변경
컬렉션의 성능 수준 변경에 대한 다른 옵션은 SDK를 통하는 것입니다. 이 섹션에서는 [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx)를 사용한 컬렉션 성능 수준 변경에 대해서만 설명하지만 다른 [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)의 경우에도 프로세스는 유사합니다. .NET SDK을 처음 사용하는 경우 [시작 자습서](documentdb-get-started.md)를 참조하세요.

offer 처리량을 초당 50,000개 요청 단위로 변경하는 코드 조각은 다음과 같습니다.

    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);

    // Set the throughput to S2
    offer = new Offer(offer);
    offer.OfferType = "S2";

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);



> [!NOTE]
> 초당 요청 단위 10,000개 미만이 프로비전되는 컬렉션은 사용자 정의 처리량을 사용하는 제품과 미리 정의된 처리량(S1, S2, S3)을 사용하는 offer 간에 언제든지 마이그레이션할 수 있습니다. 초당 요청 단위가 10,000개보다 많이 프로비전되는 컬렉션은 미리 정의된 처리량 수준으로 변환할 수 없습니다.
> 
> 

offer 메서드에 대한 자세한 내용 및 추가 예제를 보려면 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx)을 방문하세요.

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a id="change-throughput"></a>컬렉션의 처리량 변경
사용자 정의 성능을 이미 사용 중인 경우 다음을 수행하여 컬렉션의 처리량을 변경할 수 있습니다. S1, S2 또는 S3 성능 수준(미리 정의된 성능)에서 사용자 정의 성능으로 변경해야 할 경우 [S1, S2, S3에서 사용자 정의 성능으로 변경](#changing-performance-levels-using-the-azure-portal)을 참조하세요.

1. 브라우저에서 [**Azure 포털**](https://portal.azure.com)로 이동합니다.
2. **찾아보기** -> **DocumentDB 계정**을 클릭한 다음 수정할 DocumentDB 계정을 선택합니다.
3. **DocumentDB 계정** 블레이드의 **데이터베이스** 렌즈에서 수정할 데이터베이스를 선택한 다음 **데이터베이스** 블레이드에서 수정할 컬렉션을 선택합니다.
4. **컬렉션** 블레이드의 위쪽 막대에서 **설정**를 클릭합니다.
5. **설정** 블레이드에서 **처리량(RU/s)** 상자의 값을 늘린 다음 **확인**을 클릭하여 변경 내용을 저장합니다. 블레이드 아래쪽의 **가격 책정 요약**이 단일 지역에서 해당 컬렉션의 새로운 월별 예상 비용을 나타내도록 업데이트됩니다.
   
    ![처리량 상자 및 가격 책정 요약이 강조 표시된 설정 블레이드의 스크린샷](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

처리량을 얼마나 높일지 잘 모를 경우 [필요한 처리량 예측](documentdb-request-units.md#estimating-throughput-needs) 및 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 참조하세요.

## 다음 단계
Azure DocumentDB에서 가격 책정 및 데이터 관리에 대해 자세히 알아보려면 다음 리소스를 참조하세요.

* [DocumentDB 가격 책정](https://azure.microsoft.com/pricing/details/documentdb/)
* [DocumentDB 용량 관리](documentdb-manage.md)
* [DocumentDB에서 데이터 모델링](documentdb-modeling-data.md)
* [DocumentDB에서 데이터 분할](documentdb-partition-data.md)
* [요청 단위](http://go.microsoft.com/fwlink/?LinkId=735027)

DocumentDB에 대해 자세히 알아보려면 Azure DocumentDB [설명서](https://azure.microsoft.com/documentation/services/documentdb/)를 참조하세요.

DocumentDB를 사용하여 규모 및 성능 테스트를 시작하려면 [Azure DocumentDB를 사용한 성능 및 규모 테스트](documentdb-performance-testing.md)를 참조하세요.

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png

<!---HONumber=AcomDC_0831_2016-->