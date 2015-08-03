<properties 
	pageTitle="Azure 미디어 서비스 개요" 
	description="이 항목에서는 Azure 미디어 서비스에 대한 개요를 제공합니다." 
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
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Azure 미디어 서비스 개요

Microsoft Azure 미디어 서비스는 개발자가 확장 가능한 미디어 관리 및 배달 응용 프로그램을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. 미디어 서비스는 다양한 클라이언트(예: TV, PC 및 모바일 장치)로 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키징할 수 있는 REST API를 기반으로 합니다.

전체 미디어 서비스를 사용하여 종단 간 워크플로를 작성할 수 있습니다. 또한 워크플로의 일부에 타사 구성 요소를 사용하도록 선택할 수 있습니다. 예를 들어 타사 인코더를 사용하여 인코딩합니다. 그런 다음 미디어 서비스를 사용하여 업로드, 보호, 패키징 및 배달합니다.

미디어 서비스 솔루션을 빌드하려면 다음을 사용할 수 있습니다.

- [미디어 서비스 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 사용 가능한 SDK의 하나: [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services), [Java용 Azure SDK](https://github.com/Azure/azure-sdk-for-java), [Node.js용 Azure 미디어 서비스](https://github.com/fritzy/node-azure-media), [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- 기존 도구: [Azure 관리 포털](http://manage.windowsazure.com/) 또는 [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


다음 포스터는 미디어 만들기부터 소비까지의 Azure 미디어 서비스 워크플로를 보여 줍니다. 포스터는 [Azure 미디어 서비스 포스터](http://www.microsoft.com/download/details.aspx?id=38195)에서 다운로드할 수 있습니다.

![개요][overview]

**SLA(서비스 수준 계약)**:

- 미디어 서비스 인코딩을 위해 REST API 트랜잭션의 99.9% 가용성을 보장합니다.
- 하나 이상의 스트리밍 단위를 구매하는 경우 스트리밍을 위해 기존 미디어 콘텐츠에 대해 99.9% 가용성을 보장하는 요청을 서비스합니다.
- 라이브 채널을 위해 실행 중인 채널이 최소 99.9%의 시간 동안 외부 연결을 사용할 수 있도록 보장됩니다.
- 콘텐츠 보호를 위해 최소 99.9%의 시간 동안 주요 요청을 처리할 것을 보장합니다.
- 인덱서를 위해 99.9%의 시간 동안 인코딩 예약 단위로 처리되는 인덱서 작업 요청을 서비스합니다.

	자세한 내용은 [Microsoft Azure SLA](http://azure.microsoft.com/support/legal/sla/)를 참조하세요.

##개념

자세한 내용은 [개념](media-services-concepts.md)을 참조하세요.


##Azure 미디어 서비스를 사용하여 주문형 미디어 제공

다음 항목에서는 일반적인 미디어 서비스 주문형 비디오 워크플로 단계를 설명합니다. 이 항목은 미디어 서비스에서 지원되는 기술을 사용하여 이들 단계를 수행하는 방법을 보여 주는 다른 항목에 연결됩니다.

[Azure 미디어 서비스를 사용하여 주문형 미디어 제공](media-services-video-on-demand-workflow.md).

##Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공

다음 항목에서는 일반적인 미디어 서비스 라이브 스트리밍 워크플로 단계를 설명합니다. 이 항목은 미디어 서비스에서 지원되는 기술을 사용하여 이들 단계를 수행하는 방법을 보여 주는 다른 항목에 연결됩니다.

[Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공](media-services-live-streaming-workflow.md).

##콘텐츠 사용

Azure 미디어 서비스는 iOS 장치, Android 장치, Windows, Windows Phone, Xbox 및 셋톱 박스를 포함한 대부분의 플랫폼에서 풍부한 동적 클라이언트 플레이어 응용 프로그램을 만드는 데 필요한 도구를 제공합니다. 다음 항목에서 제공하는 SDK 및 플레이어 프레임워크 링크를 사용하여 미디어 서비스의 스트리밍 미디어를 사용할 수 있는 클라이언트 응용 프로그램을 개발할 수 있습니다.

[비디오 플레이어 응용 프로그램 개발](media-services-develop-video-players.md)

##패턴 및 작업 방식 지침

[패턴 및 작업 방식 지침](https://wamsg.codeplex.com/) [온라인 설명서](https://msdn.microsoft.com/library/dn735912.aspx) [다운로드 가능한 전자책](https://www.microsoft.com/download/details.aspx?id=42629)

##지원

[Azure 지원](http://azure.microsoft.com/support/options/)에서는 미디어 서비스를 포함하여 Azure에 대한 지원 옵션을 제공합니다.

##다음 단계

[Azure 미디어 서비스를 사용하여 라이브 스트리밍 제공](media-services-live-streaming-workflow.md)

[비디오 플레이어 응용 프로그램 개발](media-services-develop-video-players.md)
 
[비디오 플레이어 응용 프로그램 개발](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=July15_HO4-->