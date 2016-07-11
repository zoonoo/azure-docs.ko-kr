<properties 
	pageTitle="DocumentDB S1 계정 수퍼차지 | Microsoft Azure" 
	description="Azure 포털에서 몇 가지를 간단한 변경하여 DocumentDB S1 계정에서 향상된 처리량을 활용합니다." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/27/2016" 
	ms.author="mimig"/>

# DocumentDB 계정 수퍼차지

다음 단계를 수행하여 DocumentDB S1 계정에 대한 향상된 처리량을 활용합니다. 추가 비용 없이 250[RU/s](documentdb-request-units.md)에서 400RUs/ 이상으로 기존 S1 계정의 처리량을 늘릴 수 있습니다!

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## Azure 포털에서 사용자 정의 성능 변경

1. 브라우저에서 [**Azure 포털**](https://portal.azure.com)로 이동합니다.
2. **찾아보기** -> **DocumentDB 계정**을 클릭한 다음 수정할 DocumentDB 계정을 선택합니다.
3. **데이터베이스** 렌즈에서 수정할 데이터베이스를 선택한 다음 **데이터베이스** 블레이드에서 S1 가격 책정 계층을 포함한 컬렉션을 선택합니다.

      ![S1 컬렉션과 데이터베이스 블레이드의 스크린샷](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. **컬렉션** 블레이드의 위쪽 막대에서 **설정**를 클릭합니다.
5. **설정** 블레이드에서 **가격 책정 계층**을 클릭하면 각 계획에 대한 월별 비용 예상액이 표시되어 있습니다. **가격 책정 계층 선택** 블레이드에서 **표준**을 클릭한 다음 **선택**을 클릭하여 변경 내용을 저장합니다.

      ![DocumentDB 설정의 스크린샷 및 가격 책정 계층 블레이드 선택](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. **설정** 블레이드로 돌아가면 **가격 책정 계층**이 **표준**으로 변경되어 있으며 **처리량(RU/s)** 상자가 기본값 400으로 표시됩니다. **확인**을 클릭하여 변경 내용을 저장합니다.

    > [AZURE.NOTE] 처리량을 400에서 10,000[요청 단위](../articles/documentdb/documentdb-request-units.md)/초(RU/s) 사이로 설정할 수 있습니다. 월별 예상 비용을 제공하기 위해 페이지 아래쪽의 **가격 책정 요약**이 자동으로 업데이트됩니다.
    
	![처리량 값을 변경하는 위치를 보여 주는 설정 블레이드의 스크린 샷](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. **데이터베이스** 블레이드로 다시 돌아가면 수퍼차지된 컬렉션의 처리량을 확인할 수 있습니다.

	![수정된 컬렉션과 데이터베이스 블레이드의 스크린샷](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

미리 정의된 사용자 정의 처리량과 관련된 변경 사항에 대한 자세한 내용은 블로그 게시물 [DocumentDB: 새 가격 책정 옵션 사용에 대해 알아야 하는 모든 항목](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)을 참조하세요.

## 다음 단계

더 많은 처리량(10,000RU/s 초과) 또는 더 많은 저장소(10GB 초과)가 필요하다고 판단되는 경우 분할된 컬렉션을 만들 수 있습니다. 분할된 컬렉션을 만들려면 [컬렉션 만들기](documentdb-create-collection.md)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->