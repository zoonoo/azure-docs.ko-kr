<properties 
	pageTitle="고객에 콘텐츠 배달 개요" 
	description="이 항목에서는 Azure 미디어 서비스를 사용하여 콘텐츠를 배달하는데 필요한 항목을 간략히 설명합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2015" 
	ms.author="juliako"/>


#고객에 콘텐츠 배달 개요

##개요

스트리밍 라이브 이벤트 또는 주문형 비디오 등의 콘텐츠를 고객에게 전달할 때는 다양한 네트워크 조건의 다양한 장치에 고품질 비디오를 제공하는 것이 목표입니다.

이 목표를 위해 다음을 수행합니다.

- 사용자의 스트림을 다중 비트 전송률(가변 품질) 비디오 스트림으로 인코딩(품질 및 네트워크 상태를 관리) 
- 미디어 서비스 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 스트림을 여러 프로토콜로 동적으로 다시 패키징(여러 장치의 스트리밍 관리 가능) 미디어 서비스에서 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다.

이 항목에서는 중요한 콘텐츠 배달 개념에 대해 간략하게 설명합니다.


##동적 패키징

동적 패키징을 사용하여 콘텐츠를 배달하는 것이 좋습니다. 자세한 내용은 [동적 패키징](media-services-dynamic-packaging-overview.md)을 참조하세요.

동적 패키징을 이용하려면 먼저 콘텐츠를 배달할 계획인 스트리밍 끝점에 대한 주문형 스트리밍 단위를 하나 이상 가져와야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-manage-origins.md#scale_streaming_endpoints)을 참조하세요.

##필터 및 동적 매니페스트

미디어 서비스에서는 자산에 대한 필터를 정의할 수 있습니다. 이 필터는 고객이 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생하거나 자산과 연결된 모든 변환 대신 고객의 장치가 처리할 수 있는 오디오 및 비디오 변환의 하위 집합만 지정하는 등을 선택할 수 있도록 하는 서버 측 규칙입니다. 지정한 필터에 따라 비디오를 스트림하는 고객의 요청에 따라 생성된 **동적 매니페스트**를 통해 자산의 필터링이 이루어집니다.

자세한 내용은 [필터 및 동적 매니페스트](media-services-dynamic-manifest-overview.md)를 참조하세요.

##로케이터

콘텐츠 스트림 또는 다운로드에 사용할 수 있는 URL을 사용자에게 제공하려면 먼저 로케이터를 만들어 자산을 "게시"해야 합니다. 로케이터는 자산에 포함된 파일에 액세스할 수 있는 진입점을 제공합니다. 미디어 서비스는 두 가지 유형의 로케이터를 지원합니다.

- **OnDemandOrigin** 로케이터로, 미디어를 스트리밍(예: MPEG DASH, HLS 또는 부드러운 스트리밍)하거나 점진적으로 파일을 다운로드하는 데 사용합니다.
-  **SAS**(액세스 서명) URL 로케이터로, 미디어 파일을 로컬 컴퓨터로 다운로드하는 데 사용합니다. 

**액세스 정책**은 사용 권한(예: 읽기, 쓰기 및 나열) 및 클라이언트가 지정된 자산에 대한 액세스 권한을 갖는 기간을 정의하는 데 사용됩니다. OrDemandOrigin 로케이터를 만들 때는 목록 권한(AccessPermissions.List)을 사용할 수 없습니다.

로케이터는 만료 날짜입니다. 자산을 게시하기 위해 포털을 사용할 때 만료 날짜가 100년인 로케이터가 만들어집니다.

>[AZURE.NOTE]포털을 사용하여 2015년 3월 전에 로케이터를 만들려는 경우, 2년의 만료 날짜가 있는 로케이터가 만들어졌습니다.

로케이터의 만료 날짜를 업데이트하려면 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 또는 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API를 사용합니다. SAS 로케이터의 만료 날짜를 업데이트할 때 해당 URL도 변경됩니다.
 
