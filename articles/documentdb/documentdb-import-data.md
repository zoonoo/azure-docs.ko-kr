<properties 
	pageTitle="데이터베이스 마이그레이션 도구 - CSV에서 JSON으로 변환 | Microsoft Azure" 
	description="오픈 소스 DocumentDB 데이터 마이그레이션 도구를 사용하여 DocumentDB로 데이터를 가져오는 방법에 대해 알아봅니다. MongoDB, SQL, 테이블 저장소, DynamoDB 및 CSV에서 JSON입니다." 
	keywords="csv에서 json으로, 데이터베이스 마이그레이션 도구, csv에서 json으로 변환"
	services="documentdb" 
	authors="andrewhoh" 
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
	ms.author="anhoh"/>

# DocumentDB로 데이터 가져오기 - 데이터베이스 마이그레이션 도구

이 문서는 공개 소스 DocumentDB 데이터 마이그레이션 도구를 사용하여 JSON 파일, CSV 파일, SQL, MongoDB, Azure 테이블 저장소, Amazon DynamoDB 및 DocumentDB 컬렉션을 포함한 다양한 소스에서 데이터를 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)로 가져오는 방법을 보여줍니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

-	어떻게 JSON 파일 데이터를 DocumentDB로 가져올 수 있나요?
-	어떻게 CSV 파일 데이터를 DocumentDB로 가져올 수 있나요?
-	어떻게 SQL Server 데이터를 DocumentDB로 가져올 수 있나요?
-	어떻게 MongoDB 데이터를 DocumentDB로 가져올 수 있나요?
-	어떻게 Azure 테이블 저장소의 데이터를 DocumentDB로 가져올 수 있나요?
-	어떻게 Amazon DynamoDB의 데이터를 DocumentDB로 가져올 수 있나요?
-	HBase의 데이터를 DocumentDB로 가져오는 방법
-	어떻게 DocumentDB 컬렉션 간에 데이터를 마이그레이션할 수 있나요?

##<a id="Prerequisites"></a>필수 조건 ##

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지 확인합니다.

- [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) 이상

##<a id="Overviewl"></a>DocumentDB 데이터 마이그레이션 도구 개요 ##

DocumentDB 데이터 마이그레이션 도구는 다음을 비롯한 다양한 원본에서 DocumentDB로 데이터를 가져오는 오픈 소스 솔루션입니다.

- JSON 파일
- MongoDB
- SQL Server
- CSV 파일
- Azure 테이블 저장소
- Amazon DynamoDB
- HBase
- DocumentDB 컬렉션

가져오기 도구는 그래픽 사용자 인터페이스(dtui.exe)를 포함하지만 명령줄(dt.exe)에서 구동할 수도 있습니다. 실제로 UI를 통해 가져오기를 설정한 후 관련 명령을 출력하는 옵션이 있습니다. 가져오는 동안 계층 관계(하위 문서)를 만들 수 있도록 테이블 형식 원본 데이터(예: SQL Server 또는 CSV 파일)를 변환할 수 있습니다. 원본 옵션, 각 원본에서 가져오는 샘플 명령줄, 대상 옵션 및 가져오기 결과 보기에 대해 자세히 알아보려면 계속 진행하세요.


##<a id="Install"></a>DocumentDB 데이터 마이그레이션 도구 설치 ##

