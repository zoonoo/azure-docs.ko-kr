<properties 
	pageTitle="미디어 서비스에서 자산을 암호화하는 방법 - Azure" 
	description="Microsoft PlayReady Protection을 사용하여 미디어 서비스에서 자산을 암호화하는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다." 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# 방법: PlayReady 또는 AES 128로 자산을 동적으로 암호화하기

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 및 [미디어 서비스 라이브 스트리밍 워크플로](media-services-live-streaming-workflow.md) 시리즈의 일부입니다.
  
## 개요

Microsoft Azure 미디어 서비스를 사용하면 128 비트 암호화 키를 사용하는 AES(Advanced Encryption Standard) 및 PlayReady DRM로 동적 암호화된 콘텐츠를 제공할 수 있습니다. 또한 미디어 서비스는 인증된 클라이언트에게 키 및 PlayReady 라이선스를 배달하는 서비스를 제공합니다. 보호되는 콘텐츠를 배달하려면 콘텐츠 키 인증 정책 및 자산 배달 정책을 구성해야 합니다.

## 구성

콘텐츠 키 인증 정책을 구성하는 방법에 대한 내용은 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

자산 배달 정책 구성
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]
 


<!--HONumber=52-->