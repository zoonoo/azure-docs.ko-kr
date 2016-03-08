<properties
	pageTitle="CDN을 사용하는 방법 | Microsoft Azure"
	description="Azure CDN(콘텐츠 배달 네트워크)을 사용하여 Blob 및 정적 콘텐츠를 캐시해 고대역폭 콘텐츠를 배달하는 방법을 알아봅니다."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/25/2016"
	ms.author="casoper"/>


# Azure에 CDN 사용

Azure CDN(콘텐츠 배달 네트워크)은 Azure에서 HTTP 응용 프로그램의 크기를 조정하기 위한 기본 구성 요소입니다. CDN은 콘텐츠를 캐시하고 최종 사용자에게 가까운 곳으로 배달하여 Azure 고객에게 글로벌 솔루션을 제공합니다. 그 결과, 매번 원본을 적중하는 대신 사용자 요청이 지능적으로 성능이 가장 뛰어난 CDN 에지 POP로 라우팅됩니다. 이 경우 성능 및 사용자 환경이 훨씬 개선됩니다. CDN 노드 위치의 현재 목록은 [Azure CDN(콘텐츠 배달 네트워크) POP 위치](cdn-pop-locations.md)를 참조하세요.

CDN을 사용하여 Azure 데이터를 캐시할 경우의 혜택은 다음과 같습니다.

- 콘텐츠 소스에서 멀리 떨어져 있고 많은 HTTP 요청이 콘텐츠를 로드하기 위해 필요한 응용 프로그램을 사용 중인 최종 사용자의 성능 및 사용자 환경 향상
- 제품 런칭 등의 이벤트를 시작할 때 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 분산
- 사용자 요청을 분산하고 글로벌 에지 POP에서 콘텐츠를 제공하여 원본으로 전송되는 트래픽 양이 감소합니다.

>[AZURE.TIP] Azure CDN는 다양한 원본에서 콘텐츠를 분산할 수 있습니다. Azure 내에서 통합된 원본은 앱 서비스, 클라우드 서비스, blob 저장소 및 미디어 서비스를 포함합니다. 또한 공개적으로 액세스할 수 있는 웹 주소를 사용하여 사용자 지정 원본을 정의할 수 있습니다.

##CDN을 사용하는 방법

1. 원본이 가리키는 끝점을 통한 CDN 프로필 만들기

	CDN 프로필은 CDN 끝점의 컬렉션입니다. 각 프로필에는 CDN 끝점이 하나 이상 있습니다. CDN 프로필을 만든 후에 선택한 원본을 사용하여 새 CDN 끝점을 만들 수 있습니다.

	>[AZURE.NOTE] 기본적으로 단일 Azure 구독은 CDN 프로필이 네 개로 제한됩니다. 각 CDN 프로필은 CDN 끝점이 열 개로 제한됩니다.
	>
	> CDN 가격 책정은 CDN 프로필 수준에서 적용됩니다. 다양한 표준 및 프리미엄 CDN 기능을 사용하려는 경우 여러 CDN 프로필이 필요합니다.

	CDN 프로필 및 끝점을 만드는 방법에 대한 자세한 자습서는 [Azure에 콘텐츠 배달 네트워크를 사용하는 방법](cdn-create-new-endpoint.md)을 참조하세요.

2. CDN 구성 설정

	CDN 끝점에 대해 [캐싱 정책](cdn-caching-policy.md), [쿼리 문자열 캐싱](cdn-query-string.md), [규칙 엔진](cdn-rules-engine.md) 등의 다양한 기능을 사용하도록 설정할 수 있습니다. 자세한 내용은 왼쪽에서 **관리** 메뉴를 참조하세요.

