---
title: '자습서: C# 예제 코드에서 Azure SQL 데이터베이스의 데이터 인덱싱 - Azure Search'
description: Azure SQL 데이터베이스에 연결하고, 검색 가능한 데이터를 추출하고, Azure Search 인덱스에 로드하는 방법을 보여주는 C# 코드 예제입니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 04/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8550e220a2c87823fc337154ea33dd3c4ec81ed0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528053"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C# 자습서: Azure Search 인덱서를 사용하여 Azure SQL 데이터베이스 탐색

Azure SQL 데이터베이스 샘플에서 검색할 수 있는 데이터를 추출하기 위해 인덱서를 구성하는 방법을 알아봅니다. [인덱서](search-indexer-overview.md)는 외부 데이터 원본을 탐색하는 Azure Search의 구성 요소이며 콘텐츠로 [검색 인덱스](search-what-is-an-index.md)를 채웁니다. 모든 인덱서 중에서 Azure SQL Database에 대한 인덱서가 가장 널리 사용됩니다. 

인덱서 구성의 숙련도에 따라 작성하고 유지 관리해야 하는 코드를 단순화할 수 있습니다. 스키마 준수 JSON 데이터 세트를 준비하고 푸시하는 대신 인덱서를 데이터 원본에 연결하고, 인덱서로 데이터를 추출하고, 인덱스에 삽입하고, 필요에 따라 되풀이 일정으로 인덱서를 실행하여 기본 원본에서 변경 내용을 적용할 수 있습니다.

이 자습서에서는 [Azure Search .NET 클라이언트 라이브러리](https://aka.ms/search-sdk) 및 .NET Core 콘솔 애플리케이션을 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 애플리케이션 설정에 검색 서비스 정보 추가
> * Azure SQL Database에서 외부 데이터 세트 준비 
> * 샘플 코드에서 인덱스 및 인덱서 정의 검토
> * 데이터를 가져오는 인덱서 코드 실행
> * 인덱스 검색
> * 포털에서 인덱서 구성 보기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스, 도구 및 데이터는 다음과 같습니다. 

[Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/)는 인덱서에서 사용되는 외부 데이터 원본을 저장합니다. 샘플 솔루션은 SQL 데이터 파일을 제공하여 테이블을 만듭니다. 이 자습서에는 서비스 및 데이터베이스를 만드는 단계가 제공됩니다.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)(모든 버전)을 사용하여 이 샘플 솔루션을 실행할 수 있습니다. 샘플 코드와 지침은 Community 평가판 버전에서 테스트되었습니다.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started)는 Azure 샘플 GitHub 리포지토리에 있는 샘플 솔루션을 제공합니다. 솔루션을 다운로드 및 추출합니다. 기본적으로 솔루션은 읽기 전용입니다. 솔루션을 마우스 오른쪽 단추로 클릭하고 읽기 전용 특성을 지워서 파일을 수정할 수 있도록 합니다.

> [!Note]
> 체험 Azure Search 서비스를 사용하는 경우 세 가지 인덱스, 세 가지 인덱서 및 세 가지 데이터 원본으로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 서비스에 새 리소스를 허용하는 공간이 있는지 확인합니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="set-up-connections"></a>연결 설정
필요한 서비스에 대한 연결 정보는 솔루션의 **appsettings.json** 파일에 지정됩니다. 

1. Visual Studio에서 **DotNetHowToIndexers.sln** 파일을 엽니다.

1. 각 설정을 채울 수 있도록 솔루션 탐색기에서 **appsettings.json**을 엽니다.  

처음 두 항목은 Azure Search 서비스의 URL 및 관리 키를 사용하여 바로 채울 수 있습니다. 엔드포인트가 `https://mydemo.search.windows.net`일 경우 서비스 이름에 `mydemo`를 지정해야 합니다.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

마지막 항목에는 기존 데이터베이스가 필요합니다. 이는 다음 단계에서 만듭니다.

## <a name="prepare-sample-data"></a>샘플 데이터 준비

이 단계에서 인덱서가 탐색할 수 있는 외부 데이터 원본을 만듭니다. Azure Portal 및 샘플의 *hotels.sql* 파일을 사용하여 Azure SQL Database에서 데이터 세트를 만들 수 있습니다. Azure Search는 뷰 또는 쿼리에서 생성된 일반 행 집합을 사용합니다. 샘플 솔루션에 있는 SQL 파일은 단일 테이블을 만들고 채웁니다.

다음 연습은 기존 서버 또는 데이터베이스를 사용하지 않고 2단계에서 모두 만들도록 지시합니다. 필요에 따라 기존 리소스가 있는 경우 4단계에서부터 여기에 호텔 테이블을 추가할 수 있습니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)합니다. 