마이그레이션 도구 소스 코드는 GitHub의 [이 리포지토리](https://github.com/azure/azure-documentdb-datamigrationtool)에서 사용할 수 있으며, 컴파일된 버전은 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)에서 사용할 수 있습니다. 솔루션을 컴파일하거나, 컴파일된 버전을 다운로드하고 선택한 디렉터리에 압축을 풀 수 있습니다. 다음 중 하나를 실행합니다.

- **Dtui.exe**: 도구의 그래픽 인터페이스 버전
- **Dt.exe**: 도구의 명령줄 버전

##<a id="JSON"></a>JSON 파일 가져오기 ##

JSON 파일 원본 가져오기 옵션을 사용하면 단일 문서 JSON 파일이나 각각 JSON 문서 배열을 포함하는 JSON 파일을 하나 이상 가져올 수 있습니다. 가져올 JSON 파일을 포함하는 폴더를 추가하면, 하위 폴더에서 재귀적으로 파일을 검색하는 옵션도 있습니다.

![JSON 파일 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구](./media/documentdb-import-data/jsonsource.png)

JSON 파일을 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

	#Import a single JSON file	
	dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory of JSON files
	dt.exe /s:JsonFile /s.Files:C:\TESessions*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory (including sub-directories) of JSON files
	dt.exe /s:JsonFile /s.Files:C:\LastFMMusic***.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionTier:S3

	#Import a directory (single), directory (recursive), and individual JSON files
	dt.exe /s:JsonFile /s.Files:C:\Tweets*.*;C:\LargeDocs***.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionTier:S3

	#Import a single JSON file and partition the data across 4 collections
	dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionTier:S3

##<a id="MongoDB"></a>MongoDB에서 가져오기 ##

MongoDB 원본 가져오기 옵션을 사용하면 개별 MongoDB 컬렉션에서 가져오고 필요에 따라 쿼리를 사용하여 문서를 필터링하거나 프로젝션을 사용하여 문서 구조를 수정할 수 있습니다.

![MongoDB 원본 옵션의 스크린샷 - documentdb vs mongodb](./media/documentdb-import-data/mongodbsource.png)

연결 문자열은 표준 MongoDB 형식입니다.

	mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 MongoDB 인스턴스를 액세스할 수 있는지 확인합니다.

데이터를 가져올 컬렉션의 이름을 입력합니다. 필요에 따라 쿼리(예: {pop: {$gt: 5000}}) 및/또는 프로젝션(예: {loc:0})에 대한 파일을 제공하여 가져올 데이터를 필터링하고 형식을 지정할 수 있습니다.

MongoDB에서 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

	#Import all documents from a MongoDB collection
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionTier:S3

	#Import documents from a MongoDB collection which match the query and exclude the loc field
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionTier:S3

##<a id="MongoDBExport"></a>MongoDB 내보내기 파일 가져오기 ##

MongoDB 내보내기 JSON 파일 원본 가져오기 옵션을 사용하면 mongoexport 유틸리티에서 생성된 JSON 파일을 하나 이상 가져올 수 있습니다.

![MongoDB 내보내기 원본 옵션의 스크린샷 - documentdb vs mongodb](./media/documentdb-import-data/mongodbexportsource.png)

가져올 MongoDB 내보내기 JSON 파일이 포함된 폴더를 추가할 때 하위 폴더의 파일을 재귀적으로 검색하는 옵션이 있습니다.

MongoDB 내보내기 JSON 파일에서 가져오는 명령줄 샘플은 다음과 같습니다.

	dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionTier:S3

##<a id="SQL"></a>SQL Server에서 가져오기 ##

SQL 원본 가져오기 옵션을 사용하면 개별 SQL Server 데이터베이스에서 가져오고 필요에 따라 쿼리를 사용하여 가져올 레코드를 필터링할 수 있습니다. 또한 중첩 구분 기호를 지정하여 문서 구조를 수정할 수 있습니다(추가 정보 제공 예정).

![SQL 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구](./media/documentdb-import-data/sqlexportsource.png)

연결 문자열의 형식은 표준 SQL 연결 문자열 형식입니다.

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 SQL Server 인스턴스를 액세스할 수 있는지 확인합니다.

중첩 구분 기호 속성을 사용하여 가져오는 동안 계층 관계(하위 문서)를 만듭니다. 다음과 같은 SQL 쿼리를 고려해 보세요.

*CAST(BusinessEntityID AS varchar)를 ID, Name으로, AddressType을 [Address.AddressType]으로, AddressLine1을 [Address.AddressLine1]로, City를 [Address.Location.City]로, StateProvinceName을 [Address.Location.StateProvinceName]으로, PostalCode를 [Address.PostalCode]로, CountryRegionName을 AddressType='Main Office'인 Sales.vStoreWithAddresses에서 [Address.CountryRegionName]으로 선택합니다.*

다음과 같은 (부분) 결과가 반환됩니다.

![SQL 쿼리 결과의 스크린샷](./media/documentdb-import-data/sqlqueryresults.png)

Address.AddressType 및 Address.Location.StateProvinceName과 같은 별칭을 확인합니다. 중첩 구분 기호 '.'을 지정하면 가져오기 도구가 가져오는 동안 Address 및 Address.Location 하위 문서를 만듭니다. DocumentDB의 결과 문서 예는 다음과 같습니다.

*{
  "id": "956",
  "Name": "Finer Sales and Service",
  "Address": {
    "AddressType": "Main Office",
    "AddressLine1": "#500-75 O'Connor Street",
    "Location": {
      "City": "Ottawa",
      "StateProvinceName": "Ontario"
    },
    "PostalCode": "K4B 1S2",
    "CountryRegionName": "Canada"
  }
}*
 
SQL Server에서 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

	#Import records from SQL which match a query	
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionTier:S3

	#Import records from sql which match a query and create hierarchical relationships
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionTier:S3

##<a id="CSV"></a>CSV 파일 가져오기 - CSV에서 JSON으로 변환 ##

CSV 파일 원본 가져오기 옵션을 사용하면 하나 이상의 CSV 파일을 가져올 수 있습니다. 가져올 CSV 파일이 포함된 폴더를 추가하면, 하위 폴더에서 재귀적으로 파일을 검색하는 옵션도 있습니다.

![CSV 원본 옵션의 스크린샷 - CSV에서 JSON으로](media/documentdb-import-data/csvsource.png)

SQL 원본과 마찬가지로, 중첩 구분 기호 속성을 사용하여 가져오는 동안 계층 관계(하위 문서)를 만들 수 있습니다. 다음과 같은 CSV 머리글 행과 데이터 행을 고려하세요.

![CSV 샘플 레코드의 스크린샷 - CSV에서 JSON으로](./media/documentdb-import-data/csvsample.png)

DomainInfo.Domain_Name 및 RedirectInfo.Redirecting과 같은 별칭을 확인하세요. 중첩 구분 기호 '.'을 지정하면 가져오기 도구가 가져오는 동안 DomainInfo 및 RedirectInfo 하위 문서를 만듭니다. DocumentDB의 결과 문서 예는 다음과 같습니다.

*{
  "DomainInfo": {
    "Domain_Name": "ACUS.GOV",
    "Domain_Name_Address": "http://www.ACUS.GOV"
  },
  "Federal Agency": "Administrative Conference of the United States",
  "RedirectInfo": {
    "Redirecting": "0",
    "Redirect_Destination": ""
  },
  "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d"
}*

가져오기 도구는 CSV 파일의 따옴표가 없는 값에 대한 형식 정보를 유추하려고 합니다(따옴표로 묶인 값은 항상 문자열로 처리됨). 숫자, 날짜/시간, 부울 순서로 형식이 식별됩니다.

CSV 가져오기에 대해 다른 두 가지 사항을 기억해야 합니다.

1.	기본적으로 따옴표가 없는 값이 항상 잘리지만, 따옴표로 묶인 값은 그대로 유지됩니다. Trim 따옴표로 묶인된 값 확인란을 선택 또는 /s.TrimQuoted 명령줄 옵션으로 이 동작을 재정의할 수 있습니다.

2.	기본적으로는 따옴표가 없는 null은 null 값으로 처리됩니다. 따옴표가 없는 NULL을 문자열 확인란이나 /s.NoUnquotedNulls 명령줄 옵션으로 이 동작을 재정의할 수 있습니다(예: "null" 문자열로 따옴표가 없는 null 처리).


CSV 가져오기에 대한 명령줄 샘플은 다음과 같습니다.

	dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionTier:S3

##<a id="AzureTableSource"></a>Azure 테이블 저장소에서 가져오기 ##

Azure 테이블 저장소 원본 가져오기 옵션을 사용하면 개별 Azure 테이블 저장소 테이블에서 가져오고 필요에 따라 가져올 테이블 엔터티를 필터링할 수 있습니다.

![Azure 테이블 저장소 원본 옵션의 스크린샷](./media/documentdb-import-data/azuretablesource.png)

Azure 테이블 저장소 연결 문자열의 형식은 다음과 같습니다.

	DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 Azure 테이블 저장소 인스턴스를 액세스할 수 있는지 확인합니다.

데이터를 가져올 Azure 테이블의 이름을 입력합니다. 필요에 따라 [필터](https://msdn.microsoft.com/library/azure/ff683669.aspx)를 지정할 수 있습니다.

Azure 테이블 저장소 원본 가져오기 옵션에는 다음과 같은 추가 옵션이 있습니다.

1. 내부 필드 포함 
	2. 모두 - 모든 내부 필드 포함(PartitionKey, RowKey 및 Timestamp)
	3. 없음 - 모든 내부 필드 제외
	4. RowKey - RowKey 필드만 포함
3. 열 선택
	1. Azure 테이블 저장소 필터는 프로젝션을 지원하지 않습니다. 특정 Azure 테이블 엔터티 속성만 가져오려는 경우 열 선택 목록에 추가합니다. 다른 모든 엔터티 속성은 무시됩니다.

Azure 테이블 저장소에서 가져오는 명령줄 샘플은 다음과 같습니다.

	dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionTier:S3

##<a id="DynamoDBSource"></a>Amazon DynamoDB에서 가져오기 ##

Amazon DynamoDB 원본 가져오기 옵션을 사용하면 개별 Amazon DynamoDB 테이블에서 가져오고 필요에 따라 가져올 엔터티를 필터링할 수 있습니다. 여러 템플릿이 제공되므로 가져오기를 최대한 쉽게 설정할 수 있습니다.

![Amazon DynamoDB 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구](./media/documentdb-import-data/dynamodbsource1.png)

![Amazon DynamoDB 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구](./media/documentdb-import-data/dynamodbsource2.png)

Amazon DynamoDB 연결 문자열의 형식은 다음과 같습니다.

	ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 Amazon DynamoDB 인스턴스를 액세스할 수 있는지 확인합니다.

Amazon DynamoDB에서 가져오는 명령줄 샘플은 다음과 같습니다.

	dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:catalogCollection /t.CollectionTier:S3

##<a id="BlobImport"></a>Azure Blob 저장소에서 파일 가져오기##

JSON 파일, MongoDB 내보내기 파일 및 CSV 파일 원본 가져오기 옵션을 통해 Azure Blob 저장소에서 하나 이상의 파일을 가져올 수 있습니다. Blob 컨테이너 URL 및 계정 키를 지정한 후에 가져올 파일을 선택하는 정규식을 제공하기만 하면 됩니다.

![Blob 파일 원본 옵션의 스크린샷](./media/documentdb-import-data/blobsource.png)

Azure Blob 저장소에서 JSON 파일을 가져오려면 명령줄 예제는 다음과 같습니다.

	dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:doctest

##<a id="DocumentDBSource"></a>DocumentDB에서 가져오기 ##

DocumentDB 소스 가져오기 옵션을 사용하면 필요에 따라 쿼리를 사용하 여 문서를 필터링하고 하나 이상의 DocumentDB 컬렉션에서 데이터를 가져올 수 있습니다.

![DocumentDB 원본 옵션의 스크린샷](./media/documentdb-import-data/documentdbsource.png)

DocumentDB 연결 문자열의 형식은 다음과 같습니다.

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 DocumentDB 인스턴스를 액세스할 수 있는지 확인합니다.

단일 DocumentDB 컬렉션에서 가져오려면 데이터를 가져올 컬렉션의 이름을 입력합니다. 여러 DocumentDB 컬렉션에서 가져오려면 하나 이상의 컬렉션 이름과 일치 하는 정규식을 제공합니다(예: collection01 | collection02 | collection03). 필요에 따라 쿼리를 지정하거나 쿼리에 대한 파일을 제공하여 가져올 데이터를 필터링하고 형식을 지정할 수 있습니다.

> [AZURE.NOTE] 컬렉션 필드 이름이 정규식 문자를 포함하므로 이름에 정규식 문자를 포함한 단일 컬렉션에서 가져오는 경우 해당 문자는 이스케이프되어야 합니다.

DocumentDB 원본 가져오기 옵션에는 다음과 같은 고급 옵션이 있습니다.

1. 내부 필드 포함: 내보내기에 DocumentDB 문서 시스템 속성(예: _rid, _ts)을 포함할지 여부를 지정합니다.
2. 실패 시 다시 시도 횟수: 일시적 오류(예: 네트워크 연결 중단)의 경우 DocumentDB에 대한 연결을 다시 시도할 횟수를 지정합니다.
3. 다시 시도 간격: 일시적 오류(예: 네트워크 연결 중단)의 경우 DocumentDB에 대한 연결을 다시 시도하는 간격을 지정합니다.
4. 연결 모드: DocumentDB에 사용할 연결 모드를 지정합니다. 사용 가능한 선택 사항은 DirectTcp, DirectHttps 및 게이트웨이입니다. 직접 연결 모드는 더 빠르고, 게이트웨이 모드는 포트 443만 사용하므로 더 방화벽 친화적입니다.

![DocumentDB 원본 고급 옵션의 스크린샷](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP] 가져오기 도구는 기본적으로 DirectTcp 연결 모드로 설정되어 있습니다. 방화벽 문제가 발생하는 경우 포트 443만 요구하는 게이트웨이 연결 모드로 전환합니다.


DocumentDB에서 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

	#Migrate data from one DocumentDB collection to another DocumentDB collections
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionTier:S3

	#Migrate data from multiple DocumentDB collections to a single DocumentDB collection
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionTier:S3

	#Export a DocumentDB collection to a JSON file
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionTier:S3

##<a id="HBaseSource"></a>HBase에서 가져오기 ##

HBase 원본 가져오기 옵션을 사용하면 HBase 테이블에서 데이터를 가져오고 필요에 따라 데이터를 필터링할 수 있습니다. 여러 템플릿이 제공되므로 가져오기를 최대한 쉽게 설정할 수 있습니다.

![HBase 원본 옵션의 스크린샷](./media/documentdb-import-data/hbasesource1.png)

![HBase 원본 옵션의 스크린샷](./media/documentdb-import-data/hbasesource2.png)

HBase Stargate 연결 문자열의 형식은 다음과 같습니다.

	ServiceURL=<server-address>;Username=<username>;Password=<password>

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 HBase 인스턴스를 액세스할 수 있는지 확인합니다.

HBase에서 가져오는 명령줄 샘플은 다음과 같습니다.

	dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:hbaseimport

##<a id="DocumentDBBulkTarget"></a>DocumentDB로 가져오기(대량 가져오기) ##

DocumentDB 대량 가져오기를 사용하면 효율성을 위해 DocumentDB 저장 프로시저를 통해 사용 가능한 모든 원본 옵션에서 가져올 수 있습니다. 이 도구는 단일 DocumentDB 컬렉션 및 여러 DocumentDB 컬렉션 간에 데이터를 분할하는 분할된 데이터베이스 가져오기도 지원합니다. [여기](documentdb-partition-data.md)에서 DocumentDB에서 데이터 분할에 대해 자세히 알아봅니다. 이 도구는 대상 컬렉션에서 저장 프로시저를 만들고 실행한 다음 삭제합니다.

![DocumentDB 대량 옵션의 스크린샷](./media/documentdb-import-data/documentdbbulk.png)

DocumentDB 연결 문자열의 형식은 다음과 같습니다.

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 DocumentDB 인스턴스를 액세스할 수 있는지 확인합니다.

단일 컬렉션으로 가져오려면, 데이터를 가져올 컬렉션의 이름을 입력하고 추가 단추를 클릭합니다. 여러 컬렉션을 가져오려면 개별적으로 각 컬렉션 이름을 입력하거나 여러 컬렉션을 지정하려면 다음 구문을 사용합니다. *collection_prefix*[start index - end index]. 앞서 언급한 구문을 통해 여러 컬렉션을 지정할 때 다음 사항에 유의해야 합니다.

1. 정수 범위 이름 패턴만 지원됩니다. 예를 들어, 컬렉션 [0-3]을 지정하면 collection0, collection1, collection2, collection3 컬렉션을 생성합니다.
2. 축약된 구문을 사용할 수 있습니다. [3] 컬렉션은 1단계에서 설명한 동일한 집합을 내보냅니다.
3. 둘 이상의 대체를 제공할 수 있습니다. 예를 들어, 컬렉션 [0-1] [0-9]는 0이 이름 앞에 오는 20개의 컬렉션을 생성합니다(collection01,... 02... 03).

컬렉션 이름이 지정되면 원하는 컬렉션 가격 책정 계층을 선택합니다(S1, S2 또는 S3). 가져오기 성능을 최적화하려면 S3을 선택합니다. [여기](documentdb-performance-levels.md)에서 DocumentDB 성능 수준에 대해 자세히 알아봅니다.

> [AZURE.NOTE] 성능 계층 설정은 컬렉션 생성에만 적용됩니다. 지정된 컬렉션이 이미 있는 경우 해당 가격 책정 계층은 수정되지 않습니다.

여러 컬렉션을 가져올 때 가져오기 도구는 해시 기반 분할을 지원합니다. 이 시나리오에서는 파티션 키로 사용하려는 문서 속성을 지정합니다(파티션 키에 정보를 입력하지 않으면 문서는 대상 컬렉션에서 임의로 분할됨).

필요에 따라 가져오는 동안 DocumentDB 문서 ID 속성으로 사용할 가져오기 원본의 필드를 지정할 수 있습니다(문서에 이 속성이 없는 경우 가져오기 도구에서 GUID를 ID 속성 값으로 생성함).

가져오는 동안 사용할 수 있는 다양한 고급 옵션이 있습니다. 먼저, 도구에 기본 대량 가져오기 저장 프로시저(BulkInsert.js)가 포함되어 있지만 사용자 고유의 가져오기 저장 프로시저를 지정할 수 있습니다.

 ![DocumentDB 대량 삽입 저장 프로시저 옵션의 스크린샷](./media/documentdb-import-data/bulkinsertsp.png)

또한 SQL Server 또는 MongoDB 등에서 날짜 형식을 가져오는 경우 다음 3개의 가져오기 옵션 중에서 선택할 수 있습니다.

 ![DocumentDB 날짜 시간 가져오기 옵션의 스크린샷](./media/documentdb-import-data/datetimeoptions.png)

-	문자열: 문자열 값으로 유지
-	Epoch: Epoch 숫자 값으로 유지
-	둘 다: 문자열 및 Epoch 숫자 값으로 유지. 이 옵션은 하위 문서를 만듭니다(예: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }). 


DocumentDB 대량 가져오기에는 다음과 같은 추가 고급 옵션이 있습니다.

1. 배치 크기: 기본적으로 도구의 배치 크기는 50으로 설정되어 있습니다. 가져올 문서가 크면 배치 크기를 줄이는 것이 좋습니다. 반대로, 가져올 문서가 작으면 배치 크기를 늘리는 것이 좋습니다.
2. 최대 스크립트 크기(바이트): 기본적으로 도구의 최대 스크립트 크기는 960KB로 설정되어 있습니다.
3. 자동 ID 생성 사용 안 함: 가져올 모든 문서에 ID 필드가 포함되어 있는 경우 이 옵션을 선택하면 성능을 향상시킬 수 있습니다. 고유 ID 필드가 없는 문서는 가져오지 않습니다.
4. 실패 시 다시 시도 횟수: 일시적 오류(예: 네트워크 연결 중단)의 경우 DocumentDB에 대한 연결을 다시 시도할 횟수를 지정합니다.
5. 다시 시도 간격: 일시적 오류(예: 네트워크 연결 중단)의 경우 DocumentDB에 대한 연결을 다시 시도하는 간격을 지정합니다.
6. 연결 모드: DocumentDB에 사용할 연결 모드를 지정합니다. 사용 가능한 선택 사항은 DirectTcp, DirectHttps 및 게이트웨이입니다. 직접 연결 모드는 더 빠르고, 게이트웨이 모드는 포트 443만 사용하므로 더 방화벽 친화적입니다.

![DocumentDB 대량 가져오기 고급 옵션의 스크린샷](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP] 가져오기 도구는 기본적으로 DirectTcp 연결 모드로 설정되어 있습니다. 방화벽 문제가 발생하는 경우 포트 443만 요구하는 게이트웨이 연결 모드로 전환합니다.

##<a id="DocumentDBSeqTarget"></a>DocumentDB로 가져오기(순차 레코드 가져오기) ##

DocumentDB 순차 레코드 가져오기를 사용하면 레코드 단위로 사용 가능한 모든 원본 옵션에서 가져올 수 있습니다. 저장 프로시저의 할당량에 도달한 기존 컬렉션으로 가져오는 경우 이 옵션을 선택할 수 있습니다. 이 도구는 단일 DocumentDB 컬렉션 및 여러 DocumentDB 컬렉션 간에 데이터를 분할하는 분할된 데이터베이스 가져오기도 지원합니다. [여기](documentdb-partition-data.md)에서 DocumentDB에서 데이터 분할에 대해 자세히 알아봅니다.

![DocumentDB 순차 레코드 가져오기 옵션의 스크린샷](./media/documentdb-import-data/documentdbsequential.png)

DocumentDB 연결 문자열의 형식은 다음과 같습니다.

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Verify 명령을 사용하여 연결 문자열 필드에 지정된 DocumentDB 인스턴스를 액세스할 수 있는지 확인합니다.

단일 컬렉션으로 가져오려면, 데이터를 가져올 컬렉션의 이름을 입력하고 추가 단추를 클릭합니다. 여러 컬렉션을 가져오려면 개별적으로 각 컬렉션 이름을 입력하거나 여러 컬렉션을 지정하려면 다음 구문을 사용합니다. *collection_prefix*[start index - end index]. 앞서 언급한 구문을 통해 여러 컬렉션을 지정할 때 다음 사항에 유의해야 합니다.

1. 정수 범위 이름 패턴만 지원됩니다. 예를 들어, 컬렉션 [0-3]을 지정하면 collection0, collection1, collection2, collection3 컬렉션을 생성합니다.
2. 축약된 구문을 사용할 수 있습니다. [3] 컬렉션은 1단계에서 설명한 동일한 집합을 내보냅니다.
3. 둘 이상의 대체를 제공할 수 있습니다. 예를 들어, 컬렉션 [0-1] [0-9]는 0이 이름 앞에 오는 20개의 컬렉션을 생성합니다(collection01,... 02... 03).

컬렉션 이름이 지정되면 원하는 컬렉션 가격 책정 계층을 선택합니다(S1, S2 또는 S3). 가져오기 성능을 최적화하려면 S3을 선택합니다. [여기](documentdb-performance-levels.md)에서 DocumentDB 성능 수준에 대해 자세히 알아봅니다.

> [AZURE.NOTE] 성능 계층 설정은 컬렉션 생성에만 적용됩니다. 지정된 컬렉션이 이미 있는 경우 해당 가격 책정 계층은 수정되지 않습니다.

여러 컬렉션을 가져올 때 가져오기 도구는 해시 기반 분할을 지원합니다. 이 시나리오에서는 파티션 키로 사용하려는 문서 속성을 지정합니다(파티션 키에 정보를 입력하지 않으면 문서는 대상 컬렉션에서 임의로 분할됨).

필요에 따라 가져오는 동안 DocumentDB 문서 ID 속성으로 사용할 가져오기 원본의 필드를 지정할 수 있습니다(문서에 이 속성이 없는 경우 가져오기 도구에서 GUID를 ID 속성 값으로 생성함).

가져오는 동안 사용할 수 있는 다양한 고급 옵션이 있습니다. 먼저, SQL Server 또는 MongoDB 등에서 날짜 형식을 가져오는 경우 다음 3개의 가져오기 옵션 중에서 선택할 수 있습니다.

 ![DocumentDB 날짜 시간 가져오기 옵션의 스크린샷](./media/documentdb-import-data/datetimeoptions.png)

-	문자열: 문자열 값으로 유지
-	Epoch: Epoch 숫자 값으로 유지
-	둘 다: 문자열 및 Epoch 숫자 값으로 유지. 이 옵션은 하위 문서를 만듭니다(예: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }). 