3. CDN 콘텐츠 액세스

	CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 예를 들어 캐시된 Blob의 주소는 다음과 유사합니다.`http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Azure 저장소의 콘텐츠 캐시

Azure 저장소 계정에서 CDN을 사용하도록 설정하면 공용 컨테이너에 있고 익명 액세스에 사용할 수 있는 모든 Blob이 CDN을 통해 캐시됩니다. 공개적으로 사용할 수 있는 Blob만 Azure CDN을 사용하여 캐시할 수 있습니다. Blob을 익명 액세스에 공개적으로 사용할 수 있게 하려면 해당 컨테이너를 공용으로 표시해야 합니다. 이렇게 하면 해당 컨테이너 내의 모든 Blob을 익명 읽기 권한으로 사용할 수 있습니다. 컨테이너 데이터도 공용으로 만들거나 액세스 권한을 컨테이너 내의 Blob으로만 제한할 수 있습니다. 컨테이너 및 Blob에 대한 액세스 제어 관리와 관련된 자세한 내용은 [Azure 저장소 리소스에 대한 액세스 관리](../storage/storage-manage-access-to-resources.md)를 참조하세요.

최상의 성능을 얻으려면 크기가 10GB 미만인 Blob을 배달할 때는 CDN 에지 캐싱을 사용하세요.

저장소 계정에 대해 CDN 액세스를 사용하도록 설정하면 관리 포털에서 CDN 도메인 이름을 다음 형식으로 제공합니다. `http://<identifier>.azureedge.net/`. 이 도메인 이름을 사용하여 공용 컨테이너의 Blob에 액세스할 수 있습니다. 예를 들어 공용 컨테이너가 지정되면 사용자는 다음 두 개의 URL 중 하나를 사용하여 해당 컨테이너의 Blob에 액세스할 수 있습니다.

- **Azure Blob 서비스 URL**: `http://<account>.blob.core.windows.net/<container>/`
- **Azure CDN URL**: `http://<identifier>.azureedge.net/<container>/`

> [AZURE.TIP] 위의 예에서 *전체* 저장소 계정의 CDN 끝점을 가리켰습니다. 마찬가지로 CDN URL은 URL의 컨테이너를 포함해야 합니다. **원본 경로** 매개 변수를 사용하여 특정 컨테이너에 루트 CDN URL을 가리킬 수 있습니다.

## Azure 웹 사이트의 콘텐츠 캐시

웹 사이트의 CDN이 이미지, 스크립트 및 스타일시트와 같은 웹 콘텐츠를 캐시하도록 할 수 있습니다. [Azure CDN과 Azure 웹 사이트 통합](../app-service-web/cdn-websites-with-cdn.md)을 참조하세요.

웹 사이트에 대해 CDN 액세스를 사용하도록 설정하면 관리 포털에서 CDN 도메인 이름을 다음 형식으로 제공합니다. `http://<identifier>.azureedge.net/`. 이 도메인 이름을 사용하여 웹 사이트에서 개체를 검색할 수 있습니다. 예를 들어 cdn이라는 공용 컨테이너와 music.png라는 이미지 파일이 있을 경우 사용자는 다음 두 개의 URL 중 하나를 사용하여 개체에 액세스할 수 있습니다.

- **Azure 웹 사이트 URL**: `http://mySiteName.azurewebsites.net/cdn/music.png`
- **Azure CDN URL**: `http://<identifier>.azureedge.net/cdn/music.png`

## Azure 클라우드 서비스의 콘텐츠 캐시

Azure 클라우드 서비스에서 제공하는 개체를 CDN에 캐시할 수 있습니다.

클라우드 서비스에 대한 캐싱에는 다음과 같은 제약 조건이 있습니다.


- CDN을 사용하여 정적 콘텐츠만 캐시해야 합니다.

	>[AZURE.WARNING] volatile 콘텐츠나 동적 콘텐츠를 캐시하면 성능이 저하되거나 콘텐츠 문제가 발생할 수 있으며 모두 비용 증가를 초래합니다.
- 프로덕션 배포에 클라우드 서비스를 배포해야 합니다.
- 클라우드 서비스는 HTTP를 사용하여 포트 80에서 개체를 제공해야 합니다.
- 클라우드 서비스는 캐시하거나 배달할 콘텐츠를 클라우드 서비스의 /cdn 폴더에 배치해야 합니다.

클라우드 서비스에 대해 CDN 액세스를 사용하도록 설정하면 관리 포털에서 CDN 도메인 이름을 다음 형식으로 제공합니다. `http://<identifier>.azureedge.net/`. 이 도메인 이름을 사용하여 클라우드 서비스에서 개체를 검색할 수 있습니다. 예를 들어 myHostedService라는 클라우드 서비스와 콘텐츠를 배달하는 music.aspx라는 ASP.NET 웹 페이지가 있을 경우 사용자는 다음 두 개의 URL 중 하나를 사용하여 개체에 액세스할 수 있습니다.


- **Azure 클라우드 서비스 URL**: `http://myHostedService.cloudapp.net/music.aspx`
- **Azure CDN URL**: `http://<identifier>.azureedge.net/music.aspx`

## 사용자 지정 원본에서 콘텐츠 캐싱

공개적으로 액세스 가능한 웹 응용 프로그램에서 제공하는 CDN에 개체를 캐시할 수 있습니다.

