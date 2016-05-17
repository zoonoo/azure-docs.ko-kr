<properties
   pageTitle="Azure Data Catalog - 데이터 카탈로그 시작 | Microsoft Azure"
   description="Azure Data Catalog의 시나리오 및 기능을 제공하는 종단간 자습서입니다."
   documentationCenter=""
   services="data-catalog"
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="05/06/2016"
   ms.author="maroche"/>

# Azure 데이터 카탈로그 시작

이 문서는 **Azure Data Catalog**의 시나리오 및 기능에 대한 종단 간 개요입니다. 서비스에 등록하면 이러한 단계를 수행하여 데이터 카탈로그를 만들고 데이터 원본을 등록, 주석 추가 및 검색합니다.

## 자습서 필수 구성 요소

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독** - 구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure Active Directory** - Azure 데이터 카탈로그는 ID 및 액세스 관리를 위해 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)를 사용합니다.
- **데이터 원본** - Azure Data Catalog에는 데이터 원본을 검색하는 기능이 있습니다. 이 자습서는 Adventure Works 샘플 데이터베이스를 사용하지만 사용자의 역할과 관련된 친밀한 데이터로 작업하는 것을 선호하는 경우 지원되는 모든 데이터 원본을 사용할 수 있습니다. 지원되는 데이터 원본 목록은 [지원되는 데이터 원본](data-catalog-dsr.md)을 참조하세요.

> [AZURE.NOTE] Azure 구독 및 Azure Active Directory에 대한 자세한 내용은 [Azure Data Catalog의 필수 구성 요소](data-catalog-prerequisites.md)를 참조하세요.

이제 Adventure Works 샘플 데이터베이스를 설치하기 시작하겠습니다.

## 연습 1: Adventure Works 샘플 데이터베이스 설치

이 연습에서는 다음 연습에서 사용되는 SQL Server Database Engine용 Adventure Works 샘플을 설치합니다.

### Adventure Works 2014 OLTP 데이터베이스 설치

Adventure Works 데이터베이스는 제조, 판매 및 구매를 포함하는 가상의 자전거 제조업체(Adventure Works Cycles)에 대한 표준 온라인 트랜잭션 처리 시나리오를 지원합니다. 이 자습서에서는 제품에 대한 정보를 **Azure Data Catalog**에 등록합니다.

다음은 Adventure Works 샘플 데이터베이스를 설치하는 방법입니다.

Adventure Works 샘플 데이터베이스를 설치하려면 CodePlex의 [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661)에 있는 AdventureWorks2014 백업을 복원합니다. 데이터베이스를 복원하는 한 가지 방법은 SQL Server Management Studio에서 T-SQL 스크립트를 실행하는 것입니다.

**T-SQL 스크립트로 Adventure Works 샘플 데이터베이스 설치**

1.	C:\\DataCatalog\_GetStarted라는 폴더를 만듭니다. 다른 폴더 이름을 사용하는 경우 T-SQL 스크립트에서 경로를 변경해야 합니다.
2.	[Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661)을 다운로드합니다.
3.	Adventure Works 2014 Full Database Backup.zip을 C:\\DataCatalog\_GetStarted에 추출합니다. 아래의 스크립트는 백업 파일이 C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak에 위치한다고 가정합니다.
4.	**SQL Server Management Studio**의 **표준** 도구 모음에서 **새 쿼리**를 클릭합니다.
5.	쿼리 창에서 다음 T-SQL 코드를 실행합니다.

**이 스크립트를 실행하여 Adventure Works 2014 데이터베이스 설치**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