2. **Azure SQL Database**를 찾거나 만들어 데이터베이스, 서버 및 리소스 그룹을 만듭니다. 기본값 및 가장 낮은 수준의 가격 책정 계층을 사용할 수 있습니다. 서버를 만드는 이점은 이후 단계에서 테이블을 만들고 로드하는 데 필요한 관리자 사용자 이름 및 암호를 지정할 수 있다는 것입니다.

   ![새 데이터베이스 페이지](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. **만들기**를 클릭하여 새 서버 및 데이터베이스를 배포합니다. 서버 및 데이터베이스를 배포하도록 대기합니다.

4. 아직 새 데이터베이스에 대한 SQL Database 페이지를 열지 않은 경우 엽니다. 리소스 이름은 *SQL Server*가 아닌 *SQL Database*여야 합니다.

   ![SQL Database 페이지](./media/search-indexer-tutorial/hotels-db.png)

4. 탐색 창에서 **쿼리 편집기(미리 보기)** 를 클릭합니다.

5. **로그인**을 클릭하고 사용자 이름 및 서버 관리자의 암호를 입력합니다.

6. **쿼리 열기**를 클릭하고 *hotels.sql*의 위치로 이동합니다. 

7. 파일을 선택하고 **열기**를 클릭합니다. 이 스크립트는 다음 스크린샷과 비슷하게 표시됩니다.

   ![SQL 스크립트](./media/search-indexer-tutorial/sql-script.png)

8. **실행**을 클릭하여 쿼리를 실행합니다. 결과 창에서 3행에 대한 쿼리 성공 메시지가 표시됩니다.

9. 이 테이블에서 행 집합을 반환하려면 확인 단계로 다음 쿼리를 실행할 수 있습니다.

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    정형화된 쿼리인 `SELECT * FROM Hotels`는 쿼리 편집기에서 작동하지 않습니다. 샘플 데이터는 위치 필드에 지리 좌표를 포함합니다. 이 시점에는 편집기에서 처리되지 않습니다. 쿼리할 다른 열의 목록은 다음 문을 실행할 수 있습니다.`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. 이제 외부 데이터 세트가 있으므로 데이터베이스에 대한 ADO.NET 연결 문자열을 복사합니다. 데이터베이스의 SQL Database 페이지에서 **설정** > **연결 문자열**로 이동하고, ADO.NET 연결 문자열을 복사합니다.
 
    ADO.NET 연결 문자열은 다음 예제와 비슷하며 유효한 데이터베이스 이름, 사용자 이름 및 암호를 사용하도록 수정됩니다.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Visual Studio에 있는 **appsettings.json** 파일에서 세 번째 항목으로 "AzureSqlConnectionString"에 연결 문자열을 붙여넣습니다.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>코드 이해

데이터 및 구성 설정이 적용되면 **DotNetHowToIndexers.sln**의 샘플 프로그램을 빌드하고 실행할 준비가 완료된 것입니다. 그렇게 하기 전에 이 샘플에 대한 인덱스 및 인덱서 정의를 알아보는 데 1분이 걸립니다. 관련된 코드가 다음과 같은 두 개의 파일에 있습니다.

  + **hotel.cs**는 인덱스를 정의하는 스키마가 포함됩니다.
  + **Program.cs**는 서비스에서 구조를 만들고 관리하는 함수가 포함됩니다.

### <a name="in-hotelcs"></a>hotel.cs에서

인덱스 스키마는 다음과 같은 HotelName의 필드 정의에 표시된 대로 필드가 전체 텍스트 검색 가능, 필터링 가능, 정렬 가능한지 등 허용되는 작업을 지정하는 속성을 비롯한 필드 컬렉션을 정의합니다. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

스키마는 점수 매기기, 사용자 지정 분석기 및 다른 구문을 향상시키기 위해 점수 매기기 프로필을 비롯한 다른 요소를 포함할 수도 있습니다. 그러나 목적상 스키마는 밀도가 낮게 정의되어 샘플 데이터 세트에서 발견되는 필드를 구성합니다.

이 자습서에서 인덱서는 하나의 데이터 원본에서 데이터를 끌어옵니다. 실제로 여러 인덱서를 동일한 인덱스에 연결하여 여러 데이터 원본의 통합 검색 가능 인덱스를 만들 수 있습니다. 동일한 인덱스 인덱서 쌍을 사용하여 유연성이 필요한 경우에 다양한 인덱서 및 데이터 원본 조합으로 데이터 원본 또는 하나의 인덱스가 달라질 수 있습니다.

### <a name="in-programcs"></a>Program.cs에서

기본 프로그램에는 클라이언트, 인덱스, 데이터 원본 및 인덱서를 만드는 논리가 포함되어 있습니다. 코드는 이 프로그램을 여러 번 실행한다는 가정 하에서 동일한 이름의 기존 리소스를 확인하고 삭제합니다.

데이터 원본 개체는 Azure SQL에서 기본적으로 제공하는 [변경 내용 검색 기능](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 활용하기 위한 [증분 인덱싱](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)을 포함하여 Azure SQL 데이터베이스 리소스에 한정된 설정을 사용하여 구성됩니다. Azure SQL의 데모 호텔 데이터베이스에는 **IsDeleted**라는 "일시 삭제" 열이 있습니다. 데이터베이스에서 이 열을 true로 설정하면 인덱서가 Azure Search 인덱스에서 해당 문서를 제거합니다.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

인덱서 개체는 플랫폼의 제약을 받지 않으므로 원본에 관계없이 구성, 일정 예약 및 호출이 동일합니다. 이 예제 인덱서는 인덱서 기록을 지우는 초기화 옵션인 일정을 포함하고 있으며, 즉시 인덱서를 만들고 실행하는 메서드를 호출합니다.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>인덱서 실행

이 단계에서는 프로그램을 컴파일하고 실행합니다. 

1. 솔루션 탐색기에서 **DotNetHowToIndexers**를 마우스 오른쪽 단추로 클릭하고 **빌드**를 선택합니다.
2. 다시 **DotNetHowToIndexers** 및 **디버그** > **새 인스턴스 시작**을 마우스 오른쪽 단추로 클릭합니다.

디버그 모드로 프로그램을 실행합니다. 콘솔 창에서는 각 작업의 상태를 보고합니다.

  ![SQL 스크립트](./media/search-indexer-tutorial/console-output.png)

코드는 Visual Studio에서 로컬로 실행되어 Azure의 검색 서비스에 연결합니다. 그러면 연결 문자열을 사용하여 Azure SQL Database에 연결하고 데이터 세트를 검색합니다. 이 많은 작업에서 잠재적인 실패 지점이 있지만 오류가 발생하는 경우 다음 상태를 먼저 확인하세요.

+ 제공한 검색 서비스 연결 정보가 이 자습서에서 해당 서비스 이름으로 제한됩니다. 전체 URL을 입력한 경우 인덱스 생성 시 연결 오류와 함께 작업이 중지됩니다.

+ **appsettings.json**에 있는 데이터베이스 연결 정보입니다. 해당 항목은 포털에서 가져온 ADO.NET 연결 문자열이며 데이터베이스에 유효한 사용자 이름 및 암호를 포함하도록 수정되어야 합니다. 사용자 계정에는 데이터를 검색할 수 있는 권한이 있어야 합니다.

+ 리소스 제한 공유 (체험) 서비스에 3개의 인덱스, 인덱서 및 데이터 원본이라는 제한이 있습니다. 최대 제한 시 서비스는 새 개체를 만들 수 없습니다.

## <a name="search-the-index"></a>인덱스 검색 

Azure Portal의 검색 서비스 개요 페이지에서 맨 위에 있는 **검색 탐색기**를 클릭하고 새 인덱스에 몇 가지 쿼리를 제출합니다.

1. 맨 위에서 **변경 인덱스**를 클릭하고 *hotels* 인덱스를 선택합니다.

2. **검색** 단추를 클릭하여 빈 검색을 발급합니다. 

   인덱스에 있는 세 개의 항목이 JSON 문서로 반환됩니다. 전체 구조를 볼 수 있도록 검색 탐색기는 JSON으로 문서를 반환합니다.

3. 다음으로 검색 문자열을 입력합니다.`search=river&$count=true` 

   이 쿼리는 `river` 용어의 전체 텍스트 검색을 호출하고 결과에는 일치하는 문서 수가 포함됩니다. 일치하는 문서 수를 반환하는 작업은 수많은 문서를 포함하는 대규모 인덱스가 있을 때 시나리오를 테스트하는 경우 유용합니다. 이 경우에 하나의 문서만이 쿼리와 일치합니다.

4. 마지막으로 JSON 출력을 관심 분야로 제한하는 검색 문자열을 입력합니다.`search=river&$count=true&$select=hotelId, baseRate, description` 

   쿼리 응답이 선택한 필드로 줄어들어 더 간결한 출력이 표시됩니다.

## <a name="view-indexer-configuration"></a>인덱서 구성 보기

프로그래밍 방식으로 방금 만든 인덱서를 비롯한 모든 인덱서가 포털에 나열됩니다. 인덱서 정의를 열거나, 해당 데이터 원본을 보거나, 새로운 기능과 변경된 행을 선택하도록 새로 고침 일정을 구성할 수 있습니다.

1. [Azure Portal에 로그인](https://portal.azure.com/) 그리고 검색 서비스 **개요** 페이지에서 **인덱스**, **인덱서** 및 **데이터 원본**의 링크를 클릭합니다.
3. 구성 설정을 보거나 수정할 개별 개체를 선택합니다.

   ![인덱서 및 데이터 원본 타일](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후 정리하는 가장 빠른 방법은 Azure Search 서비스를 포함하고 있는 리소스 그룹을 삭제하는 것입니다. 리소스 그룹을 삭제하여 이제 리소스 그룹 내의 모든 항목을 영구 삭제할 수 있습니다. 포털에서 리소스 그룹 이름은 Azure Search 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

AI 지원 알고리즘을 인덱서 파이프라인에 연결할 수 있습니다. 다음 단계로 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blob Storage에서 문서 인덱싱](search-howto-indexing-azure-blob-storage.md)