사용자 지정 원본에 대한 캐싱에는 다음과 같은 제약 조건이 있습니다.

- CDN을 사용하여 정적 콘텐츠만 캐시해야 합니다.

	>[AZURE.WARNING] volatile 콘텐츠나 동적 콘텐츠를 캐시하면 성능이 저하되거나 콘텐츠 문제가 발생할 수 있으며 모두 비용 증가를 초래합니다.
- 공용 IP 주소를 사용하여 서버에서 사용자 지정 원본의 콘텐츠를 호스팅해야 합니다. CDN 에지 노드는 방화벽 뒤의 인트라넷 서버에서 자산을 검색할 수 있습니다.

사용자 지정 원본에 대해 CDN 액세스를 사용하도록 설정하면 Azure 포털에서 CDN 도메인 이름을 다음 형식으로 제공합니다. `http://<identifier>.azureedge.net/`. 이 도메인 이름을 사용하여 사용자 지정 원본에서 개체를 검색할 수 있습니다. 예를 들어 www.contoso.com에 위치한 사이트 및 콘텐츠를 배달하는 music.aspx라는 ASP.NET 웹 페이지가 있을 경우 사용자는 다음 두 개의 URL 중 하나를 사용하여 개체에 액세스할 수 있습니다.


- **사용자 지정 원본 URL**: `http://www.contoso.com/music.aspx`
- **Azure CDN URL**: `http://<identifier>.azureedge.net/music.aspx`

## 쿼리 문자열을 사용하여 특정 콘텐츠 캐시

쿼리 문자열을 사용하여 원본에서 검색된 개체를 구별할 수 있습니다. 예를 들어 원본에서 달라질 수 있는 차트를 표시하는 경우 쿼리 문자열을 전달하여 필요한 특정 차트를 검색할 수 있습니다. 예:

`http://<identifier>.azureedge.net/chart.aspx?item=1`

쿼리 문자열은 문자열 리터럴로 전달됩니다. `?area=2&item=1`과 같은 두 매개 변수를 사용하는 서비스가 있고 이후에 `?item=1&area=2`를 사용하여 원본을 호출하는 경우 동일한 개체의 두 복사본이 캐시됩니다.

쿼리 문자열 캐싱에 대한 자세한 내용은 [쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어](cdn-query-string.md)를 참조하세요.

## HTTPS를 통해 캐시된 콘텐츠 액세스

Azure에서는 HTTPS 호출을 사용하여 CDN에서 콘텐츠를 검색할 수 있습니다. 이렇게 하면 혼합된 보안 콘텐츠 형식에 대한 경고를 받지 않고 CDN에 캐시된 콘텐츠를 보안 웹 페이지에 통합할 수 있습니다.

HTTPS를 사용하여 CDN 콘텐츠에 액세스하는 경우 다음과 같은 제약 조건이 있습니다.


- CDN에서 제공된 인증서를 사용해야 합니다. 타사 인증서는 지원되지 않습니다.
- CDN 도메인을 사용하여 콘텐츠에 액세스해야 합니다. 현재 CDN이 사용자 지정 인증서를 지원하지 않으므로 CNAME(사용자 지정 도메인 이름)에는 HTTPS 지원을 사용할 수 없습니다.

CDN 콘텐츠에 대해 HTTPS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법](cdn-create-new-endpoint.md)을 참조하세요.


## 사용자 지정 도메인을 사용하여 캐시된 콘텐츠 액세스

CDN HTTP 끝점을 사용자 지정 도메인 이름에 매핑하고 해당 이름을 사용하여 CDN에서 개체를 요청할 수 있습니다.

사용자 지정 도메인 매핑에 대한 자세한 내용은 [CDN(콘텐츠 배달 네트워크) 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](cdn-map-content-to-custom-domain.md)을 참조하세요.

## 프로그래밍 방식으로 CDN 관리

Microsoft Azure CDN은 [CDN 리소스 공급자 REST API](https://msdn.microsoft.com/library/mt634456.aspx)를 사용하여 프로그래밍 방식으로 관리할 수 있습니다.


## 참고 항목

- [Azure에 대해 콘텐츠 배달 네트워크를 사용하도록 설정하는 방법](cdn-create-new-endpoint.md)
- [Azure CDN(콘텐츠 배달 네트워크) 개요](cdn-overview.md)
- [Azure CDN 끝점 삭제](cdn-purge-endpoint.md)
- [CDN 리소스 공급자 REST API](https://msdn.microsoft.com/library/mt634456.aspx)

<!---HONumber=AcomDC_0302_2016-->