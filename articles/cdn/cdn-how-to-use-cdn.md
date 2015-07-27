<properties 
	pageTitle="CDN 사용 방법 - Azure 기능 가이드" 
	description="Azure CDN(콘텐츠 배달 네트워크)을 사용하여 Blob 및 정적 콘텐츠를 캐시해 고대역폭 콘텐츠를 배달하는 방법을 알아봅니다." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="mazha"/>


# Azure에 CDN 사용

Azure CDN(콘텐츠 배달 네트워크)은 Azure에서 HTTP 응용 프로그램의 크기를 조정하기 위한 기본 구성 요소입니다. CDN은 콘텐츠를 캐시하고 최종 사용자에게 가까운 곳으로 배달하여 Azure 고객에게 글로벌 솔루션을 제공합니다. 그 결과, 매번 원본을 적중하는 대신 사용자 요청이 지능적으로 성능이 가장 뛰어난 CDN 에지 POP로 라우팅됩니다. 이 경우 성능 및 사용자 환경이 훨씬 개선됩니다. 현재 CDN 노드 위치 목록은 [Azure CDN 노드 위치]를 참조하세요.

CDN을 사용하여 Azure 데이터를 캐시할 경우의 혜택은 다음과 같습니다.

-   콘텐츠 소스에서 멀리 떨어져 있으며 콘텐츠를 로드하기 위해 많은 '인터넷 트립'이 필요한 응용 프로그램을 사용 중인 최종 사용자의 성능 및 사용자 환경 향상
-   제품 런칭 등의 이벤트를 시작할 때 순간적인 높은 부하를 더 효율적으로 처리하기 위한 대규모 분산
-   사용자 요청을 분산하고 글로벌 에지 POP에서 콘텐츠를 제공하면 원본으로 전송되는 트래픽 양이 감소하므로 원본이 오프로드됩니다.

이제 기존 CDN 고객이 [Azure 관리 포털]에서 Azure CDN을 사용할 수 있습니다. CDN은 구독에 대한 추가 기능이며 별도의 [요금제]를 사용합니다.

##1단계: Azure에 CDN 원본 만들기

CDN 원본은 CDN이 콘텐츠를 가져와 에지 POP에 캐시하는 원본 위치입니다. Azure 통합 원본에는 Azure 앱, 클라우드 서비스, 저장소 및 미디어 서비스가 포함됩니다.

## 2단계: Azure 원본을 가리키는 CDN 끝점 만들기

원본이 설정되고 나면 [새 CDN 끝점을 만들](cdn-create-new-endpoint.md) 때 원본 목록에서 사용할 수 있습니다.

> [AZURE.NOTE]끝점에 대해 만든 구성은 즉시 사용할 수 없습니다. 등록이 CDN 네트워크를 통해 전파되는 데 최대 60분 정도 걸릴 수 있습니다. 사용자가 CDN 도메인 이름을 즉시 사용하려고 시도할 경우 CDN을 통해 콘텐츠를 사용할 수 있을 때까지 상태 코드 400(잘못된 요청)이 표시될 수 있습니다.

## 3단계: CDN 구성 설정 

CDN 끝점에 대해 캐싱 정책, 쿼리 문자열 캐싱 등의 다양한 기능을 사용하도록 설정할 수 있습니다.

## 4단계: CDN 콘텐츠 액세스

CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 예를 들어 캐시된 Blob의 주소는 다음과 유사합니다. http://<*CDNNamespace*>.vo.msecnd.net/<*myPublicContainer*>/<*BlobName*>



## 참고 항목

[Azure CDN(콘텐츠 배달 네트워크) 개요](cdn-overview.md)
 

<!---HONumber=July15_HO3-->