DocumentDB - 순차 레코드 가져오기에는 다음과 같은 추가 고급 옵션이 있습니다.

1. 병렬 요청 수: 기본적으로 도구의 병렬 요청 수는 2개로 설정되어 있습니다. 가져올 문서가 작으면 병렬 요청 수를 늘리는 것이 좋습니다. 이 개수를 너무 많이 늘리면 가져오기 시 제한이 발생할 수 있습니다.
2. 자동 ID 생성 사용 안 함: 가져올 모든 문서에 ID 필드가 포함되어 있는 경우 이 옵션을 선택하면 성능을 향상시킬 수 있습니다. 고유 ID 필드가 없는 문서는 가져오지 않습니다.
3. 실패 시 다시 시도 횟수: 일시적 오류(예: 네트워크 연결 중단)의 경우 DocumentDB에 대한 연결을 다시 시도할 횟수를 지정합니다.
4. 다시 시도 간격: 일시적 오류(예: 네트워크 연결 중단)의 경우 DocumentDB에 대한 연결을 다시 시도하는 간격을 지정합니다.
5. 연결 모드: DocumentDB에 사용할 연결 모드를 지정합니다. 사용 가능한 선택 사항은 DirectTcp, DirectHttps 및 게이트웨이입니다. 직접 연결 모드는 더 빠르고, 게이트웨이 모드는 포트 443만 사용하므로 더 방화벽 친화적입니다.

