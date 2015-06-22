<properties 
	 pageTitle="Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법" 
	 description="이 항목에서는 Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법을 설명합니다." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법  

저장소 계정, 클라우드 서비스와 같은 원본에 대해 CDN 끝점을 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN 에지 캐싱에 적합합니다.  


##새 CDN 끝점을 만들려면  

1.	[Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.
2.	탐색 창에서 **CDN**을 클릭합니다.
3.	리본 메뉴에서 **새로 만들기**를 클릭합니다. **새로 만들기** 대화 상자에서 **앱 서비스**, **CDN**, **빠른 생성**을 차례로 선택합니다.
4.	**원본 도메인** 드롭다운의 사용 가능한 원본 유형 목록에서 원하는 대상을 선택합니다.
5.	**만들기** 단추를 클릭하여 새 끝점을 만듭니다.




> 참고: 끝점에 대해 만든 구성은 즉시 사용할 수 없습니다. 등록이 CDN 네트워크를 통해 전파되는 데 최대 60분 정도 걸릴 수 있습니다. 사용자가 CDN 도메인 이름을 즉시 사용하려고 시도할 경우 CDN을 통해 콘텐츠를 사용할 수 있을 때까지 상태 코드 400(잘못된 요청)이 표시될 수 있습니다.

#참고 항목
[CDN(콘텐츠 배달 네트워크) 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](cdn-map-content-to-custom-domain.md)

<!--HONumber=49--> 