<properties 
	pageTitle="미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법" 
	description="이 항목에서는 Azure 관리 포털을 사용하여 스트리밍 끝점을 관리하는 방법을 보여줍니다." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법

일부 기사는 [워크플로 주문형 미디어 서비스 비디오](../media-services-video-on-demand-workflow) 및 [미디어 서비스 라이브 스트리밍 워크플로](../media-services-live-streaming-workflow) 시리즈입니다.  


미디어 서비스 스트리밍 끝점에는 네트워크 CDN (콘텐츠 배달) 추가 배포를 위해 클라이언트 플레이어 응용 프로그램에 직접 또는 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. 현재 Microsoft Azure 미디어 서비스에 원활한 CDN 통합을 제공 하지 않습니다 하지만 (Azure CDN 또는 Akamai) 시장에서 CDN 공급자 중 하나를 사용할 수 있습니다. 라이브 스트림을 또는 미디어 서비스 계정에 자산 주문형 비디오 스트리밍 끝점 서비스의 아웃 바운드 스트림은 될 수 있습니다. 

또한 확장 단위 (스트리밍 단위)를 조정하여 증가 하는 대역폭 수요를 처리하는 스트리밍 끝점 서비스의 용량을 제어할 수 있습니다. 프로덕션 환경에서 응용 프로그램에 대한 하나 이상의 확장 단위를 할당하는 것이 좋습니다. 확장 단위는 200mbps 단위로 구입할 수 있는 전용된 송신 용량 및 동적 패키징을 사용하여 현재 포함된 추가 기능을 모두 사용하여 제공 합니다. 

이 항목에서는 Azure 관리 포털을 사용하여 스트리밍 끝점을 관리하는 방법을 보여줍니다.


##스트리밍 끝점 추가 및 삭제 

1. [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.
2. 선택 된 **스트리밍 끝점** 페이지입니다. 
3. 페이지 아래쪽에 있는 추가 또는 삭제 단추를 클릭합니다. 기본 스트리밍 끝점은 삭제할 수 없습니다. 
4. 시작 단추를 클릭하여 스트리밍 끝점을 시작합니다. 
5. 스트리밍 끝점의 이름을 클릭하여 해당 끝점을 구성합니다.   

	![Origin page][origin-page]

##<a id="scale_streaming_endpoints"></a>스트리밍 끝점 조정

스트리밍 단위 모두 200mbps 단위로 구입할 수 있는 전용 송신 용량 및 현재 [동적 패키징 능력](http://go.microsoft.com/fwlink/?LinkId=276874)을 포함한 추가 기능을 제공합니다. 기본적으로 스트리밍 서버에 대한 리소스(예: 계산, 송신 용량 등)는 다른 모든 사용자와 공유되는 공유 인스턴스 모델에서 구성 됩니다. 스트리밍 처리량을 개선하려면 스트리밍 단위를 구입하는 것이 좋습니다. 

스트리밍 단위의 수를 변경하려면 다음을 수행합니다.

1. 스트리밍 단위 수를 지정하려면 크기 조정 탭을 선택하고 **예약된 용량** 슬라이더를 움직입니다.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 저장 단추를 눌러 변경 내용을 저장합니다.

	새 스트리밍 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다. 

	 
	>[AZURE.NOTE] 현재, 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 주문형 스트리밍을 사용하지 않을 수 있습니다.

	>[AZURE.NOTE] 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.
	
##스트리밍 끝점 구성

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

<!--HONumber=45--> 
