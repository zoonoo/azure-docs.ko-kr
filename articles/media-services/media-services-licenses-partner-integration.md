<properties 
	pageTitle="파트너를 사용하여 Azure 미디어 서비스에 Widevine 라이선스 제공" 
	description="이 문서에서는 Azure 미디어 서비스(AMS)를 사용하여 PlayReady와 Widevine DRM이 모두 있는 AMS에서 동적으로 암호화된 스트림을 전달하는 방법을 설명합니다. PlayReady 라이선스는 미디어 서비스 PlayReady 라이선스 서버에서 제공되며 Widevine 라이선스는 castLabs 라이선스 서버에서 제공됩니다." 
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
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#파트너를 사용하여 Azure 미디어 서비스에 Widevine 라이선스 제공

##개요

Microsoft Azure 미디어 서비스를 사용하면 CENC(Common Encryption) 사양에 따라 암호화되는 Widevine DRM으로 보호된 MPEG DASH를 제공할 수 있습니다.

>[AZURE.NOTE]현재 미디어 서비스는 Widevine 라이선스 서버를 제공하지 않습니다. 다음 AMS 파트너를 사용하여 Widevine 라이선스를 배달할 수 있습니다. [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

[castLabs](http://castlabs.com/company/partners/azure/)를 사용하여 Widevine 라이선스를 제공할 수 있습니다. 자세한 내용은 [castLabs를 사용하여 Azure 미디어 서비스에 DRM 라이선스 제공](media-services-castlabs-integration.md)을 참조하세요.

##Axinom

[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)을 사용하여 Widevine 라이선스를 제공할 수 있습니다. 자세한 내용은 [Axinom을 사용하여 Azure 미디어 서비스에 DRM 라이선스 제공](media-services-axinom-integration.md)을 참조하세요.


##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##참고 항목

[PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-drm.md)

[Mingfei의 블로그](https://azure.microsoft.com/en-us/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Oct15_HO3-->