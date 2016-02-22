<properties
	pageTitle="Azure 검색을 사용하여 StackExchange 데이터를 검색하는 방법 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Microsoft Azure에서 클라우드 호스팅되는 검색 서비스인 Azure 검색을 사용하여 REST 검색을 수행하는 방법을 알아봅니다."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="liamca"/>

# Azure 검색을 사용하여 StackExchange 데이터를 검색하는 방법

이 문서에서는 [Azure 검색](https://azure.microsoft.com/services/search/)으로 수행할 수 있는 핵심 전체 텍스트 검색 기능 중 몇 가지를 조명합니다. 다음 [속성](http://blog.stackoverflow.com/2009/06/attribution-required/)을 통해 Stack Exchange가 Creative Commons에 [제공한](https://archive.org/details/stackexchange) 데이터를 사용합니다.

## 시작

일부 기능을 강조하기 위해, 사용자가 사용할 수 있도록 2015년 10월 14일자 Programmer StackExchange의 데이터를 포함하는 Azure 검색 인덱스를 만들었습니다. 참고: 이 데이터가 포함된 고유의 Azure 검색 인덱스를 만드는 방법도 나중에 설명합니다.

먼저 사용자가 “azure”라는 단어를 입력하여 Azure와 관련한 StackExchange 게시물을 찾을 수 있는 아주 간단한 전체 텍스트 검색 쿼리부터 시작해 보겠습니다. 이 링크를 클릭하여 이 작업의 실제 동작을 확인해 봅니다.

> [http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

이 예에서는 검색 매개 변수로 “azure”를 전달하면 그 결과로 JSON 형식의 결과가 표시됩니다. 시험해 볼 수 있는 쿼리의 다른 몇 가지 예는 다음과 같습니다.

-	`Faceting`: 사용자가 데이터 집합을 검색할 때 데이터를 필터링할 수 있으면 결과 탐색에 크게 도움이 됩니다. 이를 구현하기 위해 보통은 사용자에게 표시되는 카테고리(패싯)의 집합으로 시작하게 됩니다. 활용할 수 있는 몇 가지 패싯의 예는 다음과 같습니다.
  -	**태그**: 많은 질문에는 관련한 태그가 있어 사용자가 특정 카테고리를 상세 확인할 수 있습니다.
  -	**날짜**: 사용자가 특정 기간 동안 질문을 받았거나 답한 질문만 보고자 할 수 있습니다.
  -	**사용자**: 결과를 특정 사용자에 국한하여 보고자 할 수 있습니다. 이 예에서는 “azure”를 검색하지만 사용자 이름 tagsCollection 및 acceptedAnswerDisplayName의 패싯 카운트를 반환합니다.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering`: 사용자가 패싯을 선택한 다음 결과를 패싯 값으로 한정하는 필터를 활용하여 다른 검색을 수행하려 합니다. 예를 들어, “Azure”를 검색하지만 결과는 viewCount 내림차순에 따라 “architecture” 태그가 있는 항목으로 한정하려면

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Fuzzy Search`: [Lucene 쿼리 식](https://msdn.microsoft.com/library/mt589323.aspx)에 대한 새로운 지원을 통해, 결과 임의 일치 및 특정 필드로 검색 한정 등과 같은 몇 가지 유용한 쿼리를 수행할 수 있습니다. 이 예에서는 필드에서 단어 “visualize”를 검색하지만 ~는 임의 일치를 나타내므로 visualise, visualizing 같은 결과도 함께 반환됩니다.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning`: [평가 프로필](https://msdn.microsoft.com/library/azure/dn798928.aspx)은 검색 서비스에서 반환한 결과를 조정하는 데 크게 도움이 됩니다. 이제 [Lucene 쿼리 식](https://msdn.microsoft.com/library/mt589323.aspx)을 사용하여 즉석에서 개별 필드를 평가하고 조건을 적용할 수 있습니다. 예를 들어, 제목 필드에서 “visualize” 또는 “chart”단어를 검색하지만 그 중에서 “chart”라는 단어에 더 가중치를 주고 싶다면 다음과 같이 할 수 있습니다.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  사용자는 이 데이터 집합의 여러 다른 필드를 사용하여 관련 결과를 향상시킬 수 있습니다. 예를 들어 보겠습니다.

  -	answerCount, commentCount, favoriteCount 및 viewCount 등의 수치 필드에 대한 **크기** 평가는 해당 필드의 카운트가 높은 검색 결과에 중점을 둡니다.
  -	creationDate 및 lastActivityDate 등의 날짜 시간 필드에 대한 **최신 항목** 평가는 더 최근에 만들어졌거나 활성 상태인 항목에 중점을 둡니다.
  -	**필드 가중치**는 검색 텍스트가 질문 본문에 있는 경우 질문에 있는 것보다 더 관련도가 높다고 표시합니다.

그 밖에도 다음과 같은 항목을 사용할 수 있습니다.

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx): 사용자가 검색 상자에 입력하면 제목, 태그, 사용자 이름 등의 필드가 자동 완성되어 편리하게 사용할 수 있습니다.  

-	`Recommendations`: 종종 사용자가 보고 싶어할 수 있는 유사 질문을 표시하는 권장 사항을 만들기 위해 Apache Mahout 또는 Azure 기계 학습 같은 도구가 필요할 수 있습니다. 이 데이터 집합에는 이미 권장 사항이 있습니다.

이 JSFiddle 페이지를 활용하여 다양한 쿼리 유형을 자유롭게 테스트할 수 있습니다. 계속 읽으면 이 인덱스의 생성 방법과 관련한 자세한 내용을 확인할 수 있습니다. 궁금한 점은 Twitter 계정 [@liamca](https://twitter.com/liamca)를 통해 직접 문의하세요.

## 이 Azure 검색 인덱스가 만들어진 방법

Brent는 SQL 데이터베이스에 데이터를 준비하는 방법을 설명하기 위해 많은 것을 준비했습니다. 이 데이터 준비 과정을 단계별로 직접 해 보려면 [여기에서 자습서](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/)를 확인하세요. 그렇지 않으면 2015년 10월 15일부터 몇 가지 데이터가 미리 입력되어 있는 Azure SQL 데이터베이스를 사용하거나, 제가 만든 Azure 검색 인덱스를 사용해 볼 수 있습니다. 이에 대한 자세한 내용은 아래의 “준비된 Azure SQL 데이터베이스에서 데이터 가져오기" 섹션에서 설명합니다. Brent가 작성한 개요에서 제가 추가한 유일한 항목은 Azure SQL 데이터베이스에서 뷰를 만드는 것입니다. 이 뷰는 [Azure 검색 인덱서](https://msdn.microsoft.com/library/azure/dn946891.aspx)에서 사용하려는 테이블 그룹으로부터 데이터를 탐색 및 수집하는 데 사용하게 됩니다. 이 뷰는 다음과 같이 정의됩니다.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

이 작업이 완료되면 [Azure 클래식 포털](https://portal.azure.com)을 사용하여 위의 Azure SQL 뷰에서 “데이터를 가져와" 이 뷰의 필드 스키마를 기초로 Azure 검색 인덱스를 만들 수 있습니다. 준비한 Azure SQL 데이터베이스를 사용하려면 이 읽기 전용 연결 문자열을 사용할 수 있습니다.

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=AcomDC_0211_2016-->