로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. 개별 사용자에게 서로 다른 액세스 권한을 부여하려면 DRM(디지털 권한 관리)솔루션을 사용 합니다. 자세한 내용은 [미디어 보안 설정](http://msdn.microsoft.com/library/azure/dn282272.aspx)을 참조하세요.

로케이터를 만들 때 Azure 저장소의 필수 저장소 및 전파 프로세스로 인해 30초간 지연될 수 있습니다.


##적응 스트리밍 

적응 비트 전송률 기술에서는 비디오 플레이어 응용 프로그램이 네트워크 상태를 확인하고 여러 가지 비트 전송률 중에서 선택할 수 있습니다. 네트워크 통신 성능이 저하되면 클라이언트에서 더 낮은 비트 전송률을 선택하여 플레이어가 더 낮은 비디오 품질로 비디오를 계속 재생하도록 할 수 있습니다. 네트워크 상태가 향상되면 클라이언트에서 더 높은 비트 전송률로 전환하여 향상된 비디오 품질을 제공할 수 있습니다. Azure 미디어 서비스에서 지원하는 적응 비트 전송률 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS입니다.

사용자에게 스트리밍 URL을 제공하려면 먼저 OnDemandOrigin 로케이터를 만들어야 합니다. 로케이터를 만들면 스트리밍할 콘텐츠를 포함하는 자산에 대한 기본 경로가 제공됩니다. 그러나 이 콘텐츠를 스트리밍하려면 나중에 이 경로를 수정해야 합니다. 스트리밍 매니페스트 파일에 대한 전체 URL을 생성하려면 로케이터의 경로 값과 매니페스트(filename.ism) 파일 이름을 연결해야 합니다. 그런 다음 로케이터 경로에 /Manifest 및 적절한 형식(필요한 경우)을 추가합니다.

>[AZURE.NOTE]SSL 연결을 통해 콘텐츠를 스트리밍할 수도 있습니다. 이렇게 하려면 스트리밍 URL이 HTTPS로 시작해야 합니다.

콘텐츠를 배달하는 출발점이 될 스트리밍 끝점이 2014년 9월 10일 이후에 만들어진 경우에만 SSL을 통해 스트리밍할 수 있습니다. 스트리밍 URL이 9월 10일 이후에 만들어진 스트리밍 끝점을 기반으로 하는 경우 URL에는 "streaming.mediaservices.windows.net"(새 형식)이 포함됩니다. "origin.mediaservices.windows.net"(이전 형식)이 포함된 스트리밍 URL은 SSL을 지원하지 않습니다. URL이 이전 형식인 경우 SSL을 통해 스트리밍할 수 있도록 하려면 새 스트리밍 끝점을 만듭니다. 새 스트리밍 끝점을 기반으로 만들어진 URL을 사용하여 SSL을 통해 콘텐츠를 스트리밍합니다.


##스트리밍 URL 형식

**MPEG DASH 형식**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

예

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



**Apple HLS(HTTP 라이브 스트리밍) V4 형식**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

**Apple HLS(HTTP 라이브 스트리밍) V3 형식**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)
	
	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

**오디오 전용 필터로 Apple HTTP 라이브 스트리밍(HLS) 포맷**

기본적으로 오디오 전용 트랙은 HLS 매니페스트에 포함되어 있습니다. 셀룰러 네트워크에 대한 Apple 스토어 인증이 필요합니다. 이 경우 클라이언트가 충분한 대역폭이 없거나 2G 이상으로 연결되지 않은 경우 오디오 전용 재생으로 전환합니다. 버퍼링 없이 지속적인 스트리밍을 유지할 수 있도록 하지만 비디오가 없는 단점이 있습니다. 그러나 일부 시나리오에서 플레이어 버퍼링은 오디오 전용보다 선호될 수 있습니다. 오디오 전용 트랙을 제거하려는 경우 URL에 추가하고(오디오 전용=false) 제거할 수 있습니다.

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

