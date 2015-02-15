<properties 
	pageTitle="미디어 서비스 계정에서 원본을 관리하는 방법" 
	description="" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법

미디어 서비스를 사용하여 계정에 여러 스트리밍 끝점을 추가하고 스트리밍 끝점을 구성할 수 있습니다. 각 미디어 서비스 계정은 **기본**이라는 하나 이상의 스트리밍 끝점이 연결되어 있습니다.

>[AZURE.NOTE] 이전에는 스트리밍 끝점의 명칭이 원본이었습니다. 


## 스트리밍 끝점 추가 및 삭제 

1. [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.
2. 스트리밍 끝점 페이지를 선택합니다. 
3. 페이지 아래쪽에 있는 추가 또는 삭제 단추를 클릭합니다. 기본 스트리밍 끝점은 삭제할 수 없습니다. 
4. 시작 단추를 클릭하여 스트리밍 끝점을 시작합니다. 
5. 스트리밍 끝점의 이름을 클릭하여 해당 끝점을 구성합니다.   

	![Origin page][origin-page]

## 스트리밍 끝점 조정

스트리밍 단위는 200Mbps 단위로 구입할 수 있는 전용 송신 용량 및 동적 패키징 기능  및 [동적 패키징 기능](http://go.microsoft.com/fwlink/?LinkId=276874)이 현재 포함된 추가 기능을 제공합니다. 기본적으로 주문형 스트리밍은 다른 모든 사용자와 서버 리소스(예: 계산, 송신 기능 등)가 공유되는 공유 인스턴스 모델로 구성되어 있습니다. 주문형 스트리밍 처리량을 개선하려면 스트리밍 단위를 구입하는 것이 좋습니다. 

스트리밍 단위의 수를 변경하려면 다음을 수행합니다.

1. 스트리밍 단위 수를 지정하려면 크기 조정 탭을 선택하고 **예약된 용량** 슬라이더를 움직입니다.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 저장 단추를 눌러 변경 내용을 저장합니다.

	새 스트리밍 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다. 

	 
	>[AZURE.NOTE] 현재, 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 주문형 스트리밍을 사용하지 않을 수 있습니다.

	>[AZURE.NOTE] 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.
	
## 스트리밍 끝점 구성

이 이미지에 나온 대로 구성 탭을 사용하여 구성을 수행할 수 있습니다. 필드에 대한 설명은 다음과 같습니다.

>[AZURE.NOTE] 이 페이지의 구성은 하나 이상의 예약 단위가 있는 스트리밍 끝점에만 적용됩니다. 주문형 스트리밍 예약 단위를 예약하려면 다음을 수행합니다.

![Configure origin][configure-origin]
  

1. HTTP 응답의 캐시 제어 헤더에서 지정할 최대 캐싱 기간을 설정합니다. 이 값은 Blob 콘텐츠에 명시적으로 설정된 최대 캐시 값을 재정의하지 않습니다.

2. 게시된 스트리밍 끝점에 연결할 수 있는 IP 주소를 지정합니다. IP 주소를 지정하지 않은 경우 모든 IP 주소에서 연결할 수 있습니다.

3. Akamai 서명 헤더 인증에 대한 구성을 지정합니다.

4. Adobe Flash 클라이언트에 대해 도메인 간 액세스 정책을 지정할 수 있습니다. 자세한 내용은 [도메인 간 정책 파일 사양](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)을 참조하세요. Microsoft Silverlight 클라이언트에 대한 클라이언트 액세스 정책도 가능합니다. 자세한 내용은 [도메인 경계 간에 서비스를 사용할 수 있게 만들기](https://msdn.microsoft.com/ko-kr/library/cc197955(v=vs.95).aspx)(영문)를 참조하세요.  

5. **구성** 단추를 클릭하여 사용자 지정 호스트 이름을 구성할 수도 있습니다. 자세한 내용은 [StreamingEndpont](https://msdn.microsoft.com/ko-kr/library/dn783468.aspx) 항목의 **CustomHostNames** 속성을 참조하세요.  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=42-->
