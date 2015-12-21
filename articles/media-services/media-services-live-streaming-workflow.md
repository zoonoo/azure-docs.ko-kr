<properties 
	pageTitle="Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공" 
	description="이 항목에서는 라이브 스트리밍에 관련된 주요 구성 요소의 개요를 제공합니다." 
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
	ms.date="12/05/2015"  
	ms.author="juliako"/>


#Azure 미디어 서비스를 사용하여 라이브 스트리밍 이벤트 제공

##개요

라이브 스트리밍 작업을 수행할 때 일반적으로 다음 구성 요소가 관련됩니다.

- 이벤트를 브로드캐스트하는 데 사용되는 카메라
- 카메라에서 라이브 스트리밍 서비스로 보내는 스트림까지 신호를 변환하는 라이브 비디오 인코더. 
  
	선택적으로 여러 라이브 인코더. 뛰어난 고가용성과 체감 품질이 요구되는 특정 중요한 라이브 이벤트의 경우, 데이터 손실 없이 원활하게 장애 조치(failover)를 실현하기 위해 활성-활성 중복 인코더를 사용하는 것이 바람직합니다.
- 다음을 수행할 수 있도록 하는 라이브 스트리밍 서비스: 
	- 다양한 라이브 스트리밍 프로토콜(예: RTMP 또는 부드러운 스트리밍)을 사용하여 라이브 콘텐츠 수집 
	- 스트림을 적응 비트 전송률 스트림으로 인코딩
	- 라이브 스트림 미리 보기
	- 나중에 스트리밍하기 위해 수집된 콘텐츠 저장(주문형 비디오)
	- 일반적인 스트리밍 프로토콜(예: MPEG DASH, 부드러운, HLS, HDS)을 통해 고객에게 직접 또는 추가 배포를 위해 CDN(콘텐츠 배달 네트워크)에 콘텐츠 배달 
	
		
**Microsoft AMS(Azure 미디어 서비스)**에서는 라이브 스트리밍 콘텐츠를 수집, 인코딩, 미리 보기, 저장 및 배달하는 기능을 제공합니다.

콘텐츠를 고객에게 배달할 때는 서로 다른 네트워크 조건에 따라 다양한 장치에 고화질 영상을 제공하는 것이 목표입니다. 품질 및 네트워크 상태를 관리하려면 라이브 인코더를 사용하여 사용자의 스트림을 다중 비트 전송률(적응 비트 전송률) 비디오 스트림으로 인코딩합니다. 여러 장치에서 스트리밍을 관리하려면 미디어 서비스 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 스트림을 여러 프로토콜로 동적으로 다시 패키징합니다. 미디어 서비스에서 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다.

Azure 미디어 서비스에서 **Channel**, **Program**, and **StreamingEndpoint**는 수집, 형식 지정, DVR, 보안, 확장성 및 중복성을 포함한 라이브 스트리밍 기능 전반을 처리합니다.

**채널**은 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 현재 채널은 다음 방식으로 라이브 입력 스트림을 수신할 수 있습니다.


- 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 RTP(MPEG-TS), RTMP 또는 부드러운 스트리밍(조각화된 MP4) 형식의 하나로 미디어 서비스를 통해 라이브 인코딩을 수행할 수 있는 채널에 전송합니다. 그러면 채널은 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다. 요청된 경우 미디어 서비스는 고객에게 스트림을 배달합니다.

- 온-프레미스 라이브 인코더가 다중 비트 전송률 **RTMP** 또는 **부드러운 스트리밍**(조각화된 MP4)을 채널에 보냅니다. 다중 비트 전송률 부드러운 스트리밍을 출력하는 라이브 인코더인 Elemental, Envivio, Cisco를 사용할 수 있습니다. RTMP를 출력하는 라이브 인코더는 Adobe Flash Live, Telestream Wirecast 및 Tricaster 트랜스코더입니다. 수집된 스트림은 어떠한 추가적인 처리 없이 **채널**을 통과합니다. 또한 라이브 인코더는 라이브 인코딩이 사용되지 않는 채널에 단일 비트 전송률 스트림을 전송할 수 있지만 이 방법은 권장되지 않습니다. 요청된 경우 미디어 서비스는 고객에게 스트림을 배달합니다.


##Azure 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업


다음 다이어그램에서는 채널이 미디어 서비스를 통해 라이브 인코딩을 수행할 수 있는 라이브 스트리밍 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로][live-overview1]

자세한 내용은 [Azure 미디어 서비스를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.


##온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업


다음 다이어그램에서는 라이브 스트리밍 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로][live-overview2]

자세한 내용은 [온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업](media-services-manage-channels-overview.md)을 참조하세요.



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##관련된 항목

[미디어 서비스 개념](media-services-concepts.md)

[Azure 미디어 서비스 조각화된 MP4 라이브 수집 사양](media-services-fmp4-live-ingest-overview.md)





[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png

[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_1210_2015-->