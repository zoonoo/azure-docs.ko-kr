<properties 
	pageTitle="NodeJS에서 Azure 검색 시작" 
	description="NodeJS를 프로그래밍 언어로 사용하여 사용자 지정 Azure 검색 응용 프로그램을 빌드하는 과정을 안내합니다." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/25/2015" 
	ms.author="heidist"/>

#NodeJS에서 Azure 검색 시작#

검색 환경에 Azure 검색을 사용하는 사용자 지정 NodeJS 검색 응용 프로그램을 빌드하는 방법에 대해 알아봅니다. 이 자습서에서는 [Azure 검색 서비스 REST API](https://msdn.microsoft.com/library/dn798935.aspx)를 사용하여 이 연습에서 사용되는 개체 및 작업을 생성합니다.

이 코드를 개발하고 테스트하는 데에는 [NodeJS](https://nodejs.org) 및 NPM, [Sublime Text 3](http://www.sublimetext.com/3) 및 Windows 8.1의 Windows PowerShell이 사용되었습니다.

이 샘플을 실행하려면 Azure 검색 서비스가 있어야 합니다. 이 서비스는 [Azure 포털](https://portal.azure.com)에서 등록할 수 있습니다.

> [AZURE.TIP][AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198)에서 이 자습서의 원본 코드를 다운로드하세요.

##데이터 정보##

이 샘플 응용 프로그램에서는 데이터 집합 크기를 줄이기 위해 Rhode Island 주에 대해 필터링된 [USGS(United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm)의 데이터를 사용합니다. 이 데이터를 사용하여 병원 및 학교와 같은 랜드마크 빌딩뿐만 아니라 강, 호수, 산 등의 지질학적 특징을 반환하는 검색 응용 프로그램을 빌드합니다.

이 응용 프로그램에서 **DataIndexer** 프로그램은 [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) 구문을 사용하여 인덱스를 빌드 및 로드하며, 이를 통해 Azure SQL 데이터베이스에서 필터링된 USGS 데이터 집합을 검색합니다. 온라인 데이터 원본에 대한 자격 증명 및 연결 정보는 프로그램 코드에서 제공됩니다. 추가 구성은 필요하지 않습니다.

> [AZURE.NOTE]무료 가격 책정 계층의 문서 제한(10,000개) 미만으로 유지하기 위해 이 데이터 집합에 필터를 적용했습니다. 표준 계층을 사용하는 경우에는 이 제한이 적용되지 않습니다. 각 가격 책정 계층의 용량에 대한 자세한 내용은 [제한 및 제약 조건](https://msdn.microsoft.com/library/azure/dn798934.aspx)을 참조하세요.

##서비스 만들기##

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 표시줄에서 **새로 만들기** | **데이터 + 저장소** | **검색**을 클릭합니다.
 
     ![][1]

3. 서비스 이름, 가격 책정 계층, 리소스 그룹, 구독 및 위치를 구성합니다. 이러한 설정은 필수 사항이며, 서비스가 프로비전된 후에는 변경할 수 없습니다.

     ![][2]

	- **서비스 이름**은 고유하고 소문자여야 하며, 공백 없이 15자 미만이어야 합니다. 이 이름은 Azure 검색 서비스 끝점의 일부가 됩니다. 이름 지정 규칙에 대한 자세한 내용은 [이름 지정 규칙](https://msdn.microsoft.com/library/azure/dn857353.aspx)을 참조하세요. 
	
	- **가격 책정 계층**은 용량 및 대금 청구를 결정합니다. 두 계층 모두 동일한 기능을 제공하지만 리소스 수준이 다릅니다.
	
		- **무료**는 다른 구독자와 공유되는 클러스터에서 실행되며, 자습서를 체험하고 개념 증명 코드를 작성하기에 충분한 용량을 제공하지만 프로덕션 응용 프로그램에 사용할 수 없습니다. 일반적으로 무료 서비스를 배포하는 데 몇 분 정도 걸립니다.
		- **표준**은 전용 리소스에서 실행되고 확장성이 높습니다. 처음에는 하나의 복제본과 하나의 파티션으로 프로비전되지만 서비스를 만든 후 용량을 조정할 수 있습니다. 표준 서비스를 배포하는 데 더 오래 걸립니다(일반적으로 약 15분).
	
	- **리소스 그룹**은 일반적인 목적에 사용되는 서비스 및 리소스의 컨테이너입니다. 예를 들어 Azure 검색, Azure 웹 사이트, Azure BLOB 저장소를 기반으로 하여 사용자 지정 검색 응용 프로그램을 작성하는 경우 포털 관리 페이지에서 해당 서비스를 결합하는 리소스 그룹을 만들 수 있습니다.
	
	- **구독**에서는 둘 이상의 구독이 있는 경우 여러 구독 중에서 선택할 수 있습니다.
	
	- **위치**는 데이터 센터 지역입니다. 현재 모든 리소스는 동일한 데이터 센터에서 실행되어야 합니다. 여러 데이터 센터에 리소스를 분산하는 것은 지원되지 않습니다.

4. **만들기**를 클릭하여 서비스를 프로비전합니다.

표시줄의 알림을 확인합니다. 서비스를 사용할 준비가 되면 알림이 표시됩니다.

<a id="sub-2"></a>
##Azure 검색 서비스의 서비스 이름 및 api-key 찾기

서비스를 만든 후 포털로 돌아가 URL 또는 `api-key`를 가져올 수 있습니다. 검색 서비스에 연결하려면 URL과 호출을 인증할 `api-key`가 둘 다 있어야 합니다.

1. 표시줄에서 **홈**을 클릭한 다음 검색 서비스를 클릭하여 서비스 대시보드를 엽니다. 

2. 서비스 대시보드에 기본 정보에 대한 타일 및 관리 키에 액세스할 수 있는 키 아이콘이 표시됩니다.

  	![][3]

3. 서비스 URL, 관리 키 및 쿼리 키를 복사합니다. 세 항목 모두 나중에 config.js 파일에 추가할 때 필요합니다.

##샘플 파일 다운로드

다음 방법 중 하나를 사용하여 샘플을 다운로드합니다.

1. [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198)로 이동합니다.
2. **Download ZIP**을 클릭하고 .zip 파일을 저장한 다음 포함된 모든 파일을 추출합니다.

이후의 모든 파일 수정 및 실행 문은 이 폴더의 파일에 대해 수행됩니다.

또는 경로 문에 GIT가 있는 경우 PowerShell 창을 열고 `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git`를 입력합니다.

##검색 서비스 URL 및 api-key로 config.js를 업데이트합니다.

앞에서 복사한 URL 및 api-key를 사용하여 구성 파일에서 URL, admin-key 및 query-key를 지정합니다.

관리 키는 인덱스 만들기 또는 삭제 및 문서 로드를 포함하여 서비스 작업에 대한 모든 권한을 부여합니다. 반면, 쿼리 키는 읽기 전용 작업용이며, 일반적으로 Azure 검색에 연결하는 클라이언트 응용 프로그램에서 사용됩니다.

이 샘플에는 클라이언트 응용 프로그램에서 쿼리 키를 사용하는 모범 사례를 보강하는 데 도움이 되는 쿼리 키가 포함되어 있습니다.

다음 스크린샷에서는 텍스트 편집기에서 열려 있는 **config.js**를 보여 줍니다. 검색 서비스에 유효한 값으로 파일을 업데이트할 위치를 알 수 있도록 관련 항목이 경계선으로 구분되어 있습니다.

![][5]


##샘플에 대한 런타임 환경 호스트

이 샘플에는 npm을 사용하여 전역적으로 설치할 수 있는 HTTP 서버가 필요합니다.

PowerShell 창에서 다음 명령을 실행합니다.

1. **package.json** 파일이 들어 있는 폴더로 이동합니다.
2. `npm install`을 입력합니다.
2. `npm install -g http-server`를 입력합니다.

##인덱스 빌드 및 응용 프로그램 실행

1. `npm run indexDocuments`를 입력합니다. 
2. `npm run build`를 입력합니다.
3. `npm run start_server`를 입력합니다.
4. 브라우저에서 `http://localhost:8080/index.html`로 이동합니다.

##USGS 데이터 검색##

USGS 데이터 집합에는 Rhode Island 주와 관련된 레코드가 포함되어 있습니다. 빈 검색 상자에서 **검색**을 클릭하면 기본적으로 상위 50개 항목을 가져옵니다.

검색 용어를 입력하면 검색 엔진이 실행됩니다. 지역 이름을 입력해 봅니다. “Roger Williams”는 Rhode Island의 최초 주지사였습니다. 유명한 공원, 빌딩 및 학교가 그의 이름을 따라 이름을 지었습니다.

![][9]

다음과 같은 용어를 입력해 볼 수도 있습니다.

- Pawtucket
- Pembroke
- goose +cape


##다음 단계##

이것은 NodeJS 및 USGS 데이터 집합을 기반으로 하는 첫 번째 Azure 검색 자습서입니다. 앞으로 이 자습서를 확장하여 사용자 지정 솔루션에서 사용할 수 있는 추가 검색 기능을 보여 드릴 예정입니다.

Azure 검색에 대한 약간의 배경 지식이 이미 있는 경우 이 샘플을 기반으로 suggesters(사전 입력 또는 자동 완성 쿼리), 필터 및 패싯 탐색을 시작할 수 있습니다. 또한 사용자가 결과 페이지를 차례로 탐색할 수 있도록 개수를 추가하고 문서를 일괄 처리하여 검색 결과 페이지를 개선할 수 있습니다.

Azure 검색을 처음 사용하세요? 다른 자습서를 통해 만들 수 있는 항목에 대한 이해를 높여 보세요. 더 많은 리소스를 보려면 [설명서 페이지](http://azure.microsoft.com/documentation/services/search/)를 방문하세요. [비디오 및 자습서](https://msdn.microsoft.com/library/azure/dn798933.aspx)의 링크를 통해 추가 정보를 확인할 수도 있습니다.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
<!--HONumber=54--> 