자세한 내용은 [이 게시물](http://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support)을 참조하세요.


**부드러운 스트리밍 형식**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

예제:

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

**부드러운 스트리밍 2.0 매니페스트(레거시 매니페스트)**

기본적으로 부드러운 스트리밍 매니페스트 형식에는 반복 태그(r 태그)가 포함됩니다. 그러나 일부 플레이어에서는 r 태그를 지원하지 않습니다. 이러한 클라이언트에서는 r 태그를 사용하지 않는 형식을 사용할 수 있습니다.

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

**HDS(Adobe PrimeTime/Access 정식 사용자만 해당)**

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


##동적 패키징

미디어 서비스는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 미디어 서비스에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

동적 패키징을 이용하려면 다음을 수행해야 합니다.

- 중 2층(원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩합니다.
- 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 주문형 스트리밍 단위를 구성합니다. 자세한 내용은 [주문형 스트리밍 예약 단위를 확장하는 방법](media-services-manage-origins.md#scale_streaming_endpoints)을 참조하세요 

동적 패키징에서는 단일 저장소 형식으로 파일을 저장하고 비용을 지불하기만 하면 됩니다. 그러면 미디어 서비스가 클라이언트의 요청에 따라 적절한 응답을 빌드 및 제공합니다.

동적 패키징 기능을 사용할 수 있을 뿐만 아니라, 주문형 스트리밍 예약 단위는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. 기본적으로 주문형 스트리밍은 다른 모든 사용자와 서버 리소스(예: 계산, 송신 기능 등)가 공유되는 공유 인스턴스 모델로 구성되어 있습니다. 주문형 스트리밍 처리량을 개선하려면 주문형 스트리밍 예약 단위를 구입하는 것이 좋습니다.

##점진적 다운로드 

점진적 다운로드를 사용하면 전체 파일이 다운로드되기 전에 미디어 재생을 시작할 수 있습니다. .ism*(ismv, isma, ismt, ismc) 파일을 점진적으로 다운로드할 수 없습니다.

콘텐츠를 점진적으로 다운로드하려면 로케이터의 OnDemandOrigin 유형을 사용합니다. 다음 예제에서는 locator.r의 OnDemandOrigin 유형을 기반으로 하는 URL을 보여 줍니다.

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

다음 고려 사항이 적용됩니다.

- 점진적 다운로드를 위해 원본 서비스에서 스트리밍하려는 저장소 암호화된 자산을 암호 해독해야 합니다.


##다운로드

콘텐츠를 클라이언트 장치에 다운로드하려면 SAS 로케이터를 만들어야 합니다. SAS 로케이터를 통해 파일에 있는 Azure 저장소 컨테이너에 액세스할 수 있습니다. 다운로드 URL을 작성하려면 호스트와 SAS 서명 사이에 파일 이름을 포함해야 합니다.

다음 예제에서는 SAS 로케이터에 기반한 URL을 보여줍니다.

	https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

고려 사항은 다음과 같습니다.

- 점진적 다운로드를 위해 원본 서비스에서 스트리밍하려는 저장소 암호화된 자산을 암호 해독해야 합니다.
- 12시간 안에 완료되지 않은 다운로드는 실패합니다.



##스트리밍 끝점

**스트리밍 끝점**은 추가 배포를 위해 CDN(콘텐츠 배달 네트워크) 또는 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. 스트리밍 끝점 서비스의 아웃 바운드 스트림은 라이브 스트림 또는 미디어 서비스 계정에 주문형 비디오 자산이 될 수 있습니다. 또한 스트리밍 예약 단위를 조정하여 증가하는 대역폭 요구를 처리하기 위해 스트리밍 끝점 서비스의 용량을 제어할 수 있습니다. 제작 환경에서 응용 프로그램에 최소 한 개의 예약 단위를 할당해야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-manage-origins.md#scale_streaming_endpoints)을 참조하세요.


##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##관련된 항목

[저장소 키를 롤링 후 미디어 서비스 로케이터를 업데이트합니다.](media-services-roll-storage-access-keys.md)
 

<!---HONumber=Oct15_HO2-->