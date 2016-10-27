<properties 
    pageTitle="파트너를 사용하여 Azure Media Services에 Widevine 라이선스 제공 | Microsoft Azure" 
    description="이 문서에서는 Azure 미디어 서비스(AMS)를 사용하여 PlayReady와 Widevine DRM이 모두 있는 AMS에서 동적으로 암호화된 스트림을 전달하는 방법을 설명합니다. PlayReady 라이선스는 미디어 서비스 PlayReady 라이선스 서버에서 제공되며 Widevine 라이선스는 castLabs 라이선스 서버에서 제공됩니다." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>파트너를 사용하여 Azure 미디어 서비스에 Widevine 라이선스 제공

##<a name="overview"></a>개요

Microsoft Azure 미디어 서비스를 사용하면 CENC(Common Encryption) 사양에 따라 암호화되는 Widevine DRM으로 보호된 MPEG DASH를 제공할 수 있습니다.

미디어 서비스 .NET SDK 버전 3.5.2부터는 미디어 서비스를 사용하여 Widevine 라이선스 템플릿을 구성하고 Widevine 라이선스를 얻을 수 있습니다. 또한 다음 AMS 파트너를 사용하여 Widevine 라이선스를 배달할 수 있습니다. [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

[castLabs](http://castlabs.com/company/partners/azure/) 를 사용하여 Widevine 라이선스를 제공할 수 있습니다. 자세한 내용은 [castLabs를 사용하여 Azure 미디어 서비스에 DRM 라이선스 제공](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 을 사용하여 Widevine 라이선스를 제공할 수 있습니다. 자세한 내용은 [Axinom을 사용하여 Azure 미디어 서비스에 DRM 라이선스 제공](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>참고 항목

[PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-drm.md)

[Mingfei의 블로그](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)




<!--HONumber=Oct16_HO2-->


