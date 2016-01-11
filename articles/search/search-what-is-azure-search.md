<properties
	pageTitle="Azure 검색이란 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색은 완벽하게 관리되는 호스트된 클라우드 검색 서비스입니다. 이 기능 개요에 대해 자세히 알아보세요."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="12/18/2015"
	ms.author="ashmaka"/>

# Azure 검색이란?
Azure 검색은 서버 및 인프라 관리를 Microsoft에 위임하고 개발자와 시스템 통합 업체는 즉시 사용 가능한 서비스에 데이터만 채우면 바로 검색이 가능한 클라우드 SaaS(Search-as-a-Service) 솔루션입니다. Azure 검색을 사용하면 검색 인프라를 관리할 필요가 없고 검색 전문가가 아니라도 간단한 REST API 또는 .NET SDK를 사용하여 응용 프로그램에 강력한 검색 환경을 쉽게 추가할 수 있습니다.

## 사용자에게 강력한 검색 환경 제공

[OData 구문](https://msdn.microsoft.com/library/azure/dn798921.aspx) 및 [단순 쿼리 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx)을 사용하여 논리 연산자, 비교 식 등을 사용하는 **강력한 쿼리**를 작성할 수 있습니다. 뿐만 아니라 [Lucene 쿼리 구문](https://msdn.microsoft.com/library/azure/mt589323.aspx)(현재는 미리 보기)은 유사 항목 검색, 근접 검색, 용어 승격 및 정규식을 사용할 수 있습니다. 또한 Azure 검색은 응용 프로그램에서 음성 일치를 사용하는 복잡한 검색 쿼리와 정규식을 처리할 수 있도록 사용자 지정 어휘 분석기를 지원합니다.

**언어 지원**은 [56개 언어](https://msdn.microsoft.com/library/azure/dn879793.aspx)로 제공됩니다. Lucene 분석기와 Microsoft 분석기(Office 및 Bing에서 자연 언어를 처리해 온 연수만큼 개선됨)를 모두 활용하는 Azure 검색은 단어 분리, 텍스트 정규화, 어휘 정리 등을 사용하여 텍스트를 분석할 수 있습니다. 따라서 응용 프로그램의 검색 상자가 맞춤법 오류, 동사 시제, 불규칙 복수 명사(예: 'mouse'와 'mice') 등을 지능적으로 처리할 수 있습니다.

**제안 검색**은 자동 완성 검색 표시줄과 자동 완성 쿼리에 사용할 수 있습니다. 사용자가 검색어의 일부를 입력하면 [인덱스의 실제 문서가 제안됩니다](https://msdn.microsoft.com/library/azure/dn798936.aspx).

**적중 항목 강조 표시**는 쿼리와 일치하는 항목이 포함된 각 결과의 텍스트 조각을 사용자가 볼 수 있게 [허용](https://msdn.microsoft.com/library/azure/dn798927.aspx)합니다. 강조 표시된 조각을 반환할 필드를 선택할 수 있습니다.

**패싯 탐색**은 Azure 검색을 사용하는 검색 결과 페이지에 간단하게 추가됩니다. [쿼리 매개 변수 하나만 사용하면](https://msdn.microsoft.com/library/azure/dn798927.aspx) Azure 검색이 앱 UI에 패싯 검색 환경을 구축하는 데 필요한 모든 정보를 반환하므로 사용자가 검색 결과를 드릴다운 및 필터링할 수 있습니다(예: 가격 범위 또는 브랜드로 카탈로그 항목 필터링).

**지리 공간** [지원](https://msdn.microsoft.com/library/azure/dn798921.aspx)을 사용하여 지리적 위치를 지능적으로 처리, 필터링 및 표시할 수 있습니다. Azure 검색을 통해 사용자는 지정된 위치와 검색 결과의 근접도에 따라 또는 특정 지리적 영역에 따라 데이터를 검색할 수 있습니다.

**필터**를 사용하여 간단하게 패싯 탐색(예: 범주 또는 가격으로 필터링)을 통합하고, 쿼리 작성 능력을 향상하고, 사용자 또는 개발자가 지정한 기준에 따라 필터링할 수 있습니다.

## 사용하기 편한 서비스를 통해 개발자의 역량 강화

**고가용성**은 매우 안정적인 검색 서비스 환경을 보장합니다. 규모를 적절하게 조정하면 [Azure 검색은 99.9%의 SLA를 제공](https://azure.microsoft.com/support/legal/sla/search/v1_0/)합니다.

**완벽하게 관리되는** 종합 솔루션인 Azure 검색은 인프라 관리가 전혀 필요 없습니다. 더 많은 문서 저장소, 더 많은 쿼리 부하 또는 둘 모두를 처리할 수 있도록 두 가지 크기를 조정하여 요구 사항에 따라 간편하게 서비스를 맞춤 구성할 수 있습니다.

**인덱서**를 사용하는 [데이터 통합](https://msdn.microsoft.com/library/azure/dn946891.aspx) 덕분에 Azure 검색은 자동으로 Azure SQL 데이터베이스 또는 Azure DocumentDB를 크롤링하여 검색 인덱스의 콘텐츠를 주 데이터 저장소와 동기화할 수 있습니다.

**문서 해독**을 사용하여 Microsoft Office와 PDF 및 HTML 문서를 포함한 [주요 파일 형식을 읽고 인덱싱할 수 있습니다](search-howto-indexing-azure-blob-storage.md).

**검색 트래픽 분석**을 [간편하게 수집 및 분석](search-traffic-analytics.md)하여 사용자가 검색 상자에 입력하는 내용으로부터 정보를 얻을 수 있습니다.

**간단한 점수 매기기**는 Azure 검색의 핵심적인 장점입니다. [점수 매기기 프로필](https://msdn.microsoft.com/library/azure/dn798928.aspx)은 조직에서 문서 자체의 값에 대한 함수로써 관련성을 모델링할 수 있도록 허용하는 데 사용됩니다. 예를 들어 최신 제품 또는 할인 제품을 검색 결과의 더 높은 부분에 나타내려고 할 수 있습니다. 또한 따로 추적하고 저장한 고객 검색 기본 설정에 따라 개인화된 점수에 태그를 사용하여 점수 매기기 프로필을 만들 수도 있습니다.

**정렬**은 인덱스 스키마를 통해 여러 필드에 제공된 후 쿼리 시 단일 검색 매개 변수를 통해 전환됩니다.

검색 결과에 대해 Azure 검색에서 제공하는 정교하게 조정된 컨트롤을 사용하면 검색 결과를 간편하게 **페이징** 및 제한할 수 있습니다.

**검색 탐색기**를 사용하여 해당 계정의 Azure 포털에서 바로 모든 인덱스에 대해 쿼리를 실행할 수 있으므로 간편하게 쿼리를 테스트하고 점수 매기기 프로필을 조정할 수 있습니다.

## 작동 방법

### 1\. 서비스 프로비전
[Azure 포털](https://portal.azure.com/) 또는 [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn832684.aspx)를 사용하여 Azure 검색 서비스를 스핀업할 수 있습니다.

서비스를 구성하는 방법에 따라 다른 Azure 검색 구독자와 공유되는 무료 계층 또는 서비스에만 사용하는 전용 리소스를 제공하는 표준 [가격 책정 계층](https://azure.microsoft.com/pricing/details/search/)을 사용합니다. 서비스를 프로비전 할 때 서비스를 호스트하는 데이터 센터의 위치도 선택합니다.

Azure 검색을 표준 계층으로 사용할 경우 1) 더 많은 쿼리 부하를 처리할 수 있도록 복제본을 추가하여 용량을 확장하고 2) 더 많은 문서를 저장할 수 있도록 파티션을 추가하여 저장소를 추가하는 두 가지 방법으로 서비스를 확장할 수 있습니다. 문서 저장소 및 쿼리 처리량을 따로 처리하면 요구 사항에 맞게 검색 서비스를 사용자 지정할 수 있습니다.

### 2\. 인덱스 만들기
Azure 검색 서비스에 콘텐츠를 업로드하려면 먼저 Azure 검색 인덱스를 정의해야 합니다. 인덱스는 데이터가 보관된 데이터베이스 테이블과 비슷하며 검색 쿼리를 수용할 수 있습니다. 데이터베이스의 필드와 마찬가지로, 검색하려는 문서의 구조에 매핑할 인덱스 스키마를 정의합니다.

이러한 인덱스의 스키마는 Azure 포털에서 만들거나 [.NET SDK](search-howto-dotnet-sdk.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 사용하여 프로그래밍 방식으로 만들 수 있습니다. 인덱스를 정의한 다음 데이터를 Azure 검색 서비스에 업로드할 수 있으며 이후에는 이 서비스에서 데이터가 인덱싱됩니다.

### 3\. 데이터 인덱싱
인덱스의 필드 및 특성이 정의되면 인덱스에 콘텐츠를 업로드할 준비가 완료된 것입니다. 밀어넣기 또는 끌어오기 모델을 사용하여 데이터를 인덱스에 업로드할 수 있습니다.

끌어오기 모델은 요청 시 또는 예약 업데이트에 맞게 구성할 수 있는 인덱서를 통해 제공되며([인덱서 작업(Azure 검색 서비스 REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx) 참조), Azure DocumentDB, Azure SQL 데이터베이스, Azure Blob 저장소 또는 Azure VM에서 호스트되는 SQL Server에서 데이터 및 데이터 변경 내용을 쉽게 수집할 수 있습니다.

밀어넣기 모델은 SDK 또는 업데이트된 문서를 인덱스에 보내기 위해 사용되는 REST API를 통해 제공됩니다. JSON 형식을 사용하여 거의 모든 데이터 집합에서 데이터를 밀어넣을 수 있습니다. 데이터 로드에 대한 지침은 [문서 추가, 업데이트 또는 삭제](https://msdn.microsoft.com/library/azure/dn798930.aspx) 또는 [.NET SDK를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

### 4\. 검색
Azure 검색 인덱스를 채웠으면 REST API 또는 .NET SDK와 함께 간단한 HTTP 요청을 사용하여 서비스 끝점에 [검색 쿼리를 실행](https://msdn.microsoft.com/library/azure/dn798927.aspx)할 수 있습니다.

## 지금 평가판 사용(무료!)
지금 당장 Azure 검색을 사용해 볼 수 있습니다! Azure 계정이 있는 분은 [무료 계층에서 서비스를 프로비전](search-create-service-portal.md)할 수 있습니다.

Azure 계정이 없는 분은 등록 없이 무료 60분 세션을 사용할 수 있습니다. [Azure 앱 서비스 시도](http://go.microsoft.com/fwlink/p/?LinkId=618214)로 이동하여 "웹앱"을 선택하세요. 그런 다음 "ASP.NET + Azure 검색" 템플릿을 선택하여 시작하세요.

<!---HONumber=AcomDC_1223_2015-->