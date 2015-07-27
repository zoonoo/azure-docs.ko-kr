<properties 
	pageTitle="미디어 서비스 할당량 및 제한 사항" 
	description="이 항목은 할당량 및 Microsoft Azure 미디어 서비스와 관련된 제한 사항에 설명 합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="juliako"/>


#할당량 및 제한 사항

이 항목은 할당량 및 Microsoft Azure 미디어 서비스와 관련된 제한 사항에 설명 합니다.

## 할당량 및 제한 사항

[AZURE.INCLUDE [azure-mediaservices-limits](../../includes/azure-mediaservices-limits.md)]

##기타 제한 사항

미디어 서비스 제한 메커니즘은 서비스에 과도한 요청을 보내는 응용 프로그램의 리소스 사용을 제한합니다. 해당 서비스에서 서비스를 사용할 수 없음(503) HTTP 상태 코드가 반환될 수 있습니다. 자세한 내용은 [Azure Media Services 오류 코드](http://msdn.microsoft.com/library/azure/dn168949.aspx) 항목에서 503 오류에 대한 설명을 참조하세요.

##<a id="request_higher_limit"></a>업데이트할 수 있는 할당량의 제한을 상향 요청하는 방법

###업데이트할 수 있는 할당량

다음 할당량에 대 한 제한을 지원 티켓을 열어 업데이트를 요청할 수 있습니다.

- 인코딩 단위

- 라이브 채널(실행되고 중지된 상태)
 
- 스트리밍 끝점(실행되고 중지된 상태)
 
- 스트리밍 단위

###지원 티켓 열기

지원 티켓을 열려면 다음을 수행합니다.

1. [지원 받기](https://manage.windowsazure.com/?getsupport=true)를 클릭합니다. 로그인하지 않은 경우 자격 증명을 입력하라는 메시지가 표시됩니다.

1. 사용 중인 구독을 선택합니다.
 
1. 지원 유형으로 "기술적"을 선택합니다.
 
1. "티켓 만들기"를 클릭합니다.
 
1. 다음 페이지에 나타나는 제품 목록에서 "Azure 미디어 서비스"를 선택합니다.
 
1. 사용자의 문제에 적절한 "문제 유형"을 선택합니다.
 
1. 계속을 클릭합니다.
 
1. 다음 페이지의 지시에 따라 문제에 대한 세부 정보를 입력합니다.
 
1. 제출을 클릭하여 티켓을 엽니다.
  

<!---HONumber=July15_HO3-->