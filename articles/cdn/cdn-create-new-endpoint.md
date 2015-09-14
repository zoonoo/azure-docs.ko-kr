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
	ms.date="09/01/2015"
	ms.author="mazha"/>



#Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법  

Azure 관리 포털을 통해 원본에 대해 CDN을 사용할 수 있습니다. 현재 사용 가능한 원본 형식은 웹앱, 저장소, 클라우드 서비스가 있습니다. 또한 Azure 미디어 서비스 스트리밍 끝점에 대 해 CDN을 사용할 수 있습니다. 원본에 대해 CDN 끝점을 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN 에지 캐싱에 적합합니다.

이제 사용자 지정 원본도 만들 수 있으며 꼭 Azure일 필요는 없습니다.

##새 CDN 끝점을 만들려면  

1.	[Azure 관리 포털](http://manage.windowsazure.com/)에 로그인합니다.
2.	탐색 창에서 **CDN**을 클릭합니다.
3.	리본에서 **New**를 클릭합니다. **New** 대화 상자에서 **APP SERVICES**, **CDN**, **QUICK CREATE**를 차례로 선택합니다.
4.	**원본 형식** 드롭다운 있는 사용 가능한 원본 유형 목록에서 원하는 원본 형식을 선택합니다.
	
	그러면 **원본 URL** 드롭다운 목록에 사용 가능한 원본 URL 목록이 표시됩니다.
	

	![createnew][createnew]

	**사용자 지정 원본**을 선택하면 사용자 지정 URL을 입력할 수 있습니다. Azure 원본이 아니어도 상관 없습니다.

	![customorigin][customorigin]

	>[AZURE.NOTE]현재는 원본에 HTTP만 지원되며 미디어 서비스 확장명을 사용하여 Azure 미디어 서비스 스트리밍 끝점에 대해 Azure CDN을 사용하도록 해야 합니다.
	
5.	**Create** 단추를 클릭하여 새 끝점을 만듭니다.


>[AZURE.NOTE]끝점에 대해 만든 구성은 즉시 사용할 수 없습니다. 등록이 CDN 네트워크를 통해 전파되는 데 최대 60분 정도 걸릴 수 있습니다. 사용자가 CDN 도메인 이름을 즉시 사용하려고 시도할 경우 CDN을 통해 콘텐츠를 사용할 수 있을 때까지 상태 코드 400(잘못된 요청)이 표시될 수 있습니다.

##참고 항목
[CDN(콘텐츠 배달 네트워크) 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=September15_HO1-->