![DocumentDB 순차 레코드 가져오기 고급 옵션의 스크린샷](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP] 가져오기 도구는 기본적으로 DirectTcp 연결 모드로 설정되어 있습니다. 방화벽 문제가 발생하는 경우 포트 443만 요구하는 게이트웨이 연결 모드로 전환합니다.

##<a id="IndexingPolicy"></a>DocumentDB 컬렉션을 만들 때 인덱싱 정책 지정 ##

가져오는 동안 컬렉션을 만들 수 있도록 마이그레이션 도구를 허용하는 경우 컬렉션의 인덱싱 정책을 지정할 수 있습니다. DocumentDB 대량 가져오기의 고급 옵션 섹션 및 DocumentDB 순차 레코드 옵션에서 인덱싱 정책 섹션으로 이동합니다.

![DocumentDB 인덱싱 정책 고급 옵션의 스크린샷](./media/documentdb-import-data/indexingpolicy1.png)

인덱싱 정책 고급 옵션을 사용하여 인덱싱 정책 파일을 선택하고 인덱싱 정책을 수동으로 입력하거나 일련의 기본 템플릿에서 선택할 수 있습니다(인덱싱 정책 텍스트 상자를 마우스 오른쪽 단추로 클릭).

도구가 제공하는 정책 템플릿은 다음과 같습니다.

