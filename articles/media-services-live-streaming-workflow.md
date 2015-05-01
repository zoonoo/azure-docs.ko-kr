<properties 
	pageTitle="Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공" 
	description="이 항목은 일반적인 미디어 서비스 라이브 스트리밍 워크플로 단계를 설명합니다." 
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
	ms.date="03/10/2015" 
	ms.author="juliako"/>


# Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공

## 개요

이 항목은 일반적인 AMS(Azure 미디어 서비스) 라이브 스트리밍 워크플로 단계를 설명합니다. 각 단계는 관련 항목에 연결됩니다. 다른 기술을 사용하여 얻을 수 있는 작업의 경우, 선택한 기술(예:.NET 또는 REST)에 연결되는 단추가 있습니다.   

미디어 서비스와 기존 도구 및 프로세스를 통합할 수 있습니다. 예를 들어 현장에서 콘텐츠를 인코딩한 후 여러 형식으로 트랜스코딩하고 Azure CDN 또는 타사 CDN을 통해 전달하기 위해 미디어 서비스에 업로드합니다. 

다음 다이어그램에서는 라이브 스트리밍 워크플로 관련 비디오에 관련된 미디어 서비스 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로][live-overview]

이 항목은 라이브 스트리밍에 관련된 개념을 설명하고 라이브 스트리밍 작업을 달성하는 방법을 시연하는 항목으로 연결합니다.

## 개념

라이브 스트리밍에 관련된 개념은 [미디어 서비스 개념](media-services-concepts.md)을 참조하세요.

## 미디어 서비스 계정 만들기

**Azure 관리 포털**을 이용하여 [Azure 미디어 서비스 계정을 만듭니다](media-services-create-account.md).

## 스트리밍 끝점 구성

스트리밍 끝점에 대한 개요 및 관리 방법에 대한 정보는 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-manage-origins.md)을 참조하세요.

## 개발 환경 설정하기  

개발 환경에 **.NET** 또는 **REST API**를 선택합니다.

[AZURE.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## 프로그래밍 방식으로 연결하기  

Azure Media Services에 프로그래밍 방식으로 연결할 **.NET** 또는 **REST API**를 선택합니다.

[AZURE.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]


## 다양한 비트 전송률 스트림을 채널로 출력하기 위해 온-프레미스 라이브 인코더 사용하기

## 타사 라이브 트랜스코더 사용하기

자세한 내용은 [Azure 미디어 서비스에서 타사 라이브 인코더 사용](https://msdn.microsoft.com/library/azure/dn783464.aspx)(영문)을 참조하세요.

## 채널, 프로그램, 자산 관리하기

**개요**: [채널 및 프로그램 관리하기 개요](media-services-manage-channels-overview.md).

**포털**, **.NET**, **REST API**를 선택하여 예를 봅니다.

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

## 자산 배달 정책 구성

**.NET** 또는 **REST API**를 사용하여 자산 배달 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## 콘텐츠 키 만들기

**.NET** 또는 **REST API**를 사용하여 자산을 암호화하려는 콘텐츠 키를 만듭니다.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

## 콘텐츠 키 인증 정책 구성하기 

**.NET** 또는 **REST API**를 사용하여 콘텐츠 보호 및 키 인증 정책을 구성합니다.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


## 자산 게시 및 제공

**개요**: [콘텐츠 개요 제공하기](media-services-deliver-content-overview.md)

**Azure 관리 포털** 또는**.NET**을 사용하여 자산을 게시합니다(로케이터를 만들어서 게시).

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## Azure CDN 사용하기

미디어 서비스는 Azure CDN과의 통합을 지원합니다. Azure CDN 사용 방법에 대한 자세한 내용은 [미디어 서비스 계정에서 스트리밍 끝점을 관리하는 방법](media-services-manage-origins.md#enable_cdn)을 참조하세요.

## Media Services 계정 크기 조정하기

계정을 프로비전하려는 **스트리밍 예약 단위**의 수를 지정하여 **미디어 서비스** 크기를 조정할 수 있습니다. 

스트리밍 단위 크기 조정에 대한 자세한 내용은 [스트리밍 단위 크기를 조정하는 방법](media-services-manage-origins.md#scale_streaming_endpoints.md).




[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png


<!--HONumber=52-->