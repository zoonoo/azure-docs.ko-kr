<properties 
	pageTitle="미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법" 
	description="이 항목에서는 Azure 관리 포털을 사용하여 스트리밍 끝점을 관리하는 방법을 보여줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 및 [미디어 서비스 라이브 스트리밍 워크플로](media-services-live-streaming-workflow.md) 시리즈의 일부입니다.  


Microsoft Azure 미디어 서비스에서 **스트리밍 끝점**은 추가 배포를 위해 CDN(콘텐츠 배달 네트워크) 또는 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. 미디어 서비스는 매끄러운 Azure CDN 통합도 제공합니다. StreamingEndpoint 서비스의 아웃바운드 스트림은 라이브 스트림이거나 미디어 서비스 계정의 주문형 비디오 자산일 수 있습니다.  

또한 확장 단위 (스트리밍 단위)를 조정하여 증가 하는 대역폭 수요를 처리하는 스트리밍 끝점 서비스의 용량을 제어할 수 있습니다. 프로덕션 환경에서 응용 프로그램에 대한 하나 이상의 확장 단위를 할당하는 것이 좋습니다. 확장 단위는 200mbps 단위로 구입할 수 있는 전용 송신 용량 및 다음을 포함하는 추가 기능을 모두 사용하여 제공합니다. [동적 패키징](https://msdn.microsoft.com/library/azure/jj889436.aspx), CDN 통합 및 고급 구성.

스트리밍 끝점이 실행 상태에 있을 때만 청구됩니다. 

이 항목에서는 스트리밍 끝점이 제공하는 기본 기능의 개요를 제공합니다. 이 항목에서는 스트림이 끝점을 관리하기 위해 Azure 관리 포털을 사용하는 방법도 살펴봅니다.


## 스트리밍 끝점 추가 및 삭제 

.NET SDK, REST API 또는 Azure 관리 포털을 사용하여 스트리밍 끝점을 추가 또는 제거할 수 있습니다.

포털을 사용하여 스트리밍 끝점을 추가\삭제하려면 다음을 수행합니다.

1. [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.
2. 선택 된 **스트리밍 끝점** 페이지입니다. 
3. 페이지 아래쪽에 있는 추가 또는 삭제 단추를 클릭합니다. 기본 스트리밍 끝점은 삭제할 수 없습니다. 
4. 시작 단추를 클릭하여 스트리밍 끝점을 시작합니다. 
5. 스트리밍 끝점의 이름을 클릭하여 해당 끝점을 구성합니다.   

	![스트리밍 끝점 페이지][streaming-endpoint]


기본적으로 최대 2개의 스트리밍 끝점을 가질 수 있습니다. 더 많이 요청해야 하는 경우 [할당량 및 제한 사항](media-services-quotas-and-limitations/)을 참조하세요.

## <a id="scale_streaming_endpoints"></a>스트리밍 끝점 조정

스트리밍 단위 모두 200mbps 단위로 구입할 수 있는 전용 송신 용량 및 현재 [동적 패키징 능력](http://go.microsoft.com/fwlink/?LinkId=276874)을 포함한 추가 기능을 제공합니다. 기본적으로 스트리밍 서버에 대한 리소스(예: 계산, 송신 용량 등)는 다른 모든 사용자와 공유되는 공유 인스턴스 모델에서 구성 됩니다. 스트리밍 처리량을 개선하려면 스트리밍 단위를 구입하는 것이 좋습니다. 

.NET SDK, REST API 또는 Azure 관리 포털을 사용하여 크기를 조정할 수 있습니다.

포털을 사용하여 스트리밍 단위 수를 변경하려면 다음을 수행합니다.

1. 스트리밍 단위 수를 지정하려면 크기 조정 탭을 선택하고 **예약된 용량** 슬라이더를 움직입니다.

	![크기 조정 페이지](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 저장 단추를 눌러 변경 내용을 저장합니다.

	새 스트리밍 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다. 

	 
	>[AZURE.NOTE] 현재, 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 주문형 스트리밍을 사용하지 않을 수 있습니다.

	>[AZURE.NOTE] 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.
	
## <a id="configure_streaming_endpoints"></a>스트리밍 끝점 구성

스트림이 끝점을 사용하면 최소 1개의 크기 조정 단위가 있을 때 다음 속성을 구성할 수 있습니다. 

- 액세스 제어
- 사용자 지정 호스트 이름
- 캐시 제어
- 교차 사이트 액세스 정책

이러한 속성에 대한 자세한 정보는 [스트리밍 끝점](https://msdn.microsoft.com/library/azure/dn783468.aspx)을 참조하세요.

.NET SDK, REST API 또는 Azure 관리 포털을 사용하여 이러한 속성을 구성할 수 있습니다.

포털을 사용하여 스트리밍 단위 수를 변경하려면 다음을 수행합니다.

1. 구성하려는 스트리밍 끝점을 선택합니다.
1. 구성 탭을 선택합니다.
  
필드에 대한 간략한 설명은 다음과 같습니다.

![원본 구성][configure-origin]
  

1. HTTP 응답의 캐시 제어 헤더에서 지정할 최대 캐싱 기간을 설정합니다. 이 값은 Blob 콘텐츠에 명시적으로 설정된 최대 캐시 값을 재정의하지 않습니다.

2. 게시된 스트리밍 끝점에 연결할 수 있는 IP 주소를 지정합니다. IP 주소를 지정하지 않은 경우 모든 IP 주소에서 연결할 수 있습니다.

3. Akamai 서명 헤더 인증에 대한 구성을 지정합니다.

4. Adobe Flash 클라이언트에 대해 도메인 간 액세스 정책을 지정할 수 있습니다(자세한 내용은 [도메인 간 정책 파일 사양](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)을 참조) Microsoft Silverlight 클라이언트에 대한 클라이언트 액세스 정책도 지정할 수 있습니다(자세한 내용은 [도메인 경계 간에 서비스를 사용할 수 있게 만들기](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx 참조).  

5. **구성** 단추를 클릭하여 사용자 지정 호스트 이름을 구성할 수도 있습니다. 자세한 내용은 [StreamingEndpont](https://msdn.microsoft.com/library/dn783468.aspx) 항목에서 **CustomHostNames** 속성을 참조하세요.  


## <a id="enable_cdn"></a>Azure CDN 통합 사용

스트리밍 끝점에 Azure CDN 통합을 사용하도록 지정할 수 있습니다(기본 설정은 사용 안 함).

Azure CDN 통합을 true로 설정하는 방법:

- 스트리밍 끝점에는 최소 1개의 스트리밍(크기 조정) 단위가 있어야 합니다. 나중에 크기 조정 단위를 0으로 설정하려는 경우 먼저 CDN 통합을 사용 안 함으로 설정해야 합니다. 

- 스트리밍 끝점은 중지된 상태에 있어야 합니다. CDN을 사용함으로 설정하면 스트림이 끝점을 시작할 수 있습니다. 

Azure CDN 통합을 사용함으로 설정하는 데에는 최대 90분 가량 소요될 수 있습니다.   

사용함으로 설정되면 다음 구성을 사용할 수 없습니다. **사용자 지정 호스트 이름** 및 **액세스 제어**.

일부 데이터 센터만 Azure CDN 통합을 지원합니다. Azure 관리 포털을 사용하는 경우 데이터 센터를 사용할 수 있으면 **CDN 사용** 옵션이 보입니다. 

![스트리밍 끝점 CDN 사용][streaming-endpoint-enable-cdn]

.NET SDK 또는 REST API를 사용하는 경우, 데이터 센터에 사용할 수 있는 Azure CDN 통합이 있는지 여부를 확인해야 합니다. 

확인하려면 다음을 수행합니다.

1. CdnEnabled를 true로 설정합니다.
1. "현재 지역에서 CDN 기능을 사용할 수 없기 때문에 스트리밍 끝점 CdnEnabled 속성을 true로 설정할 수 없습니다."라는 메시지와 함께 HTTP 오류 코드 412(PreconditionFailed)에 대해 반환된 결과를 확인합니다. 

	이 오류가 표시되면 해당 데이터 센터에서 이를 지원하지 않는 것입니다. 다른 데이터 센터로 시도해야 합니다.


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=52-->