- 기본값 이 정책은 문자열에 대해 같음 쿼리를 수행하고 숫자에 대해 ORDER BY, 범위 및 같음 쿼리를 사용할 때 가장 좋습니다. 이 정책에는 범위보다 더 낮은 인덱스 저장소 오버헤드가 있습니다.
- 해시입니다. 이 정책은 숫자 및 문자열 모두에 대해 같음 쿼리를 수행할 때 가장 좋습니다. 이 정책에는 가장 낮은 인덱스 저장소 오버헤드가 있습니다.
- 범위입니다. 이 정책은 숫자와 문자열 모두에 ORDER BY, 범위 및 같음 쿼리를 사용할 때 가장 좋습니다. 이 정책에는 기본값 또는 해시보다 더 높은 인덱스 저장소 오버헤드가 있습니다.


![DocumentDB 인덱싱 정책 고급 옵션의 스크린샷](./media/documentdb-import-data/indexingpolicy2.png)

> [AZURE.NOTE] 인덱싱 정책을 지정하지 않으면 기본 정책이 적용됩니다. DocumentDB 인덱싱 정책에 대한 자세한 정보는 [여기서](documentdb-indexing-policies.md) 읽을 수 있습니다.


## JSON 파일로 내보내기

DocumentDB JSON 내보내기를 사용하면 사용 가능한 모든 원본 옵션을 JSON 문서 배열이 포함된 JSON 파일로 내보낼 수 있습니다. 도구에서 자동으로 내보내기를 처리하거나, 결과 마이그레이션 명령을 보고 직접 명령을 실행할 수 있습니다. 결과 JSON 파일은 로컬로 또는 Azure Blob 저장소에 저장될 수도 있습니다.