T-SQL 스크립트를 실행하는 대신 SQL Server Management Studio를 사용하여 데이터베이스를 복원할 수 있습니다. [데이터베이스 백업(SQL Server Management Studio) 복원](http://msdn.microsoft.com/library/ms177429.aspx)을 참조하세요.

이 연습에서는 나머지 연습에서 사용되는 Adventure Works 샘플 데이터베이스를 설치했습니다. 다음 연습에서는 Adventure Works 샘플 데이터베이스의 테이블에서 **Azure Data Catalog** 데이터 원본을 등록하는 방법을 알아봅니다.

## 연습 2: 데이터 원본 등록

이 연습에서는 **Azure Data Catalog** 등록 도구를 사용하여 카탈로그로 Adventure Works 데이터 원본을 등록합니다. 등록의 목적은 데이터 원본 및 포함하는 자산에서 이름, 유형 및 위치와 같은 주요 구조적 메타데이터를 추출하고 카탈로그에 메타데이터를 복사하는 것입니다. 데이터 원본 및 해당 데이터는 있는 위치에 유지되지만 메타데이터는 보다 쉽게 검색 및 이해할 수 있도록 카탈로그를 통해 사용됩니다.

### 다음은 데이터 원본을 등록하는 방법입니다.

1.	https://azure.microsoft.com/services/data-catalog로 이동하여 **시작**을 클릭합니다.
2.	**Azure Data Catalog** 포털에 로그인하고 **데이터 게시**를 클릭합니다.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	**응용 프로그램 시작**을 클릭합니다.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. **시작** 페이지에서 **로그인**을 클릭하고 자격 증명을 입력합니다.
5. **Microsoft Azure Data Catalog** 페이지에서 **SQL Server**를 두 번 클릭하거나 **SQL Server** 및 **다음**을 클릭합니다.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	AdventureWorks2014에 대한 SQL Server 연결 속성을 입력(아래 예제 참조)하고 **연결**을 클릭합니다.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	다음 페이지는 데이터 원본의 메타데이터를 등록하는 위치입니다. 이 예제에서는 AdventureWorks 프로덕션 네임스페이스에서 **프로덕션/제품** 개체를 등록합니다. 수행하는 방법은 다음과 같습니다.

    a. **서버 계층 구조** 트리에서 **프로덕션**을 클릭합니다.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Product, ProductCategory, ProductDescription 및 ProductPhoto를 Ctrl+클릭합니다.

    c. 선택 항목을 이동시키는 화살표(**>**)를 클릭합니다. 선택한 모든 제품 개체를 **등록할 개체** 목록으로 이동시킵니다.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. **태그 추가**에서 설명, 사진을 입력합니다. 이러한 데이터 자산에 대한 검색 태그가 추가됩니다. 태그는 사용자가 등록된 데이터 원본을 찾을 수 있는 훌륭한 방법입니다.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e. **선택 사항**: **미리 보기를 포함**하고 **데이터 원본 전문가를 추가**할 수 있습니다.

    f. **등록**을 클릭합니다. **Azure Data Catalog**는 선택한 개체를 등록합니다. 이 연습에서는 Adventure Works에서 선택한 개체가 등록됩니다.

    g. 등록된 데이터 원본 개체를 보려면 **포털 보기**를 클릭합니다. **Azure Data Catalog** 포털의 **타일** 또는 **목록**에서 데이터 원본 개체를 볼 수 있습니다.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

이 연습에서는 Adventure Works 샘플 데이터베이스에서 개체를 등록했으므로 조직 전체의 사용자가 쉽게 검색할 수 있습니다. 다음 연습에서는 등록된 데이터 자산을 검색하는 방법을 배웁니다.

## 연습 3: 등록된 데이터 자산 검색

이 연습에서는 **Azure Data Catalog** 포털을 사용하여 등록된 데이터 자산을 검색하고 해당 메타데이터를 확인합니다. **Azure Data Catalog**는 간단한 키워드 검색, 대화형 필터 및 “고급” 사용자에 대한 고급 검색 구문을 비롯하여 데이터 자산 검색을 위한 여러 도구를 제공합니다.

### 다음은 등록된 데이터 자산을 검색하는 방법입니다.

**Azure Data Catalog**는 간단하지만 사용자가 필요한 데이터를 반환하는 쿼리를 쉽게 작성할 수 있는 강력한 검색 구문을 포함합니다. **Azure Data Catalog** 검색에 대한 자세한 내용은 [데이터 카탈로그 검색 구문 참조](https://msdn.microsoft.com/library/azure/mt267594.aspx)를 참조하세요.

**Azure 데이터 카탈로그**에는 다음과 같은 검색 옵션이 있습니다.

- 키워드 검색
- 필터
- 고급 검색

볼 수 있는 데이터 자산을 구체화할 수도 있습니다. **Azure 데이터 카탈로그**에는 다음과 같은 보기 옵션이 있습니다.

- 보기 속성
- 보기 열
- 보기 미리 보기

이 예에서는 키워드 검색을 사용합니다. **Azure 데이터 카탈로그** 검색에는 여러 쿼리 기법이 있습니다. 이 예에서는 **그룹화** 검색 쿼리를 사용합니다.

**쿼리 기법**

|기법|사용|예
|---|---|---
|속성 범위|지정된 속성에서 일치하는 검색 용어가 있는 데이터 원본만 반환|name:product
|논리 연산자|페이지의 부울 연산자 섹션에 설명한 대로 부울 연산을 사용하여 검색 범위 확장 또는 축소|finance NOT corporate
|괄호로 그룹화|특별히 부울 연산자와 함께 논리적 격리를 수행하도록 쿼리의 일부를 그룹화하기 위해 괄호 사용|name:product AND (tags:illustration OR tags:photo)
|비교 연산자|숫자 및 날짜 데이터 유형이 있는 속성에 대한 일치가 아닌 비교 사용|creationTime:>11/05/14

이 예에서는 이름이 제품과 동일하고 태그가 그림과 동일하거나 태그가 사진과 동일한 데이터 자산에 대해 **그룹화** 검색을 수행합니다.

1. https://azure.microsoft.com/services/data-catalog로 이동하여 **시작**을 클릭하고 **Azure Data Catalog** 포털에 로그인합니다.
2. **데이터 카탈로그 검색** 상자에 **그룹화**를: 쿼리:(tags:description OR tags:photo)로 입력합니다.
3. 검색 아이콘을 클릭하거나 Enter를 누릅니다. **Azure 데이터 카탈로그**에서 이 검색 쿼리에 대한 데이터 자산을 표시합니다.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

이 연습에서는 카탈로그로 등록된 Adventure Works 데이터 자산을 검색하고 확인하기 위해 **Azure Data Catalog** 포털을 사용했습니다.

<a name="annotating"/>
## 연습 4: 등록된 데이터 소스에 주석 추가

이 연습에서는 **Azure Data Catalog** 포털을 사용하여 이전에 카탈로그에 등록된 데이터 자산에 주석을 추가합니다. 제공하는 주석은 등록하는 동안 데이터 원본에서 추출된 구조적 메타데이터를 보충 및 강화하며 데이터 자산을 보다 쉽게 검색하고 이해할 수 있도록 합니다. 각 **데이터 카탈로그** 사용자는 자신만의 주석을 추가할 수 있기 때문에 데이터에 대한 관점을 모든 사용자와 쉽게 공유할 수 있습니다.

### 다음은 데이터 자산에 주석을 추가하는 방법입니다.

1. https://azure.microsoft.com/services/data-catalog로 이동하여 **시작**을 클릭하고 **Azure Data Catalog** 포털에 로그인합니다.
2. **검색**을 클릭합니다.
3. 하나 이상의 **데이터 자산**을 선택합니다. 이 예에서는 **ProductPhoto**를 선택하고 "마케팅 자료용 제품 사진"을 입력합니다.
4. 선택한 데이터 자산을 사용하는 이유와 방법을 다른 사람이 검색하고 이해할 수 있도록 **설명**을 입력합니다. 예를 들어, "제품 이미지"를 입력합니다. 더 많은 태그를 추가하고 열을 볼 수도 있습니다.
5. 이제 카탈로그에 추가된 설명이 포함된 메타데이터를 사용하여 데이터 자산을 검색하기 위해 검색 및 필터링을 시도할 수 있습니다.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

이 연습에서는 등록된 데이터 자산에 설명이 있는 정보를 추가했으므로 카탈로그 사용자가 이해하는 용어를 사용하여 데이터 원본을 검색할 수 있습니다.

> [AZURE.NOTE] 데이터 카탈로그의 표준 버전에는 카탈로그 관리자가 중앙 비즈니스 분류를 정의할 수 있는 비즈니스 용어집이 포함되어 있습니다. 그런 다음 카탈로그 사용자는 용어집 용어를 사용하여 데이터 자산에 주석을 추가할 수 있습니다. 자세한 내용은 [관리 대상 태깅을 위해 비즈니스 용어집을 설정하는 방법](data-catalog-how-to-business-glossary.md)을 참조하세요.

## 연습 5: 크라우드소싱 메타데이터

이 연습에서는 다른 사용자와 함께 작업하여 카탈로그에서 데이터 자산에 메타데이터를 추가합니다. 주석 추가에 대한 **Azure Data Catalog**의 크라우드소싱 접근 방식에서는 사용자가 태그, 설명 및 기타 메타데이터를 추가할 수 있기 때문에 데이터 자산 및 사용에 대한 어떤 관점이 있는 사용자가 해당 관점을 캡처하여 다른 사용자가 사용할 수 있도록 할 수 있습니다.

> [AZURE.NOTE] 이 자습에 대해 함께 작업할 다른 사용자가 없다고 해도 걱정하지 마세요. 데이터 카탈로그에 액세스하는 모든 사용자는 수행 방식을 선택할 때 자신만의 관점을 추가할 수 있습니다. 메타데이터에 대한 크라우드소싱 접근 방식을 통해 시간이 지날수록 카탈로그의 내용 및 카탈로그 메타데이터의 풍부함이 더해집니다.

### 다음은 데이터 자산에 대해 메타데이터를 크라우드소싱하는 방법입니다.

위의 [등록된 데이터 원본 주석 추가](#annotating) 연습을 반복하도록 동료에게 요청하세요. 동료가 ProductPhoto 등의 데이터 자산에 설명을 추가하고 나면 여러 주석이 표시됩니다.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

이 연습에서는 카탈로그 사용자가 크라우드소싱 메타데이터에 대한 **Azure Data Catalog**를 사용하여 자신이 검색한 데이터 자산에 주석을 추가할 수 있는 기능에 대해 알아보았습니다.

## 연습 6: 데이터 원본에 연결

이 연습에서는 **Azure Data Catalog** 포털과 Microsoft Excel을 사용하여 데이터 원본에 연결합니다.

> [AZURE.NOTE] 사용자가 **Azure 데이터 카탈로그**를 통해 실제 데이터 원본에 액세스할 수 있는 것은 아닙니다. 단지 사용자가 더 쉽게 검색하고 이해하는 데 도움이 됩니다. 사용자가 데이터 원본에 연결할 때 사용자가 선택하는 클라이언트 응용 프로그램은 Windows 자격 증명을 사용하거나 필요에 따라 자격 증명에 대한 메시지가 표시됩니다. 사용자가 이전에 허용된 데이터 원본에 대한 액세스가 없는 경우 이 사용자가 연결할 수 있으려면 액세스가 허용되어야 합니다.

### 다음은 Excel에서 데이터 원본에 연결하는 방법입니다.

1. https://azure.microsoft.com/services/data-catalog로 이동하여 **시작**을 클릭하고 **Azure Data Catalog** 포털에 로그인합니다.
2. **검색**을 클릭합니다.
3. 데이터 자산을 선택합니다. 이 예제에서는 ProductCategory를 선택합니다.
4. **다음에서 열기** > **Excel**을 선택합니다.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. **Microsoft Excel 보안 공지** 창에서 **사용**을 클릭합니다.
6. **ProductCategory.odc** 파일을 엽니다.
7. 데이터 원본은 Excel에서 열립니다.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

이 연습에서는 **Azure Data Catalog**를 사용하여 검색된 데이터 원본에 연결했습니다. **Azure 데이터 카탈로그** 포털에서는 **다음에서 열기...** 메뉴에 통합된 클라이언트 응용 프로그램을 사용하여 사용자가 직접 연결할 수 있으며 사용자가 자산 메타데이터에 포함된 연결 위치 정보를 통해 선택하는 모든 응용 프로그램을 사용하여 연결할 수 있습니다.

## 연습 7: 데이터 원본 메타데이터 제거

이 연습에서는 **Azure 데이터 카탈로그** 포털을 사용하여 등록된 데이터 자산에서 미리 보기 데이터를 제거하고 해당 카탈로그에서 데이터 자산을 삭제합니다.

> [AZURE.NOTE] 카탈로그의 기본 동작을 통해 사용자는 모든 데이터 원본을 등록할 수 있으며 등록된 데이터 자산을 삭제할 수 있습니다. **Azure 데이터 카탈로그의 표준 버전**에 포함된 관리 기능은 자산의 소유권을 얻고, 자산을 검색할 수 있는 사람을 제한하며, 자산을 삭제할 수 있는 사람을 제한하는 추가 옵션을 제공합니다.

**Azure Data Catalog**에서 개별 자산을 삭제하거나 여러 자산을 삭제할 수 있습니다.

### 다음은 여러 데이터 자산을 삭제하는 방법입니다.

1. https://azure.microsoft.com/services/data-catalog로 이동하여 **시작**을 클릭하고 **Azure Data Catalog** 포털에 로그인합니다.
2. **검색**을 클릭합니다.
3. 하나 이상의 데이터 자산을 선택합니다.
4. **삭제**를 클릭합니다.

이 연습에서는 카탈로그에서 등록된 데이터 자산을 제거했습니다.

## 연습 8: 등록된 데이터 원본 관리

이 연습에서는 **Azure Data Catalog**의 관리 기능을 사용하여 데이터 자산의 소유권을 얻고 사용자가 검색할 수 있는 내용 및 해당 자산을 관리하는 방법을 관리합니다.

> [AZURE.NOTE] 이 연습에 설명하는 관리 기능은 **무료 버전**에서 사용할 수 없으며 **표준 버전의 Azure Data Catalog**에서만 사용할 수 있습니다. **Azure 데이터 카탈로그**에서 데이터 자산의 소유권을 얻고, 공동 소유자를 데이터 자산에 추가하고, 데이터 자산의 표시 여부를 설정할 수 있습니다.

### 다음은 데이터 자산의 소유권을 얻고 표시 여부를 제한하는 방법입니다.

1. https://azure.microsoft.com/services/data-catalog로 이동하여 **시작**을 클릭하고 **Azure Data Catalog** 포털에 로그인합니다.
2. **검색**을 클릭합니다.
3. 하나 이상의 데이터 자산을 선택합니다.
4. **속성** 패널의 **관리** 섹션에서 **소유권 가져오기**를 클릭합니다.
5. 표시 여부를 제한하려면 **소유자 및 다음 사용자**를 클릭합니다.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

이 연습에서는 **Azure Data Catalog**의 관리 기능을 살펴보았으며 선택한 데이터 자산에 대한 표시를 제한했습니다.

## 요약

이 자습서에서는 엔터프라이즈 데이터 원본 등록, 주석 추가, 검색, 관리를 비롯하여 **Azure Data Catalog**의 필수 기능을 살펴보았습니다. 이제 자습서를 완료했습니다. 시작할 시간입니다. 사용자 및 팀이 필요로 하는 데이터 원본을 등록하고, 카탈로그를 사용하도록 동료를 초대하여 시작할 수 있습니다.

<!---HONumber=AcomDC_0511_2016-->