![DocumentDB JSON 로컬 파일 내보내기 옵션의 스크린샷](./media/documentdb-import-data/jsontarget.png)

![DocumentDB JSON Azure Blob 저장소 내보내기 옵션의 스크린샷](./media/documentdb-import-data/jsontarget2.png)

필요에 따라 결과 JSON을 꾸밀 수 있으며, 이 경우 콘텐츠의 가독성은 향상되지만 결과 문서의 크기가 증가합니다.

	Standard JSON export
	[{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
	
	Prettified JSON export
	[
 	{
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
	}]
	
## 고급 구성

고급 구성 화면에서 원하는 모든 오류가 기록된 로그 파일의 위치를 지정합니다. 이 페이지에는 다음 규칙이 적용됩니다.

1.	파일 이름을 제공하지 않으면, 모든 오류는 결과 페이지에 반환됩니다.
2.	파일 이름에서 디렉토리가 없는 경우 파일은 현재 환경 디렉토리에 만들어지거나 덮어씁니다.
3.	기존 파일을 선택하는 경우 파일을 덮어쓰며 추가 옵션은 없습니다.

	![고급 구성 화면의 스크린샷](./media/documentdb-import-data/AdvancedConfiguration.png)

## 가져오기 설정 확인 및 명령줄 보기

1. 원본 정보, 대상 정보 및 고급 구성을 지정한 후, 마이그레이션 요약을 검토하고, 필요에 따라 결과 마이그레이션 명령을 확인/복사합니다(명령 복사는 가져오기 작업을 자동화하는 데 유용함).

	![요약 화면의 스크린샷](./media/documentdb-import-data/summary.png)

	![요약 화면의 스크린샷](./media/documentdb-import-data/summarycommand.png)

2. 원본 및 대상 옵션이 적절하면 **가져오기**를 클릭합니다. 가져오기가 처리되는 동안 경과 시간, 전송 횟수 및 실패 정보가 업데이트됩니다(고급 구성에서 파일 이름을 지정하지 않은 경우). 완료되면 결과를 내보낼 수 있습니다(예: 가져오기 오류를 처리하기 위해).

	![DocumentDB JSON 내보내기 옵션의 스크린샷](./media/documentdb-import-data/viewresults.png)

3. 기존 설정(예: 연결 문자열 정보, 원본 및 대상 선택 등)을 유지하거나 모든 값을 다시 설정하여 새 가져오기를 시작할 수도 있습니다.

	![DocumentDB JSON 내보내기 옵션의 스크린샷](./media/documentdb-import-data/newimport.png)

## 다음 단계

- DocumentDB에 대해 자세히 알아보려면 [여기](http://azure.com/docdb)를 클릭하세요.


 

<!---HONumber=AcomDC_0128_2016-->